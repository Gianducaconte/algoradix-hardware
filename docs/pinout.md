# Pinout

## FPC cable between the boards

The 30-pin, 1.0 mm FPC connector FPC1 on the interface board (LCSC C2914061) and its counterpart on the connector
board. The right-hand column shows where the connector board routes each signal on the Axoloti Core. Those
assignments were measured with the firmware.

| Pin | Net | Function | Core pin |
|---|---|---|---|
| 1 | MIDI_IN_TIP | MIDI in, TRS tip | Core MIDI in (via the connector board's M-IN) |
| 2 | IN_L_PIN | audio in L, buffered | codec line in L |
| 3 | MIDI_IN_RING | MIDI in, TRS ring | Core MIDI in (via M-IN) |
| 4 | IN_R_PIN | audio in R, buffered | codec line in R |
| 5 | MIDI_OUT_TIP | MIDI out, TRS tip | Core MIDI out (via M-OUT) |
| 6 | OUT_L_PIN | audio out L, from the codec | codec line out L |
| 7 | MIDI_OUT_RING | MIDI out, TRS ring | Core MIDI out (via M-OUT) |
| 8 | OUT_R_PIN | audio out R, from the codec | codec line out R |
| 9 | 5V | +5 V (not used on the interface board) | +5 V |
| 10 | 3V3 | +3.3 V supply for the interface board | 3.3 V |
| 11, 12 | GND | ground | GND |
| 13 | IO_1 | FILT pot + CV | PB0 (ADC1_IN8) |
| 14 | IO_18 | not connected | — |
| 15 | IO_2 | ASSIGN CV | PA7 (ADC1_IN7) |
| 16 | IO_17 | ring LED LD2 | ring LED pin (see below) |
| 17 | IO_3 | f1 pot + CV | PA5 (ADC1_IN5) |
| 18 | IO_16 | ring LED LD3 | ring LED pin |
| 19 | IO_4 | f2 pot + CV | PA3 (ADC1_IN3) |
| 20 | IO_15 | ring LED LD1 | ring LED pin |
| 21 | IO_5 | TUNE pot + CV | PC4 (ADC1_IN14) |
| 22 | IO_14 | ring LED LD4 | ring LED pin |
| 23 | IO_6 | D pot + CV | PC3 (ADC1_IN13) |
| 24 | IO_13 | encoder phase B | encoder pin (see below) |
| 25 | IO_7 | LEVELS pot | PC0 (ADC1_IN10) |
| 26 | IO_12 | encoder phase A | encoder pin |
| 27 | IO_8 | OCT pot | PC1 (ADC1_IN11) |
| 28 | IO_11 | encoder push | PB7 |
| 29 | IO_9 | A pot | PC2 (ADC1_IN12) |
| 30 | IO_10 | GATE / FUNC button | PB6 |

## Panel controls → STM32

As used by the firmware ([`board_algoradix.h`](https://github.com/Gianducaconte/algoradix-firmware/blob/main/firmware/board/board_algoradix.h) in the firmware repository).

| Control | STM32 pin | Notes |
|---|---|---|
| f1 + CV | PA5 | ADC1_IN5 |
| f2 + CV | PA3 | ADC1_IN3 |
| TUNE + CV | PC4 | ADC1_IN14, 1 V/oct |
| OCT | PC1 | ADC1_IN11 |
| A | PC2 | ADC1_IN12 |
| D + CV | PC3 | ADC1_IN13 |
| FILT + CV | PB0 | ADC1_IN8 |
| LEVELS | PC0 | ADC1_IN10 |
| ASSIGN | PA7 | ADC1_IN7, CV only |
| GATE / FUNC button | PB6 | active high |
| encoder push | PB7 | active high |
| encoder phases | PC5, PA2 | quadrature, pulled up on the interface board |
| ring LED "ALGO" (top left) | PA6 | active high |
| ring LED "CLOCK / VCA" (top right) | PA0 | active high |
| ring LED "PATCH" (bottom left) | PA1 | active high |
| ring LED "GATE" (bottom right) | PA4 | active high |
| OLED SCL / SDA | PB8 / PB9 | I2C1, SSD1306 at 0x3C, through the connector board's I2C header |
| spare | PB1 | the only unused header pin |

All pots read **inverted** (fully left ≈ 4080 counts, fully right 0), and all CV inputs are **inverting** at
400 counts per volt. The firmware compensates for both.

## Eurorack power (connector board)

Standard 10-pin shrouded header. The silkscreen marks the **−12 V** side next to the square pin and stripe, the usual
Eurorack red-stripe convention. Diodes D2 and D3 protect against a reversed cable.

## OLED header (connector board)

| Pin | Signal |
|---|---|
| 1 | GND |
| 2 | 3V3 |
| 3 | SCL (PB8) |
| 4 | SDA (PB9) |

Pin order as printed on the connector board, from the square pad. Check it against your display module, because the
order of GND and VCC varies between modules.
