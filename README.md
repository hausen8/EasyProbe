# Probe_panel

Probe_panel is a simple probe add-in for the LinuxCNC Axis UI. Since it is not more than a PyVCP panel, a postgui halfile and a folder with some macros, it should work regardless of the LinuxCNC version.

### 1) Install Probe_panel

Copy the following content to your machine configuration folder (typically under USERNAME/linuxcnc/configs/CONFIG_NAME):
- macros folder with all ngc files
- Probe_panel.xml
- Probe_postgui.hal

### 2) Edit your INI

Add the following lines to the appropriate sections of your machine ini file:

```
[DISPLAY]
# Load the Probe_panel as side panel or copy its content as a new tab to your existing side panel.
# See http://linuxcnc.org/docs/html/gui/pyvcp.html#_containers for details
PYVCP = Probe_panel.xml
```

*Note: Probe_panel is designed as a tab of a side panel. If you already have a PyVCP side panel, simply copy all the lines between the comments "Beginning of probe/coordinates tab" and "End of probe/coordinates tab" to your panel and add a name to the names tag.*

```
[RS274NGC]
SUBROUTINE_PATH = macros
FEATURES = 12
INI_VARS = 1
HAL_PIN_VARS = 1
```

*Note: SUBROUTINE_PATH ist the path to your probe macros folder. If you rename the folder or move it i.e. to nc_file/macros, you have to enter the right path here.*

```
[HAL]
POSTGUI_HALFILE = Probe_postgui.hal
```

*Note: LinuxCNC allows not more than one postgui hal file called from the ini file. If you already have a postgui hal file, you may copy all content from the Probe_postgui.hal to your postgui hal file.*

```
[HALUI]
MDI_COMMAND = G54
MDI_COMMAND = G55
MDI_COMMAND = G56
MDI_COMMAND = G57
MDI_COMMAND = G58
MDI_COMMAND = G59
MDI_COMMAND = G59.1
MDI_COMMAND = G59.2
MDI_COMMAND = G59.3
MDI_COMMAND = G10 L20 P0 X0
MDI_COMMAND = G10 L20 P0 Y0
MDI_COMMAND = G10 L20 P0 Z0
MDI_COMMAND = O <probe_x_plus>				CALL	[#<_ini[probe]PROBE_TOOL_NUMBER>][#<_ini[probe]MAX_XY_DISTANCE>][#<_ini[probe]XY_CLEARANCE>][#<_ini[probe]PROBE_SLOW_FR>][#<_ini[probe]PROBE_FAST_FR>][#<_ini[probe]CALIBRATION_OFFSET>]
MDI_COMMAND = O <probe_x_minus> 			CALL	[#<_ini[probe]PROBE_TOOL_NUMBER>][#<_ini[probe]MAX_XY_DISTANCE>][#<_ini[probe]XY_CLEARANCE>][#<_ini[probe]PROBE_SLOW_FR>][#<_ini[probe]PROBE_FAST_FR>][#<_ini[probe]CALIBRATION_OFFSET>]
MDI_COMMAND = O <probe_y_plus> 				CALL	[#<_ini[probe]PROBE_TOOL_NUMBER>][#<_ini[probe]MAX_XY_DISTANCE>][#<_ini[probe]XY_CLEARANCE>][#<_ini[probe]PROBE_SLOW_FR>][#<_ini[probe]PROBE_FAST_FR>][#<_ini[probe]CALIBRATION_OFFSET>]
MDI_COMMAND = O <probe_y_minus>				CALL	[#<_ini[probe]PROBE_TOOL_NUMBER>][#<_ini[probe]MAX_XY_DISTANCE>][#<_ini[probe]XY_CLEARANCE>][#<_ini[probe]PROBE_SLOW_FR>][#<_ini[probe]PROBE_FAST_FR>][#<_ini[probe]CALIBRATION_OFFSET>]
MDI_COMMAND = O <probe_z_minus>				CALL	[#<_ini[probe]PROBE_TOOL_NUMBER>][#<_ini[probe]MAX_Z_DISTANCE>][#<_ini[probe]Z_CLEARANCE>][#<_ini[probe]PROBE_SLOW_FR>][#<_ini[probe]PROBE_FAST_FR>]
MDI_COMMAND = O <probe_round_pocket_center_start>	CALL	[#<_ini[probe]PROBE_TOOL_NUMBER>][#<_ini[probe]MAX_Z_DISTANCE>][#<_ini[probe]MAX_XY_DISTANCE>][#<_ini[probe]XY_CLEARANCE>][#<_ini[probe]Z_CLEARANCE>][#<_ini[probe]FAST_VEL_IN_BETWEEN>] [#<_ini[probe]PROBE_SLOW_FR>][#<_ini[probe]PROBE_FAST_FR>][#<_ini[probe]CALIBRATION_OFFSET>]
MDI_COMMAND = O <probe_calibration>			CALL
```

*Note: MDI commands are called from the hal file in the order they are listed in the ini file. So make sure that these MDI commands are either the first or only MDI commands or change the numbers in Probe_postgui.hal. Please note that numbers start at 00.*

```
[PROBE]
PROBE_TOOL_NUMBER = 99
TOOL_NUMBER_MIN = 95
TOOL_NUMBER_MAX = 99
MAX_XY_DISTANCE = 3
XY_CLEARANCE = 1
CALIBRATION_OFFSET = 0
MAX_Z_DISTANCE = 3
Z_CLEARANCE = 1
PROBE_SLOW_FR = 10
PROBE_FAST_FR = 50
FAST_VEL_IN_BETWEEN = 300
```

### 3) Edit your POSTGUI HAL

Open your Probe_postgui.hal or the postgui hal file where you added the content of Probe_postgui.hal and find the following lines:

```
net    probe_in    PHYSICAL_PROBE_PIN    =>
net    probe_in    motion.probe-input    <=
net    probe-in    pyvcp.probe_led       <=
```

*"PHYSICAL_PROBE_PIN" must be exchanged to the physical pin of your interface card where your probe tool is connected to. In case of a Mesa 7i76e it should be something like hm2_7i76e.0.7i76.0.0.input-NN, where NN is the number of the input.*


