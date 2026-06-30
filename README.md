# project-04-opamp-active-filter
Five op-amp configurations built and verified on breadboard, simulated first in LTSpice — inverting/non-inverting amps, voltage follower, Sallen-Key active low-pass filter, and a comparator with LED.

COMPLETE ✅

![Sallen-Key filter breadboard](media/photos/sallen_key_breadboard.jpg)

Goal: Build and verify five foundational op-amp circuits using the TL072CP (JFET, dual-rail) and LM358P (single-supply) — inverting amp, non-inverting amp, voltage follower, a 2nd-order Sallen-Key low-pass filter, and a comparator. Each circuit was simulated in LTSpice before breadboarding, then verified with a multimeter and the FNIRSI 2C53T oscilloscope/signal generator.

I learned:
- "104" on a ceramic cap is the EIA three-digit code (10 × 10⁴ pF = 0.1µF), not 104pF — caught this before it became a wiring mistake.
- TL072CP/LM358P are dual op-amps: pin 6 is op-amp B's inverting *input*, not an output. Mixed up op-amp A and B pins early on.
- Voltage divider orientation: the resistor to GND sets the midpoint voltage, not the resistor to V+. Got this backwards once on circuit 1 and chased a phantom saturation bug for several measurements before catching it.
- Electrolytic capacitors are unsuitable for AC signal paths — swapped for ceramics in the Sallen-Key filter after seeing a sharp resonant peak/notch instead of a clean rolloff.
- For a unity-gain, equal-R/equal-C Sallen-Key filter, the measured −3dB point is the calculated pole frequency × 0.6436 (Butterworth response correction), not the pole frequency itself.
- A floating comparator input picks up ambient noise and switches unpredictably — always drive both inputs with a defined voltage.
- Batteries read 9.5V fresh, not 9V — explains the small (~5-7%) gaps between calculated and measured gains throughout.

Circuits:
| # | Configuration | IC | Spec (calc) | Measured |
|---|---|---|---|---|
| 1 | Inverting amp | TL072CP | gain −4.70 | −4.36 |
| 2 | Non-inverting amp | TL072CP | gain 5.70 | 5.76 |
| 3 | Voltage follower | TL072CP | gain 1.00 | 1.00 |
| 4 | Sallen-Key LPF | TL072CP | −3dB @ 1061Hz | −3dB @ ~682Hz |
| 5 | Comparator + LED | LM358P | 4.5V threshold | verified switching |

Full pin-level wiring for every circuit: [`hardware/wiring_notes.md`](hardware/wiring_notes.md)

Sallen-Key frequency response (measured with FNIRSI 2C53T, R1=R2=1.5kΩ, C1=C2=0.1µF):
| Frequency | Vin (pp) | Vout (pp) | Gain |
|---|---|---|---|
| 500 Hz | 104.2 mV | 85.85 mV | −1.68 dB |
| 1 kHz | 105.6 mV | 56.81 mV | −5.38 dB |
| 5 kHz | 108.0 mV | 6.40 mV | −24.54 dB |
| 10 kHz | 108.1 mV | 2.37 mV | −33.18 dB |

Raw data: [`data/measurements.csv`](data/measurements.csv)

BOM:
| Part | Value / Part# | Qty | Notes |
|------|-----------------|-----|-------|
| Op-amp | TL072CP | 1 | Dual JFET, DIP-8 — circuits 1-4 |
| Op-amp | LM358P | 1 | Dual, single-supply, DIP-8 — circuit 5 |
| IC socket | DIP-8 | 2 | - |
| Resistor | 1kΩ | 1 | Test input divider (top) |
| Resistor | 8.2kΩ | 1 | Test input divider (bottom) |
| Resistor | 10kΩ | 4 | R_in (circuits 1, 2); threshold divider (circuit 5, x2) |
| Resistor | 47kΩ | 2 | R_f (circuits 1, 2) |
| Resistor | 1.5kΩ | 2 | R1, R2 — Sallen-Key (circuit 4) |
| Resistor | 330Ω | 1 | LED current limit (circuit 5) |
| Capacitor | 0.1µF ceramic | 2 | C1, C2 — Sallen-Key (circuit 4) |
| LED | 5mm red | 1 | Comparator indicator (circuit 5) |
| Battery | 9V | 2 | Split ±9V rail (circuits 1-4) |
| Battery snap connector | 9V, bare-wire leads | 2 | - |

Instruments: FNIRSI 2C53T (oscilloscope + signal generator), Uni-T UT61E+ multimeter.
