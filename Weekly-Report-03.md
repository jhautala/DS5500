# Time Performance

The current preprocessing implementation can handle one file in just under 14 seconds, or the entire library in under 21 hours. Since the it is CPU-bound, with parallel processing I should be able to get this much lower. I implemented a simple division of labor by odd/even indices for the initial results, but without interprocess communication, this leaves the merging of the results as a separate task.

Having prior experience with parallel processing in Python, I know that some solutions (e.g. [threading](https://docs.python.org/3/library/threading.html#module-threading)) do not afford true parallelism (i.e. all Python code is executed in a single process). I have implemented truly parallel solutions with the [multiprocessing](https://docs.python.org/3/library/multiprocessing.html) library previously, and this will be worth the effort, but having some fairly tight concurrent deadlines for this week I decided to focus on making sure I have some results before I refine the time performance further.

In the current process, there are a number of hierarchical features that depend on each other. When everything is executed in dependency order, with reuse of intermediate models, I am able to minimize redundancy and improve time performance. For example, many feature extraction algorithms require series of time-sliced, windowed FFTs (or "[STFT](https://librosa.org/doc/0.10.1/generated/librosa.feature.chroma_stft.html)" model); by creating this once up front, analyzing it, and feeding subsequent steps with those outputs, I can bypass a significant amount of processing. There may be a few other minor opportunities for these sort of "in-line" performance improvements, but the biggest gains should come from proper parallel processing.

# Feature Extraction

After deciding to adopt more conventional feature extraction techniques, I was able to make fast progress defining a broad set of features that are likely to correspond with aesthetic properties. These fall into three broad categories: Loudness, Harmonic, and Percussive.

## Loudness

The primary metric here is [RMS](https://librosa.org/doc/0.10.1/generated/librosa.feature.rms.html), which I calculate over our STFT bins. The [pyloudnorm](https://pypi.org/project/pyloudnorm/) library would give us more flexibilty and more precise control over our representation of perceived loudness, but for the time being I opted to use RMS as a simple solution that suffices for initial results. This blends slightly into other categories where spectral magnitude, and onset strength are essentially "loudness" features, but I draw the line at [spectral contrast](https://librosa.org/doc/0.10.1/generated/librosa.feature.spectral_contrast.html); I divided that into 7 bands, based on conventional breakpoints:


* 20Hz to 60Hz: So-called "sub-bass" frequencies are perceived more through vibration than hearing, important for the "body" or "thump" in music.
* 60Hz to 250Hz: The "bass" frequencies contain the fundamental frequencies in the lower ranges of instruments and male vocal range.
* 250Hz to 500Hz: The "low midrange" contains lower formants for many instruments and voices; crucial for "warmth".
* 500Hz to 2kHz: Most sound sources require "midrange" frequencies in order to be clear and prominent within the musical picture.
* 2kHz to 4kHz: "Upper midrange" frequencies are important for conveying the distinct character of instruments (i.e. timbre) and give percussive onsets their "attack". Too much of these can be fatiguing.
* 4kHz to 6kHz: "Presence" is important for consonants in speech and clarity and definition in music. Too much of these can be harsh and irritating.
* 6kHz and above: "Brilliance" frequencies are likely to be harmonics, conveying more information about ambience and the fidelity of the recording than distinct musical content (i.e. note and chords can be distinguished without much reliance on these frequencies). Again, overly boosting these can produce harshness and induce ear fatigue.

## Harmonic

I use harmonic information to try and extract the musical content: key, chords, and melody. These features largely depend on the output of the STFT algorithm, but I also leverage the [constant-Q chromagram](https://librosa.org/doc/0.10.1/generated/librosa.feature.chroma_cqt.html) and a variant thereof, the ["Chroma Energy Normalized"](https://librosa.org/doc/0.10.1/generated/librosa.feature.chroma_cens.html) (or CENS), for binning frequencies according to the so-called "half-step" bins of the twelve-tone equal temperament (or "12tet") chromatic scale. This supports a crude method of estimating musical key, by observing the mean energy per frequency bin.

In order to capture some of the more complex harmonic relationships, I feed the CENS output into the [tonnetz](https://librosa.org/doc/0.10.1/generated/librosa.feature.tonnetz.html) algorithm to obtain a six-dimensional representation, axes that correspond with fourths/fifths, major thirds, and minor thirds, and intersect with 12tet at regular intervals.

Another feature that overlaps a bit with loudness features, I calculate ["spectral flatness"](https://librosa.org/doc/0.10.1/generated/librosa.feature.spectral_flatness.html) to try and capture the distribution of frequencies across the spectrum. This yields a simple one-dimensional representatio, which roughly measures how "noise-like" a signal is (as opposed to musical). I have not been satisfied with any metrics I have been able to devise to address spectral energy distributions; [KDE](https://en.wikipedia.org/wiki/Kernel_density_estimation) would be equivalent to STFT frequency bins (perhaps with something equivalent to QISP for smoothing), and polynomial fits don't seem to yield orthogonal independent features.

For pitch or "note" features, I delegate to [pYIN](https://librosa.org/doc/0.10.1/generated/librosa.pyin.html#librosa.pyin) to calculate probabilities of different fundamental frequencies over time. In future work, I might like to use those probabilities directly, but in the Librosa interface, these probabilities are reduced to a monophonic model (i.e. one frequency at a time). I count these occurrences and bin them per scale degree. I also record each pair of consecutive notes and record the 12tet interval differences between them, for a simple sort of bigram feature.

## Percussive

For percussive features, I have only developed two use cases: tempo, and "onset intervals". In the case of tempo, I simply delegate to the [beat_track](https://librosa.org/doc/0.10.1/generated/librosa.beat.beat_track.html) function, but I am intrigued by the concept of "predominant local pulse estimation" (an [implementation of which](https://librosa.org/doc/0.10.1/generated/librosa.beat.plp.html) is also provided in the Librosa library).

For onset intervals, I calculate the pairwise difference of consecutive percussive events and bin them (in units of "frames", roughly 11.6ms each). This produces a lot of bins and I have yet to reduice this to a fixed-width "feature", but I think it is a good start for trying to identify the "rhythmic energy" of a piece (along with tempo, of course).
