# This file is meant for the developer for reference only and only for viewing in the code editor, not a rendering preview. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

###### #####################################
###### All Global "list" objects in KWGT in editable format
###### #####################################


###### readme
```
\"Thanks for downloading HA Device Control Widget! Setup:\n
1. Use following settings to configure the UI for the style you want\n
2. Once UI is configured, read the setup instructions by clicking on the button in the widget.\n
Please note you'll need to have Tasker installed to facilitate communication to HA\n\n

The below global settings are to configure the interface, while HA communication is set up on each icon.\n
Box 1 - Smaller box, usually containing main device controls like power\n
Box 2 - Larger box, usually containing device controls such as volume or brightness\n\n

Created by Corey Welsh to fill the need of universal Home Assistant Android widget\"
```


###### `deviceicons`
```
DATA##"Select the icon that best matches your device. The number next to your device type is how many icons of that category are available",
1##Icon 1,
2##Icon 2,
3##Icon 3,
4##Icon 4,
5##Icon 5,
6##Icon 6,
0##Custom Icon from pack
```

###### ``
```
DATA##\"Select how many devices to control with this widget\",
1##1 Device,
2##2 Devices,
3##3 Devices
```

###### `deviceiconshrink`
```
DATA##\"Select Shrinking options for the Device Icon. Shrinking will allow more space for icons to fit. You can also remove the space that Device icon takes up on the widget to allow the icon to stay while making more room for icons\",
0##Default size,
2##Default Size & Auto-Shrink For Space,
1##Manually Shrink For Space
```

###### `deviceiconrotation` 
DATA##\"Select the rotation of the Device Icon on your Widget\",
NONE##0 Degrees,
DEG90##90 Degrees,
DEG180##180 Degrees,
DEG270##180 Degrees,
FLIP_X##Flip Vertically,
FLIP_Y##Flip Horizontally
```
```

###### `core/deviceiconposition` > Set by template file now
```
DATA##\"Select the location of the Device Icon on your Widget. \",
1##Default Location,
2##Inside Box 1 (ONLY for templates not using Box 1)
3##Hidden,
```

###### `core/box1dir2` > `core/box1vis`
Changed to be 0 = Remove, 1 = Show

**V1**
```
DATA##\"Customize Box 1.\n\nSelect how many columns to display in Box 1, or \"Custom\" to enter the percentage of the widget that Box 1 fills (entered in the next list item).\",
0##Remove (Single Box Mode),
1##1 Column (Auto-Center),
2##2 Column (Auto-Center),
3##Custom Size (Below)
```

###### `box2align`
```
DATA##\"Select alignment of Box 2 content. Use for:\n- Templates without icons (like komponents)\n- Single box mode\",
0##Beside Box 1,
1##Centered
```

###### `boxdir1`
```
DATA##\"Customize Box 1.\n\nSelect how many rows (horizontal widget) or columns (vertical widget) to display in Box 1.\" ,
1##1 Row,
2##2 Rows
```

###### `appiconvis`
```
DATA##\"Set visbility of App Icons. Hiding these when using one row will make the device icon to fit the widget height.\",
0##Show App Icons,
1##Hide App Icons
```

###### `entitynote`

`"ENTITY LIST\n\nIf you're only controlling one device, enter it in "entity1". If you're controlling more than one, enter them in the order they appear in the template. (1st device is "entity1", 2nd is "entity2", etc)"`



###### #####################################
###### Template-specfic Global Lists
###### #####################################

###### `templateselect`
```
DATA##"Select the template you'd like to use for the device selected above",
1##Regular,
2##(2) 1 Device per row,
3##(3) Pages, 1 per device,
4##(4) Pages, 1 per device
5##Single Row,
6##Device Icon > Box 1,
0##Custom Template
```

**V2** - Pre-2025
```
DATA##"Select the template you'd like to use for the device selected above",
1##Template 1,
2##Template 2,
3##Template 3,
4##Template 4,
5##Template 5,
6##Template 6,
0##Custom Template
```

**V1**
```
DATA##"Select the template you'd like to use for the device selected above",
1##1-2 - Toggle On/Off or Open/Closed,
2##Level Up/Down,
3##Template 3,
4##Template 4,
5##2 Dev - Toggle On/Off,
6##Template 6,
0##Custom Template
```


###### `deviceicons`
```
DATA##"Select the icon that best matches your device.",
1##Icon 1,
2##Icon 2,
3##Icon 3,
4##Icon 4,
5##Icon 5,
6##Icon 6,
0##Custom Icon from pack
```

###### `devicetype`
```
DATA##"Select the type of device you'd like to control",
light##Lights,
climate##Climate,
tv##Television,
sound##Sound System,
security##Security,
lock##Doors and Locks,
power##Charging and Battery,
remote##Remote control,
appliance##Home Appliances,
home##Home Features?,
electronics##Personal Electronics,
maintence##Home Maintence,
other##Others
```

