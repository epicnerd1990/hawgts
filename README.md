# (2) Home Assistant control widgets for Android devices (HAWGT)

A customizable and themable Home Assistant (HA) widget for creating "Cards" on your Android Launcher. Designed for easy use for those not in the Kustom ecosystem, the widget comes with one-click theme and device layout templates. Simply open the widget, choose your device type, choose a layout template that fits your layout and enter your "entity_id" from HA to get it functional.

For advanced users, there's a powerful customization system that allows you to create custom themes and adjust nearly every layout and color detail, all from the [Globals menu](/docs/globals.md) (settings). And if you want even more customization, see [JSON database specs](/docs/json.md) to learn how to make your own themes and device templates to transfer across widgets and devices.

This widget was designed with the following goals:
- Easy to configure widget once connected to HA, for those who dont use Kustom software
- Ability for multiple widgets to create a "Dashboard" on your launcher
- Infinately customizable and can be made to match any style, configuration or layout on your device

This widget uses the powerful [Kustom](https://docs.kustom.rocks) customization suite and [Tasker](https://tasker.joaoapps.com/index.html), one of the best automation apps for Android to facilitate the customization  and communication to HA. Because of this, it requires an initial configuration that can take a bit of time. Please see the [HAWGTS > HA Default widget](#2-hawgts-vs-built-in-ha-app-widget) comparision chart to see if this widget will be worth your time. This document was written assuming you're new to Kustom and Tasker. Terminology will be basic and explanations offered for each step.

##  (2) Terminology
HA = Home Assistant  
Settings menu = Globals tab on widget  
Layout template = Layout of objects on widget  
Objects = Icons on widget
`Global name`


## *Section Completion Stages
- 1: Basic layout - Title and maybe some data
- 2: Started - may need reformatting and more data added
- 3: Mostly complete - add new data as needed
- None: Complete - add new data as needed


## (2) Widget features:
- Control your Smart Home Device from the Android Home Screen
- Comprehensive theme, color and template system
    - Choose between the preset themes - Basic, Samsungs "OneUI" and Googles "Material" themes - that use color data from your system and wallpaper
    - Override preset color, size, padding, etc directly from the settings
    - Preset device templates for quick configuration of layout and available commands
    - Theme and device template data are stored in [JSON](/docs/json.md), so advanced users can create their own layouts/themes and duplicate them across widgets
- Based on a 2-box design to differentiate between main controls and secondary controls
- Combine multiple copies to create a unique "Home Assistant page" on your launchers homepage
- Material Design icon pack included with many basics to get yourself started and to match your HA icons. Easily add your own icons in KWGT
- Automatically align and adjust to work your home screen layout
- Advanced customization options allow adjustment of virtually every element in the widget to fit with any home screen grid patterns
- Preset templates for most common devices
    - Widget can scale from a single on/off button to controlling multiple devices with multiple pages from a single widget
    - Home security dashboard, TV remote control, Climate control, etc

## (2) Current limitations
- Only on/off state can be indicated for each widget object, and on/off/toggle are the only commands supported. [HA Template]<> data is planned for the future.
- Kustom software is extremely sophisticated and allows far more customizability then most Android apps. Unfortunately, this means it has a learning curve to learn its scripting language. This widget attempts to overcome that learning curve by offering many options in a "settings" menu. However, users looking for a simple solution should try the widgets included with the HA app first, as they already communicate with your HA instance and dont require any additonal configuration
- Requires Tasker and KWGT to be installed and configured to communicate with each other, as well as a HA Blueprint configured. This initial configuration can take a bit of time, but once its complete, it will handle as many widgets as you'd like
- Support for Kustom "Komponents" coming soon. This will allow you to control your local device and your HA instance from the same widgets, or allow the creation of dials and levels


## (2) HAWGTS vs built-in HA app widget
You should consider using the built-in HA widget if you want simple but easy to configure controls. HAWGTS fills the niche of those looking for ultimate Android Launcher customization.
| HAWGTS                                | HA Default                             |
|---------------------------------------|----------------------------------------|
| Many touch commands for each device   | One touch command per widget           |
| Basic entity state information        | Advanced entity state information      |
| Complicated initial config            | No inital config - uses HA app         |
| Basic commands like on/off/brightness | Advanced commands and details in popup |
| Basic presets & advanced theme config | Basic presets                          |


## (3) Installation and Configuration
1. Install [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=en_CA) and [KWGT](https://play.google.com/store/apps/details?id=org.kustom.widget&hl=en_CA) on your device if you don't have them already. If you haven't used these apps before, open each one up and follow the configuration process. This involves asking for system permissions and setting working folders for the applications. In Tasker, choose to use the full version, not Tasky. For Kustom, make sure to create a new working folder for it to work in on newer Android versions in the settings
2. Get your API key from HA. It's located in your user settings under the security tab. At the bottom of the page is the option to generated a new long-lived access key. 
> WARNING: It's only displayed once, so keep it open in a tab until you've tested the connection!
3. Install the [KWGT Plugin](<URL>) for tasker. Clicking the link on your Android device should open it directly in Tasker. You'll be asked for the API key you just got and your HA address information. You have 3 options for your adress information:
    - [Nabu Casa](https://www.nabucasa.com) account - HA service offering a custom secure URL to your HA Server
    - Custom URL - For advanced users who have already set up a public URL
    - Local IP - You can use the address you use locally, however it will only work when your device connected to your WiFi network.
4. Next, log into Home Assistant and install the [HA Blueprint](<URL>), following the instructions from the Tasker developer. Use the same info you used in the taskernet setup and add your server information **VERIFY**. You will need to add all entities you want state data from to the HA Blueprint
5. Last, download the hawgts.kwgt file above and install it in < your-kwgt-folder >/widgets/hawgts.kwgt. This must be done after configuring KWGT for the first time!


## (3) Set up your widget Interface - Basic
> Installing a custom Android launcher is highly recommended for this widget. If using a default launcher, support will be limited. Common launchers include Apex, Nova and Niagara.

1. Create a new 2x4 KWGT widget on your home screen. Remove the padding from the widget if your launcher supports it (long-press)
2. Click on the widget to set it uo. This will open KWGT's preset selection mode. Choose the HAWGTS widget you placed in the Kustom Widget folder
3. You will be presented with the HAWGTS preset in KWGT. Hit the save button at the top to complete the widget setup. Note that you will have to hit the save button after making big changes to allow all the values to recalculate
5. Find the "Globals" tab in the bottom section and select it. This is where all of the widget configuration is done, it will be refered to as the "Settings" page for the rest of this readme
6. View the [Globals guide](/docs/globals.md) to walk through the settings and configure your widget how you like it. Return here after you've configured the widgets theme and template to continue setting up
7. Test your configuration and review the setup instructions if you run into problems

## (1) Set up your widget Interface - Advanced



## (2) Tips and tricks

#### (3) Changing settings not included in "Globals" menu
Using the Kustom editor, you can override and change all of the settings the widget presets for you. This can be done by going to the item you want to edit in the "Items" tab. If an option has a calculator or global icon to the right of it, it is pre-set. You can modify this configutration by touching the item, or remove it by selecting the checkbox and touching the calculator or globe icon in the top right of the editor. Once the preset configuration is removed, you can then edit the option.


#### (3) Custom Icons
Included with the widget is a large number of "Material" style icons, the same used in HA. You can change any icon by overriding the pre-configured icon as shown above and selecting the icon you'd like from the Kustom icon picker.


#### (2) Padding and alignment
- Some launchers allow you to remove additional padding to allow widgets to touch, and placement of widgets on half-grid locations. Look at the settings menu for your launcher to see if this option is available
- The widget allows many object size and padding overrides in the "theme" folder. See the [Globals guide](/docs/globals.md#global-table) for specifics
- To configure advanced alignment, use the `theme/advanced/autoalign` toggle in the advanced section of the settings to disable auto-sizing formulas and create custom values

