---
layout: post
title: "Synesthetic Phonetics Part 2"
date: 2017-04-28 19:38:33 -0700
comments: true
sharing: true
categories: lambda lyrics music programming rap serverless synesthesia
---

## Use this fiddle to color your own lyrics!
<script async src="//jsfiddle.net/jsat/3pzhn23a/embed/result/"></script>

[Fullscreen](http://jsfiddle.net/jsat/3pzhn23a/embedded/result/#Result)

**[Github for CLI](https://github.com/JohnSatriano/ColorLyrics)**

## Context
In my [previous blogpost](http://jsat.io/blog/2015/10/14/synesthetic-phonetics/) I described a program I wrote that colorizes the phonetic patterns in song lyrics. I included a few examples that demonstrated what the program can do, but I did not give others the opportunity to play with its functionality. After a year and change, I decided to implement the changes necessary to expose this nifty program to the public. The result is above, enjoy!

# Implementation

*If you're looking for implementation details related to the colorization of lyrics, [see my previous blogpost](http://jsat.io/blog/2015/10/14/synesthetic-phonetics/).*

## Summary

When AWS Lambda was announced, I knew it would be the perfect avenue for this project. The serverless, atomic nature of Lambda suited the needs of a straight-forward I/O application like this one. This section will describe how I retrofitted my program to work with AWS Lambda. To summarize, this application uses a javascript front-end to POST a request to an AWS API Gateway endpoint which routes the input to an AWS Lambda function that returns the colorized lyrics in HTML. Using AWS Lambda + API Gateway did not come without challenges. I tried a number of misguided workarounds to get my application working, but I'll only describe what actually worked.

## Supporting Libraries
The [AWS Lambda application environment](http://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html) is a specific flavor of 64 bit Amazon Linux that **does not contain 32 bit libraries**. I originally developed this application on 64bit Ubuntu that had compatibility for 32 bit libraries. eSpeak, the application I used to convert text to phonetic symbols, is not available on the Lambda flavor of Linux as well. I had to get a portable version of 64bit eSpeak. Solving this problem was a headache and a half, but the solution ended up being relatively straight-forward. I pulled the source for eSpeak off SourceForge and spun up an EC2 instance running the flavor of Linux Lambda uses. There I compiled the source for eSpeak with a few tweaks. eSpeak by default expects dictionary files to be located in `/usr/share/espeak-data` , but a Lambda program doesn't have permissions on those folders. I made a config change to expect the dictionary files in the folder where the program is executed. eSpeak also expects an audio library to execute because one of its main function is text-to-speech. Luckily commenting out the audio compilation steps in the Makefile worked without breaking everything. Lessons learned:

1. Don't try to port an application to another environment without stepping into that environment.
2. Don't fear the Makefile, but respect the Makefile.
3. Portable programs often require nontrivial additional work.

## Supporting Python3
*At the time of writing, AWS Lambda did not support Python3. [This recently changed.](https://aws.amazon.com/about-aws/whats-new/2017/04/aws-lambda-supports-python-3-6/)*

AWS Lambda also only supports a few specific runtimes for executing the application: Python2.7, Java8, Node, and .NET. In my application's case, I was using a mix of bash, Python3 and an application fetched from APT (espeak). Luckily, you can call the system via shell script from the limited selection of runtimes, and Python3 just so happens to be available. So essentially, Python3 is technically supported with this workaround. I believe other scripting languages like Ruby are also available using this hack.

## I/O Workarounds
AWS Lambda *really* wants you to use JSON. It's understandable; I'm sure most applications using Lambda are talking to other applications. However, I wanted: input:text, output:html. [This blogpost](https://kennbrodhagen.net/2016/01/31/how-to-return-html-from-aws-api-gateway-lambda/) was very useful for returning HTML from Lambda + API Gateway. In order to accept plain text as an input, I used the Integration Request template "Method Request passthrough" which maps the body of the request to a JSON element "body-json". The Lambda application I wrote reads in this JSON element. You cannot avoid the JSON in AWS Lambda (easily).

## Conclusion

I'm glad I decided to finish this project. This experience gave me the opportunity to learn about the intricacies of serverless deployments and how to make them work. I will certainly consider using serverless architecture providers like AWS Lambda in the future.
