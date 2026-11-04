# Architecture

Algoradix is a stack of four parts behind one panel:

```
 ┌──────────────────────── front panel (12 HP) ────────────────────────┐
 │  Core jacks poke through   │   interface board controls and jacks   │
 └────────────┬───────────────┴───────────────────┬────────────────────┘
              │                                   │
     ┌────────┴─────────┐   30-pin FPC    ┌───────┴──────────┐
     │  Axoloti Core    │◀──────┐        │ interface board  │
     │  v1.2            │       │  ┌─────│ pots, CV, audio, │
     └────────┬─────────┘       │  │     │ MIDI TRS, LEDs,  │
              │ GPIO header     │  │     │ encoder, button  │
     ┌────────┴─────────────────┴──┴─┐   └───────┬──────────┘
     │  connector board              │           │ ±12 V (3-pin)
     │  Eurorack power, OLED (I2C),  │───────────┘
     │  MIDI to the Core, power      │
     └───────────────┬───────────────┘
                     │ 10-pin Eurorack power
```

## Axoloti Core v1.2

The Core does all the computing and owns the audio path. Its own connectors line up with the left-hand column of the
panel: line TRS in and out, headphone out, mini-USB (device: flashing and USB drive), micro-SD, USB-A (host: MIDI
keyboards and controllers), the power LED and the two status LEDs.

Only its 2×20 GPIO header is used for the panel. That header carries PA0–PA7, PB0, PB1, PB6–PB9, PC0–PC5,
+5 V, VDD (3.3 V), VDDA and GND, and every one of those I/O pins except PB1 is used (see [pinout.md](pinout.md)).

## Connector board

The connector board sits on the Core's GPIO header and is the hub of the module. Going by its silkscreen:

| Connector | What |
|---|---|
| Axoloti GPIO header (pin row along the long edge) | all panel signals to and from the STM32 |
| **FPC**, 30 pins, 1.0 mm, "TO INTERFACE BOARD" | the cable to the interface board ([pinout](pinout.md#fpc-cable-between-the-boards)) |
| **POWER**, 2×5 shrouded, +12 V / −12 V marked | Eurorack power in, through the reverse-polarity diodes D2 / D3 and the caps C8, C9 |
| **U20**, 3 pins, "TO BOARD INTERFACE" | ±12 V and GND to the interface board's op-amps (its connector U3) |
| **U7**, 3 pins, "TO AXO REGULATOR" | power to the Core's power input |
| **M-IN / M-OUT**, 2 + 2 pins | the TRS MIDI lines from the panel to the Core's MIDI circuit |
| **I2C**, 4 pins: SDA, SCL, 3V3, GND | the OLED (PB9 = SDA, PB8 = SCL) |

## Interface board

All the panel controls, plus the analog conditioning that sits between Eurorack levels and the Core. Details are in
[interface-board.md](interface-board.md).

| Supply | From | Used by |
|---|---|---|
| ±12 V | connector board, 3-pin U3 | TL072 audio buffers (U1, U2), the −10 V reference |
| 3.3 V | the Core, through the FPC (pin 10) | MCP6002 CV amplifiers, pots without CV, encoder and button pull-ups |
| 5 V | FPC pin 9 | not used on this board |

## Signal flow

| Signal | Path |
|---|---|
| Pot + CV | pot wiper (0…−10 V) and CV jack → inverting summing amp (MCP6002, gain −0.33) → 0…3.3 V → FPC → STM32 ADC1 |
| Pot only (OCT, A, LEVELS) | pot between 3.3 V and GND → FPC → ADC1 |
| Audio in | jack → attenuator (÷7.25) → TL072 follower → FPC → codec line in |
| Audio out | codec line out → FPC → TL072 non-inverting amp (×7.25) → 1 kΩ → jack |
| MIDI | TRS jacks → FPC → connector board → the Core's MIDI DIN circuit (USART6) |
| Button, encoder | 3.3 V logic through 1 kΩ series resistors → FPC → GPIO |
| Ring LEDs | GPIO → FPC → 330 Ω → LED → GND |
| Display | connector board I2C header → I2C1 on PB8 / PB9 |

## Grounding and protection

- All jack sleeves and the op-amp references go to the single GND net that the FPC (pins 11 and 12) shares with the
  Core.
- CV inputs reach the STM32 only through MCP6002 outputs on a 3.3 V supply, so no CV voltage can drive an ADC pin
  outside 0…3.3 V. Each one also has a 1 kHz RC low-pass (33 kΩ ∥ 4.7 nF in the feedback).
- Audio outputs have 1 kΩ series resistors to protect the TL072 against shorted or patched-together outputs.
