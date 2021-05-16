# Wiring

A lot of what I've done is specific to my setup and was learned with *a lot of trial and error*. It's not perfect and you might need to do things differently, but this should at least give you a good start

## Panel Sensors and Cab Buttons

There's two ways to do the wiring here. In either case, you'll want all of your panel sensors to be wired in parallel (so any activated sensor activates the whole panel).

### Pulldown
The code is pre-configured for this method.
One wire of each sensor group/button is connected to +5V, the other wire to the Arduino and a pulldown resistor (usually 10k ohms) to ground.

For this config:
 - Modify the `pinMode()` calls for the approriate buttons, change them from `INPUT_PULLUP` to just `INPUT`
 - Invert the input logic for the appropriate buttons as well (`tmpx = digitalRead(pin);` -> `tmpx = !digitalRead(pin)`).

### Pullup (pre-configured)

In my cab, all the cab buttons (test, service, etc) were already wired to ground on one side. To interface with buttons wired up like these:
 - Modify the `pinMode()` calls for the appropriate buttons to change it from `INPUT` to `INPUT_PULLUP` to enable the Arduinos's internal pullup resistors.
 - Don't invert the input logic for the appropriate buttons as well (`tmpx = !digitalRead(pin);` -> `tmpx = digitalRead(pin)`).
**Connect the button's ground (which was already wired up to the 12V power supply's ground in my cab) to the Arduino's ground.**


## Shift Registers
My setup outputs lighting data to 3x 74HFC595 shift registers, but can be modified to only use 2. The shift register output pins are connected to the MOSFETs for panel lights.

## Panel lights
Lighting for each panel is controlled by a MOSFET. I used the IRLB8721 but any other N-channel MOSFET should work. High-power loads may require a heatsink (The model above caps out at 2W without one, and my panel lights didn't draw that much power ). **Connect your 12V power supply's ground to the Arduino's ground to avoid issues.**

## Cabinet lights
I interfaced with my Pump it Up SX cabinet's original, unmodified amp box to control cabinet lighting. Looking at the 7 pin connector that originally connected to a PIUIO with the tab on top, connect these pins to the 3rd shift register:
| Green | Blue | Brown | Red | Orange | Yellow | Purple |
|--|--|--|--|--|--|--|
| Neon- | LeftCent- | RightCent- | Left- | Right- | FG (cabinet ground) | VCC |
| 5 | 4 | 3 | 2 | 1 | | +5V |

Each light is turned on by grounding its pin, so turning a shift register pin OFF turns the corresponding light ON.

For other DIY cabinet lighting solutions, use the same circuit as the panel lights.
