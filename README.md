# Pro VS Maxi
Custom Firmware for the Behringer Pro VS Mini 

<img width="235" height="92" alt="image" src="https://github.com/user-attachments/assets/2b77f7ce-3bd4-458f-900a-0b1ab43e5fe1" />


---


If Behringer open sources the Firmware Packer for the Pro VS Mini, the plan is to implement some long standing feature requests within a new custom firmware.

**As recent events have already proven a custom firmware is completely possible.**

In fact, Behringer’s recent open-source push (such as releasing
the **PRO800-Firmware-Packer** along with schematics and memory layouts, as well as the open-source dev kits for the KICK, Ignite, and AQ64L) shows the exact blueprint for how this works. Notably, Behringer didn't even open-source the *factory firmware* of the Pro-800—they published the **firmware packer, memory map, and mainboard schematic**, which allowed third-party developers (like the `pro-8000` project) to write completely custom firmware from scratch and flash it safely via the protected factory bootloader.

If Behringer did the exact same thing for the **PRO-VS Mini** (releasing the packer/bootloader specs and pinout/schematic), developers could build a replacement firmware with substantial new features.

---

### 1. Why a "Bootloader/Packer Release" is Enough

Just like the Pro-800, the PRO-VS Mini runs on an ARM microcontroller paired with an analog low-pass filter and DACs.

* **The Bootloader's Role:** The bootloader sits in a write-protected sector of the flash memory. Its only job is to receive a properly formatted/packed binary (usually via USB SysEx) and write it into the main application flash area.
* **What Developers Need:** You don't need Behringer's proprietary DSP source code to write custom firmware—you just need the **packer tool** (so the bootloader accepts the file), the **memory map**, and the **MCU pinout/schematic** (to know which GPIO pins read the joystick, buttons, pots, and capacitive keyboard, and which pins drive the OLED display, MIDI, and analog filter CV). Even without schematics, hobbyists can trace pins, though Behringer releasing them removes 90% of the reverse-engineering guesswork.

---

### 2. What Missing Features Could Be Implemented?

Because the PRO-VS Mini generates all four oscillators, envelopes, LFOs, and modulation routing digitally on the ARM chip before hitting the analog filter, almost every functional limitation of the stock firmware is purely a software constraint.

#### **High-Probability Software Additions:**

* **Manual Joystick / Vector Override (Disabling the Mix Envelope):** One of the biggest complaints about the PRO-VS Mini is that the 5-stage Mix Envelope always hijacks the vector position unless you use awkward menu workarounds. Custom firmware could add a simple toggle to bypass the Mix Envelope so the joystick stays wherever you leave it, or acts as a direct XY performance controller.
* **Custom User Wavetables:** The stock firmware gives you 128 fixed waveforms. Custom firmware could easily allow loading custom single-cycle waveforms over USB/SysEx, turning it into a true custom wavetable vector synth.
* **Expanded Modulation Matrix:** Adding extra LFOs, envelope-to-pitch/filter routings, or allowing the joystick X/Y axes to modulate filter cutoff, resonance, or LFO rate directly—not just oscillator mix.
* **Full MIDI CC & NRPN Implementation:** Mapping every single hidden menu parameter (envelope stages, vector coordinates, wavetable selection, arpeggiator division) to incoming MIDI CCs/NRPNs so you can control it completely from an external MIDI controller without touching the tiny OLED screen.
* **MIDI Out & Joystick Controller Mode:** Transmitting joystick movements, sequencer notes, and touch-keyboard notes out over USB MIDI (and hardware MIDI if wired on the board), letting the PRO-VS Mini double as a compact vector MIDI controller.
* **Improved Sequencer & Arpeggiator:** Expanding beyond the basic 16-step sequencer to add polyphonic step recording, parameter locks (motion sequencing the vector position per step), Euclidean rhythms, or probability/ratcheting.
* **Alternative Synthesis Engines:** Since the digital audio is calculated on the MCU before the analog filter, there is nothing stopping someone from writing an FM engine, a 4-oscillator supersaw monosynth mode, or a digital wavefolder/bitcrusher before the filter.

---

### 3. Hardware Limits (What Software *Cannot* Fix)

Even with total control over the firmware, a few limitations are baked into the physical circuit board:

* **The Joystick Click Button:** Teardowns of the PRO-VS Mini have shown that while the physical PS3/PSP-style thumbstick mechanism has a push-button switch underneath it, **the trace isn't connected to anything on the PCB**. No firmware update can make clicking the joystick do anything without soldering a bodge wire to a spare MCU pin.
* **True Polyphonic Filters:** It is a 4-voice (5-voice after firmware 2.0) **paraphonic** synth—all digital voices sum together into a *single* analog low-pass filter chip. Firmware cannot give each voice its own analog filter (though it *could* implement per-voice digital filtering before the DAC if CPU cycles allow).
* **CPU & RAM Ceiling:** The ARM chip inside the PRO-VS Mini is a budget microcontroller. While firmware 2.0 proved there was enough headroom to squeeze in an extra voice and chorus/effects, heavy DSP (like lush stereo reverbs or high-voice-count oversampled oscillators) would eventually hit a wall.
* **No Velocity/Aftertouch on the Built-in Keys:** The capacitive touch keyboard is simple on/off touch pads, though custom firmware *could* easily add full velocity and aftertouch response when played via an external MIDI keyboard.


Note: Behringer and Pro VS MINI are trademarks of Music Tribe. This is an independent community project, not affiliated with or endorsed by Music Tribe.
