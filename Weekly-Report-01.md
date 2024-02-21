# Note Extraction: challenges and possible dovetail with CS7150 project:
## Observation:

Among the techniques for identifying fundamentals, sliding a kernel of natural harmonics over the frequencies from STFT seems promising, and it happens to resemble (in my estimation) a sort of convolutional layer.

**TODO**: *Investigate whether it would be appropriate to try and scope the CS7150 (Deep Learning) project to fit into the capstone. If we can use Deep Learning techniques to improve the prformance of Note Extraction, that might make a lot of sense (though so far, it does not seem that it will make things simpler/easier).*

Ideas from Kohei (first response, from brief discussion around note extraction):
* Generative Adversarial Network (GAN)
* autoencoders

I'm not sure, but I don't think these techniques will directly yield note extraction. Perhaps, for instance, we could train an autoencoder to reproduce harmonic audio (i.e. chords) and then inspect the intermediate outputs; if we are really lucky, maybe we will observe some sort of representation of notes and figure out how to decode those intermediate outputs? This seems far fetched, almost like a bigger challenge than just working out note detection directly...

## Supervised Learning:
Perhaps I should just break down and start labeling some data? This would probably fit into a deep learning solution better. In any case, we need to define the desired output format for extracted note data. The basic concept is "notes and when", meaning fundamental frequencies, start times and durations. It might be interesting to try converting these to [MIDI](https://www.midi.org/specifications) (though we need to find an accessible spec for the data format); it seems to fit my rough understanding of MIDI messages for "note on" and "note off".

# Project structure and EDA:
* Initial experimentation in the [phonemes](https://github.com/jhautala/phonemes) repo.
* Testing in two different development environments (good for demonstrating replicability):
    * M2 Air: configured conda environment to use built-in GPU with tensorflow-macos and tensorflow-metal.
    * Windows/WSL: installed GPU and confirmed CUDA support in tensorflow.
* Used DBSCAN clustering to group related spectral peaks across time slices.
    * Most likely value as a minor refinement after [HPSS](https://librosa.org/doc/0.10.1/generated/librosa.effects.hpss.html#librosa.effects.hpss); it might be good to drop some peaks out of the model, for parsimony.
    * Inital results seem OK; I have a small sample size, and my evaluation method was fairly manual (and mostly qualitative); I was able to resynthesize the original sample excluding the spectral peaks of the "harmonic" channel (per HPSS) that were deemed "noise" by DBSCAN and it sounds perfectly fine (slightly cleaner; less "wobbly"/"chorus").
    * I expect this technique will follow frequencies across smooth glissandi. A possible pathological case for this method (or possible interesting/valuable training data): [THX Deep Note](https://www.thx.com/deepnote/).
* Minor refinement of frequency calculation for FFT bins:
    * was: `sample_rate/window_size`
    * better: `nyquist/(window_size/2 + 1)`, `nyquist=sample_rate/2`
* Started working on scanning for natural harmonics (see discussion above about Note Extraction and convolutional kernels).