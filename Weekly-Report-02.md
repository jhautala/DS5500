# Note Extraction

Working on polyphonic pitch detection, it seems there are a number of ways to create sets of candidate frequencies for fundamentals. The magnitude of QISP-refined spectral peaks would be the most straightforward solution (from where I am in the project). Otherwise, there are a few established methods and an experimental method I have been working on.

## Experimental "Harmonic Kernel"
I implemented a "harmonic kernel" solution (this seems conceptually similar to [Harmonic Spectrum Product](http://musicweb.ucsd.edu/~trsmyth/analysis/Harmonic_Product_Spectrum.html)) for detecting fundamental frequencies. The idea is to create a `1xN` kernel, with `N` frequency bins evenly spaced along a log2-scaled axis (e.g. bin zero would be an interval of zero cents, or a "unison"; bin 10 would represent an interval of ten cents, etc.). Then we populate the frequency bins with values between zero and one, following a distribution that peaks at intervals that match natural harmonics.

Initially, I tried simple linear interpolation to zero between peaks. Then I tried adding exponential decay as we progress into higher harmonics (at around ten octaves, the harmonics start to get pretty thick for 1-cent resolution).

<img src="figs/linear_neighbors.png" size=400>
<img src="figs/exponential_neighbors.png" size=400>

Here is a fairly raw example (using exponential decay):

<img src="figs/harm_kern_exponential_decay.png" size=600>

After tweaking the kernel shape a bit more, I was able to get fairly distinct horizontal lines at fundamentals (along with some artifical emphasis of related notes). We can see many intervals in the key are reinforced by the kernel, but this is far from conclusive.

<img src="figs/harm_kern_no_oct_time_norm.png" size=600>

## Moving Forward
Ultimately, I think there may not be a "perfect" kernel to use for such a solution, so at this point, I think it is best to choose a provisional solution for note extraction and proceed with the subsequent steps. Librosa supports pYIN, which seems like one of the strongest candidates, for performance reasons (including time performance, where it certainly outperforms my initial implementation of the "harmonic kernel" solution). CREPE seems to do better, but that is a CNN-based solution and initial tests with pYIN were very promising.

<img src="figs/pyin.png" size=600>

I found something interesting when I accidentally loaded my test sample at a lower sample rate. It looks like maybe the implicit low-pass filter that comes along with lower sample rates actually improved fundamental detection via pYIN. Here we see the fundamentals detected actually follow the bassline much better than when sampled at 44.1kH.

<img src="figs/pyin_22050.png" size=600>

I do not have a theoretical explanation yet, but it seems that low-frequency resolution has been directly and beneficially impacted by decreasing the sample rate. This is visible in the QISP-refined peaks. Compare the spectrograms above with this one (particaularly from ~E2 to A2).

NOTE: This resolution issue should *not* be caused by QISP refinement; it should actually be improved by it. I think it may have to do with the happenstance interaction between frequency bins and the specific notes in the signal. It may be caused by interference from artifacts in higher frequencies that are mitigated by the lower high pass filter for 22050Hz. This reminds me that we are working in an area where the famous Heisenberg uncertainty principle essentially comes into play: in the context of a wave analysis via FFT, the trade-off is between frequency (or momentum/velocity) and timing (or position).

<img src="figs/beck_spec_22050.png" size=600>

## Other Techniques
I also found [PITCH DETECTION METHODS REVIEW](https://ccrma.stanford.edu/~pdelac/154/m154paper.htm), which seems like a decent summary of a few diverse techniques.