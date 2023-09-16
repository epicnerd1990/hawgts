# Home Assistant control widgets for Android devices

These widgets were built to fill a need for a customizable, nice-looking android widget for controlling Smart home devices and TV's without opening the Home Assistant app. The original concept was nice-looking device controls for a wall-mounted android device, so they are primarily designed to be used in a page layout, stacked on a single Android launcher page as a Control Hub. However, the smaller widgets are designed to also work on your regular launcher pages, and on a larger device like a tablet, all widgets should work well with your layout.

This document was written assuming you're very new to Home Assistant or KWGT, more advanced users can skip to <HERE>. For readability, Home Assistant will be shortened to HA 


## Widget package will contain:
- Medium-sized widget
    - Dual room-by-room or simple device controls
    - Room-by-room single device controls (like lighting) supports up to 4 devices, indicated by room
    - Device-specific controls for devices that only have a few controls (climate controls, single
    - Easily scalable to fit in a corner with a few controls or can span across a tablet with entire rows of controls
- Large media controller
    - Dual TV Control and Local modes
    - TV remote replacement controls and 2-way streaming service icons (only Samsung Tizen TV's so far)
    - Local Device mode with media controls, status widgets of smart devices and android device controls
- Small media controllers
    - The large media player is a bit bulky for smaller devices, so to make basic device controls avaliable for all devices, the small media controllers are no-frills controls that take up as little space as possible
    - One is specifically designed for TV controls, while the other for local media
- Potential "slim" single-device controller
    - Single device "Quick Controls" for on/off or basic HA entity/script control
    - Devices that arent used often but sometimes need the a button pushed to resync your system (eg soundsystem)

 
## All widgets will have the following features:
- Comprehensive background control with the following color sources
    - Extract colors from your wallpaper
    - Extract specific custom system colors from Android 13? onwards to match your current device customization
        - See [Android Color Specs?](<URL>) for details of which colors are avalible for extraction
    - Filters for extracted colors to customize them to your preferences or choose complementary colors
    - Input your own ARGB colors
- Adaptability to be resized to many sizes
    - Smaller widgets will auto-resize and drop icons as needed
    - Complete control of rounded corners, paddings, margins, icon sizes and other adjustments in one place to get your widget looking as clean as possible
- Different modes to minimalize the number of widgets, the style of controls you prefer and what type of devices you want to control
- A number of preset layouts that can be chosen in the widget configuration
- Default material-style icon pack with many basics to get yourself started
- Distributed as a Kustom "Komponent" for use across Kustom software (Step-by-step assumes KWGT)


## Pre-requisites
### Required
- A Home Assistant setup configured with the devices you'd like to control
- Tasker installed on the device you'd like to use this widget on
- KWGT or KLWP ([Kustom](<URL>) software is phenomenal and offers very advanced android customization)
- A basic knowlege of how these programs work (step-by-step assumes familiarity with terminology used in software)

### Recommended
- Previous use of Kustom and Tasker software will significantly speed up your setup process
- Use of a second screen/device (your android device + computer/laptop is ideal) for easy transfer of information
- Time to get the inital communication between all the software working and functional.

## Setup and installation of software


## Getting your software to talk to each other - inital setup


## Setting up widgets with devices you want to control


## Linking tasker commands to KWGT

You'll need the following information from Home Assistant for each of the devices you want to control
- [Entity ID](<HAURL>) (eg. light.living_room)
- The [Service Call]<HAURL> you want to use for the entity (eg. light.turn_on). If you use the pre-setup templates in the widget, the service calls for the relavent devices will be preset with common defaults
- Parameters you want to pass to the entity (eg. light brightness or hue). [This](https://www.home-assistant.io/integrations/light/#service-lightturn_on) is a list of the parameters accepted by the light.turn_on as an example

![Screenshot of a code sample from the Home Assistant Docs website indicating the previous information](<ha-light-sample>)

###Helpful Information
If you don't know much about service calls, go to Developer Tools > Services in your HA installation. The dropdown menu will show all avalible services and you can test your calls here before using them in KWGT. Below is a few basic service calls to get you started if you're just doing basics
- ```light.toggle```, ```light.turn_on``` and ```light.turn_off``` for light controls
- ```switch.toggle```, ```switch.turn_on``` & ```switch.turn_off``` for smart switches
- ```homeassisstant.turn_on``` & ```homeassisstant.turn_off``` for running scripts

Formatting commands - KWGT touch event
Switching from Yaml to JSON [link](https://www.json2yaml.com/convert-yaml-to-json) & removing lines
(haservicecall)&(hatemplatejson)
light.turn_on&{ "entity_id": "light.bedroom_lights", "brightness": 30 }

## Testing all controls


## Troubleshooting


## Advanced

### Add more icons to rows in Device Control Widget
Create a new Overlay Group and place it in the list where you'd like it. If this is the third (or higher) control in the row, you have to make sure the groups visibility is set to the following formula:
```$if(gv(generate/icocount)  < #, REMOVE, ALWAYS)$```
Where # is the number of this icon lists in the order. This is used to hide the last icons first when the widget is sized smaller.

Name your Overlay Group to your device name and place a control icon inside the group

### Create a custom device control
This widget comes with many examples of device controls you can try, complete with HA commands that should work with only minor tweaks. If you're comfortable with Kustom products and want to create your own controls, the following information should help. All icons material design icons, either official or from [Pictogrammers](https://pictogrammers.com/library/mdi/). Its a vast database of material-styled icons for almost every use.

The following attributes are used for the FontIcon in this widget for alignment and sizing:
- Size: Global Variable "icomain"
- Color: Global Varaible "colico"
- Touch: Toggle Global Switch > Global Variable "tasker/hactrl" > HA Command code


## Final notes