**V1**
```
generic##Multiple types of devices,
light##Lighting,
climate##Climate Control,
entertainment##Entertainment and Media,
sound##Sound & Media Control,
lock##Locks and Security,
power##Batteries and Power,
appliance##Home Appliances,
home##Home Automation & Maintenance,
electronics##Personal Electronics,
plug##Wall Plugs and Switches,
custom##Custom -Set icon manually in object icon browser\nDevice Icon > Device Icon > \"Icon\" setting
```


###### ``
```
DATA##\"Select the location of the Device Icon on your Widget. \",
1##Default Location,
2##Inside Box 1 (ONLY for templates not using Box 1),
3##Swap with settings icons,
4##Hidden,
```


###### #####################################
###### Main Icon List
###### #####################################

I believe that this icon list is not used with the template system anymore,
which is why it was converted to be easy to read.

###### Schemea

```
  DATA##"Select an alternative icon for your widget.",

  light##-- Lighting --,
  material-ico-wb-incandescent-mod##Light Bulb,
  mdi-dev-desk-lamp##Desk Lamp,
  mdi-dev-string-lights##String Lights On,
  mdi-dev-string-lights-off##String Lights Off,
```

-- Lighting --
Light Bulb                         material-ico-wb-incandescent-mod
Desk Lamp                          mdi-dev-desk-lamp
String Lights On                   mdi-dev-string-lights
String Lights Off                  mdi-dev-string-lights-off

Flashlight                         mdi-flashlight
Lightbulb Off Outline              mdi-lightbulb-off-outline
Lightbulb On 30                    mdi-lightbulb-on-30
Lightbulb On 60                    mdi-lightbulb-on-60
Lightbulb On Outline               mdi-lightbulb-on-outline
Lightbulb On                       mdi-lightbulb-on

-- Climate Control --
Thermostat                         mdi-dev-thermostat
Climate AC                         mdi-ico-climate-ac-cus
Climate Split                      material-dev-climate-mini-split
Air Conditioner                    mdi-dev-air-conditioner
Ceiling Fan                        mdi-ico-ceiling-fan-mod
Fan                                mdi-ico-fan-mod
Radiator                           mdi-ico-radiator-mod
Mode Heat                          material-dev-mode-heat
Fireplace                          mdi-dev-fireplace
Fireplace Off                      mdi-dev-fireplace-off
Heat Pump                          mdi-dev-heat-pump
Heating Coil                       mdi-dev-heating-coil
HVAC                               mdi-dev-hvac

Speed Hi                           custom-speed-hi
Speed Low                          custom-speed-low
Speed Med                          custom-speed-med
Speed Off                          custom-speed-off
Air Conditioner Power              mdi-air-conditioner-power-mod
Fan Oscillation                    mdi-fan-oscillation-cus
Fan Power                          mdi-fan-power-cus
Fan Speed                          mdi-fan-speed-mod
Fire                               mdi-fire-mod
Home Thermometer                   mdi-home-thermometer
Snowflake Melt                     mdi-snowflake-melt
Snowflake                          mdi-snowflake
Sun Snowflake                      mdi-sun-snowflake-variant
Thermometer Minus                  mdi-thermometer-minus
Thermometer Plus                   mdi-thermometer-plus

-- Entertainment & Media --
Television                         mdi-ico-television-mod
Soundbar                           mdi-ico-soundbar-mod
Remote TV                          mdi-dev-remote-tv
Projector                          mdi-ico-projector-mod
Projector Screen                   mdi-ico-projector-screen-variant-mod
Music                              mdi-dev-music
Speaker                            mdi-dev-speaker
Speaker Wireless                   mdi-dev-speaker-wireless
Remote                             mdi-dev-remote

Input                              material-input
Mode Dual                          material-mode-dual
Mode Heat Cool                     material-mode-heat-cool
Arrow U Left Top                   mdi-arrow-u-left-top
Bluetooth Audio                    mdi-bluetooth-audio
Chevron Down                       mdi-chevron-down
Chevron Left                       mdi-chevron-left
Chevron Right                      mdi-chevron-right
Chevron Up                         mdi-chevron-up
Pause Circle                       mdi-pause-circle
Pause                              mdi-pause
Play Circle                        mdi-play-circle
Play Pause                         mdi-play-pause
Play                               mdi-play
Music Box Multiple                 mdi-music-box-multiple
Skip Next                          mdi-skip-next
Skip Previous                      mdi-skip-previous
Surround Sound                     mdi-surround-sound
Volume Minus                       mdi-volume-minus
Volume Mute                        mdi-volume-mute
Volume Plus                        mdi-volume-plus
Woofer Volume Minus                mdi-woofer-volume-minus-cus
Woofer Volume Plus                 mdi-woofer-volume-plus-cus
Video Input Hdmi                   mdi-video-input-hdmi

