# Home Assistant control widgets for Android devices

These widgets were built to fill a need for a customizable, nice-looking android widget for controlling Smart home devices and TV's without opening the Home Assistant app. The original concept was nice-looking device controls for a wall-mounted android device, so they are primarily designed to be used in a page layout, stacked on a single Android launcher page as a Control Hub. However, the smaller widgets are designed to also work on your regular launcher pages, and on a larger device like a tablet, all widgets should work well with your layout.


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
Formatting commands - KWGT touch event
Switching from Yaml to JSON [link](https://www.json2yaml.com/convert-yaml-to-json) & removing lines
(haservicecall)&(hatemplatejson)
light.turn_on&{ "entity_id": "light.bedroom_lights", "brightness": 30 }

## Testing all controls


## Trouboleshooting


## Final notes
