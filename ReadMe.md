# Ender 3 S1 PRO - Klipper - Tuarus V5 Cooler - 4020 Cooler
This Ender 3 S1 Pro setup has 3 4020 fans, 2 for the hot end, and 1 for the print head.

Tuarus Cooler: https://www.printables.com/model/511962-taurus-v5-cooling-duct-for-creality-sprite-extrude

Hot end duct: https://cults3d.com/en/3d-model/tool/ender-3-s1-pro-4020-fan-cr-touch-no-y-offset


# Obico Server Setup Info:

### How to change ip address and External ip address: 
```cd ~/Downloads/obico-server ```

```nano ~/Downloads/obico-server/backend/config/settings.py```


Then find:

```ALLOWED_HOSTS = ['localhost', '127.0.0.1', '192.168.86.45', '<External IP>']```

and add/change the ports (internal and external) 

Stay CD'd into ```~/Downloads/obico-server ``` 

to then restart the docker stuff: 

```docker-compose down && docker-compose up -d```

### Obico Client Setup:
To update the server address on the raspberry pi: 
Check the file called: moonraker-obico.cfg
and change the address there:
```
[server]
url = http://192.168.86.45:3334
```

also change the client IP address as well 

```
[moonraker]
host = 192.168.86.20
port = 7125
```


## Adjust the Camera settings:
File: ```crowsnest.conf```

Adjust the resolution here:

```
[cam 1]
mode: ustreamer                         # ustreamer - Provides mjpg and snapshots. (All devices)
                                        # camera-streamer - Provides webrtc, mjpg and snapshots. (rpi + Raspi OS based only)
enable_rtsp: false                      # If camera-streamer is used, this enables also usage of an rtsp server
rtsp_port: 8554                         # Set different ports for each device!
port: 8080                              # HTTP/MJPG Stream/Snapshot Port
device: /dev/video0                     # See Log for available ...
resolution:  1280x720                         # widthxheight format
max_fps: 15                             # If Hardware Supports this it will be forced, otherwise ignored/coerced.
#custom_flags:                          # You can run the Stream Services with custom flags.
#v4l2ctl:                               # Add v4l2-ctl parameters to setup your camera, see Log what your cam is capable of.
```


## Start G-Code:

```
; Ender 3 S1 Pro Start G-code
; Disable power loss recovery (Klipper does not support this, so it is removed)
; M413 S0

; Reset Extruder
G92 E0

; Start heating the bed and hotend
M140 S{material_bed_temperature_layer_0} ; Set bed temperature
M104 S{material_print_temperature_layer_0} ; Set hotend temperature

; Home all axes
G28

; Move Z Axis up a little to prevent scratching of Heat Bed
G1 Z10.0 F3000

; Safe move to the center of the build volume
G1 X110 Y110 F3000 ; Move to the center of the build volume for consistent priming

; Wait for bed to reach target temp
M190 S{material_bed_temperature_layer_0}

; Wait for hotend to reach target temp
M109 S{material_print_temperature_layer_0}

; Prime the nozzle by drawing lines on the bed
G1 X5 Y20 Z0.3 F5000.0 ; Move to start position (near front of the bed)
G1 X5 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
G1 X5.4 Y200.0 Z0.3 F5000.0 ; Move to the side a little
G1 X5.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line

; Reset Extruder
G92 E0

; Move Z Axis up a little to prevent scratching of Heat Bed
G1 Z2.0 F3000

; Move over to prevent blob squish
G1 X10 Y20 Z0.3 F5000.0
```


End G-Code:

```
; Ender 3 S1 Pro End G-code

; Relative positioning for retraction and Z movement
G91
G1 E-2 F2700 ; Retract filament
G1 E-2 Z0.2 F2400 ; Retract and raise Z slightly

; Wipe the nozzle and raise Z
G1 X5 Y5 F3000 ; Move away from the print
G1 Z10 ; Raise Z more to clear the print

; Switch back to absolute positioning
G90

; Present the print at the front
G1 X0 Y220 F3000 ; Move the print to the front (Y = 220, adjusted to be within range)

; Turn off cooling fan, hotend, and bed
M106 S0 ; Turn off fan
M104 S0 ; Turn off hotend
M140 S0 ; Turn off bed

; Disable all steppers except Z to prevent sagging
M84 X Y E ; Disable X, Y, and extruder steppers
```
