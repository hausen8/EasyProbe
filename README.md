# [easy]probe

[easy]probe is a simple probe add-in for the LinuxCNC Axis UI. Since it is not more than a PyVCP panel, two hal files and a folder with some macros, it should work regardless of your LinuxCNC version.

### 1) Copy files

Copy the following content to your machine configuration folder (typically under USERNAME/linuxcnc/configs/CONFIG_NAME):

```
/macros
Probe_panel.xml
Probe_postgui.hal
Probe_preload.hal
```

### 2) Edit your INI

Add the following lines to the appropriate sections of your machine ini file:

```
[DISPLAY]
PYVCP = Probe_panel.xml
```

*Note: [easy]probe is designed as a tab of a side panel. If you already have a PyVCP side panel, simply copy all the lines between the comments "Beginning of probe tab" and "End of probe tab" to your panel and add a name to the names tag.*

```
[RS274NGC]
SUBROUTINE_PATH = macros
ON_ABORT_COMMAND = O <on_abort> call
FEATURES = 12
RETAIN_G43 = 0
INI_VARS = 1
HAL_PIN_VARS = 1
```

*Note: SUBROUTINE_PATH ist the path to your probe macros folder. If you rename the folder or move it i.e. to nc_files/macros, you have to enter the right path here.*

```
[HAL]
TWOPASS = on
HALFILE = Probe_preload.hal
POSTGUI_HALFILE = Probe_postgui.hal
```

*Note: When TWOPASS is activated, realtime components can not only be loaded in the main hal file but in any other hal file, too. This allows us to offer a hal file with all required realtime components for Probe_panel so there is no need for you to edit your machine hal file.*

*Note: LinuxCNC allows not more than one postgui hal file called from the ini file. If you already have a postgui hal file, copy all content from the Probe_postgui.hal to your postgui.hal.*

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
MDI_COMMAND = O <probe_toolchange>  CALL [1]
MDI_COMMAND = O <probe_toolchange>  CALL [2]
MDI_COMMAND = O <probe_toolchange>  CALL [3]
MDI_COMMAND = O <probe_x_plus>      CALL
MDI_COMMAND = O <probe_x_minus>     CALL
MDI_COMMAND = O <probe_y_plus>      CALL
MDI_COMMAND = O <probe_y_minus>     CALL
MDI_COMMAND = O <probe_z_minus>     CALL	
MDI_COMMAND = O <probe_pocket>      CALL	
MDI_COMMAND = O <probe_calibration> CALL
```

*Note: MDI commands are called from the hal file in the order they are listed in the ini file. So make sure that these MDI commands are either the first or only MDI commands or change the numbers in Probe_postgui.hal. Keep in mind that numbers start at 00.*

```
[PROBE]
# Define up to three tool numbers of different probes in your tool table. Set the unused numbers to zero
TOOL_NUMBER_1 = 99
TOOL_NUMBER_2 = 0
TOOL_NUMBER_3 = 0

# Maximum safety travel of your probe in X/Y direction. Value is given by the manufacturer. If in doubt, set it to ~3 mm.
MAX_XY_DISTANCE = 3

# Distance the probe will move back after fast probe. Recommended values between 0.5 and 2 mm.
XY_CLEARANCE = 0.5

# Maximum safety travel of your probe in z direction. Value is given by the manufacturer. If in doubt, set it to ~2 mm.
MAX_Z_DISTANCE = 2

# Distance the probe will move back after fast probe. Recommended values between 0.5 and 2 mm.
Z_CLEARANCE = 0.5

# Fast probe velocity. Be aware to not set it too high, otherwise you will not have enough time in case of a malefunction. Recommended values between 50 and 500 mm per min.
VEL_FAST = 200

# Slow probe velocity. This value is important for the overall accuracy. Recommended values between 10 and 50 mm per min.
VEL_SLOW = 20

# Fast forward velocity in between pocket probe movements without actual probing.
VEL_FF = 2000

# Additional probetrips will lead to a better calibration. Values between 0 and 5.
ADD_PROBETRIPS = 2

# For development, debug messages can be turned on
DEV_MSG = 0

# Time delay in ms for debouncing probe switch. Try to keep the value as low as possible. Recommended values between 2 and 20 ms.
DEBOUNCE_TIME = 10
```

### 3) Edit your POSTGUI HAL

Open your Probe_postgui.hal or the postgui hal file where you added the content of Probe_postgui.hal and find the following lines:

```
net   probe_raw       dbounce_probe.in      <=    #PHYSICAL_PROBE_PIN
net   probe_signal    dbounce_probe.out     =>
net   probe_signal    motion.probe-input    <=
net   probe-signal    pyvcp.probe_led       <=
```

#PHYSICAL_PROBE_PIN must be exchanged to the physical pin of your interface card where your probe tool is connected to. In case of a Mesa 7i76e it should be something like hm2_7i76e.0.7i76.0.0.input-NN, where NN is the number of the input.

### 4) Edit your TOOL TABLE

Open LinuxCNC -> File -> Tooledit or open your tool.tbl file in any text editor and add an entry for each probe tool. You have to enter at least the tool number, pocket number and tip diameter. The sample below shows a tool with tool number 99, pocket number 99 and a 2 mm diameter.

```
T99 P99 D2 ;
```

### 5) Fine tune your configuration

If you for example want to automatically activate a wireless probe tool, there are 4 HAL pins that you can use. While the first 3 only switch to high when the appropriate probe tool is selected, the 4th one switches to high when any of these probe tools is selected.

```
probetool-1-on
probetool-2-on
probetool-3-on
probetool-is-on
```

### HISTORY

- **v1.2**: Minor changes in macros.
- **v1.1**: Removed function to disable all buttons when a program is running due to a problem with pushed buttons remaining in high state after the program started. Added a spindle inhibit function while a probe tool is selected with M6. Some minor changes in macros. Read changelog file in macro folder for more information.
- **v1.0**: Initial release

