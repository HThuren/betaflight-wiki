### INFORMATION COMMANDS
* "VERSION" - Shows the current firmware loaded and the last github code commit [ie: "(9f67a584b)"] for the specific build of the firmware.

* "STATUS" - Shows various information about the quad setup such as ROM space available for firmware, gyro type, detected voltage, etc...

* "TASKS" - Shows the active tasks running and their CPU utilization.  Make sure the "gyro/PID tasks rate/hz is running at the specified rate.

* "DSHOT_TELEMETRY_INFO" - Shows the Dshot RPM bi-directional telemetry packet success for each ESC. (4.1+)

* "RC_SMOOTHING_INFO" - Shows the detected RX frame rate.  This only works if the "Filter" RC signal smoothing type is selected in the Receiver tab.  The radio and RX need to be connected and powered up for the detected frame rate data to be valid.

DSHOT_RPM_ERRORS:
* [0] = Motor #1: the per-motor invalid packet percentages in hundredths of a percent (so 123 is 1.23%)
* [1] = Motor #2:   "
* [2] = Motor #3:   "
* [3] = Motor #4:   "

SMARTAUDIO:
* [0] = SmartAudio Version * 100 + Device Mode
* [1] = Device Channel
* [2] = Device Frequency
* [3] = Device Power

CYCLETIME:
* [0] = Cycle Time (uS)
* [1] = Average System Load Percent (%)
* [2] = Motor Update time (uS)
* [3] = Motor Deviation (uS)

PIDLOOP:
* [0] = 
* [1] = 

### GYRO SIGNAL (https://youtu.be/A09sprstYqI)
GYRO_RAW: (does have gyro's internal Digital Lowpass Filter (DLPF) applied)

For use in seeing the unscaled gyro signal into the firmware for use in stack overflow detection (ICM gyros).
* [0] = roll: gyro signal input to firmware **UN**scaled
* [1] = pitch: gyro signal input to firmware **UN**scaled
* [2] = yaw: gyro signal input to firmware **UN**scaled
* [3] = [empty]

GYRO_SCALED: (does have gyro's internal Digital Lowpass Filter (DLPF) applied)

For use in seeing the "raw" noise from the gyro into the firmware for filter tuning.
* [0] = roll: gyro signal input to firmware **scaled** into deg/sec
* [1] = pitch: gyro signal input to firmware **scaled** into deg/sec
* [2] = yaw: gyro signal input to firmware **scaled** into deg/sec
* [3] = [empty]

GYRO_FILTERED: (SAME AS GYRO TRACES RECORDED BY DEFAULT)
* [0] = roll: filtered gyro trace
* [1] = pitch: filtered gyro trace
* [2] = yaw: filtered gyro trace
* [3] = [empty]


### DYNAMIC NOTCH AND LOWPASS FILTERS (https://youtu.be/__vyp60cU_8)
DYN_LPF:
* [0] = roll: raw gyro data (scaled)
* [1] = roll: notch center frequency
* [2] = roll: lowpass filter cutoff frequency
* [3] = roll: pre-dyn notch (post lowpass filters)gyro data

FFT_FREQ:
* [0] = roll: notch center frequency
* [1] = pitch: notch center frequency
* [2] = roll: pre-dyn notch (post lowpass filters) gyro data
* [3] = roll: raw gyro data (scaled)

FFT:
* [0] = pre-dyn notch gyro roll data
* [1] = gyro roll data only filtered with the dynamic notch
* [2] = resampled and bandpass filtered data used for FFT
* [3] = weighted center Index of the FFT-bins * 100. This is used to calculate the center frequency

FFT_TIME:
* [0] = currently active calculation step
* [1] = duration of this step
* [2] = duration of additional steps
* [3] = [empty]

RPM_FILTER:
* [0] = motor #1 RPM Notch center frequency (where peak motor noise is anticipated)
* [1] = motor #2 RPM Notch center frequency
* [2] = motor #3 RPM Notch center frequency
* [3] = motor #4 RPM Notch center frequency

DSHOT_RPM_TELEMETRY: (eRPM x # motor poles = RPM)
* [0] = motor #1 eRPM
* [1] = motor #2 eRPM
* [2] = motor #3 eRPM
* [3] = motor #4 eRPM

### RC COMMAND SMOOTHING (https://youtu.be/M50fKpvFjT8)

RC_INTERPOLATION:
* [0] = raw un-smoothed rc channel data [roll]
* [1] = current RX frame rate
* [2] = interpolation step count
* [3] = rc setpoint [roll]

RC_SMOOTHING:
* [0] = raw un-smoothed rc channel data
* [1] = raw un-smoothed setpoint derivative
* [2] = filtered setpoint derivative before applied to setpoint weight
* [3] = the current calculated average (shows the current "locked" rate used to set the filters)

RC_SMOOTHING_RATE:
* [0] = log each RX frame interval (shows the delay from the previous frame in microsecond)
* [1] = log the training step count
* [2] = the current calculated average (shows the current "locked" rate used to set the filters)
* [3] = indicates whether guard time is active

### FLIGHT DYNAMICS
AC_ERROR (Absolute Control Error):
* [0] = roll: axis error * 10
* [1] = pitch: axis error * 10
* [2] = yaw: axis error * 10
* [3] = [none]

AC_CORRECTION (AC = Absolute Control):
* [0] = roll: axis AC correction * 10
* [1] = pitch: axis AC correction * 10
* [2] = yaw: axis AC correction * 10
* [3] = [none]

FF_THUMB (Absolute Control Correction):
* [0] = roll normal FF
* [1] = roll FF after stick limit
* [2] = FF after max deflection
* [3] = projected max rate due to stick extrapolation

ITERM_RELAX: (https://youtu.be/QfiGTG5LfCk)
* [0] = highpass filter to detect large setpoint changes
* [1] = relax factor (percent, only used in `SETPOINT` mode)
* [2] = relaxed I-term Error
* [3] = absolute control axis error [roll]

D_MIN:
* [0] = gyro factor (percent, scaled by `d_min_gain`)
* [1] = setpoint factor (percent, scaled by `d_min_advance`). The larger of _gyro_ and _setpoint_ factors takes effect.
* [2] = roll: active D-term gain
* [3] = pitch: active D-term gain

### SENSOR FUSION GYRO BOARDS:
DUAL_GYRO_RAW:
* [0] = roll: RAW gyro #1 data (NOT scaled to Deg/sec)
* [1] = pitch: RAW gyro #1 data (NOT scaled to Deg/sec)
* [2] = roll: RAW gyro #2 data (NOT scaled to Deg/sec)
* [3] = pitch: RAW gyro #2 data (NOT scaled to Deg/sec)

DUAL_GYRO_SCALED:
* [0] = roll: RAW SCALED gyro #1 data (scaled to Deg/sec)
* [1] = pitch: RAW SCALED gyro #1 data (scaled to Deg/sec)
* [2] = roll: RAW SCALED gyro #2 data (scaled to Deg/sec)
* [3] = pitch: RAW SCALED gyro #2 data (scaled to Deg/sec)

DUAL_GYRO_DIFF:
* [0] = roll: gyro #1 filter – gyro #2 filtered
* [1] = pitch: gyro #1 filter – gyro #2 filtered
* [2] = yaw: gyro #1 filter – gyro #2 filtered
* [3] = [empty]

DUAL_GYRO_COMBINED:  (programmer useful only)
* [0] = [empty]
* [1] = roll: filtered gyro (same as “gyro” trace)
* [2] = pitch: filtered gyro (same as “gyro” trace)
* [3] = [empty]


