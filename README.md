# Probe_panel

Probe_panel is a simple probe add-in for the LinuxCNC Axis UI. Since it is not more than a PyVCP panel, a postgui halfile for the pins and a folder with some macros, it should work regardless of the LinuxCNC version.

### Installing Probe_panel

First copy the macro folder, Probe_panel.xml and Probe_postgui.hal to your configuration folder
Then add the following lines to the appropriate sections of your machine ini file:

##### [DISPLAY]
PYVCP = Probe_panel.xml

*Note: Probe_panel is designed as a tab of a side panel. If you already have a PyVCP side panel, simply copy all the lines between <!-- Beginning of probe/ccordinates tab --> and <!-- End of probe/ccordinates tab --> to your panel.*

##### [HAL]
POSTGUI_HALFILE = Probe_postgui.hal

*Note: LinuxCNC allows not more than one postgui hal file called from the ini file. If you already have a postgui hal file, you may copy all content from the Probe_postgui.hal to your postgui hal file.*


