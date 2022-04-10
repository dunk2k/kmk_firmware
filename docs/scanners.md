# Scanners

The default key scanner in KMK assumes a garden variety switch matrix, with
one diode per switch to prevent ghosting.
This doesn't cover all hardware designs though. With macro pads, for example, it
is very common to not have a matrix topology at all.
Boards like this aren't compatible with the default matrix scanner, so you will
need to swap it out with an alternative scanner.


## Keypad Scanners

### keypad MatrixScanner
This is the default scanner used by KMK.
It uses the CircuitPython builtin `keypad.KeyMatrix`.

```python
from kmk.scanners.keypad import MatrixScanner

class MyKeyboard(KMKKeyboard):
    def __init__(self):
        # create and register the scanner
        self.matrix = MatrixScanner(
            cols=self.col_pins,
            rows=self.row_pins,
            diode_orientation=self.diode_orientation,
        )

```


### keypad KeysScanner

The `keypad.Keys` scanner treats individual GPIO pins as discrete keys. To use
this scanner, provide a sequence of pins that describes the layout of your
board then include it in the initialisation sequence of your keyboard class.

```python
import board
from kmk.kmk_keyboard import KMKKeyboard
from kmk.scanners.keypad import KeysScanner


# GPIO to key mapping - each line is a new row.
_KEY_CFG = [
    [board.SW3,  board.SW7,  board.SW11, board.SW15],
    [board.SW2,  board.SW6,  board.SW10, board.SW14],
    [board.SW1,  board.SW5,  board.SW9,  board.SW13],
    [board.SW0,  board.SW4,  board.SW8,  board.SW12],
]


# Keyboard implementation class
class MyKeyboard(KMKKeyboard):
    def __init__(self):
        # create and register the scanner
        self.matrix = MatrixScanner(pins=_KEY_CFG)
```


## Digitalio Scanners

### digitalio MatrixScanner

The digitalio Matrix can scan over, as the name implies, `digitalio.DigitalInOut`
objects. That is especially usefull if a matrix is build with IO-expanders.

```python
from kmk.scanners.digitalio import MatrixScanner

class MyKeyboard(KMKKeyboard):
    def __init__(self):
        # create and register the scanner
        self.matrix = MatrixScanner(
            cols=self.col_pins,
            rows=self.row_pins,
            diode_orientation=self.diode_orientation,
            rollover_cols_every_rows=None, # optional
        )
```

## `Scanner` base class

If you require a different type of scanner, you can create your own by
providing a subclass of `Scanner`. This is a very simple interface, it only
contains a single method, `scan_for_changes(self)` which returns a key report
if one exists, or `None` otherwise.