_Originally published by The Tuesday Night Machines [on Muffwiggler](https://www.muffwiggler.com/forum/viewtopic.php?t=209972&highlight=), 2018-11-24_


Okay folks, here’s an explanation of [Ken Stone’s Gated Comparator](https://web.archive.org/web/20170817105215fw_/http://www.cgs.synth.net/modules/cgs13v2_gated_comparator.html) module, as found on the Best of CGS SWAMP panel. It’s not too complex, but it might still be useful to have the real module in front of you while you read this.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Gated%20Comparator/images/CGS_Gated_Comparator_001.jpg "")


In a nutshell, the module creates high or low states from an analog input CV going through a comparator. Those states are clocked into an 8-stage digital shift register (DSR) of which you can access all eight stages via individual gate outputs. All high stages are also combined by a DAC, as well as a simple mixer, generating analog CV from the eight binary states.


### Controls and connections:
**IN:** Plug an analog signal in here. This will be analyzed by the comparator.

**SENS:** Sets the threshold above which the INput signal makes the comparator output go high.

**VC SENS:** External CV control with an attenuator for the comparator threshold.

**CLOCK:** Input a clock signal here. On each pulse the current comparator state will be written into the first stage of the DSR, shifting previous states down the line.

**COMPARATOR OUT** (above RANDOM output): Simply the current comparator state derived from the INput signal, independent of the clock or DSR.

**GATE OUTPUTS 1-8:** Direct gate outputs of the eight DSR stages.

**STAGE KNOBS 1-8:** Turning a knob sets the amount of CV which is added to the MIX output when the stage’s output is high. Pulling a knob out (maybe that’s a mod?), will remove its stage from the DAC’s “RANDOM” output.
> tojpeters wrote:
> Bit disable is a mod



**RANDOM OUTPUT:** It’s like an 8-Bit binary counter, with the least significant bit (LSB) being stage 1 and the most significant bit (MSB) being stage 8. So the amount of CV added to the RANDOM output is lowest when stage 1 is high and highest when stage 8 is high. The in-between stages are bits 2 to 7 then obviously. Using the pull-out knobs, you can restrict which bits are being used. There is also an inverted output.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Gated%20Comparator/images/CGS_Gated_Comparator_002.jpg "")

**MIX OUTPUT:** All high stages’ gate signals are sent through their individual attenuator knobs and mixed together at this output. This is like the KLEE sequencer (I’ve been told) or simply like using a mixer to create analog CV signals from gates, if you know what I mean. On the Gated Comparator, all of the DSR stages which are high are mixed together at the MIX output, with each knob adjusting its stage’s CV amount being added. There is also an inverted output and an output attenuator labeled RANGE.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Gated%20Comparator/images/CGS_Gated_Comparator_003.jpg "")

**MANUAL LOOP SWITCH:** This one is latching at the up and middle positions and momentary at the down position (at least on my build and according to Ken’s build notes). Holding it down when a clock pulse is being received at the CLOCK input, will write a high state to the first DSR stage, no matter the current comparator state. This means you can manually add high stages to the DSR, even without an INput signal.

In the middle position the switch doesn’t do anything.

In the latching up position the GATE 8 output will be written to the first DSR stage on the next clock pulse, but only when the LOOP ENABLE switch is up as well. This will make the pattern loop. Flipping the switch up is the same as patching GATE 8 to the LOOP IN socket (more on this below):

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Gated%20Comparator/images/CGS_Gated_Comparator_004.jpg "")

**LOOP ENABLE SWITCH:** When down, this switch doesn’t do anything. In the up position the DSR pattern will loop, if the MANUAL LOOP switch is up as well, or if the GATE 8 output is patched to the LOOP IN input.

You can use this to chain multiple Gated Comparators together too, by patching the GATE 8 outputs into the next module’s LOOP IN and flipping all LOOP ENABLE switches up, apart from the one on the first module.

![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Gated%20Comparator/images/CGS_Gated_Comparator_005.jpg "")

In my build, flipping the LOOP ENABLE switch up also disconnects the IN socket, which is a useful mod. Originally, the INput and the LOOP IN are OR-gated, meaning they will both add high states to the DSR when either signal is high. This can make the whole DSR pattern go completely high quickly though, which isn’t that great.
>tojpeters wrote:
>loop enable would more appropriately be labeled loop disable

>It works as you say

>TheDPDT switch to cut off the input is a nearly required mod

>For all the mods available for the Swamp panel see my [Black Swamp build thread](https://www.muffwiggler.com/forum/viewtopic.php?t=147603&highlight=)


**LOOP ENABLE SOCKET:** This is a bit weird. If the LOOP ENABLE switch is up and the socket receives a high signal, looping stops. I have to check again if it’s also possible to enable looping with a high signal and the switch being down. Does anybody else have any insights regarding this?
>tojpeters wrote:
>Loop enable jack only works when the loop switch is on


**LOOP IN:** This is another signal input instead of the IN socket, which is used when looping is enabled. In the original circuit both this input as well as the IN signal’s comparator output are mixed when the LOOP ENABLE switch is up (on).


Okay, that’s all of the controls. I hope it made the behavior of this cool module clearer.

### So what can you use this for?

- It’s two CV sequencers with additional inverted outputs, so four CV sequences total!

- It’s an eight step gate sequencer too, of course!

- The whole thing can work randomly by feeding the INput noise or fast CV, or more deterministically by sending another sequencer’s output or a synced LFO or something like that to the INput. And of course you can loop the patterns with the flick of a switch, taming the chaos.

- It’s also a simple comparator, which is nice.

- It can act as a gate delay, because an incoming gate signal is shifted down the DSR for up to eight clock pulses, which would be the maximum delay time.

- Here’s a patch tip to get 16 steps from the eight step DSR, by inverting the sequence (similar to what the Turing Machine can do):
>zthee wrote:
> ![alt text](https://raw.githubusercontent.com/TuesdayNightMachines/CGS-Serge-Modular-Synth/master/CGS%20Gated%20Comparator/images/CGS_Gated_Comparator_006.jpg "")
>If you just clock it (With no input) you should see it go from no lit to all lit, and back again.


- Another cool thing is to manually load only one high bit into the DSR (only one stage LED lit), using the MANUAL LOOP switch’s momentary side, and turn on looping. Now, together with the stage knobs and the MIX output, the module acts as a regular 8-step CV sequencer, because not more than one stage can be active at the same time, thus only one knob CV is being output.

Alright, let me know if you have questions or corrections!
