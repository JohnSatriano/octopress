---
layout: post
title: "Synesthetic Phonetics"
date: 2015-10-14 22:50:01 -0700
comments: true
sharing: true
categories: lyrics music programming rap synesthesia
---

## "Lose Yourself" by Eminem, phonetically colored.

This blog post explores synesthesia as it relates to music and lyrics. I wrote a program that colorizes song lyrics to expose the complex rhyming patterns used by talented lyricists.

The following is the lyrics to Eminem's "Lose Yourself" paired with its [**International Phonetic Alphabet**](https://en.wikipedia.org/wiki/International_Phonetic_Alphabet) notation. I recommend listening to [the song](https://open.spotify.com/track/77Ft1RJngppZlq59B6uP0z) first or while scrolling through this frame to fully experience the patterns.

<iframe width="100%" height="400" src="/synesthesia/loseYourself.html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

# Inspiration

[Synesethesia](https://en.wikipedia.org/wiki/Synesthesia) is really weird. To quote Wikipedia, synesthesia is "a neurological phenomenon in which stimulation of one sensory or cognitive pathway leads to automatic, involuntary experiences in a second sensory or cognitive pathway." Some people see colors when they see letters or numbers, some see numbers in two or three dimensional space, and **some even taste guacamole when they hear the word "Chipotle"** (ok, maybe that's everyone). I've always been fascinated by synesthesia and its possible practical applications. If numbers were innately colored, not just by individual digits, what kind of patterns would emerge? [What if words were colored? Would it be easier to read?](http://www.beelinereader.com/) A form of synesthesia you're experiencing right now is the association of these words with sounds in your mind... unless you're mute. Let's go further. Some forms of synesthesia combine audible experiences like music with colors. Could lyrics in music invoke color?

## Patterns in Music

Music theory in a nutshell: music sounds good because it follows certain patterns. As demonstrated in my previous blog post, even a little structure can make random garbage sound good. The [best music](http://ratatatmusic.com), however, creatively combines various structures together in ways that engage the listener. The most standard way to visualize music is through notes drawn on a set of bars that denote what is to be played. For the musically talented, this allows them to translate what they see into sound. As I write this post, I'm imagining sheet music colored. It would be ugly, but wouldn't it be convenient if every note had its own color? Sure enough, someone thought of it first:

![Colored musical notes. Source: http://fretlessfingerguides.com ](http://www.fretlessfingerguides.com/images/ColorCodedMusic/Songs/cielito_lindo_easy_violin_sheet_music.png)

I'm surprised this isn't more common. The most useful aspect of this coloring would be for notes that are far above or below the clef. I'd like to see something like this without the letters on a more complex piece. Another project for another day.

## Poetry in Motion

The words that artists use in their music add an entirely new dimension to the art form. Combine poetry and music and what's left is a song. Poetry is an art unto itself that has various techniques and patterns lyricists use to make the words sound more pleasing. The most common of which is [**rhyme**](https://en.wikipedia.org/wiki/Rhyme), and there is an incredible amount of depth in the subject. Many songs use rhyme, but rap tends to rely on the synergy of words and their collective sounds more than any other genre. Check out this video for a demonstration of the outstanding detail in the rhyme of Eminem's "Lose Yourself":

{% youtube ooOL4T-BAg0 %}

This fantastic video on rhyming inspired me to write this program, and it made me wonder: What if each phonetic sound had a unique color and were superimposed over the lyrics. Wouldn't it be cool to see the lyrical detail exposed in color?

# Implementation

So that's what I did. I made a tool that colorizes the most common phonetic sounds in song lyrics and converts them to an HTML page for people to view. From here I'll go into the technical details of how I wrote this program.

## Words to Sounds

First, I began looking for ways to convert the complex English language into phonetic symbols like one might find in the dictionary. I knew that this task alone was an enormous undertaking, so I furiously googled for a program that already existed. After multiple attempts to use third party libraries or scrape [Wiktionary](https://wiktionary.org), I found the solution under my nose. Linux distributions usually come preinstalled with a program called [eSpeak](http://espeak.sourceforge.net/). This made converting lyrics to the International Phonetic Alphabet as easy as

    cat loseYourself | espeak --ipa -q

This funnels the lyrics into eSpeak and outputs the words in IPA. The `-q` operator prevents eSpeak from audibly speaking the lyrics. eSpeak is no Eminem.

eSpeak has some limitations. eSpeak implies an interesting, possibly European, accent and will never be able to capture [slant rhymes](https://en.wikipedia.org/wiki/Half_rhyme). Some words may be translated into a phonetic sound that we don't really use in America, but it's [close enough](http://www.pickapop.fr/365-thickbox_default/mug-meme-close-enough.jpg).

## Sounds to Colors

The next challenge was converting the newly phonetic lyrics into some sort of meaningful color scheme. First, I needed a method of coloring. I looked into libraries that converted text to an image like [ImagingBook](http://effbot.org/imagingbook/) python library, but I needed something faster. Then I remembered that the Linux terminal supports colors! Luckily, someone has already written a python library for formatting terminal output with color, [termcolor](https://pypi.python.org/pypi/termcolor). Next, I need to figure out how to color these lyrics.

I considered searching for rhyme patterns, but it became too complex. I decided to give each IPA symbol its own color and the individual colors would work together to expose these rhyme patterns. However, there are a limited number of colorings available to the terminal (6 colors and 6 color backgrounds). This limitation was unfortunate, but I think it helps keep the final product from becoming too cluttered. To cope with this limitation, I decided that vowel sounds were the most important for coloring rhyming patterns. I filtered out everything but the vowel sounds and vowel modifiers. IPA has a number of modifiers that change the way a character sounds. The most common is **'ː'** which indicates a long vowel. I decided to pair the modifier with the associated vowel and treat it as a single unique sound character as well. For example, **'uː'** represents the long **'u'** sound.

Given these phonetic vowel characters, I found the 12 most used characters and gave them a unique color. I gave the most popular characters the background colors because they stand out the most. After I had a reliable coloring scheme I had a bunch of gibberish on the screen that was color coded for some reason. To make this readable, I printed out each plain English line before each colorized phonetic line. There was some additional black magic hackery to make the lines match properly, but that's less interesting. Finally, I used [**this script**](https://github.com/pixelb/scripts/blob/master/scripts/ansi2html.sh) to convert the [ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code) output to html and added some of my own formatting. The end product is fairly readable and the patterns are evident. Here's one of my favorite verses from the Wu Tang Clan's ["Da Mystery of Chessboxing"](https://play.spotify.com/track/4xOTVtbCPcfxMs2hSt9D6i):

<iframe width="100%" height="400" src="/synesthesia/ChessboxingInspectaDeck.html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

# Conclusion

Synesthesia is a cool concept/phenomenon that can enhance our perception of reality. I think mixing senses helps us grok things more quickly, and there are plenty of other ways we can combine senses for our collective benefit. This tool is my first stab at it, but maybe I'll make something better in the future. I have an [Oculus Rift DK2](http://oculus.com), which I noticed has a distinct lack of smell-o-vision.

So this is the part where I link to the code on Github, right? The code is pretty nasty right now, so I think I'll take some time to clean it up before sharing. However, if you have a sick rhyme that needs coloring, [**let me know.**](mailto:jack@jsat.io)

I promise my next blog post won't be about music!
