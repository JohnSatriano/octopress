---
layout: post
title: "Creating a Pop Ballad Generator"
date: 2015-03-26 22:00:37 -0500
comments: true
sharing: true
categories:  music programming javascript software theory random number generator
---

<iframe width="100%" height="400" src="//jsfiddle.net/jsat/Mb8sQ/embedded/result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

#Randomly Generated Content

[Procedurally Generated Content](https://en.wikipedia.org/wiki/Procedural_generation) is a method of creating content using algorithms. It originally served to save space for video games on systems with limited memory, but game developers soon discovered that they could create near infinite unique experiences by procedurally generating content with the power of **[Random Number Generators](https://en.wikipedia.org/wiki/Random_number_generation)**. 

Game devs continue to improve techniques of random content generation. Random numbers allow them to multiply the creative potential of their works. One of the most popular examples of random numbers powering content generation is [Minecraft](http://minecraft.org). Minecraft creates an entirely new world when a player starts a new game. While algorithms guide the creation of their world, **each and every player's world is unique.** Minecraft systematically generates a wonderful hodgepodge of forests, oceans, deserts, tundra, caves, enemies, and treasures that are unique to each and every player. This trend has taken off and provided humanity with some of the world's best gaming experiences.

![A thing of beauty. Source: http://topminecraftworldseeds.com/](http://topminecraftworldseeds.com/wp-content/uploads/2014/10/1.8-minecraft-seed-triple-crater-caverns-1.jpg)

#Randomly Generated Music 

I continue to enjoy games that use this type of content creation. In fact, it may be my [favorite genre of games](https://en.wikipedia.org/wiki/Roguelike). I suppose it's the result of my obsession that I began to think of other mediums where we could use random numbers to procedurally generate content. The first thing that came to mind was music. [I'm not going to tell you that I'm the first person to think of this concept. ](https://en.wikipedia.org/wiki/Generative_music) However, I want to present to you my attempt at outsourcing musical creativity to the power of computers and random numbers.

## Methodology

### Middle school band, where dreams are made

As a quick musical autobiography, I played piano for 2 years starting in 2nd grade and played trumpet for 4 years from 5th to 8th grade. In middle school I played in the jazz band, and our instructor introduced us to a beautiful thing, **improvisation**. While learning to improvise, students are given a set of rules: play at X beats per minute and use these seven specific notes ([a key](https://en.wikipedia.org/wiki/Key_%28music%29)) in any octave. Given these rules, it isn't very hard to do some basic improvisation with some minor proficiency in scales. By mixing up the rhythm and the seven notes given, improvisation almost comes naturally. 

### Pop knows best, right?

Have you ever thought that all pop sounds the same? Have you ever been frustrated with hipsters who say these things? Well, there might be some truth to their ire. There is a common formula (read: algorithm) to many of the pop songs we hear on the radio and television. This formula is the combination of any key and a special [chord progression](https://en.wikipedia.org/wiki/Chord_progression): **[I-V-vi-IV](https://en.wikipedia.org/wiki/I%E2%80%93V%E2%80%93vi%E2%80%93IV_progression)**. It is truly stunning how many songs use this chord progression to drive their theme. Somehow it manages to capture the human ear in a special way that unites Western culture. Could the popularity of the chord progression simply be self perpetuating? Maybe, but I think there's more to it than that. I'm not a musical theorist or any sort of sociologist; I'm a software engineer. So I'll just write something in javascript that's useful for about five minutes before people move on to the next thing on the internet.

Here's a fun example of the four chords in action. There's no denying its influence.

{% youtube oOlDewpCfZQ %}

### Bringing it all together

So now I've gone over a few concepts: Random content generation, improvisation, and the pop mega chord progression of your dreams/nightmares. Based on these concepts, let's make some really naive assumptions. 

* **Random content generation is awesome.**
* **Improvisation is easy when given a beat and a key.**
* **Music is easy to make in I-V-vi-IV.**

Given these assumptions, _anyone_ can make a hit pop record by randomly playing notes in a key while playing the pop chord progression in the background. Even a computer. I ran with this idea and created the music generator at the top of the page. After listening for a moment, it sounds like a chaotic pop ballad, hence the name!

## Implementation

Now I'll go over the technical details of the project. If you're not technical and/or familiar with music theory, this section may get a little hairy. 

### Picking a platform

I wanted to make a computer attempt to make random music in a key with a special chord progression. I also wanted people I know to be able to use it free of charge. The only platform I know that's available ubiquitously in that manner is the web browser. Javascript runs on almost every web browser from your phone to your PC to your Mac. This availability made it perfect for my program. And luckily, it has a relatively simple library for artificial sound.


### Determining frequencies
The javascript library for sound, AudioContext, allows you to create pure oscillators that take an input frequency and play a basic waveform until you tell it to stop. There isn't any built in logic for musical notes, so I needed to set up the math to allow me to work within the framework of modern music. I was surprised to learn that there is a very specific equation based on a constant derived from a fractional exponent to determine musical notes. Given this constant, you can determine each note of a scale by taking the appropriate steps. Below is the equation for the nth half step in a scale given a base note $$f_{0}$$, which in this case is the key.

$$ 

f_{n} = f_{0} a^{n} \\
\\
\mbox{where} \\
\\
a = 2^{1/12}\\

$$

### Deriving scales
Given this equation, I was able to generate 7 note scales with a base key. Scales follow another formula to determine each note in the scale. There are 2 full steps followed by a half step, 3 full steps, and finally one half step. If this sounds completely foreign, take a look at a piano keyboard. Each directly adjacent key is a half step, so if there is a black key inbetween, the two keys are a whole step apart. The C scale is a great example because it uses no black keys.

![A scale starting at C, E-F and B-C are half steps. Source: http://bandnotes.info/tidbits/scales/half-whl.htm](http://bandnotes.info/tidbits/scales/images/image_726.gif)

Here's the for loop I use to generate an array that I use to find the correct frequencies to play in a scale. The `base` variable is derived from a set of constant frequencies for each key and multiplied by the desired octave. The `range` is the number of notes in the scale we want to generate. 

    // Build scale
    var buildScale = function () {
      notes = [];
      var freq = base;
      var step = 0;
      for (var i = 0; i < range; i++) {
        notes[i] = freq
        step++;
        //handle half steps
        if (i % 7 != 2 && i % 7 != 6) {
          step++;
        }
        freq = base * Math.pow(a, step);
      }
    }



### Chord Progression

Chord progressions, in a nutshell, are a sequence of chords played in the background of a song to help drive the theme and feel of the music. Chords can be loosely defined as 3 notes played simultaneously. Now that I have an array of notes to pick from, it's easy to generically define the notes I need to play for each chord in the progression. Here is the data structure I use:

    //I   V    vi     IV
    //Standard Pop Progression
    chordProg = [
      [0, 2, 4], //I
      [4, 6, 8], //V
      [5, 7, 9], //vi
      [3, 5, 7]  //IV
    ]


### The Beat

You can't make music without a beat. For simplicity, I use four beats per measure and change the chord on each measure. I originally implemented this music generator with 4 quarter notes per measure, but I knew that real improvisation mixes up the duration of notes along with their frequencies. Currently the program randomly chooses to change the note length `minimumNote/quarterNote * 100` % of the time it progresses the length of a minimum note. The default setting for the minimum note is an eighth note, so it changes notes 50% of the time every eighth note. This isn't the best variety, but I think it gives just enough to make it interesting without going off the rails. The random note length and time signature implementation certainly have room for improvement. 

### The Notes

Given the scale and the range, the program selects a new, random note in that range each time the program decides to change note length. This has the added benefit of further randomizing note length given the chance that the same note is played.

### Chord Progression + Beat + Notes = Jam

Let's walk through the melody function. To preface, the melody function is called using javascript intervals. Before the interval is defined, the oscillators `melody, chor1, chor2, chor3` are started and continue to generate sound until the program is stopped. Every interval (defined in ms), the function is run again. The interval is defined by the minimum note that we expect to play. In the programs configurations, this is set to an eighth note. (An eighth note at 100 bpm is 300 ms.)

    //run melody function based on minimum note length
    melodyInt = setInterval(melodyFun, minNote);

    //melody function
    var melodyFun = function () {
      time++;
      //chord progression    
      if (time % (notesPerMeasure * (qtrNote / minNote)) == 0 && time != 0) {
        chord++;
        chord %= 4
        chor1.frequency.value = notes[chordProg[chord][0]];
        chor2.frequency.value = notes[chordProg[chord][1]];
        chor3.frequency.value = notes[chordProg[chord][2]];
      }
      //Random note length
      if (time % (Math.floor(Math.random() * (qtrNote / minNote))) == 0) {
        //random note 0 to range-1
        var note = Math.floor((Math.random() * (range - 1)));
        freq = notes[note];
        melody.frequency.value = freq;
      }
    }

Everything is based on an integer time and the minimum note. If you want to simplify the logic, imagine `qtrNote == minNote`. Through liberal use of the modulus function, we determine when to change chords and notes. The chords are changed every (predefined) 4 quarter `notesPerMeasure`. The `chord` integer runs through the 2D array we defined earlier to play the correct chord each measure by assigning the oscillators the correct frequency from the `notes` array we defined as our scale. The melody randomly decides to change notes at a rate dependent on the shortest note possible as discussed above. Then the function decides which note to play within the scale and range and sets the oscillator's value to that frequency. 

# The Final Product

So there you have it, a random pop ballad generator. I'd like to note that this is the most tonally basic implementation possible. It uses four AudioContext oscillators (the maximum), three for the chords and one for the melody. I'm aware the code isn't perfect. I'm not a javascript developer, and I don't feel like refactoring it.

Play it for more than 5 seconds. I can't say that it will sound amazing to you, but it will be **unique** to you.

## Room for Improvement

There's plenty of room for improvement. Here's a few of my ideas:

* More chord progressions
  * This shouldn't be too hard to implement based on how the code is structured, but it isn't there today.
* Minimum note length toggles
  * I want to add a "solo" button that temporarily lowers the minimum note to a sixteenth note.
* New time signatures
* Pauses
* Stock percussion?
* Harmony? Unlikely given the current limits of AudioContext.
* Move to Github (If there's ever enough pressure I will, but right now it's not a huge priority.)
