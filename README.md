# JavaScript DX7 Emulator

This is my hack from Music Hack Day London 2011 - it's a Yamaha DX7
Emulator written entirely in JavaScript using HTML5 audio. I wrote it
mainly to learn how to generate audio in a browser, but FM synthesis
is cool too.

## Functionality

Right now it implements basic FM synthesis (in practice it's actually
phase modulation), with 6 oscillators ('operators' in DX7 speak) and
the 32 'algorithms' from the physical synth. It's also capable of
reading .SYX patch dumps, so you can load existing sound
libraries. This mainly makes it obvious how bad it currently sounds!

## Known Limitations

There are quite a few limitations currently - if I have time I'll try
to work through some of these soon:

* Above all, it mostly sounds pretty bad. Some patches are merely
  lacking the dynamics of a real DX7, some sound generally rubbish,
  and a few are just noise. /If you download this, please experiment
  at low volume before turning it up./

* Low level audio generation is very basic - it's generating .WAV
  files, base64 encoding and whacking that in the src attribute of an
  audio tag, which means its not very real time. I'd like to switch it
  to use newer APIs. As a result of this, all notes are .75 seconds
  long - it doesn't make use of offset information. 

* Fixed frequency operators are suspect - the numerical data from the
  patches I've looked at doesn't map in an obvious way to the
  behaviour of the DX7 UI, and patches with fixed frequency operators
  tend to be the ones that sound bad.

* Envelope generation involves a lot of guesswork in mapping the rate
  parameters (0-99) to physical rates of change. It's not that
  terrible at the moment, but it's not authentic either.

* Feedback loops and LFO modulation need some work. The	LFO currently
  only supports sine and triangle waveforms.

* There's currently no support for velocity information, nor keyboard
  level or rate scaling.

* Many other unsupported features:
    * Velocity sensitivity.
    * Keboard level or rate scaling.
    * Oscillator synchronisation (kind of irrelevant with the current approach to audio generation).
    * Oscilaltor detune.
    * Transposition.
    * Pitch bend or modulation wheel.
    * Portmanteau mode.

* A better patch editing UI would be nice. Right now you can edit the
  JSON in the text area and the synthesis routines will pick it up,
  but that's not very user friendly.

* I'd like to add a MIDI bridge at some point, although I had
  difficulty getting the one I played with during the hack day to
  work.

Many of these are missing or broken because I don't know how they're
supposed to work, so if anyone does, input would definitely be
appreciated.

Here's a more specific list of assumptions:

* Envelope rates are linear, with 1 = full dynamic range in 1 second.

* The same non-linearity is applied to LFO pitch modulation as is to
  regular operator modulation.

* LFO amplitude modulation depth is linear between 0 and half the
  dynamic range.

* Pitch envelope is applied linearly betwen +/- 4 octaves (this is a
  /very/ bad assumption - extremes are known to be correct, but that
  gives ridiculously low sensitivity around zero).

* LFO speed is linear, with 1 = 4 Hz.

* Operator feedback is implemented by taking the output value from the
  previous sample as the input, applying the same nonlinearity as with
  regular modulation.

* Feedback level is linear between 0 and 7/50.

* In fixed frequency mode, frequency = (10 ^ (coarse frequency))
  Hz. (In practice this gives frequencies in the MHz range for some of
  the patches I've tried, so unlikely.)



## DX7 Resources

* [DX7 manual](http://ems.music.utexas.edu/docs/dx7-man.pdf)
* [General DX7 resources](http://www.abdn.ac.uk/~mth192/html/dx7.html)
* [SysEx patch format](http://www.abdn.ac.uk/~mth192/dx7/sysex-format.txt)
