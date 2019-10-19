_Originally published by The Tuesday Night Machines [on Muffwiggler](https://www.muffwiggler.com/forum/viewtopic.php?t=209006&highlight=), 2018-11-04_

Alright, after explaining the CGS Modulo Magic, here comes Ken Stone’s Infinite Melody module, also found on the Best of CGS MARSH panel.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Infinite%20Melody/images/CGS_Infinite_Melody_001.jpg "")

Oddly, understanding it seemed way easier than trying to explain it. So consider this a first try and let me know how it worked for you. Here’s Ken’s original website for the Infinite Melody for your research too:
[https://web.archive.org/web/20170826181343fw_/http://www.cgs.synth.net  :80/modules/cgs32_infinite_melody.html](https://web.archive.org/web/20170826181343fw_/http://www.cgs.synth.net  :80/modules/cgs32_infinite_melody.html)

I think understanding the Infinite Melody is awesome, but since the module doesn’t offer any way to check its internal states when patched (which are plenty and which change at different rates), using it in a 100% deterministic way is very difficult and might even be impossible. Nevertheless, please read on! It’s a cool thing to get your head around and it’s obviously a fun module to simply generate random or chaotic CVs with.


![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Infinite%20Melody/images/CGS_Infinite_Melody_002.jpg "")

**For this explanation I’ll assume that you know how a digital shift register (DSR) works and how to count in binary.** You don’t have to be an expert in those things, but you’ll need a basic understanding. There are a lot of tutorials about those topics online fortunately.

### What the Infinite Melody does in a nutshell:
You patch a signal into its Noise input, a comparator creates a series of high and low states from that signal and feeds those binary values into A WHOLE LOTTA DIGITAL SHIFT REGISTERS HOLY MOLY! First, it populates a six-stage DSR at the rate of a clock signal at the Clock input. Then, on each incoming clock pulse at the Advance input, the stages of this DSR are fed into six more four-stage DSRs, from where the 1, 2, 3 and Mix outputs are derived. Phew … here, take a look at this beautiful illustration and read on:


![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Infinite%20Melody/images/CGS_Infinite_Melody_003.jpg "")

***The pink stuff:***
IN (Noise) receives an input signal which is analyzed by the comparator. Sense knob and CV input control the comparator threshold.

When a pulse is sent to the Clock (CLK) input, the current comparator state (high or low … or 1 or 0) is recorded into the DSR’s first stage. On another clock pulse, a new state is recorded into the DSR’s first stage, shifting the previous value to stage two, then stage three, etc. So the DSR always contains a string of 1s and 0s or rather high and low voltages, shifting from right to left in the illustration above on each clock pulse.

***The green stuff:***
Below each of the six pink DSR stages is a green four-stage DSR, which records the pink stage’s value upon a clock pulse at the Advance (ADV) input. As usual, on new pulses, new values will be recorded into stage one and previously held values are shifted onwards to the next stages.

The green DSRs’ first stages are representing a 6 bit binary value, being fed into a DAC, which outputs an according voltage at output 1. This means that the Bit 1 DSR (Least Significant Bit - LSB) will affect the output voltage very little while Bit 6 (Most Significant Bit - MSB) will affect it the most.

Stages two and three act the same way and their DAC voltages are sent to the 2 and 3 outputs respectively.

The Mix output behaves differently. Here, each of the six bits (high or low voltages) are sent through their own individual attenuator (pots 1-6) and the resulting voltages are added together before appearing at the Mix output. This let’s you tune each bit to create harmonic melodies. It’s like using a gate sequencer with a CV mixer to create melodies, if you know what I mean.


_Maybe take a short break, get a drink and re-read all of the above again, because it gets even more exciting!_


Okay, we haven’t discussed the one remaining input yet, the Mode socket. When receiving a high voltage there, the module is in “random” mode, which is a little misleadingly titled in my opinion. It really just means that all green DSRs record new values from the pink DSR stages at the same time, whenever a pulse is detected at the Advance input. So all green DSRs shift together at the Advance clock rate.

When a low voltage is sent to the Mode input, or if nothing is connected there, the module is in “1/f” mode. I won’t explain why it’s called that, because I also think it just makes things too complicated for this document here. You can read about it in Ken’s description linked at the top. In this mode the green DSRs don’t shift together all at once on each Advance clock pulse, but at different clock divisions. The Bit 1 DSR shifts at every pulse, so at the exact Advance clock rate. The Bit 2 DSR shifts at rate /2 (so half as often as Bit 1), Bit 3 DSR at rate /4, Bit 4 DSR at rate /8, Bit 5 DSR at rate /16, Bit 6 DSR at rate /32. So Bit 1 (LSB) may change on each clock pulse, making the DAC output voltage change on each pulse too then, but only very little (because it’s the LSB). Bit 6 (MSB) drastically changes the DAC output voltage, but it only does so every 32nd clock pulse. So small voltage changes happen more frequently than large changes.

Here’s a gif animation. Black arrows indicate clock pulses (left arrow = Clock in, down arrow = Advance in).

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Infinite%20Melody/images/CGS_Infinite_Melody_004.gif "")

