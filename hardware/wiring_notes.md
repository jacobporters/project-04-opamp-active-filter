# Wiring notes — project-04-opamp-active-filter

## IC pinout (TL072CP and LM358P)

Both ICs share the same dual op-amp DIP-8 pinout.

| Pin | Function |
|---|---|
| 1 | Output A |
| 2 | Inverting input A (−) |
| 3 | Non-inverting input A (+) |
| 4 | V− (GND for LM358P single-supply use) |
| 5 | Non-inverting input B (+) |
| 6 | Inverting input B (−) |
| 7 | Output B |
| 8 | V+ |

Pin 6 is an input (op-amp B's inverting input), not an output — easy to mix up.

Sources: [TL072 product page, TI.com](https://www.ti.com/product/TL072) · [LM358 product page, TI.com](https://www.ti.com/product/LM358) · [LM358 datasheet, TI.com](https://www.ti.com/lit/ds/symlink/lm358.pdf)

All circuits below use op-amp A (pins 1, 2, 3) unless noted.

## Power
- Circuits 1-4 (TL072CP): split ±9V rail from two 9V batteries in series, center tap = GND.
- Circuit 5 (LM358P): single +9V supply, pin 4 tied to GND (not −9V).

## Circuit 1 — Inverting amplifier
| Pin / node | Connected to |
|---|---|
| 2 (−IN A) | R_in (10kΩ) ← test input divider midpoint; R_f (47kΩ) ← pin 1 |
| 1 (OUT A) | R_f (47kΩ) → pin 2; measurement point |
| 3 (+IN A) | GND |
| 4 (V−) | −9V rail |
| 8 (V+) | +9V rail |
| 5, 6, 7 | unconnected |
| Test input divider | 8.2kΩ from +9V to midpoint, 1kΩ from midpoint to GND (~1.03V) |

## Circuit 2 — Non-inverting amplifier
| Pin / node | Connected to |
|---|---|
| 2 (−IN A) | R_in (10kΩ) → GND; R_f (47kΩ) ← pin 1 |
| 1 (OUT A) | R_f (47kΩ) → pin 2; measurement point |
| 3 (+IN A) | Test input divider midpoint (~1.03V) |
| 4 (V−) | −9V rail |
| 8 (V+) | +9V rail |

## Circuit 3 — Voltage follower
| Pin / node | Connected to |
|---|---|
| 1 (OUT A) | Direct wire → pin 2; measurement point |
| 2 (−IN A) | Direct wire ← pin 1 |
| 3 (+IN A) | Test input divider midpoint (~1.03V) |
| 4 (V−) | −9V rail |
| 8 (V+) | +9V rail |

## Circuit 4 — Sallen-Key low-pass filter (unity gain, R1=R2=1.5kΩ, C1=C2=0.1µF ceramic)
| Pin / node | Connected to |
|---|---|
| Signal in | R1 (1.5kΩ) → Node A |
| Node A | R1 ← signal in; R2 (1.5kΩ) → Node B; C1 (0.1µF) ← pin 1 (feedback) |
| Node B | R2 ← Node A; pin 3 (+IN A); C2 (0.1µF) → GND |
| 1 (OUT A) | C1 → Node A (feedback); pin 2 (direct feedback); measurement point (scope CH2) |
| 2 (−IN A) | Direct wire ← pin 1 |
| 4 (V−) | −9V rail |
| 8 (V+) | +9V rail |

Signal source: FNIRSI 2C53T DDS output → R1, ground clip → GND rail. Scope CH1 on signal in, CH2 on pin 1 (output).

## Circuit 5 — Comparator with LED (LM358P, single supply)
| Pin / node | Connected to |
|---|---|
| 2 (−IN A) | Threshold divider midpoint (4.5V): R1 (10kΩ) +9V→midpoint, R2 (10kΩ) midpoint→GND |
| 3 (+IN A) | Test input (~1V or +9V) |
| 1 (OUT A) | R3 (330Ω) → LED anode |
| LED cathode | GND |
| 4 (V−) | GND (single-supply — not −9V) |
| 8 (V+) | +9V rail |

## Gotchas
- Voltage divider orientation: the resistor between the midpoint and GND sets the output voltage — large R to V+, small R to GND gives a *low* midpoint. Wired this backwards once on circuit 1 and got ~8V instead of ~1V.
- Use ceramic, not electrolytic, capacitors in the Sallen-Key filter — electrolytics are unsuitable for AC signal paths in this position.
