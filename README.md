# Home Assistant control widgets for Android devices

These widgets were built to fill a need for a customizable, nice-looking android widget for controlling Smart home devices and TV's without opening the Home Assistant app. The original concept was nice-looking device controls for a wall-mounted android device, so they are primarily designed to be used in a page layout, stacked on a single Android launcher page as a Control Hub. However, the smaller widgets are designed to also work on your regular launcher pages, and on a larger device like a tablet, all widgets should work well with your layout.

This document was written assuming you're very new to Home Assistant or KWGT, more advanced users can skip to <HERE>. For readability, Home Assistant will be shortened to HA 


## Widget package will contain:
- Medium-sized widget
    - fff
    - Dual room-by-room or simple device controls
    - Room-by-room single device controls (like lighting) supports up to 4 devices, indicated by room
    - Device-specific controls for devices that only have a few controls (climate controls, single
    - Easily scalable to fit in a corner with a few controls or can span across a tablet with entire rows of controls
- Large media controller
    - Dual TV Control and Local modes
    - TV remote replacement controls and 2-way streaming service icons (only Samsung Tizen TV's so far)
    - Local Device mode with media controls, status widgets of smart devices and android device controls
- Small media controllers
    - The large media player is a bit bulky for smaller devices, so to make basic device controls available for all devices, the small media controllers are no-frills controls that take up as little space as possible
    - One is specifically designed for TV controls, while the other for local media
- Potential "slim" single-device controller
    - Single device "Quick Controls" for on/off or basic HA entity/script control
    - Devices that aren't used often but sometimes need the a button pushed to resync your system (eg your sound system)

 
## All widgets will have the following features:
- Comprehensive background control with the following color sources
    - Extract colors from your wallpaper
    - Extract specific custom system colors from Android 13? onwards to match your current device customization
        - See [Android Color Specs?](<URL>) for details of which colors are available for extraction
    - Filters for extracted colors to customize them to your preferences or choose complementary colors
    - Input your own ARGB colors
- Different modes to minimalize the number of widgets, the style of controls you prefer and what type of devices you want to control
- A number of preset layouts that can be chosen in the widget configuration
- MDI-style icon pack to match HA, with many basics to get yourself started 
- Distributed as a Kustom "Komponent" for use across Kustom software (Step-by-step assumes KWGT)
- KWGT Features:
    - Auto-resizing and adjusting
        - Widget will auto-scale and adjust its layout based on size given
        - Virtually unlimited resizing capability, from 2 icons to 20 (limited to 2 rows of icons)
        - Many preset templates will "drop" icons that don't have room to display
    - Scaling - Adjust the size of virtually every element from the globals
        - Adjust horizontal/vertical padding separately and adjust icon size, padding, and margins
        - Adjustments are complex to allow the widget layout look clean on different screens and grid patterns
    - "Mini" mode which eliminates one of the boxes
        - Simple device controls that only need a couple of icons
        - Komponent mode, Allows adding of a Komponent the box
    - Preset "styles" such as OneUI and Material
        - Adjust details like rounded corners and colors/transparency to blend into existing desktops


## Pre-requisites
### Required
- A Home Assistant setup configured with the devices you'd like to control
- Tasker installed on the device you'd like to use this widget on
- KWGT or KLWP ([Kustom](<URL>) software is phenomenal and offers very advanced android customization
- A basic knowlege of how these programs work (step-by-step assumes familiarity with terminology used in software)

### Recommended
- Previous experience using Kustom and Tasker software will significantly speed up your setup process
- Use of a second screen/device (your android device + computer/laptop is ideal) for easy transfer of information
- Time to get the inital communication between all the software working and functional.

## Setup and installation of software
Install the following applications on your device:
- Tasker 6.2.12 or newer
- KWGT 3.74 or newer (Pro recommended)

Once you've got those applications set up, open them up and follow the intital startup process *VERIFY*. In Tasker, choose to use the full version, not Tasky. Allow all the permissions Tasker requires. Same with KWGT.

Next, you need to install [this]<TASKERNET> tasker plugin from TaskerNet. It includes some tasks from the official Tasker plugin by Joan, the KWGT plugin by *UNKNOWN* and some custom tasks used to format data between KWGT and Home Assistant better.

When installing the plugin, you'll be asked *VERIFY* for your Home Assistant server information and API key. If you don't have a [Nabu Casa]<HAURL> account set up, which is used to give you a custom URL, you'll only be able to control data when on the same network as the server. In this case, you can provide your IP Address and Port (usually 8123). To get your API key, open Home Assistant and click on your user settings. At the bottom of the page is the option to generated a new long-lived access key.

Next, log into Home Assistant and install [this]<TASKERURL> blueprint into home assistant, following the instructions from the Tasker developer. Use the same info you used in the taskernet setup and add your devices local IP address or Nabu Casa account *VERIFY*




## Getting your software to talk to each other - Configuration and testing


## Setting up widgets with devices you want to control

## Setting up device states

Explain devname and how to add a new device (HA Blueprint and change state once)


## Linking tasker commands to KWGT

You'll need the following information from Home Assistant for each of the devices you want to control
- [Entity ID](<HAURL>) (eg. light.living_room)
- State information and respective HA Templates if you want to show current device status' (eg )
- dfdfd 
- Parameters you want to pass to the entity (eg. light brightness or color). [This](https://www.home-assistant.io/integrations/light/#service-lightturn_on) is a list of the parameters accepted by the light.turn_on as an example

To write a command, use the following syntax:
service_call&entity_id&parameters
Where "service_call" can be one of the following: on, off, light, toggle, or template

The following code will set a light with the entity id "light.bedroom_light" to a brightness to 50%, remembering that full brightness is "255":
light&light.bedroom_lights&brightness=125

Basic devices and scripts are simple:
off&switch.coffee_maker
toggle&mediaplayer.lr_tv
on&script.send_ir_command

The parameters argument is used for common devices pre-setup in tasker, such as lights and thermostats *VERIFY*. Its also used to utilize Home Assistants template support. The template support is used mostly to manually set data into the list of devices or for extra custom control. Read more about it [below]<READMEURL>.
light&light.bedroom_light&brightness=125,color=255,235,35
template& {{ states.light.bedroom_lights.attributes.brightness }}

## New device status indicators
# Match Existing
Copy the "Device Highlight" template from dev templates into the group(s) where you'd like it. Make the following edits to each copy:

- Edit the entity id for the color of the Circle shape
- Edit the color and filter entity id's for the icon shape
- Customize your icon & other details

# ADVANCED
If you want to make custom changes, you can create a local variable called "glbname" and set its value to your entity id. Once done, you can use the following global variables. "glbname" will be used in their formulas to return the result.
Global variable     |       Returns
formulas/state              Device state
formulas/lgtlevel           Light Brightness (out of 255)
formulas/lgtcolor           Light Color
generated/pagenum            Number of current page if using a "pages" template




## Using templates for cus

### Helpful Information


Formatting commands - KWGT touch event
Switching from Yaml to JSON [link](https://www.json2yaml.com/convert-yaml-to-json) & removing lines
(haservicecall)&(hatemplatejson)
light.turn_on&{ "entity_id": "light.bedroom_lights", "brightness": 30 }

## Testing all controls


## Troubleshooting


### Advanced

## Using templates for more customizability


## Add more icons to rows in Device Control Widget
Create a new Overlay Group and place it in the list where you'd like it. If this is the third (or higher) control in the row, you have to make sure the groups visibility is set to the following formula:
```$if(gv(generated/icocount)  < #, REMOVE, ALWAYS)$```
Where # is the number of this icon lists in the order. This is used to hide the last icons first when the widget is sized smaller.

Name your Overlay Group to your device name and place a control icon inside the group

## Create a custom device control
This widget comes with many examples of device controls you can try, complete with HA commands that should work with only minor tweaks. If you're comfortable with Kustom products and want to create your own controls, the following information should help. All icons material design icons, either official or from [Pictogrammers](https://pictogrammers.com/library/mdi/). Its a vast database of material-styled icons for almost every use. You can download the svg of the icon you like and put it in the [Kustom]<FOLDERLOCATIONEXPLANATION> folder. If you're going to have a bunch of icons, make a new folder called "mdi" and place them in there.

The following attributes are used for the FontIcon in this widget for alignment and sizing:
- Size: Global Variable "icomain"
- Color: Global Variable "colico"
- Touch: Toggle Global Switch > Global Variable "tasker/hactrl" > HA Command code


## Final notes
