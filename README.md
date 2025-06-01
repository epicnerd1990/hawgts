# (3) Home Assistant Widget (HAWGT)

A customizable and themable [Home Assistant](https://www.home-assistant.io) (HA) widget for  Android Launchers. It uses the powerful [Kustom](https://docs.kustom.rocks) customization suite and [Tasker](https://tasker.joaoapps.com/index.html), one of the most popular automation apps for Android to facilitate the customization and communication to HA. 

This widget attempts to mimimize the learning curve and time needed by these complex apps by offering powerful customization through Kustom [Globals](/docs/globals.md) (settings). With these, those who love to customize their smart home can do the same to their Android interface easily. In the Globals menu, simply choose a device type, layout template that fits your widget size and enter your "entity_id" from HA to get it functional. You can choose a theme, then customize it by adjusting nearly every layout, alignment and color detail to match the style of your device. See [installation](#installation-and-configuration) for step-by-step setup instructions.

For advanced users or those already familiar with Kustom and Tasker, the widget appearance is infinately customizable. To start, create [custom themes](/docs/json.md#custom-theme) and [device templates](/docs/json.md#custom-template) using JSON. Themes control colors, alignment and padding, while device templates control the object (widget icon) layout and configure the icon, action, etc.


## *Section Completion Stages **(Delete after drafts complete)**
- 1: Basic layout - Title and maybe some data
- 2: Started - may need reformatting and more data added
- 3: Mostly complete - add new data as needed
- None: Complete - add new data as needed


## (3) HAWGTS vs HA offical app widget:
HAWGTS prioritizes style customization and fills the niche of those wanting to customize the way their smart home controls look on their Android home screen. Because of this, it does not (yet) come close to matching the functionality of the widgets included with the offical [HA app](https://play.google.com/store/apps/details?id=io.homeassistant.companion.android&hl=en_CA). See this feature comparision chart showing each widgets strengths and weaknesses.

| Feature:                                | HAWGTS                    | HA Offical App          |
|-----------------------------------------|---------------------------|-------------------------|
| Control widget from Android Launcher    | Yes                       | Yes                     |
| Multi-button layout                     | Yes                       | Media Player only       |
| Theme/style suppport                    |                           |                         |
| > Preset styles                         | Dark/Light/OneUI/Material | Dark/Light/System       |
| > Override colors, layout, alignment    | Yes                       | No                      |
| > Widget Sizes                          | Any                       | Preset                  |
| Material Design Icons                   | Some (included)           | Some (from HA)          |
| > Use custom icons                      | Yes                       | No                      |
| State information                       | Basic                     | Advanced                |
| Command Setup                           | Basic                     | Advanced                |
| Easy widget setup                       | No                        | Yes (HA App config)     |
| App requirements                        | Requires 2 apps           | Only HA App             |
|                                         |                           |                         |

## Installation and Configuration
1. Install [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=en_CA) and [KWGT](https://play.google.com/store/apps/details?id=org.kustom.widget&hl=en_CA) (part of the Kustom suite) on your Android device if they're not installed already. If you haven't used these apps before, open each one up and follow the initial configuration process. This involves asking for system permissions and setting working folders for the applications. In Tasker, choose to use the full version, not Tasky. Make sure to create a working folder for KWGT when prompted (on newer Android versions)
2. Get an API key from HA. To do this, click your name at the bottom of the sidebar and go to the Security tab. At the bottom of the page is the option to generated a new long-lived access key
> Warning: It's only displayed once, so keep it open in a tab until you've tested the connection!
3. Install the [Kustom Plugin](<URL>) for Tasker. Clicking the link on the desired Android device should open it directly in Tasker. If that doesnt happen, download the plugin manually and import it into Tasker. Once the plugin is loaded, follow the setup to enter the API key and HA server information. Server address options:
    - [Nabu Casa](https://www.nabucasa.com) account - HA service offering a secure public URL to your HA Server
    - Existing - For those users who have already set up a public-facing address
    - Local IP - Use the servers hostname and port on your local network - This will only work when the device is connected to the WiFi
4. Assign a static IP to the device using your network router configuration (optional, but adds connection stability)
5. Install the [Tasker HA Blueprint](<URL>) in HA. Fill in the device IP and add all entities to control from the widget
6. Download the newest HAWGTS release into `<your-kwgt-folder>/widgets/`. This must be done _after_ configuring KWGT for the first time in step 1


## Set up widget interface - Basic
> Installing a custom Android launcher is highly recommended for this widget. Support will be limited using some default launchers. Common custom launchers include Apex, Nova and Niagara.

1. Create a new 2x4 KWGT widget on the home screen. Remove the padding from the widget if your launcher supports it (long-press)
2. Click on the widget to set it up. This will open KWGT's preset selection mode. Choose the HAWGTS widget downloaded into `<your-kwgt-folder>/widgets/` folder in the [installation](#installation-and-configuration) stage
3. This will open the widget in the Kustom editor. 
    - Important tabs along the bottom to know are **Items** and **Globals**
    - **Items** are where the widgets objects are configured. Advanced users can explore this to add even more customization to their widgets.
    - **Globals** are where the widget settings/configuration is done
    - Hit the save button at the top to complete the widget setup. Note that you will have to hit the save button after making any big changes [to allow all the values to recalculate](#widget-looks-funky-after-making-a-change)
4. Open the **Globals** tab and configure the widget. View the [**Globals**](/docs/globals.md) guide for information on what each setting does
5. Test the configuration and make sure everything works as expected. Review [installation](#installation-and-configuration) and [troubleshooting](#troubleshooting) if something doesn't work
6. Once the widget is configured, use the KWGT editor to export a copy. This is a backup and can also be used to make more widgets based on your initial style configuration.

## (1) Set up widget interface - Advanced

### (1) Building widget

- KWGT = zip
- preset.json
- Bash script

### (1)Commands for KWGT > Tasker

- URI info
- "type" "onstate" "action" template tags

### (2) Function API information
As a scripting language, Kustom "Kode" doesn't support global functions or any form of function import. However, one can create their own by declaring variables inside of a global variable, and then calling that variable in the same way one would import a class into code. In Kode, there are global variables `$gv(varName)$` and local variables `$lv(varName, value)$`. Global variables cannot be set inside the code, but local variables can, thus the difference in syntax. `#varName` will display the value of a local variable. These can be used anywhere in the widget. HAWGTS contains some accessible "Global Function" groups that you can use to get data from the theme, template or device state. These values are used directly in the widget items, so the output might be different then the JSON if overrides are made in the widget settings. Variable names can be matched to the respective [JSON](/docs/json.md) keys. 

#### Theme and color functions
Sample: `$gv(func/theme)$$#border$`

|`func/theme` | `func/themecolors` |
|-------------|--------------------|
| #border     | #back1             |
| #radius     | #back2             |
| #padwgtver  | #objiconon         |
| #padwgthor  | #objiconoff        |
| #padobjhor  | #objringon         |
| #padobjver  | #objringoff        |
| #padobjside | #border            |
| #ringsha    |                    |
| #iconsize   |                    |
| #shadow     |                    |
| #wgtopa     |                    |

#### Template and object information
`func/jsontemp` This returns values specific to the chosen template  
`func/jsonobj` This returns values specific each object. Must set `#posid` (as per sample below) to get the data from the correct object before calling the global variable  

Sample: `$lv(posid, ".box2.row1.0")$$gv(func/jsonobj)$$#obicon$`

|`func/jsontemp` | `func/jsonobj` |
|----------------|----------------|
| #tname         | #obid          |
| #ticon         | #obtype        |
| #tbox1         | #obicon        |
| #trowct        | #obaction      |
| #ticopos       | #obcmd         |
| #tdevct        | #obdata        |
|                | #obparam       |

#### Current state data
`#stoutput` returns a simple 0 or 1 indicating if the device state matches the "onstate" value in the template JSON. This must have `#posid` set

Sample: `$lv(posid, ".box2.row1.0")$$gv(func/jsonstate)$$#stoutput$`

## (1) Troubleshooting

#### (1) Widget looks funky after making a change

- Save all the time in Kustom editor

#### (1) Widget stopped connecting to HA randomly

- Static or locked ip for android device helps

## (2) Tips and tricks

#### (3) Changing settings not included in "Globals" menu
Using the Kustom editor, you can override and change all of the settings the widget presets for you. This can be done by going to the object you want to edit in the "Items" tab. If an object has a calculator or global icon to the right of it, it is pre-confured as part of HAWGTS. You can modify this configuration by touching the item, or remove it by selecting the checkbox and touching the calculator or globe icon in the top right of the editor to cancel it out. Once the preset configuration is removed, you can then edit the option.


#### (3) Custom Icons
Included with the widget is a large number of Material icons, the same used in HA. They include most common HA devices for variety. You can change any icon by overriding the pre-configured icon (as shown above) and selecting the icon you'd like from the Kustom icon picker.


#### (2) Padding and alignment
- Some launchers allow you to remove additional padding to allow widgets to touch, and placement of widgets on half-grid locations. Look at the settings menu for your launcher to see if this option is available
- The widget allows many object size and padding overrides in the "theme" folder. See the [Globals guide](/docs/globals.md#global-variable-table) for specifics
- To configure advanced alignment, use the `theme/advanced/autoalign` toggle in the advanced section of the settings to disable auto-sizing formulas and create custom values

## (1) Future work and contributions
- To get to V1, goals have prioritized user control, not information display. As of now, only on/off state can be indicated for each widget object (except lights, which can have color and brightness information). Much more state data and [HA Template](https://www.home-assistant.io/docs/configuration/templating/) support is planned for V2
- Support for [Kustom Komponents](https://docs.kustom.rocks/docs/general_information/groups_explained/#komponent) planned for V2. This will allow many more template customizations like dials, levels and gauges
Feedback, bug reports and pull requests are welcome

## Licence
Copyright (C) 2025 Corey Welsh

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, version 3 of the License.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

See <https://www.gnu.org/licenses/> for full licence.