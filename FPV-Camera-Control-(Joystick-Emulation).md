## Description
This functionality provides OSD joystick emulation for HS1177-style cameras, e.g. cameras having a single OSD input pin driven by a button resistor divider.
Most cameras up to date seem to adhere to the design and have the following resistor nominals:

* 47 kΩ - camera internal resistor, 9.9 kΩ for RunCam Sparrow, Foxeer Monster and probably other HS1190 cameras (thanks to @khalinatek)
* 45 kΩ - enter
* 27 kΩ - left
* 15 kΩ - up
* 6.8 kΩ - right
* 0 - down

## Setting up
* `resource camera_control PIN` -  allows you to designate a `PIN` for camera control functionality. The list of suitable pins depends on the way you're going to wire your camera to the FC.
* `set camera_control_mode = hardware_pwm` - mode of operation, `software_pwm` is the least restrictive in terms of available PIN selection, but it requires both a resistor and a capacitor to work properly; `hardware_pwm` is almost guaranteed to work with just a resistor given you can spare a timer for it; `dac` (not yet implemented) is supported on the very few FCs that have a DAC pin broken out and unoccupied by other functions, it works by direct connection to the camera.
* `set camera_control_ref_voltage = 330` - voltage (in 10 mV steps) measured across your camera's floating `OSD` and `GND` pins, usually 3V3, but not guaranteed, e.g. my RunCam Sky has 3V4, and some cameras have reportedly have as low as 3V1.
* `set camera_control_key_delay = 180` - the duration of each key press (in ms presence at the `camera_control` pin, after consulting with RunCam it was set to 180 ms to accommodate most cameras, while some of them accept as low as 125 ms.
* `set camera_control_internal_resistance = 470` - the internal resistance (in 100 Ω steps) of your camera, most HS1177 derivatives have 47 kΩ, but that's not guaranteed. You'll have to derive this value for your camera in case the default one doesn't work.

## Modes of operation
### Hardware PWM
Requires a 150-600 Ω resistor inline from your FC `PIN` to Camera `OSD`. Additional `GND` connection is advised.

Assumes that the camera (or FC) has sufficient capacitance at OSD pin (look at `Tips for hardware designers` section). If you camera is not working, try adding a capacitor between camera `OSD` and `GND`.
#### How do I select a suitable `PIN`?
@todo

### Software PWM
Requires a 150-600 Ω resistor and a 1-10 µF capacitor, forming an RC-filter. Resistor goes from `PIN` to `OSD`, while the capacitor should be connected between `OSD` and `GND`.
@todo draw a schematic

### DAC
Requires no additional components, wire your `PIN` to `OSD`, preferably with a `GND` connection as well.

## Accessibility
* RC Stick Commands
* `MSP_CAMERA_CONTROL`
* MSP over Telemetry, e.g. Lua scripts for FrSky radios ([Pull Request #23](https://github.com/betaflight/betaflight-tx-lua-scripts/pull/23))

## Stick Commands
![Camera Control Stick Commands](https://raw.githubusercontent.com/wiki/betaflight/betaflight/images/camera-control-stick-commands.png)

Many cameras have an OSD name menu (example RunCam Eagle Pro 2) this OSD can be accessed via camera contol with the stick commands throttle down, pitch up.

## Troubleshooting
First of all, make sure `resource list` CLI command lists `camera_control` as allocated.

If your camera doesn't work, chances are you have to adjust values for key delay, reference voltage or internal resistance.
Defaults work fine for most HS1177-derivatives, if you have issues, try changing the key delay value first.

### Key Delay
If you have multi-click occurrences, decrease the delay by 10-15%, if your clicks don't register consistently - try increasing it instead.
Foxeer Arrow v3 is known to work best with delay of 125 ms.

### Reference Voltage
Just measure the voltage between OSD and GND pins on the camera while it is powered.

### Internal Resistance
Foxeer Monster, RunCam Micro Sparrow (and probably other cameras I haven't yet discovered) are known to require `camera_control_internal_resistance = 99`.

To derive this value for an unknown camera, you'll need a multimeter and a suitable joystick. First set your multimeter to resistance measurement mode and measure the resistance of your OSD joystick while pressing individuals keys, write the values down somewhere, you'll need them later.

Now you'll have to power your camera, connect the OSD joystick to it and find a way to measure the OSD-pin voltage while pressing individual keys on joystick, measuring voltages for all keys is not necessary, one or two is sufficient. Also measure the `Reference Voltage` if you haven't yet.

You'll end up with a set of resistance and voltage values for each key. To find internal resistance value, plug in your values into this formula:
`Rin = Rkey * (Vref / Vkey - 1) where key in (enter, left, up, right, down)`

Calculate this value for a few keys as a sanity check, the resulting values should be roughly the same. The `Rin` you've arrived at is your `camera_control_internal_resistance` value, keep in mind it is measured in 100 Ω steps, hence divide your value by 100.

## Example FC configurations

### Emax Magnum stack + Foxeer Arrow v3  
600 Ω resistor in line  
Camera control Mapped to Motor 6 output 
set camera_control_key_delay = 125

Use following CLI Commands
```
resource MOTOR 6 none
resource CAMERA_CONTROL A08
set camera_control_key_delay = 125
save
```

### Foxeer Arrow Mini, Arrow Micro, Monster
These cameras have insufficient to no capacitance on OSD pin, as a result you'll have to add at least 0.1 µF to get Hardware PWM working. Foxeer may release a new revision which will address this problem.

1206 capacitors work perfectly while soldering directly to JST connector backside, here's an illustration of a 100 nF 1206 capacitor soldered onto an Arrow Micro v2, but it also works for Arrow Mini as they share PCB design:
![Foxeer Arrow Micro v2 modified for camera control](https://raw.githubusercontent.com/wiki/betaflight/betaflight/images/camera-control-foxeer-arrow-micro-mod.png)

A similar trick works for Foxeer Monster.
@todo add a photo

### Foxeer Predator
**UP&DOWN do not work**, hopefully future batches will have the firmware flaw addressed.
`set camera_control_internal_resistance = 99`

Predator expects a really low value for DOWN, while the FC can't output lower than `3.3 * R / (R + 9.9 kΩ)`.
Use a resistor of 75-150 Ω to make it work.

Reference voltage for my samples seems to be on the higher side, staying at 3.43 V for a 3S battery, which leads to the problem with UP.
This camera seems to register UP key with a logic like:

```
if voltage is V_up:
  time_start = now
  while (voltage is not V_max): wait
  time_end = now
  total_time = time_end - time_start

  if total_time is short: UP
  else: LONG_UP
```

It results in the camera getting stuck in an infinite loop, **even the OSD timer stops counting**, as the FC can never output `Vmax ~= 3.4`.

### Caddx.us Micro Turbo S1
Works perfectly on the default settings, no capacitor required.
```
set camera_control_internal_resistance = 470
set camera_control_ref_voltage = 325
```

### Caddx.us Micro Turbo F1
This camera has usually low reference voltage, at least no sample, running up a 4S battery it showed 2.77 V, works perfectly otherwise. No capacitor required.
```
set camera_control_internal_resistance = 470
set camera_control_ref_voltage = 277
```

**Please follow manufacturers guidelines from the bottom of this page to avoid such problems!**

## Frequently asked questions
>Which resistor value is best?

Any resistor from the 150-600 Ω will do, with preference to lower values.


>Which resistor power rating is required?

Irrelevant


>RCG Discussion Thread

https://www.rcgroups.com/forums/showthread.php?2961216-Betaflight-Camera-Control-Compatibility-Reports

Read this thread for help on compatibility.


## Tips for hardware designers
If you're designing a flight controller, select an MCU pin which you'll be able to provide a dedicated timer for.
To add your camera control pin to default target resources assignment, use the following preprocessor macro in `target.h` file:
```
#define CAMERA_CONTROL_PIN pin_name
```
Use 150-220 Ω resistor inline between designated MCU pin and the solder pad.
Consider adding some additional capacitance to support the widest variety of cameras, a 100 nF ceramic SMD capacitor would be sufficient, anything in the range of 100-500 nF will increase user experience greatly.

If you're designing a camera, the same advice about capacitance applies. It provides debouncing for the physical joystick and simplifies FC configuration for the end user.
Having control over camera firmware, make it less restrictive in terms of _key_ voltage levels with some 5-10% of margin from both sides.
