# Build guide

You'll be soldering fine-pitch SMD parts (0805, SOIC-8, SOT-23 and a 1.0 mm FPC connector) and fitting a stack of
boards behind a 12 HP panel. If you've built a few Eurorack DIY kits with SMD parts, this is a comfortable build.

## What you need

| Item | Notes |
|---|---|
| **Axoloti Core v1.2** | the brain. Not part of this repository |
| Interface board PCB | [`pcb/interface-board/gerbers/`](../pcb/interface-board/gerbers/) |
| Connector board PCB | [`pcb/connector-board/gerbers/`](../pcb/connector-board/gerbers/) |
| Front panel | [`panel/variant-A/`](../panel/variant-A/) or [`panel/variant-B/`](../panel/variant-B/) |
| Interface board parts | [`bom/interface-board-bom.csv`](../bom/interface-board-bom.csv) |
| Connector board parts | 2×5 shrouded Eurorack power header, 30-pin 1.0 mm FPC connector (same as the interface board), two 3-pin 2.54 mm connectors (U7, U20), pin headers for the Core's GPIO header, M-IN / M-OUT and I2C, reverse-polarity diodes D2 / D3, 0805 capacitors C8, C9 and the two next to the diodes |
| OLED | SSD1306 64 × 32 I2C module (0.49"), on the connector board's I2C header |
| FFC cable | 30 conductors, 1.0 mm pitch, long enough to reach from the connector board to the interface board |
| Cables | 3-wire ±12 V lead from U20 to U3 on the interface board; 3-wire lead from U7 to the Core's power input; MIDI wires from M-IN / M-OUT to the Core |
| Hardware | knobs for 9 mm T18 shafts (two larger ones for f1 and f2), an encoder knob, jack nuts, Eurorack rails screws, a 10-pin to 16-pin Eurorack power cable |

## Ordering the boards

The Gerber folders are ready to upload as they are. Zip each folder and send it to any PCB fab. Both boards are
standard 2-layer FR4 with plated through-holes. The interface board's drill file contains a few slotted holes, which every common fab handles.

The panel is ordered as an **aluminium PCB**: the outline, the holes and the artwork are all in its Gerbers. The
artwork is in the **top solder-mask layer** (`.GTS`), drawn inverted, and the top copper (`.GTL`) gives the plated
rings around the jack holes. Order with **no silkscreen**: the print comes from the mask itself, so there's no ink
that has to grip on the aluminium. Variant A and variant B are the two colourways. Choose the solder-mask colour to
match the variant (see the [photos](../images/photos/04-panels-black-white.jpg)).

## Assembly order

1. **Interface board, SMD (back side).** Op-amps first (U1, U2 = TL072; 0, 1, 2 = MCP6002; check pin 1), then D1,
   then the FPC connector (flux, tack two corner pins, check alignment, then the rest), then the 0805 passives. Clean
   off the flux and inspect the FPC pins for bridges under magnification.
2. **Interface board, through-hole (front).** Put the jacks, pots, encoder, button and LEDs in place, **fit the
   panel**, tighten a few jack nuts, and only then solder. That keeps everything aligned with the panel holes.
3. **Connector board.** SMD parts first, then the FPC connector, the 3-pin connectors, the power header and the GPIO
   pin header. Fit it onto the Core before soldering the GPIO header, so the pins line up.
4. **OLED.** Wire or plug the display onto the I2C header. Check its GND / VCC order first, because it varies between
   modules.
5. **Stack.** Connector board on the Core, the FFC between the two FPC connectors (the contacts face the right way on
   both ends), the ±12 V lead to U3, the power lead to the Core, MIDI to the Core.

## First power-up

1. Before connecting power, check for shorts between +12 V, −12 V, 3.3 V and GND on the interface board.
2. Power the module from the rack, with the power cable's red stripe on −12 V.
3. Measure the AREF_-10V net (for example at the top of a CV pot): it should read about −10 V.
4. Flash the firmware (see [flashing.md](https://github.com/Gianducaconte/algoradix-firmware/blob/main/docs/flashing.md) in the firmware repository). If you're bringing up a new build, flash the
   bring-up stages first: STAGE 0 checks the clocks and LEDs, STAGE 1 shows every pot, CV, button and encoder value on
   the display, and STAGE 2 tests audio in and out.

## Calibration

Nothing to trim. The firmware's 1 V/oct scaling assumes the nominal 400 counts per volt of the CV amplifiers
(33 kΩ / 100 kΩ), so use 1 % resistors in the CV channels, especially TUNE (R35, R36, R37).
