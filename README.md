# Home Assistant Widget (HAWGT)

A customizable and themable [Home Assistant](https://www.home-assistant.io) (HA) widget for Android Launchers. Designed to be more customizable then the widgets included with the Home Assistant android app to blend into any home screen. Choose from preset themes and layouts to control your smart home the way you want with this icon-based widget

HAWGTS uses the powerful [Kustom](https://docs.kustom.rocks) customization suite and [Tasker](https://tasker.joaoapps.com/index.html), the best automation app for Android, to facilitate customization and communication to HA. The design of HAWGTS attempts to mimimize the learning curve and time needed by these complex apps by offering most customization through Kustom [**Globals**](/docs/globals.md) (settings). For advanced users or those already familiar with Kustom and Tasker, see [custom themes](/docs/json.md#create-a-custom-theme), [device templates](/docs/json.md#custom-template) and [**Globals**](/docs/globals.md) in addition to the information below

(2) The original concept for this widget was a HA dashboard on the wall using an old android phone. I wanted to put all of my commonly controlled devices on the screen and mount it next to a light switch to use as a smart home hub. I prefered this look over just leaving the dashboard open. I also wanted to be able to control my phone and smarthome from widgets of the same style when I was using my main device, which is heavily customized and themed. Once I realized how much work this project would entail, I decided to go all-in and make it as customizable and fluid as it could, ensuring HA enthusists could use it without having to learn "Kustom" coding like I did.

## Quickstart
Download the KWGT file from releases *(not yet)* and place it in the Kustom "widget" directory. Create a new widget and choose HAWGTS. In the Globals menu, choose a device type, layout template that fits your widget size and enter your "entity_id" from HA to get it functional. You can choose a theme, then customize it by adjusting nearly every layout, alignment and color detail to match the style of your device. See [installation](#installation-and-configuration) below for full instructions, widget config and communicating with HA.

## HAWGTS vs HA offical app widget:
HAWGTS prioritizes style customization and fills the niche of those wanting to customize the way their smart home controls look on their Android home screen. Because of this, it doesn't come close to matching the functionality of the widgets included with the offical [HA app](https://play.google.com/store/apps/details?id=io.homeassistant.companion.android&hl=en_CA). For many users, the offical app widgets will work fine. Below is a comparision chart to help you decide which one is best for you

| Feature:                                | HAWGTS                    | Offical HA App          |
|-----------------------------------------|---------------------------|-------------------------|
| Control smart devices from Home Screen  | Yes                       | Yes                     |
| Multi-button layouts                    | Yes                       | Media Player only       |
| Device-specific layouts                 | Yes                       | Media Player only       |
| Theme/style suppport                    |                           |                         |
| > Preset styles                         | Dark/Light/System         | Dark/Light/System       |
| > Override colors, layout, alignment    | Yes                       | No                      |
| > Widget Sizes                          | Any                       | Preset                  |
| Use custom icons to match device style  | Yes                       | No                      |
| HA State information and data           | Basic                     | Advanced                |
| HA Device Commands configuration        | Basic                     | Advanced                |
| Simple widget setup                     | No                        | Yes (HA App config)     |
| App requirements                        | 2 apps + HA app           | Only HA App             |


## Installation and Configuration
1. Install [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=en_CA) and [KWGT](https://play.google.com/store/apps/details?id=org.kustom.widget&hl=en_CA) (part of the Kustom suite) on the Android device if they're not installed already
    - If you haven't used these apps before, open each one up and follow the initial configuration process. This involves asking for system permissions and setting working folders for the applications
    - Tasker: choose to use the full version, not Tasky
    - KWGT: Create a working folder for KWGT when prompted (on newer Android versions)
2. Get an API key from HA. To do this, click your name at the bottom of the sidebar and go to the Security tab. At the bottom of the page is the option to generated a new long-lived access key
> Warning: It's only displayed once, so keep it open in a tab until you've tested the connection!
3. Assign a static IP to the Android device using your network router configuration (optional, but creates connection stability)
4. Follow the instructions from the Tasker developer to [install the Tasker Blueprint](https://tasker.joaoapps.com/home_automation/home_assistant.html) in HA, but stop at importing the HA Tasker project, since we will use our own. Fill in the device IP and add all entities to control from the widget
5. Install the [Kustom Plugin](/src/plugin.xml) for Tasker. Clicking this link on the desired Android device should open it directly in Tasker. If that doesnt happen, download the plugin manually and import it into Tasker. Once the plugin is loaded, follow the setup to enter the API key and HA server information. Server address options:
    - [Nabu Casa](https://www.nabucasa.com) account - HA service offering a secure public URL to your HA Server
    - Existing - For those users who have already set up a public-facing address
    - Local IP - Use the servers hostname and port on your local network - This will only work when the device is connected to the WiFi
6. Download the newest HAWGTS release into `<your-kwgt-folder>/widgets/`, or copy it from your downloads. This must be done _after_ configuring KWGT for the first time in step 1, as it will create this directory


## Setting up the widget interface
> Installing a custom Android launcher is highly recommended for this widget. Support will be limited using some default launchers. Common custom launchers include Apex, Nova and Niagara.

1. Create a new 2x4 KWGT widget on the home screen. Remove the padding from the widget if your launcher supports it (long-press)
2. Click on the widget to open KWGT's preset selection mode. Choose the HAWGTS widget
3. This will open the widget in the Kustom editor
    - Important tabs along the bottom to know are **Items** and **Globals**
    - **Items** are where the widgets objects are configured. Advanced users can explore this to add even more customization to their widgets
    - **Globals** are where the widget settings/configuration is done. Most settings and configuration will be done here
    - Hit the save button at the top to complete the widget setup. Note that you will have to hit the save button after making any big changes [to allow all the values to recalculate](#widget-looks-funky-after-making-a-change-or-resizing)
4. Open the **Globals** tab and configure the widget. View the [**Globals**](/docs/globals.md) guide for information on what each setting does if unsure
5. Test the configuration and make sure everything works as expected. Review [installation](#installation-and-configuration) and [troubleshooting](#troubleshooting) if something doesn't work
6. Once the widget is configured, use the KWGT editor to export a copy. This is a backup and can also be used to make more widgets based on your initial style configuration


## Tips and tricks

#### Changing settings not included in "Globals" menu
Using the Kustom editor, all of the parameters set from HAWGTS can be overridden. This can be done by going to the object you want to edit in the "Items" tab. If an object parameter has a calculator or global icon to the right of it, it is pre-configured as part of HAWGTS. This configuration can be modified by tapping the item, or remove it by selecting the checkbox and touching the calculator or globe icon in the top right of the editor to cancel it out. Once the HAWGTS configuration is removed, the option can be edited freely

#### Changing Icons
HAWGTS includes some "Material" icons (what HA uses). They include most common HA devices. While the tempalates are pre-configured for most configurations, some may want to use alternative Material icons, or even your own. Icons can be changed by following the instructions [above](#changing-settings-not-included-in-globals-menu) to override the icon set by the template on the object. The icon you'd like can now be selected using the Kustom icon picker. For advanced users, [create your own template](/docs/json.md#custom-template) and set icons to objects directly

#### Padding and alignment
Some launchers allow you to remove additional padding to allow widgets to touch, and placement of widgets on half-grid locations. Look at the settings menu for your launcher to see if this option is available. Within the widget, you can make many padding and alignment adjustments in the "theme" folder. See [**Globals**](/docs/globals.md#global-variable-table) for specifics.

#### Control your Android device using HAWGTS
This widget can be used without HA. The main reason for this is if you want to set up a "dashboard" style page on your device and control your Android device and smart home devices together. For example, you can turn your lights off and turn your hub screen brightness down, both from the same page, or even the same widget!

1. Override the object settings as shown [above](#changing-settings-not-included-in-globals-menu)
2. Change the "Touch" tab to control whatever you'd like, using Kustoms many options. To do two things at once, two different "Touch" commands can be set.

For more power and integration, you can [send custom commands to Tasker directly](/docs/json.md#1-custom-kwgt--tasker-commands) and write actions there.


## Building process
To build this widget, simply add the preset.json to "DCW.kwgt" in the [/src](/src/) folder. Be sure to verify the JSON using [jq](https://github.com/jqlang/jq) or equivalent, or it will not open in KWGT. If you use linux, there is a bash script included in [TO-DO](TO-DO.md#shortcuts-and-scripts-for-kwgt-use).


## Troubleshooting

#### Widget looks funky after making a change or resizing
Sometimes the formulas dont calculate properly. Try saving first, as this calculates the formulas, then try the following:
1. Close KWGT completely (swipe from recent apps) if only glitching in the Editor
2. Close KWGT completely, then restart your phone if the widget on the home screen is also glitching
3. See [Kustom docs](https://docs.kustom.rocks/docs/faq/faq_kwgt/#widget-resizes-itself--size-is-not-right) for more options

#### Widget stopped connecting to HA randomly
Things to check to ensure connectivity:
1. Your device IP address hasn't changed. If this happens often, it may help to assign your device a static IP using your router configuration
2. Verify KWGT or Tasker are not being put to sleep by your system. For Tasker, this should have been configured when you first installed it. See [Kustom Docs](https://docs.kustom.rocks/docs/common_issues/widget_not_udpating/) for more info

> See [KWGT FAQ](https://docs.kustom.rocks/docs/faq/faq_kwgt/) and [Common Issues](https://docs.kustom.rocks/docs/common_issues/) from the developer for additional troubleshooting, or open an issue


## Future work and contributions
- To get to V1, goals have prioritized a widget that is designed for control, not information display. As of now, only on/off state can be indicated for each widget object (except lights, which can have color and brightness information). Much more state data and [HA Template](https://www.home-assistant.io/docs/configuration/templating/) support is planned for V2
- Support for [Kustom Komponents](https://docs.kustom.rocks/docs/general_information/groups_explained/#komponent) planned for V2. This will allow many more template customizations like dials, levels and gauges. This will also enable the widget to be added to [KWLP wallpapers](https://play.google.com/store/apps/details?id=org.kustom.wallpaper&hl=en)
- Feedback, bug reports and pull requests are welcome


## Licence
Copyright (C) 2025 Corey Welsh

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, version 3 of the License.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

See <https://www.gnu.org/licenses/> or repo licence page for full licence.


## *Section Completion Stages **(Delete after all documents complete)**
- 1: Basic layout - Title and maybe some data
- 2: Started - may need reformatting and more data added
- 3: Mostly complete - add new data as needed
- None: Complete - add new data as needed