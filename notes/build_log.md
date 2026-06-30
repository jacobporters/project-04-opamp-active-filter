# Build log — project-04-opamp-active-filter

## Circuit 1 — Inverting amp
- Built TL072 inverting amp (R_in=10k, R_f=47k) on breadboard with ±9V split rail.
- First measurement: 9.5V on output (positive rail saturation) instead of the expected ~−4.2V.
- Traced through pin 3 (0V, correct) and pin 2 (5.12V, wrong — should sit near 0V virtual ground in a working inverting amp).
- Root cause: the test input voltage divider was wired backwards (large resistor on the GND side instead of the V+ side), putting ~8V at the midpoint instead of ~1V. Swapped the 1k/8.2k orientation — big R to V+, small R to GND, to get a *low* midpoint voltage.
- Final measured output: −4.49V vs −4.36V calculated gain × measured Vin (close enough given 9.5V batteries vs the assumed 9V).

## Circuit 2 — Non-inverting amp
- Same TL072 op-amp A; R_in moved to pin 2 → GND, signal moved to pin 3.
- Measured 5.93V out on 1.03V in (gain 5.76 measured vs 5.70 calculated). No surprises.

## Circuit 3 — Voltage follower
- Direct pin 1 → pin 2 feedback, signal on pin 3.
- 1.03V in, 1.03V out. Gain = 1.00 exactly, as expected.

## Circuit 4 — Sallen-Key LPF
- Simulated first in LTSpice: R1=R2=1.5k, C1=C2=0.1µF unity-gain Sallen-Key, calculated pole at 1061Hz.
- AC sweep showed −3dB at 682Hz, not 1061Hz. Recalculated: for a unity-gain, equal-R/equal-C Sallen-Key, the actual −3dB frequency is the pole frequency × 0.6436 (Butterworth response correction). 1061 × 0.6436 ≈ 683Hz — matches the simulation.
- Breadboarded with electrolytic caps by mistake at first (grabbed electrolytics off the bench instead of the 0.1µF ceramics). With the op-amp rails momentarily disconnected during debugging, the scope showed a sharp resonant peak/notch instead of a clean rolloff — a sign the cap type was wrong for an AC signal path. Swapped to ceramic 0.1µF, reconnected the rails, got a clean result.
- Verified with the FNIRSI 2C53T signal generator + 2-channel scope, swept 500Hz → 10kHz. Results in `data/measurements.csv`. The −3dB point falls between 500Hz (−1.68dB) and 1kHz (−5.38dB), consistent with the 682Hz calculated/simulated value.

## Circuit 5 — Comparator with LED
- LM358P, single +9V supply (no −9V needed). R1=R2=10k threshold divider on pin 2 sets a 4.5V reference.
- Pin 3 at ~1V → LED off. Pin 3 at 9V → LED on. Matches expected comparator behavior.
- Floating pin 3 (disconnected) also turned the LED on — floating op-amp inputs pick up ambient noise and switch unpredictably. Always terminate comparator inputs to a defined voltage.

## General
- Confirmed the TL072CP/LM358P pinout before wiring circuit 1: both are dual op-amps, pin 6 is op-amp B's inverting input, not an output (see `hardware/wiring_notes.md` for the full pinout and sources).
- "104" on a ceramic cap = 100,000pF = 0.1µF (EIA three-digit code), not 104pF. Caught this during BOM planning, before it became a wiring mistake.
- Batteries read 9.5V instead of nominal 9V across the whole build — explains the consistent small gap between calculated and measured gains.
