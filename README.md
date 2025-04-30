# Home Assistant control widgets for Android devices (HAWGT)

These widgets were built to fill a need for a customizable, nice-looking android widget for controlling Smart home devices and TV's without opening the Home Assistant app. The original concept was nice-looking device controls for a wall-mounted android device, so they are primarily designed to be used in a page layout, stacked on a single Android launcher page as a Control Hub. However, the smaller widgets are designed to also work on your regular launcher pages, and on a larger device like a tablet, all widgets should work well with your layout.

This document was written assuming you're new to Kustom and Tasker. Terminology will be basic and explanations offered for each step. More advanced users can skip to <HERE>. For readability, Home Assistant will be shortened to HA.

## *Section Completion Stages
Stage 1: Basic layout - Title and maybe some data  
Stage 2: Started - may need reformatting and more data added  
Stage 3: Mostly complete - add new data as needed  
Stage 4: Complete (beta)
Stage 5: Incorrect data - review thouroughly


## (3) Widget features:
- Common features shared with both widgets
    - Works on any Android device, but a few features are limited to newer versions of android
    - Combine multiple copies to create a unique "Home Assistant page" on your launchers homepage
    - Material Design icon pack included with many basics to get yourself started and to match your HA icons. Easily add your own icons in KWGT
    - Automatic widget layout adjustment and alignment based on given size
    - Advanced scaling options allow adjustment of virtually every element in the widget to fit good on any device and with different home screen grid patterns
    - Comprehensive background color control with the following color sources
        - Extract colors from your wallpaper
        - Extract specific custom system colors from Android 13? onwards to match your current device customization
            - See [Android Color Specs?](<URL>) for details of which colors are available for extraction
        - Filters for extracted colors to customize them to your preferences or choose complementary colors
        - Input your own ARGB colors
    - Advanced customization available to integrate common android tasks on your device using tasker, for example casting your device music to a HA connected device (see [Advanced]<#URL>)
- Device Control Widget
    - Control any device from your Android Home Screen
    - Based on a "2-box" mode to differentiate between main controls (power, room select, etc) and device controls (light settings, climate controls, etc)
    - Several preset size modes which scale widget from a single on/off control to controlling several devices with multiple settings from a single widget
    - Several preset styles such as OneUI and Material, with advanced customization of colors, transparency and box sizes to maintain the 
    - Many preset templates for common HA devices - a few listed below
        - Lighting controls by room
        - Climate controls with multiple modes
        - Battery meters for your devices
        - Home security dashboard
        - Home entertainment devices such as TV or sound system
- Large media controller
    - Dual TV Control and Local modes to make home screen space more efficiently used
    - TV remote replacement controls and 2-way streaming service data (only Samsung Tizen TV's so far)
    - Local Device mode with media controls, status widgets of smart devices and (advanced) Android device controls using Tasker (see [Advanced]<#URL>)

## (3) Setup and installation of software
This widget will take a bit of time to set up initially, as it requires as bit of configuration, but once its set up, you can make as many widgets as you like and even your own! It will also help to have a second devie available with your entities page open in home assistant
1. Install [Tasker](<GPURL>) and [KWGT](<GPURL>) or [KLWP](<GPLAYURL>) on your device if you don't have them already. If you haven't used these apps before, open each one up and follow the configuration process. This involves asking for system permissions and setting working folders for the applications. In Tasker, choose to use the full version, not Tasky. For Kustom create a new folder for it to work in on newer Android versions

2. Get your API key from HA. It's located in your user settings under the security tab. At the bottom of the page is the option to generated a new long-lived access key. WARNING: It's only displayed once, so keep it open in a tab until you've tested the connection!

3. Install the [KWGT Plugin](<URL>) for tasker. Clicking the link on your Android device should open it directly in Tasker **VERIFY**. You'll be asked for the API key you just got and your HA address information. You have 3 options for your adress information:
    - [Nabu Casa](<HAURL>) account - HA service offering a custom secure URL to your HA Server
    - Custom URL - For advanced users who have already set up a public URL
    - Local IP - You can use the address you use locally, however it will only work when your device connected to your WiFi network.

4. Next, log into Home Assistant and install the [HA Blueprint](<URL>), following the instructions from the Tasker developer. Use the same info you used in the taskernet setup and add your server information **VERIFY**.

5. Last, download the hawgts.kwgt file above and install it in < your-kwgt-folder >/widgets/hawgts.kwgt. This must be done after configuring KWGT for the first time!


## (2) Set up your widget Interface (beginners guide)
> Installing a custom Android launcher is highly recommended for this widget. If using a default launcher, support will be limited. Common launchers include Apex, Nova and Niagara.

1. Configure your launcher to support removing padding from widgets and half-grid placement (if possible) **VERIFY** to allow maximum customizability
2. Create a new 2x4 KWGT widget on your home screen. Remove padding from the widget if your launcher supports it
3. Click on the widget to set it uo. This will open KWGT's preset selection mode. Choose the widget you'd like to start with (this setup assumes you've chosen DCW)
4. You will be presented with the HAWGTS preset in KWGT. Hit the save button at the top to complete the widget setup
5. Find the "Globals" tab in the bottom section and select it. This is where all of the widget configuration is done, its essentially a "settings" page.
6. View the [Globals reference](/setup/globals.md) to walk through the settings and configure your widget how you like it. Return here after you've configured the widgets style to continue setting up


## (1) Getting your software to talk to each other - Configuration and testing

### Setting up widgets with devices you want to control
- Many preset templates will "drop" icons that don't have room to display


## (5) Linking tasker commands to KWGT

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

## (1) Setting up device states

Explain devname and how to add a new device (HA Blueprint and change state once)


### Helpful Information


Formatting commands - KWGT touch event
Switching from Yaml to JSON [link](https://www.json2yaml.com/convert-yaml-to-json) & removing lines
(haservicecall)&(hajson/template)
light.turn_on&{ "entity_id": "light.bedroom_lights", "brightness": 30 }

## Testing all controls


## (1) Troubleshooting



# ADVANCED
## (1) Make new device states indicators
# Match Existing
Copy the "Device Highlight" template from dev templates into the group(s) where you'd like it. Make the following edits to each copy:

- Edit the entity id for the color of the Circle shape
- Edit the color and filter entity id's for the icon shape
- Customize your icon & other details


If you want to make custom changes, you can create a local variable called "glbname" and set its stset to your entity id. Once done, you can use the following global variables. "glbname" will be used in their formulas to return the result.
Global variable     |       Returns
func/state              Device state
func/lgtlevel           Light Brightness (out of 255)
func/lgtcolor           Light Color
core/curpage            Number of current page if using a "pages" template



## (1) Using templates for more customization 

# Pre-made templates


2025:
- Make note about overriding any UI item by turning off formula mode. Eg. the device icon can be changed with the icon selector by removing the formula
- Also make note of which flows can be disabled to customize which features, eg auto-align and such. Could use to remove toggle for auto-align


To set up your template,
1. Select the number of devices you'd like to control
2. Select type of device (select multiple for more than one device type)
3. Scroll through the list of templates until you find one close to what you're looking for
4. Choose "Edit JSON" to edit the template to open the templates JSON.

In the JSON, the group of objects in the "controllist" key are the names of the icons that are available to use in the widget. Go to the keys "box_1" and :box_2". In the "row" sub-keys, you can put the names of the icons you'd like to use from "controllist" to link them to specific icons on the widget.You can then customize the icon to set the HA command, state status and other details as shows in the following section.



# Komponents 
To make the widgets as customizable as possible, you can put a Kustom Komponent in Box 2 as an alternative to HA icons. This is useful for those using this widget in page format, as you can easily add other device widgets to match the interface. 

> Kompnents require advanced setup to link into HAWGTS globals

Awesome Komponents to try out:
- 1
- 2
- 3

HAWGTS HA Komponents:
- Battery Meter
- 



## (1) Add more icons to rows in Device Control Widget
Create a new Overlay Group and place it in the list where you'd like it. If this is the third (or higher) control in the row, you have to make sure the groups visibility is set to the following formula:
```$if(gv(core/icocount)  < #, REMOVE, ALWAYS)$```
Where # is the number of this icon lists in the order. This is used to hide the last icons first when the widget is sized smaller.

Name your Overlay Group to your device name and place a control icon inside the group

## (1) Create a custom device control
This widget comes with many examples of device controls you can try, complete with HA commands that should work with only minor tweaks. If you're comfortable with Kustom products and want to create your own controls, the following information should help. All icons material design icons, either official or from [Pictogrammers](https://pictogrammers.com/library/mdi/). Its a vast database of material-styled icons for almost every use. You can download the svg of the icon you like and put it in the [Kustom]<FOLDERLOCATIONEXPLANATION> folder. If you're going to have a bunch of icons, make a new folder called "mdi" and place them in there.

The following attributes are used for the FontIcon in this widget for alignment and sizing:
- Size: Global Variable "icomain"
- Color: Global Variable "colico"
- Touch: Toggle Global Switch > Global Variable "tasker/hactrl" > HA Command code


## (1) Final notes