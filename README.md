# 3D-Printer-GCode-Macros
I have collected this set of helpful commands and scripts for use with a marlin-based 3D printer.  They may work with other firmware as well.

> **_NOTES:_** 
> <ol>
>   <li>I wrote these scripts intending to save them to an Octoprint macro plugin. I added M117 lines to the longer one to update the printer display with the current script step. If you are typing the commands manually, please skip the M117 commands.</li>
>   <li>If your firmware has PRINTJOB_TIMER_AUTOSTART enabled make sure to include the M77 command after any macros with moves.</li>
> </ol> 

## Basic Operations

### Home
```
G28                                                 ; Home all axes
```

### Store Settings 
```
M500                                                ; Save settings
M117 Settings Stored
```

### Quick Cool Down 
```
M117 Cool Down: Start
M106 P0 S255                                        ; Set part fan to 100%
M140 S0                                             ; Cool Down
G4 S360                                             ; Wait for 6 minutes
M104 S0
M107 P0                                             ; Fan Off
M117 Cool Down: Done
```

## Z-Probe

### Z-Probe Repeatability Test
```
M48 P10 X154.0 Y157.5 V2 L4                         ; Probe Repeatability Test (change X and Y for your printer)
```

## Prep for Z Adjust
```
M117 Z Adjust: Start
M117 Z Adjust: Warm Up Bed and Hotend
M104 S215                                           ; set extruder temp
M190 S60                                            ; wait for bed temp
M117 Z Adjust: Allow temps to stabilize
G4 S60                                              ; Allow temps to stabilize
M117 Z Adjust: Home all axes
G28                                                 ; Home all axes
M117 Z Adjust: Align Z steppers
G34 I10 T0.01                                       ; Z Steppers Auto-Alignment
M117 Z Adjust: Enable UBL
G29 A L0 F0 J2                                      ; Activate the UBL System from slot 0 mesh with 0 fade height and 4 point tilt
M117 Z Adjust: Move to the calibration point
G90                                                 ; use absolute coordinates
G0 X154.0 Y157.5 Z50 F5000                          ; move hotend to bed center (change X and Y for your printer)
G0 Z0.2                                             ; Lower Z-axis to the feeler reference height
M77                                                 ; Stop the print job timer
M117 Z Adjust: Ready for z-offset adjustment
```

### Z Auto-Alignment
This will only work if you have indipendent z stepper motors and have enabled G34 in the firmware.
```
M117 Z Auto-Align: Start
M117 Z Auto-Align: Warm Up Bed and Hotend
M104 S215                                           ; set extruder temp
M190 S60                                            ; wait for bed temp
M117 Z Auto-Align: Allow temps to stabilize
G4 S60                                              ; Allow temps to stabilize
M117 Z Auto-Align: Home all axes
G28                                                 ; Home all axes
M117 Z Auto-Align: Begin Alignment
G34 I10 T0.01                                       ; Z Steppers Auto-Alignment
M500                                                ; Save settings
M105 S0                                             ; Cool Down
M140 S0
G90                                                 ; use absolute coordinates
G0 X154.0 Y157.5 Z50 F5000                          ; move hotend to bed center (change X and Y for your printer)
M77                                                 ; Stop the print job timer
M117 Z Auto-Align: Done
```

## Thermals

### PID Tune End
```
M117 PID Tune End: Start
M301                                                ; Report Current Values
M117 PID Tune End: Set Part Fan to 50%
M106 P0 S128                                        ; Set part fan to 50%
M117 PID Tune End: Auto-tune 215C for 10 cycles
M303 C10 S215 E0 U1                                 ; Auto-tune 215C for 10 cycles
M117 PID Tune End: Save settings
M500                                                ; Save settings
M117 PID Tune End: Fan Off
M107 P0                                             ; Fan Off
M117 PID Tune End: Done
```

### PID Tune Bed
```
M117 PID Tune Bed: Start
M304                                                ; Report Current Values
M117 PID Tune Bed: Set Part Fan to 50%
M106 P0 S128                                        ; Set part fan to 50%
M117 PID Tune Bed: Auto-tune 60C for 10 cycles
M303 C10 S60 E-1 U1                                 ; Auto-tune 60C for 10 cycles
M117 PID Tune Bed: Save settings
M500                                                ; Save settings
M117 PID Tune Bed: Fan Off
M107 P0                                             ; Fan Off
M117 PID Tune Bed: Done
```

## Bed Leveling

