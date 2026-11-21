# Nada · नाद

Conway's Game of Life, played as a raga. Live at **https://nada.avlokai.com**.

A playhead sweeps a 16×16 toroidal board; every living cell it crosses is a note, rows mapped to the swaras of
Bhupali, Yaman, Malkauns, Bhairav or Hamsadhwani. At the end of each bar (or half bar, or step) the colony evolves.

- Notes are scheduled on the audio clock with a look-ahead scheduler, so timing holds even when the tab is busy
- Voice: detuned triangle and sine through an enveloped resonant low-pass, into a convolution reverb built from
  generated noise and a tempo-synced dotted-eighth delay
- A synthesized tanpura (Pa–Sa–Sa–low Sa) with a sweeping band-pass for the jivari buzz
- "Keep it alive" drops in a glider when the board freezes or starts repeating

One file, raw Web Audio, no dependencies.
