# Unmix

Split any song into **drums, bass, vocals and everything else** using pure signal-processing math. No AI, no server: it all runs in your browser.

**Live:** https://1129chengyuan.github.io/unmix/

Drop in an MP3/WAV/M4A (the first 90 seconds get split), then mix the stems like a DJ: scrolling waveform lanes, mute/solo, faders, level meters, and automatic BPM detection with a beat grid. Or hit **Demo track** to try it instantly.

## How it works

| Stem | The math |
| --- | --- |
| **Spectrogram** | A short-time Fourier transform (2048-sample frames, 75% overlap) turns sound into frequency × time. Steady notes draw horizontal lines and drum hits draw vertical ones. |
| **Drums** | Harmonic/percussive separation: a median filter across frequency keeps vertical lines (hits), and one across time keeps horizontal lines (notes). Soft masks split each bin between them. |
| **Bass** | Harmonic content below ~120 Hz, fading out by 260 Hz. |
| **Vocals** | Harmonic content in the voice range that's identical in both channels, since lead vocals are mixed dead center. Centeredness = 1 − \|L−R\| / \|L+R\| per bin. |
| **Other** | Everything left over. The four masks sum to 1, so the stems add back up to the original exactly. |
| **Tempo** | Onset strength from the drum stem's energy jumps, then autocorrelation finds the beat spacing (60–180 BPM). |

Each stem is rebuilt with an inverse FFT and overlap-add. The heavy lifting runs in a Web Worker so the page stays responsive.

## Limits

Real mixes break the rules these tricks rely on: reverb-heavy or doubled vocals leak into "other", sustained synths in the voice range leak into "vocals", and low kick drums can land in "bass". Mono files can't be split by position at all.

## Run locally

It's a single `index.html` with no build step. Just open it.

## Keyboard

`Space` play/pause · `1`–`4` mute · `Shift`+`1`–`4` solo · `←` `→` skip 5 s
