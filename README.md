# Auto Z-Offset Calibration for the QIDI Q1 Pro

This is a plugin for Klipper that makes use of the QIDI Q1 Pro's bed sensors to automatically set the toolheads Z-Offset.

### Install
```
cd ~
git clone https://github.com/frap129/qidi_auto_z_offset
ln -s ~/qidi_auto_z_offset/auto_z_offset.py ~/klipper/klippy/extras/auto_z_offset.py
```

### Command Reference
**AUTO_Z_PROBE**: `AUTO_Z_PROBE` works like the normal PROBE command, using the bed sensors instead.
**AUTO_Z_CALIBRATE**: `AUTO_Z_CALIBRATE` uses the bed sensor as a Z endstop, then probes with the inductive probe. Averages the values reported by each to find the approximate offset.
### Config Reference
```
[auto_z_offset]
pin:
#   Pin connected to the Auto Z Offset output pin. This parameter is required.
z_offset: 0.0
#   Unused in actual code, but required because this is technically probe.
#probe_accel:
#   If set, limits the acceleration of the probing moves (in mm/sec^2).
#   A sudden large acceleration at the beginning of the probing move may
#   cause spurious probe triggering, especially if the hotend is heavy.
#   To prevent that, it may be necessary to reduce the acceleration of
#   the probing moves via this parameter.
#endstop_positon:
#   The z positon that triggering the bed sensors corresponds to.
#   default is -0.1
#probe_hop:
#   The amount to hop between probing with bed sensors and probing with probe.
#   default is 5.0, min is 4.0 to avoid triggering the probe early
#offset_samples:
#   The number of times to probe with bed sensors and inductive probe. Note,
#   this is not the same as `samples`. 
#   default is 3
prepare_gcode:
#   gcode script to run before probing with auto_z_offset. This is required, and an
#   example script is provided below.
#speed:
#samples:
#sample_retract_dist:
#samples_result:
#samples_tolerance:
#samples_tolerance_retries:
#activate_gcode:
#deactivate_gcode:
#deactivate_on_each_sample:
#   See the "probe" section for more information on the parameters above.
```

### Example Configuration from OpenQ1
This example config also includes the control pin for the bed sensors and the config for the inductive probe. Use them as shown for the best compatiblity.
```
[output_pin bed_sensor]
pin: !U_1:PA14
value:0

[probe]
pin:!gpio21
x_offset: 17.6
y_offset: 4.4
z_offset: 0.0
speed:10
samples: 3
samples_result: average
sample_retract_dist: 3.0
samples_tolerance: 0.05
samples_tolerance_retries: 5

[auto_z_offset]
pin: U_1:PC1
z_offset: 0.0
speed: 10
probe_accel: 50
samples: 3
samples_result: average
samples_tolerance: 0.05
samples_tolerance_retries: 5
prepare_gcode:
    SET_PIN PIN=bed_sensor VALUE=0
    G91
    {% set i = 4 %}
    {% for iteration in range(i|int) %}
        G1 Z1 F900
        G1 Z-1 F900
    {% endfor %}
    G90
    SET_PIN PIN=bed_sensor VALUE=1
```

