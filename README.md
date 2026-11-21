# Nada · नाद

**Conway's Game of Life, played as a raga.**
Live: **https://nada.avlokai.com**

A playhead sweeps a 16×16 board. Every living cell it crosses plays a note, with rows mapped to the swaras
of an Indian raga. At the end of each bar the colony takes one step of Conway's evolution, so the melody
grows, decays and mutates by itself. Underneath, a synthesized tanpura drones Pa–Sa–Sa–Sa. Every sound is
built from raw Web Audio oscillators; there are no samples.

---

## What it does

- **Paint** cells with mouse or touch (click-drag toggles a run of cells), or use the keyboard: arrows move
  a cursor, Space toggles.
- **Seeds**: Acorn, two gliders, a pulsar, a lightweight spaceship, random, or clear.
- **Ragas**: Bhupali, Yaman, Malkauns, Bhairav, Hamsadhwani, each with its own swaras across three octaves
  above a Sa of D3.
- **Tempo** 60–180 BPM, in sixteenth-note steps.
- **Evolve** every bar, every half bar or every step.
- **Tanpura** on or off.
- **Keep it alive**: when the board freezes, dies or starts repeating, a glider is dropped in at random.
- Shows generation, live cells, notes played, and the swaras sounding right now.

## How it works

### Life

A 16×16 torus (edges wrap). The standard B3/S23 rule is computed into a fresh buffer each generation.
Newborn cells are marked so they animate in. Stagnation is detected by fingerprinting the board and
comparing against the last six: any repeat (still lifes, oscillators of period up to 6) or an empty board
counts as stuck.

### Pitch

Row 0 is the highest note. Row k from the bottom maps to swara k mod n of the chosen raga, in octave
⌊k / n⌋, where n is the number of swaras (5 for the pentatonic ragas, 7 for Yaman and Bhairav).

```
f = (Sa / 2) · 2^(octave + semitones / 12)        Sa = 146.83 Hz (D3)
```

### Timing: a look-ahead scheduler

JavaScript timers are not precise enough for music, but the Web Audio clock is. Nada uses the classic
two-clock pattern: a `setTimeout` loop wakes every 25 ms and schedules every step that falls within the next
120 ms *on the audio clock*. Visuals are queued with their audio timestamps and drawn when
`audioContext.currentTime` reaches them, so the playhead and the flash of a cell line up with what you hear.
Evolution is scheduled the same way, so the board changes exactly on the downbeat. Changing the tempo
mid-phrase does not drift.

### The voice

Each note is a triangle oscillator plus a quiet sine an octave up, detuned by 0.3% for a slow beat, through a
resonant low-pass filter (Q = 6) whose cutoff jumps open on the attack and closes as the note decays. The amp
envelope is exponential: attack in 8 ms, then down to 35% within 180 ms, then a long tail. Chords are scaled
by 1/√n so busy columns are not louder.

### The space

- A **convolution reverb** whose impulse response is generated in code: 3.2 seconds of stereo noise under a
  power-law decay.
- A **tempo-synced dotted-eighth delay** with a low-pass filter in its feedback path, so repeats darken as
  they fade.
- A gentle **compressor** on the master bus.

### The tanpura

Four strings plucked in the traditional order Pa, Sa, Sa, low Sa, once every 1.5 beats. Each pluck is two
slightly detuned sawtooth waves through a band-pass filter that sweeps down from the 9th harmonic to the 3rd
over 3.6 seconds — a cheap imitation of the *jivari* buzz of the real instrument's bridge.

## How it was built

One HTML file, no dependencies. The board is drawn on a `<canvas>` at device-pixel resolution with
rounded-rect cells. Flash and birth animations decay by elapsed time rather than per frame, so they look the
same at 30 or 144 fps. Colours come from CSS custom properties and switch with the system theme. Audio starts
only after a click on Play, as browsers require.

A test hook (`window.__nada.step()`) lets automated checks advance the colony without audio. That is how the
page was exercised in headless Chrome: play started, five generations stepped, and screenshots taken in both
themes with no console errors.

## Run locally

```bash
python -m http.server 8000   # then http://localhost:8000
```

## Name

*Nāda* (नाद) is Sanskrit for primordial sound — in Indian music theory, the vibration that underlies all
music.

---

Built by Sushanth Kasturi as one of a set of deliberately useless things. More at
https://sushanth.avlokai.com/projects