Of course both clock inputs work at the same time too, so the pink DSR stages change continuously and the values are recorded into the green DSRs on every Advance pulse.

That’s it actually. It’s really not that difficult in the end, but as I wrote earlier, using it deterministically is a challenge … at least for me. There are so many things to keep track of when patching it, but it’s good to know that the module itself does not actually behave randomly at all, if you don’t feed its Noise and Clock inputs random voltage levels and random pulse sequences. It gets chaotic quickly though, which is great!

### So, what do I use this for?
That’s the beauty of the CGS Serge stuff ... you are offered a very abstract feature set and then you have to figure out how to incorporate it into your patches for yourself. Haha! Some things to note though:

- Outputs 1-3 are only somewhat related to each other in 1/f mode (because the green DSRs shift at different times, and the pink input values change frequently). In random mode however, output 1’s voltage is shifted exactly as it is to output 2 and then to output 3 on each clock pulse, because all green DSRs shift at the same rate. You can create some cool CV sequences by changing the Mode with CV (i.e. sending gates into the Mode socket). Changing the Mode resets the 1/f clock division counter too I think.
- In 1/f mode, when the more significant bits change, the output voltage can jump up or down drastically and stay there for a while with only smaller changes. Use the Modulo Magic to reign the voltage range in.
- The Mix knobs can be set to produce harmonically pleasing pitch CV levels. To do this, turn all of them to zero first and start with knob 1, then knob 2, etc. Remember that in 1/f Mode the DSR’s shift at different rates, so Mix knob 1 may change it’s on/off state more often than Mix knob 6.
- Sending very fast, audio-rate clock pulses to the Clock input and a white noise signal to the Noise input will generate random values in the pink DSR. In Random mode those will appear more random at the DAC outputs than in 1/f mode, where they will appear more chaotic (because small changes happen more often than large changes).


### What about that Diatonic Converter?
The Diatonic Converter is an add-on circuit which is included in the MARSH panel’s Infinite Melody module.
[https://web.archive.org/web/20170826181343fw_/http://www.cgs.synth.net  :80/modules/cgs32_infinite_melody.html](https://web.archive.org/web/20170826181343fw_/http://www.cgs.synth.net  :80/modules/cgs32_infinite_melody.html)

It takes six binary input values (who would have thought?!) and creates diatonic pitch CV from them. It is not a quantizer, as it doesn’t accept analog voltages, but only digital high & low (1 & 0) signals. So it fits right in there with the Infinite Melody, as this one provides DSRs full of binary values.

A diatonic scale is a seven-note scale, with five whole tone and two half tone steps. Like this: C D E F G A B … Look at that! Those are the white keys on the piano, which are called the C Major diatonic scale. Whoop!

The Diatonic Converter uses two binary counters, or binary-to-integer converters, each receiving three of the Infinite Melody’s six binary DSR stages. Why three? Because you only need three binary digits to count to seven … well, to eight actually, so the root note (C in our above example) will sound at the end again one octave higher.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Infinite%20Melody/images/CGS_Infinite_Melody_005.jpg "")

The first counter, receiving the Infinite Melody’s Bits 1-3, outputs one of eight diatonic note CV values. So, for example, when it receives the binary number 011, the Diatonic Converter’s output 3 goes high, playing an E note, which is the third step of the C Major diatonic scale.

The second counter, receiving Bits 4-6, sets the octave (1-8), which is added to the note CV. That's a huge octave range of course and as the second counter receives the DSR's more significant bits, the values don't change very often in 1/f mode. So it can happen that the Diatonic Converter stays in very high or low octaves for a long time. It would be nice to be able to disable the Bits 5 & 6 to keep the octave range down - not just for the Diatonic Converter, but also for the Infinite Melody module itself ...

Oh hey, what's that?!

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Infinite%20Melody/images/CGS_Infinite_Melody_006.png "")

Looks like Ken already thought of that in his circuit and on the PCB! His Bits are labelled 0-5 (instead of my counting 1-6), so in the image Bits 4 & 5 are the last ones. Those switches are not on the MARSH's Infinite Melody panel unfortunately, but if you're crafty, you might be able to find an empty corner to drill those two wholes into maybe.

### Diatonic Converter Inputs:
The Diatonic Converter is hardwired internally to the Infinite Melody’s DSR 1 stages for melody generation (at least in my MARSH build), but it does feature other inputs on the front panel too.

**Root:** This input accepts an analog CV which is added to the diatonic output, meaning you can offset the pitch CV, shifting the melodies up or down, changing the root note of the diatonic scale.

**Span:** By default, the pitch CV steps are tuned to produce a diatonic scale with its typical whole- and half-step intervals. Patching an analog CV into the Span input lets you change the step sizes, creating microtonal scales (very experimental).

**Maj/Min:** This one accepts a gate signal which switches the diatonic scale from Major to Minor on a high voltage.


Okay dokay, that pretty much covers the whole module! Let me know if anything is still unclear and go ahead and experiment with your Infinite Melody module!