-- Locks & Security --
Lock Smart                         mdi-dev-lock-smart
Doorbell Video                     mdi-dev-doorbell-video
CCTV                               mdi-ico-cctv-mod
Garage Open                        mdi-dev-garage-open-variant
Garage Closed                      mdi-dev-garage-variant
Gate Open                          mdi-dev-gate-open
Gate Closed                        mdi-dev-gate

Dev Security                       mdi-dev-security
Dev Shield Home                    mdi-dev-shield-home
Dev Shield Lock                    mdi-dev-shield-lock
Lock Open                          mdi-lock-open-variant
Lock Open                          mdi-lock-open
Lock                               mdi-lock
Shield Home Outline                mdi-shield-home-outline
Shield Lock Open                   mdi-shield-lock-open

-- Batteries & Power --
Charging                           mdi-ico-charging-cus
Battery Charging                   mdi-dev-battery-charging
Car Electric                       mdi-dev-car-electric
Home Battery                       mdi-dev-home-battery
Solar Panel Large                  mdi-dev-solar-panel-large
Wind Power                         mdi-dev-wind-power

Battery                            mdi-battery-mod
Battery                            mdi-battery
Charging                           mdi-charging-cus
Power Cycle                        mdi-power-cycle-mod
Power Cycle                        mdi-power-cycle
Power                              mdi-power-mod
Power                              mdi-power

-- Home Appliances --
Fridge                             mdi-dev-fridge
Stove                              mdi-dev-stove
Dishwasher                         mdi-dev-dishwasher
Microwave                          mdi-dev-microwave
Washing Machine                    mdi-dev-washing-machine
Tumble Dryer                       mdi-dev-tumble-dryer
Coffee Maker                       mdi-dev-coffee-maker

-- Home Automation & Maintenance --
Blinds Open                        mdi-ico-blinds-open-mod
Blinds Closed                      mdi-ico-blinds-mod
Smoke Detector                     mdi-dev-smoke-detector
Bathroom Scale                     mdi-dev-scale-bathroom
Robot Lawn Mower                   mdi-dev-robot-mower
Robot Vacuum                       mdi-dev-robot-vacuum
Sprinkler                          mdi-dev-sprinkler

Sensors                            material-sensors
Gauge                              mdi-gauge
Google Assistant                   mdi-google-assistant
Home Assistant                     mdi-home-assistant
Home Circle                        mdi-home-circle
Home Clock                         mdi-home-clock
Home Map Marker                    mdi-home-map-marker
Home                               mdi-home

-- Personal Electronics --
Cellphone                          mdi-dev-cellphone
Laptop                             mdi-dev-laptop
Devices                            mdi-dev-devices
Earbuds                            mdi-dev-earbuds
Earbuds Battery                    material-dev-earbuds-battery
Watch 1                            mdi-dev-watch-variant
Watch 2                            mdi-dev-watch

Wifi Lock Open                     mdi-wifi-lock-open
Wifi Lock                          mdi-wifi-lock
Map Marker Account                 mdi-map-marker-account
Map Marker Remove                  mdi-map-marker-remove

-- Wall Plugs & Switches --
Socket AU                          mdi-dev-power-socket-au
Socket CH                          mdi-dev-power-socket-ch
Socket EU                          mdi-dev-power-socket-eu
Socket UK                          mdi-dev-power-socket-uk
Socket US                          mdi-dev-power-socket-us
Switch Off                         mdi-dev-toggle-switch-variant-off
Switch On                          mdi-dev-toggle-switch-variant

-- Multiple types of Devices --
Smart Home                         mdi-home

Room Preferences                   material-room-preferences
Scene                              material-scene
1                                  mdi-1-circle-outline
2                                  mdi-2-circle-outline
3                                  mdi-3-circle-outline
4                                  mdi-4-circle-outline
5                                  mdi-5-circle-outline
6                                  mdi-6-circle-outline
Cog                                mdi-cog
Cogs                               mdi-cogs
Palette                            mdi-palette
Plus Minus                         mdi-plus-minus-variant
Bell                               mdi-bell
Tune Vertical                      mdi-tune-vertical-variant
Water Percent                      mdi-water-percent


###### APP ICONS
app-alexa
app-amazon-prime
app-apple-tv
app-crave
app-disney
app-home-assistant
app-hulu
app-mdi-plex
app-mdi-spotify
app-mdi-youtube-tv
app-mdi-youtube
app-netflix
app-paramount+
app-plex
app-shazam
app-stash
app-youtube-music
