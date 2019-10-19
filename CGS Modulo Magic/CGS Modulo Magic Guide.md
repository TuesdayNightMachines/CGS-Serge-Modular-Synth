This is an attempt to explain Ken Stone’s CGS Modulo Magic module, as found on the Best of CGS MARSH panel, so that users will be able to patch it in a more deterministic manner.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Modulo%20Magic/images/CGS_Modulo_Magic_001.jpg "")

After re-reading [Ken’s webpage](https://web.archive.org/web/20170607093045/http://www.cgs.synth.net:80/modules/cgs40_modulo_magic.html) about the module tons of times and analyzing its output on an oscilloscope, I think I finally got a grip on how the Modulo Magic’s different parts interact.

I’d like to start without actually talking about the mathematical Modulo principle, because it seems to be just a small part of this module’s functionality.

What this module does in a nutshell:
You set a voltage threshold, called “Initiation CV”, and when the input signal reaches this level, another voltage, called “Step Size CV” is added or subtracted. Then, if the new voltage reaches the threshold again, or rather if it rises by the threshold amount, the Step Size voltage is added or subtracted again, and so on ... up to eight times, eight so-called “Steps”. After the last Step, no more processing takes place and the Output signal simply tracks the Input signal changes.

Example:
- Input signal is a linearly rising voltage, starting at 0V
- Initiation CV is set to 1.0V
- Step Size CV is set to -0.5V

This would look like the following finger paint sketch (sorry, I’m on an old iPad):

After the input voltage rises to 1V, it drops by 0.5V, then it rises by another 1V and drops by 0.5V again, etc.

That’s it. Well, there’s more to do obviously and there are some quirks, but those are the basics really.


Controls and Features:


Input: Patch the audio or CV signal to be processed in here.
Output: This is where your processed signal comes out.

Initiation Knob: Sets a positive voltage level threshold at which the module starts processing the input CV. There’s a CV input for this knob too, with a polarity switch. Without CV, keep this switch at the + position.

Offset Knob: Adds an offset to the Initiation CV threshold, but only for the very first Step. More on this later. This control also has a CV input with polarity switch. Keep it at the + position if not using CV.

Step Size Knob: This sets the voltage to be added or subtracted to/from the Input signal after the Initiation CV threshold is reached. This knob is bipolar, which is not indicated by the Best of CGS front panel graphics and also it’s reversed. In the middle position there is no voltage change after reaching the threshold, turning it CW to the right subtracts CV and turning it CCW to the left adds CV. Think of it like this:


VC Sub & VC Add: CV inputs with attenuators to add or subtract to/from the Step Size voltage.

Steps Rotary Switch: Sets the amount of times the threshold can be reached and processing takes place. Mine seems to be wired from left to right like this: 8, 1, 2, 3, 4, 5, 6, 7. Odd.


The Modulo
Okay, let’s talk about the Modulo principle now. The mathematical Modulo % operation returns the remainder after a division, blah blah blah. My head is spinning already! Haha! More importantly, the Modulo restricts an input to a certain range:

x % y returns only values from 0 to y-1, no matter how large the value of x.
Anything above y-1 is wrapped around and starts back at 0.

You can recreate this behavior with the Modulo Magic, by setting Step Size to the inverse of Initiation CV, which requires a bit of fiddling, but it shouldn't be too hard to get close enough.

Example:
- Input signal is a linearly rising voltage, starting at 0V again
- Initiation CV is set to 1.0V
- Step Size CV is set to -1.0V

This restricts the input voltage to a range between 0 and 1V:


This might be handy, for example, to restrict a pitch CV to only one octave and it’s probably why we find the Modulo Magic on the same panel as the Infinite Melody module, with its wide octave range and unpredictable CV levels. I used this technique in the following video, to more or less keep the melodies in lower octaves:

https://www.youtube.com/watch?v=0d8qU9nQ8To

The Step Count:
You can enable up to eight steps with the Steps rotary switch, which will send the Input signal through a series of comparators, each of which receives the processed signal of the previous one. So below the Initiation CV threshold, the signal simply passes through all Steps unchanged. When it reaches the threshold, the first comparator triggers the Step Size voltage addition or subtraction. This new voltage is then sent onwards through the comparator chain and as soon as it reaches the threshold again, it triggers the next one. After a maximum number of eight Steps, the last voltage result is kept and further increasing Input voltage will simply be added.

Example:
- Input signal is a linearly rising voltage, starting at 0V (yawn)
- Initiation CV is set to 1.0V
- Step Size CV is set to -1.0V
- Steps set to 4



After subtracting 1V four times after reaching the threshold, the last Step has been triggered and the output voltage now tracks the input voltage with a -4V offset.


Going down:
So far we only looked at situations where Initiation CV plus Step Size resulted in a positive voltage. If the Step Size is subtracting a larger voltage than the Initiation CV level, then the Output signal can actually decrease and even go negative.

Example:
- Input signal is a linearly rising voltage, starting at 0V
- Initiation CV is set to 0.5V
- Step Size CV is set to -1.0V




Up and Down:
Now, let’s look at a rising and falling Input signal, like a Sine wave, which is a lot more common than the infinitely rising voltage of our previous examples.

Example:
- Input signal is a Sine wave between 0 and just below 2V
- Initiation CV is set to 1.0V
- Step Size CV is set to -1.0V



As soon as the Input voltage reaches 1V, the Step Size of -1V is applied. When the Input signal drops below 1V again, the Step Size voltage is removed, so Input signal equals Output signal again.

The blue wave above obviously sounds a lot different than the original Sine wave, so we’re using the Modulo Magic as a waveshaping audio effect now. Imagine what can be done with the multitude of CV modulation inputs. Or better yet, try it out! But first, let’s look at the modulation options together.


Initiation CV Offset:
The Offset knob and CV allow you to alter the voltage at which the first Step is initiated. It basically shifts the whole processing chain up or down the voltage range, but leaves the actual Initiation-plus-Step-Size processing unchanged.

Example:
- Input signal is a linearly rising voltage, starting at 0V
- Initiation CV is 1.0V
- Offset is 3.0V
- Step Size CV is -1.0V
- Steps set to 4



As you can see, the Offset of 3V will delay the Initiation of the Steps until the Input reaches the level of Offset plus Initiation CV threshold, i.e. 3 + 1 = 4V.

Thinking of the above Sine wave example, you could imagine sending an LFO into the Offset CV input to alter the point on the Sine wave where the waveshaping starts to occur. If the Offset CV is higher than the Input signal amplitude no waveshaping will be done, but if the CV drops, the processing will slowly move along the Sine wave:



Crap, I’m on an airplane now and my lines are getting squigglier and my girlfriend is rolling her eyes at me and mocking my enthusiasm writing this document. But you get the idea. Waveshaping is cool! Try it with CV. Bet you haven’t heard that one before.

So, what’s left? Oh yes:

VC Step Size Addition and Subtraction:
You can use the VC Sub and VC Add inputs and their attenuators to modulate the Step Size for even more waveshaping fun.

An interesting feature, which is also mentioned briefly in Ken’s description, is that you can use those inputs as a mixer, if you don’t do any other processing with the module. Setting Initation CV, Offset and Step Size to 0 (middle position for Step Size!Remember it’s bipolar!), will pass the Input signal through to the Output unchanged, without the whole stepping stuff. At least in theory, as you’ll probably not get everything exactly to 0 with the knobs, and stepping might still occur lightning fast at some levels, but you’ll get close enough. When you turn the Step Size knob now, you will apply a constant voltage offset to the Input signal and by sending other signals into the VC Sub and VC Add inputs, you will subtract and/or add those to the signal.

Example:
- Input signal is a slow Sine wave
- Initiation CV is 0V
- Offset is 0V
- Step Size is 0V
- VC Add receives a fast Sine wave



While I’m not sure if this example was necessary, the drawing might at least add some comical value.

Alright, that’s it for now. Let’s quickly recap the important bits.


Recap:

Use-cases of the Modulo Magic:
- Restricting voltages to a certain range (Initiation CV equals Step Size inverted)
- Waveshaping (whoop!)
- Signal offsetting (using Step Size only, Initiation CV and Offset set to 0)
- Signal mixing (using Step Size and VC inputs only, Initiation CV and Offset set to 0)
- more?

I have a feeling that there are probably some more wild things to be done with feedback loops, which I haven’t tested yet.

Watch out for:
- Step Size knob is bipolar, with 0 in the middle, and reversed polarity, with negative voltages CW and positive ones CCW.
- Steps knob rotary switch wiring. Mine has 8 at fully CCW and then goes 1-7 when turned CW.


I think the Modulo Magic fits the Serge Modular’s “patch programming” concept incredibly well. You’ve got this rather simple mathematical principle for restricting values to a range based on the Modulo operation, but then you get access to all the variables in the underlying circuit on the front panel and can totally mess the whole thing up and create … magic.

Thanks for reading! nanners

Let me know if there are any mistakes or if you have additional insights. I’m writing this in between things from memory, because I neglected to take any notes during the past days of researching and testing. Oh well. I need a drink now.
