# Modifying HAWGTS JSON
Use these instructions to create custom themes and templates for your widget. Refer to [globals.md](globals.md) for global names and values

## Custom theme
 Instructions for creating a custom theme
 1. Create a theme JSON using the data below
    - Use the color generator in the settings or see the [Kustom Reference for si()](https://docs.kustom.rocks/docs/reference/functions/si/) for generated colors
 2. Append the new theme JSON into the global `json/theme`, being careful not to interfere with the existing themes
 3. Edit `settheme` and append your theme name to the end. It must match the name used in the JSON exactly!
 `0##OneUI,1##Material,2##Basic` > `0##OneUI,1##Material,2##Basic,3##ThemeName`
 4. Save everything and select your custom theme from `settheme`
 5. Export modified widget to use your changes on multiple widgets

> If you're advanced and want to add custom options to the JSON, edit `func/theme` to add support for new JSON keys and features

#### Theme syntax reference
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

#### Working example of the "Material" theme
```
{
  "Material": {
    "border_width": 0,
    "corner_radius": 2,
    "pad_widget": 10,
    "pad_obj": 10,
    "pad_sides": 16,
    "ring_shape": "CIRCLE",
    "ring_percent": 25,
    "dev_ico_ring": 1,
    "wgt_opacity": 50,
    "shadow": 1,
    "colors": {
      "light": {
        "back_1": "syscn1, 90",
        "back_2": "syscn2, 90",
        "obj_icon_off": "sysca2, 30",
        "obj_ring_off": "sysca2, 90",
        "obj_icon_on": "sysca1, 100",
        "obj_ring_on": "sysca1, 40",
        "border": ""
      },
      "dark": {
        "back_1": "syscn1, 22",
        "back_2": "syscn2, 22",
        "obj_icon_off": "sysca2, 90",
        "obj_ring_off": "sysca2, 30",
        "obj_icon_on": "sysca1, 20",
        "obj_ring_on": "sysca1, 80",
        "border": ""
      }
    }
  }
}
```

## Custom template
Instructions for creating a custom template
1. Create a template JSON using the data below. Name it `custom` and `custom1-6`
2. Append the new template JSON into the global `json/template`, being careful not to interfere with the existing templates
3. Save everything and select `custom` template from `devicetype`
4. Select the Template number in `templateselect` that matches your
5. Save everything again

> If you're advanced and want to add custom options to the JSON, edit `func/template` to add support for new JSON keys and features

#### Template syntax reference
```
{
  "device_type": {                                // Device category
    "objects": {                                  // "objects" - list of controls/commands
      "control_namea": {                          // Name of control/command
        "icon": "icon-name",                      // Icon name from hawgts icon pack
        "type": "action,page",                    // Type of control - Internal "page" or external "action"
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

#### Working example of a "light" device with 3 templates
```
{
  "light": {
    "objects": {
      "bedroom": {
        "icon": "mdi-room-king-bed",
        "type": "page",
        "onstate": "page1",
        "action": "page",
        "cmd": "",
        "data": "1"
      },
      "office": {
        "icon": "mdi-room-desk",
        "type": "page",
        "onstate": "page2",
        "action": "page",
        "cmd": "",
        "data": "2"
      },
      "livingroom": {
        "icon": "mdi-room-sofa-single-outline",
        "type": "page",
        "onstate": "page3",
        "action": "page",
        "cmd": "",
        "data": "3"
      },
      "foyer": {
        "icon": "mdi-room-coat-rack",
        "type": "page",
        "onstate": "page4",
        "action": "page",
        "cmd": "",
        "data": "4"
      },
      "power": {
        "icon": "mdi-power",
        "type": "action",
        "onstate": "",
        "action": "toggle",
        "cmd": "",
        "data": ""
      },
      "controlapp": {
        "icon": "app-home-assistant",
        "type": "action",
        "onstate": "",
        "action": "",
        "cmd": "",
        "data": ""
      },
      "color": {
        "icon": "mdi-palette",
        "type": "action",
        "onstate": "",
        "action": "color",
        "cmd": "color",
        "data": "#FFFFFF"
      },
      "off": {
        "icon": "mdi-lightbulb-off-outline",
        "type": "action",
        "onstate": "off",
        "action": "off",
        "cmd": "",
        "data": ""
      },
      "low": {
        "icon": "mdi-lightbulb-on-30",
        "type": "action",
        "onstate": "1>75",
        "action": "light",
        "cmd": "brightness",
        "data": "74"
      },
      "med": {
        "icon": "mdi-lightbulb-on-60",
        "type": "action",
        "onstate": "76>200",
        "action": "light",
        "cmd": "brightness",
        "data": "150"
      },
      "high": {
        "icon": "mdi-lightbulb-on",
        "type": "action",
        "onstate": "200>255",
        "action": "light",
        "cmd": "brightness",
        "data": "255"
      },
      "on": {
        "icon": "mdi-lightbulb-on",
        "type": "action",
        "onstate": "on",
        "action": "light",
        "cmd": "brightness",
        "data": "255"
      }
    },
    "light1": {
      "name": "Light - 2-4 Page/Device, 2 Row",
      "note": "Testing Template V3",
      "icon": "material-ico-wb-incandescent-mod",
      "page": 1,
      "box1_size": 2,
      "row_count": 2,
      "device_icon_pos": 1,
      "device_count": "2-4",
      "page1": {
        "box1": {
          "row1": {
            "0": "bedroom",
            "1": "office"
          },
          "row2": {
            "0": "livingroom",
            "1": "foyer"
          }
        },
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
      },
      "page2": {
        "box1": {
          "row1": {
            "0": "bedroom",
            "1": "office"
          },
          "row2": {
            "0": "livingroom",
            "1": "foyer"
          }
        },
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
      },
      "page3": {
        "box1": {
          "row1": {
            "0": "bedroom",
            "1": "office"
          },
          "row2": {
            "0": "livingroom",
            "1": "foyer"
          }
        },
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
      },
      "page4": {
        "box1": {
          "row1": {
            "0": "bedroom",
            "1": "office"
          },
          "row2": {
            "0": "livingroom",
            "1": "foyer"
          }
        },
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
    },
    "light3": {
      "name": "Light - 0 page, 1 Device, 1 Row",
      "note": "Testing Template V3",
      "icon": "material-ico-wb-incandescent-mod",
      "page": 0,
      "box1_size": 2,
      "row_count": 1,
      "device_icon_pos": 1,
      "device_count": 1,
      "box1": {
          "row1": {
            "0": "controlapp",
            "1": "color"
          }
      },
      "box2": {
          "row1": {
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