### Unified Bed Leveling
#### Activate UBL
This is an example of how to activate UBL.  This can be modified to be used as part of your slicer start g-code.
```
M117 Activate UBL: Start
M117 Activate UBL: Warm Up Bed and Hotend
M109 S215                                           ; set extruder temp
M190 S60                                            ; wait for bed temp
M117 Activate UBL: Allow temps to stabilize
G4 S60                                              ; Allow temps to stabilize
M117 Activate UBL: Home all axes
G28                                                 ; Home all axes
M117 Activate UBL: from slot 0 with 4 point tilt
G29 A L0 F0 J2                                      ; Activate the UBL System from slot 0 mesh with 0 fade height and 4 point tilt
M105 S0                                             ; Cool Down
M140 S0
G90                                                 ; use absolute coordinates
G0 X154.0 Y157.5 Z50 F5000                          ; move hotend to bed center (change X and Y for your printer)
M77                                                 ; Stop the print job timer
M117 Activate UBL: Done
```

#### Heat & Level Bed S0
This will heat the bed and perform the leveling measurements and save the mesh in slot 0
```
M117 Bed Leveling: Start
M117 Bed Leveling: Warm Up Bed and Hotend
M109 S215                                           ; set extruder temp
M190 S60                                            ; wait for bed temp
M117 Bed Leveling: Allow temps to stabilize
G4 S60                                              ; Allow temps to stabilize
M117 Bed Leveling: Home all axes
G28                                                 ; Home all axes
M117 Bed Leveling: Begin Alignment
G34 I10 T0.01                                       ; Z Steppers Auto-Alignment
M117 Bed Leveling: Automated Probing
G29 P1                                              ; Do automated probing of the bed.
M117 Bed Leveling: Smart Fill
G29 P3                                              ; Smart Fill Repeat until all mesh points are filled in, Used to fill unreachable points.
M117 Bed Leveling: Save to Slot 0
G29 S0                                              ; Save UBL mesh points to slot 0 (EEPROM).
M117 Bed Leveling: Set Fade at 0.0 mm
G29 F 0.0                                           ; Set Fade Height for correction at 0.0 mm.
M117 Bed Leveling: Activate UBL
G29 A                                               ; Activate the UBL System.
M117 Bed Leveling: Save Settings
M400                                                ; Finish Moves
M500                                                ; Save current setup
M117 Bed Leveling: Turn Off Heaters
M105 S0                                             ; Cool Down
M140 S0
G90                                                 ; use absolute coordinates
G0 X154.0 Y157.5 Z50 F5000                          ; move hotend to bed center (change X and Y for your printer)
M77                                                 ; Stop the print job timer
M117 Bed Leveling: Done
```

## Copy Mesh S0 to S1
```
M117 Swap Mesh: Start
M117 Swap Mesh: Load Slot 0
G29 L0                                              ; Load UBL mesh points from slot 0 (EEPROM).
M117 Swap Mesh: Save Slot 1
G29 S1                                              ; Save UBL mesh points to slot 1 (EEPROM).
M117 Bed Leveling: Activate New Mesh
G29 A
M117 Bed Leveling: Save Settings
M500                                                ; Save current setup
M117 Mesh S0 and S1 have been swapped
```

## Swap Mesh S0 and S1
```
M117 Swap Mesh: Start
M117 Swap Mesh: Load Slot 0
G29 L0                                              ; Load UBL mesh points from slot 0 (EEPROM).
M117 Swap Mesh: Save Slot 2
G29 S2                                              ; Save UBL mesh points to slot 2 (EEPROM).
M117 Swap Mesh: Load Slot 1
G29 L1                                              ; Load UBL mesh points from slot 1 (EEPROM).
M117 Swap Mesh: Save Slot 0
G29 S0                                              ; Save UBL mesh points to slot 0 (EEPROM).
M117 Swap Mesh: Load Slot 2
G29 L2                                              ; Load UBL mesh points from slot 2 (EEPROM).
M117 Swap Mesh: Save Slot 1
G29 S1                                              ; Save UBL mesh points to slot 1 (EEPROM).
M117 Swap Mesh: Load Slot 2			
G29 L2                                              ; Load UBL mesh points from slot 2 (EEPROM).
M117 Swap Mesh: Zero Mesh Data
G29 P0                                              ; Zero UBL mesh points to slot 2 (EEPROM).
M117 Swap Mesh: Save Slot 2
G29 S2                                              ; Save UBL mesh points to slot 2 (EEPROM).
M117 Swap Mesh: Load Slot 0			
G29 L0                                              ; Load UBL mesh points from slot 0 (EEPROM).	
M117 Bed Leveling: Save Settings
M500                                                ; Save current setup											
M117 Bed Leveling: Activate New Mesh
G29 A
M117 Mesh S0 and S1 have been swapped
```
