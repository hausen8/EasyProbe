# Probe_panel

Probe_panel is a simple probe add-in for the LinuxCNC Axis UI. Since it is not more than a PyVCP panel, a postgui halfile for the pins and a folder with some macros, it should work regardless of the LinuxCNC version.

### 1) Copy files

Copy the following content to your machine configuration folder (typically under USERNAME/Linuxcnc/config/machine):
- sub folder with all ngc files
- Probe_panel.xml
- Probe_postgui.hal

### 2) Edit your INI

Add the following lines to the appropriate sections of your machine ini file:

```
[DISPLAY]
PYVCP = Probe_panel.xml
```

*Note: Probe_panel is designed as a tab of a side panel. If you already have a PyVCP side panel, simply copy all the lines between the comments "Beginning of probe/ccordinates tab" and "End of probe/ccordinates tab" to your panel and add a Name to the names tag.*

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
MDI_COMMAND = 
...
```

*Note: MDI commands are called from the hal file in the order they are listed in the ini file. So make sure that these MDI commands are either the first or only MDI commands or change the numbers in Probe_postgui.hal. Please note that numbers start at 00.*

### 3) Edit your HAL

Open your Probe_postgui.hal or the postgui hal file where you added the content of Probe_postgui.hal and find the following lines:

```
net    probe_in    PHYSICAL_PROBE_PIN    =>
net    probe_in    motion.probe-input    <=
net    probe-in    pyvcp.probe_led       <=
```

*"PHYSICAL_PROBE_PIN" must be exchanged to the physical pin where your probe tool is connected to. In case of a Mesa 7i76e it should be something like hm2_7i76e.0.7i76.0.0.input-NN, where NN is the number of the input.*


