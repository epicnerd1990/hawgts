# Advanced widget configuration

This page contains information that will help you to contribute to the project or create advanced widgets that go beyond the default configuration

## Useful information and links
- Kustom and KWGT education
    - Kustom and "Kode" offical [documentation](https://docs.kustom.rocks)
    - A [detailed guide](https://theothertored.github.io/kode-guide/) to understanding "Kode" by contributer [tored](https://github.com/theothertored)
    - Kustom [discord group](https://kustom.rocks/discord) for Kustom and Kode support and tutorials
- Tasker education
    - Official [userguide](https://tasker.joaoapps.com/userguide/en/index.html) that matches `?` info in Tasker
- See [globals reference](/docs/globals.md) for what each **Global**/setting does


## Create a custom theme
 Instructions for creating a custom theme
 1. Create a theme JSON. If you're using a desktop editor, [theme.json](theme.json) is identical to `json/theme` included in the widget
> Use the color generator in the settings or see the [Kustom reference for si()](https://docs.kustom.rocks/docs/reference/functions/si/) for generated colors
 2. Copy the theme JSON into the global `json/theme`. Only copy your "ThemeName" array and paste it into the blank space at the bottom of the JSON (leaving the remaining brackets below the insertation)
 3. Edit the `settheme` global and append the new theme name to the end. This must match the name used in the JSON exactly!
 `0##OneUI,1##Material,2##Basic` > `0##OneUI,1##Material,2##Basic,3##ThemeName`
 4. Save everything, then select your custom theme from the `settheme` menu
 5. Export modified widget to use your changes on multiple widgets

> If you're an advanced user and want to add custom options to the JSON, edit `func/theme` to add support for new JSON keys and features and see [theme information](#theme-and-color-functions)

### Theme syntax reference
```
{
  "ThemeName": {
    "border_width": 0,
    "corner_radius": 0,
    "pad_widget": 0,          // Padding around outside of widget
    "pad_obj": 0,             // Padding between objects
    "pad_sides": 0,           // Padding from box edge to first object
    "ring_shape": "shape",    // Insert any Kustom shape value here
    "ring_percent": 0,        // Size - percentage of the ring compared to the icon
    "dev_ico_ring": 0,        // Padding around outside of widget
    "wgt_opacity": 0,
    "shadow": 0,
    "colors": {
      "light": {              // Either a KWGT si() command or a Hex color value
        "back_1": "",         // syscn1,90 or #FFFFFF
        "back_2": "",
        "obj_icon_off": "",
        "obj_ring_off": "",
        "obj_icon_on": "",
        "obj_ring_on": "",
        "border": ""
      },
      "dark": {               // Either a KWGT si() command or a Hex color value
        "back_1": "",         // sysca3,20 or #000000
        "back_2": "",
        "obj_icon_off": "",
        "obj_ring_off": "",
        "obj_icon_on": "",
        "obj_ring_on": "",
        "border": ""
      }
    }
  }
}
```


## Custom template
Templates are used to indicate what type of interface to present, based on the device chosen and your prefered style. They control icons, layouts, rows and commands send to HA. See the JSON below for detailed specifications. Instructions for creating a custom template:
> A desktop editor is highly reccommended for editing large amounts of code like this. See [editing Kode from your desktop](https://docs.kustom.rocks/docs/tutorials/Edit_kode_desktop/) for more information

1. Open the template JSON. It can be found in the global `json/template` or [templates.json](templates.json) (these are identical)
2. Find an existing template that is similar to what you want and make a copy of the array. Assign "device_type" to `custom` and name templates accordingly (see below reference JSON)
3. Append your theme JSON into the global `json/theme`. Only copy your template array and paste it into the blank space at the bottom of the JSON (leaving the remaining brackets below your insertation)
3. Save everything, then select `custom` template from `devicetype`
4. Select the Template number in `templateselect` that matches the name you assigned
5. Save everything again

> If you're an advanced user and want to add custom options to the JSON, edit `func/template` to add support for new JSON keys and features and reference [function information](#template-and-object-information) below

### Template syntax reference
```
{
  "device_type": {                                // Device category
    "objects": {                                  // "objects" - list of controls/commands
      "control_namea": {                          // Name of control/command
        "icon": "icon-name",                      // Icon name from hawgts icon pack
        "type": "action,page",                    // Control - "action" > Tasker, future "template"
        "onstate": "#>#,on,off,page#",            // Matches "active" Device state from HA or `curpage`
        "action": "light,on,off,toggle,template", // Indicate which action to run when activated
        "cmd": "",                                // Used internally for light control (brightness and color)
        "data": "1"                               // Internal light control data or HA template command
      },
      "control_nameb": {                          // Name of control/command
      }
    },
    "light1": {                                   // Device category + Template#
      "name": "Light - 2-4 Page/Device, 2 Row",   // e.g., "Room Light Controls"
      "note": "Testing Template V3",              // Internal notes, remove for V1
      "icon": "icon-name",                        // Icon name from hawgts icon pack
      "page": 1,                                  // 0 = no paging    1 = use pages (page1, page2, …)
      "box1_size": 2,                             // 0 = Hidden,      1 = 1 "column",   2 = 2 "columns"
      "row_count": 2,                             // 0 = Hidden,      1 = 1 "row",      2 = 2 "rows"
      "device_icon_pos": 1,                       // 1 = Corner,      2 = Box 1,        3 = Hidden
      "device_count": "2-4",                      // 1 = one,       2-4 = Paging,     1-2 = ??
      "page1": {                                  // If page = 0, no "page#" key
        "box1": {                                 // Remove Box 1 when not applicable
          "row1": {                               // Max 2 per row in Box 1
            "0": "controlnamea",
            "1": "controlnameb"
          },
          "row2": {                               // Remove Row 2 when not applicable
            "0": "controlnamec",
            "1": "controlnamed"
          }
        },
        "box2": {
          "row1": {
            "0": "controlnamee",
            "1": "controlnamef",
            "2": "controlnameg"
          },
          "row2": {                               // Remove Row 2 when not applicable
            "0": "controlnameh",
            "1": "controlnamei",
            "2": "controlnamej",
            "3": "controlnamek"
          }
        }
      },
      "page2": {
      }
    },
    "light2": {
      "name": "Light - 0 Page, 1 Device, 2 Row",
      "note": "Testing Template V3",
      "icon": "material-ico-wb-incandescent-mod",
      "page": 0,
      "box1_size": 2,
      "row_count": 2,
      "device_icon_pos": 2,
      "device_count": 1,
      "box2": {
          "row1": {
            "0": "power",
            "1": "controlapp",
            "2": "color"
          },
          "row2": {
            "0": "off",
            "1": "low",
            "2": "med",
            "3": "high"
          }
      }
    }
  }
}
```


## (2) KWGT \> Tasker
Currently, HAWGTS uses Taskers [secondary app uri](https://www.youtube.com/watch?v=37afVpb8R_8) interface to send commands. It uses a **Flow** to process the touch command of the widget, then send the URI with the information to Tasker.

Syntax:  `tasker://secondary?service_call=var&entity_id=var&parameters=var:100`  
Example: `tasker://secondary?service_call=light&entity_id=light.bedroom_lights&parameters=brightness:125`

- `service_call`: light, on, off, toggle, template, custom. [Set by `#obaction`](#template-and-object-information)
- `entity_id`: entity to control
- `parameters`: Optional. Only used for following service calls: light, template, custom
    - `brightness`: "light" only, 0-255 range
    - `template`: Send a HA template command directly from widget
    - `custom`: Send a custom command to Tasker for any use


To use the `custom` service call, you will need to configure Tasker directly. Open the Tasker HAWGTS plugin you installed during your setup. Inside the task labelled "command", there is a space for you to insert custom code to run. You can also include additional variables to process in `parameters` if needed

To use a HA template, insert the template into [`#obdata`](#template-and-object-information) when building your template

## (1) Tasker > HA

The HAWGTS tasker plugin is a modified version of [Taskers HA plugin](https://tasker.joaoapps.com/home_automation.html). The HAWGTS version removes unneeded sections and adds HAWGTS-specific features in.


## Making modifications to HAWGTS
If you wish to contribute or modify the widget, the following information will help and allow you to tap into the widget backend. Please also view the documents included in [/codesnippets](../codesnippets/) as they contain all the core widget code and specifications. They're designed to allow code to be modified in an IDE like VSCODE. The below information is basic and requires knowledge of Kustom and Tasker. See [useful information](#useful-information) at the top for links to Kode and Tasker documentation and tutorials.

### Basic Kustom notes
As a scripting language, Kustom "Kode" doesn't support custom functions globally or any form of function import. However, one can create their own "global functions" by declaring variables inside of a global variable, and then calling that variable in the same way one would import a class into code. In Kode, there are global variables `$gv(varName)$` and local variables `$lv(varName, value)$`. Global variables (from your **Globals** tab) cannot be set inside the code, but local variables can. `#varName` will display the value of a local variable. These can be used anywhere in the widget. HAWGTS contains some "Global Functions" that you can use to get data from the theme, template or device databases. Kode can be used in text and formula fields, as long as its surrounded by `$`.

Another main feature utilized in Kustom for this widget is **Flows**, which are located on the tab next to **Globals**. Flows function in a way similar to Tasker "actions". These are automated groups of functions that are preformed in order. They have the ability to call and write variables, download data from a site and output data in many ways. These are not documented in the same way as the [**Globals**](/docs/globals.md) are, but are pretty easy to understand if you view them in the editor.

### Theme and color functions
Sample: `$gv(func/theme)$$#border$` = border color
- See [theme JSON](#create-a-custom-theme) for key and variable information
- Append "t" to function name to force JSON data or "g" to force global data. Otherwise, use normal priority (eg `#border` or `#tborder`)

| `theme.json`                     |`func/theme`        |
|----------------------------------|--------------------|
| .theme.border_width              | #border            |
| .theme.corner_radius             | #radius            |
| .theme.pad_widget                | #padwgtver         |
| .theme.pad_widget                | #padwgthor         |
| .theme.pad_obj                   | #padobjhor         |
| .theme.pad_obj                   | #padobjver         |
| .theme.pad_sides                 | #padobjside        |
| .theme.ring_shape                | #ringsha           |
| .theme.ring_percent              | #objsize           |
| .theme.ring_random               | #objsize           |
| .theme.wgt_opacity               | #wgtopa            |
| .theme.shadow                    | #shadow            |
|                                  | `func/themecolors` |
| .theme.colors.(d/l).back_1       | #back1             |
| .theme.colors.(d/l).back_2       | #back2             |
| .theme.colors.(d/l).obj_icon_on  | #objiconon         |
| .theme.colors.(d/l).obj_icon_off | #objiconoff        |
| .theme.colors.(d/l).obj_ring_on  | #objringon         |
| .theme.colors.(d/l).obj_ring_off | #objringoff        |
| .theme.colors.(d/l).border       | #border            |

### Template and object information
Sample: `$lv(posid, ".box2.row1.0")$$gv(func/jsonobj)$$#obicon$` = icon for this object
- `func/jsontemp` returns values specific to the chosen template
- `func/jsonobj` returns values specific each object. Must set `#posid` (as per sample) to get the data from the correct object before calling the global variable

| `template.json`                |`func/jsontemp` |
|--------------------------------|----------------|
| .(dev).(type1).name            | #tname         |
| .(dev).(type1).icon            | #ticon         |
| .(dev).(type1).box1_size       | #tbox1         |
| .(dev).(type1).row_count       | #trowct        |
| .(dev).(type1).device_icon_pos | #ticopos       |
| .(dev).(type1).device_count    | #tdevct        |
|                                | `func/jsonobj` |
| \<object exists\>              | #obid          |
| .(dev).objects.type            | #obtype        |
| .(dev).objects.icon            | #obicon        |
| .(dev).objects.action          | #obaction      |
| .(dev).objects.cmd             | #obcmd         |
| .(dev).objects.data            | #obdata        |
| \<#obcmd and/or #obdata\>      | #obparam       |

### Current state data
Sample: `$lv(posid, ".box2.row1.0")$$gv(func/jsonstate)$$#stoutput$`
- `#stoutput` returns a simple 0 or 1 indicating if the device state matches the "onstate" value in the template JSON. Must set `#posid` (as per sample) to get the data from the correct object before calling the global variable

