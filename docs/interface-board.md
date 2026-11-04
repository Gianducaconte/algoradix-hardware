# Interface board

52.7 × 110.2 mm, 2 layers. The jacks, pots, encoder, button and LEDs are through-hole from the front. Every SMD part
(0805 passives, SOIC-8 op-amps, the SOT-23 reference and the FPC connector) is on the back. Component values below
come from the schematic; the full connectivity is in
[`schematic/interface-board-netlist.txt`](../schematic/interface-board-netlist.txt), and the parts are in
[`bom/interface-board-bom.csv`](../bom/interface-board-bom.csv).

> The schematic names the FILT pot and jack **EFFECT** / **EFFECT_CV**, and the A pot **ATT**. The three MCP6002s
> carry the designators **0**, **1** and **2** on the board.

## −10 V reference (AREF_-10V)

D1 is an LM4040 2.0 V shunt reference sitting on the −12 V rail. R1 (330 Ω) feeds it from GND, so the net AREF_-10V
sits 2.0 V above −12 V, at about **−10 V**, decoupled by C2 (100 nF). This net is the top of the five pots that have
a CV jack (f1, f2, TUNE, D, FILT) and the offset source for ASSIGN.

## CV summing amplifiers (MCP6002 × 3, 3.3 V single supply)

Each pot-plus-CV channel is an inverting summing amplifier whose non-inverting input is tied to GND:

```
 pot wiper (0 … −10 V) ── 100k ──┐
                                 ├──(−) MCP6002 ──┬── to the ADC (0 … 3.3 V)
 CV jack tip ───────────── 100k ─┘     (+)=GND    │
                                  └─ 33k ∥ 4.7n ──┘
 Vout = −0.33 × (Vpot + Vcv), clipped to 0 … 3.3 V by the rails
```

- The pot alone sweeps the output from 0 V to 3.3 V, the whole ADC range.
- +1 V of CV lowers the output by 0.33 V, which is **400 ADC counts per volt**. That's what the firmware measures and
  uses for 1 V/oct on TUNE. The firmware undoes the inversion of the pots and the CV.
- 33 kΩ ∥ 4.7 nF gives each channel a first-order low-pass at about 1 kHz against noise and aliasing.
- Each pot wiper also has 100 nF to GND.

| Channel | Op-amp | Pot R / CV R | Feedback | Out (FPC) |
|---|---|---|---|---|
| FILT (EFFECT) | 0 A | R6 / R7 | R5, C4 | IO_1 (pin 13) |
| ASSIGN (CV only) | 0 B | — / R2 | R4, C1 | IO_2 (pin 15) |
| f1 | 1 A | R26 / R25 | R27, C20 | IO_3 (pin 17) |
| f2 | 1 B | R30 / R29 | R31, C22 | IO_4 (pin 19) |
| TUNE | 2 A | R36 / R37 | R35, C25 | IO_5 (pin 21) |
| D (DECAY) | 2 B | R33 / R34 | R32, C23 | IO_6 (pin 23) |

**ASSIGN** has no pot. Instead, R3 (200 kΩ) from the −10 V reference adds a fixed offset, so 0 V at the jack sits at
mid-scale: Vout = 1.65 V − 0.33 × Vcv. The jack takes about ±5 V; a 5 V gate pulls the output close to 0 V. The
firmware reads ASSIGN as a gate (with hysteresis) or as a 1 V/oct or modulation input.

The CV jacks' switch (normal) contacts are tied to GND, so an unpatched jack adds 0 V.

## Pots without CV

OCT, A (ATT) and LEVELS go straight from 3.3 V to GND with the wiper to the FPC (IO_8, IO_9, IO_7), each with 100 nF
to GND (C27, C29, C28).

## Audio input (TL072 U1, ±12 V)

```
 IN L tip ─┬─ R8 100k ─┬─ R10 12k ── GND        (R8 ∥ R9 = 75k)
           └─ R9 300k ─┤─ C12 220p ── GND
                       └──(+) U1A ── follower ── IN_L_PIN (FPC 2) → codec line in
```

The divider brings a Eurorack signal down by about ÷7.25 (10 V p-p → about 1.4 V p-p) to the codec's line level.
220 pF adds a gentle low-pass far above the audio band. U1A is a unity-gain buffer. The right channel is the same
(R12, R13, R11, C13, U1B → IN_R_PIN, FPC 4). **IN R is normalled to IN L**: with nothing in the right jack, the left
signal feeds both channels.

## Audio output (TL072 U2, ±12 V)

```
 codec line out (OUT_L_PIN, FPC 6) ──(+) U2A ──┬── R16 1k ── OUT L tip
                                    (−) ──┬────┘
                                          ├── R14 75k ∥ C14 100p (feedback)
                                          └── R15 12k ── GND
```

Non-inverting gain 1 + 75k / 12k = **×7.25 (+17 dB)**, which brings line level up to Eurorack level. 100 pF in the
feedback rolls off above about 21 kHz. 1 kΩ in series protects the output. The right channel is the same (R19, R18,
C15, R17, U2B from OUT_R_PIN, FPC 8).

## MIDI

M_IN and M_OUT are TRS jacks (PJ366ST). The sleeves go to GND. Tip and ring go straight through the FPC
(pins 1 and 3 for IN, 5 and 7 for OUT) to the connector board's M-IN / M-OUT pins and on to the Core's MIDI circuit
(opto-isolated input, USART6). There's no MIDI
circuitry on the interface board itself.

## Controls and LEDs

| Part | Circuit | FPC |
|---|---|---|
| S1, GATE / FUNC button (C&K D6) | contact to 3.3 V, 1 kΩ (R50) in series to the MCU, 100 nF (C34) to GND. Active high; the MCU uses an internal pull-down | IO_10 (pin 30) |
| SW1, encoder push | contact to 3.3 V, 1 kΩ (R20) in series, 100 nF (C18). Active high | IO_11 (pin 28) |
| SW1, encoder A / B (EC11, 20 detents) | 10 kΩ pull-ups (R24, R23) to 3.3 V, common to GND, 1 kΩ in series (R21, R22), 100 nF (C6, C7) to GND | IO_12, IO_13 (pins 26, 24) |
| LD1–LD4, ring LEDs | MCU pin → 330 Ω (R46, R47, R38, R39) → LED → GND. Active high | IO_15, IO_17, IO_16, IO_14 (pins 20, 16, 18, 22) |

## Decoupling

- ±12 V: C10, C17 (+12 V) and C11, C16 (−12 V), 100 nF at the TL072s.
- 3.3 V: C3, C31, C32, C33, 100 nF at the MCP6002s.
