# HAWGTS Globals
The *Global* tab in the Kustom editor is configured as a "Settings Menu" for this widget. Always check the settings first before editing the widget interface directly. Below is a summary of the setting groups, followed by a chart of every setting available and information about it, in order. This is the same information available in the Global tab, just formatted in an easier-to-read way.

- Initial configuration
    - Set your device type first, then toggle through the templates to see which one works for your layout and launcher
    - Adjust basic widget settings
- `theme` - Set size, alignment and visual variables such as object sizes, padding, opacity, etc
- `theme/advanced` - Advanced alignment and padding options. Use only if needed
- `colors` - Override the colors set by whatever theme you're using. Remove the colors to go back to the theme defaults. Takes 3-8 digit RBG Hex color codes (#FFFFFFFF)
- `colors/colorgen` - A color generator that uses colors from your system theme where available and allows you to edit them to "Material" specifications or anything you want!
    - Advanced users: This generator uses the Kustom [si(syscx#)](https://docs.kustom.rocks/docs/reference/functions/si/) formula. [Color chart of what it generates]().
- `colors/coloredit` - A color editor that allows you to tweak the colors that your current widget theme uses, such as darkening the icon color or adding transparency. It can also extract colors to use from your wallpaper
    - Advanced users: Color editor uses the Kustom [si(wpcolor#)](https://docs.kustom.rocks/docs/reference/functions/si/) function to get wallpaper colors and [ce(sat/lum/comp)](https://docs.kustom.rocks/docs/reference/functions/ce/) to modify the colors
- `entities` - Set your entity here, and set up to 4 if you use a multi-device template
- `core` and `func` - Do not edit values in these folders, they run the backend. Advanced users can visit [JSON.md](json.md) to see the global variable data provided by `func`
- `json` - Widget databases - for advanced users only. Create your own theme or template for ultimate customization. See [JSON.md](json.md) for details.



### Global table
| Name                    | Description                                                    | Values,Cur | Editable |
|-------------------------|----------------------------------------------------------------|------------|----------|
| `readme`                | User Readme                                                    | Note       | No       |
| `settheme`              | Select the theme to use on this widget. Customize with "theme" | List       | Yes      |
|                         | *OneUI, Material, Basic*                                       | List       | Yes      |
| `devicetype`            | Select the type of device you'd like to control:               | List       | Yes      |
||*light, climate, tv, sound, security, lock, power, remote, appliance, home, electronics, maintence, custom*|List |
| `templateselect`        | Select your template                                           | List       | Yes      |
| `templatename`          | Debugging - Chosen Template name                               |            | Yes      |
| `templateinfo`          | Template Description via flow                                  |            | Yes      |
| `fitmoreobj`            | Disable widget object count restrictions                       | Toggle     | Yes      |
| `appiconvis`            | Set visbility of app-launcher object(s). On = Hidden           | Toggle     | Yes      |
| `appiconsize`           | Set the size of the small app-launcher object(s).              | 0-75, 32   | Yes      |
| `deviceopeneditor`      | Enable device icon click to open KWGT Editor                   | Toggle     | Yes      |
| **`theme`**             | **Override preset sizing and alignment variables**             | **Folder** | **Yes**  |
| > `themeinfo`           | User custom theme instructions                                 | Note       | No       |
| > `iconsize`            | (Preset to 80) - Size of the object icons                      | 20-200, 80 | Yes      |
| > `ringsize`            | Icon ring percentange/ratio (icon background:icon size)        | 0-100, 0   | Yes      |
| > `deviceiconsize`      | (Preset to 120) - Size of the Device Icon                      | 0-450, 120 | Yes      |
| > `deviceiconrotation`  | Rotate or flip the device icon on your Widget                  | List       | Yes      |
| > `darkmode`            | System dark mode on/off. Remove formula to override            | Toggle     | Yes      |
| > `shadow`              | Enable/disable shadow                                          | Toggle     | Yes      |
| > `bordersize`          | Size of the border                                             | 0-50       | Yes      |
| > `opacity`             | Opacity of the widget background                               | 0-100      | Yes      |
| > `cornerradius`        | Radius of the rounded corners                                  | 0-100      | Yes      |
| > `ringshape`           | Shape of the rings behind the objects                          | Kus. Shape | Yes      |
| > `objpaddinghor`       | Object padding across each row                                 | 0-50       | Yes      |
| > `objpaddingver`       | Object padding between the two rows                            | 0-50       | Yes      |
| > `objpaddingside`      | Padding from the edge of the box to the first icon             | 0-50       | Yes      |
| > `verticalpadding`     | Widget vertical padding                                        | 0-50       | Yes      |
| > `horizontalpadding`   | Widget horizontal padding                                      | 0-50       | Yes      |
| **`theme/advanced`**    | **Advanced alignment settings**                                | **Folder** | **Yes**  |
| >> `box1sq`             | Make Box 1 a perfect square                                    | Toggle     | Yes      |
| >> `boxtoggle`          | Set the order of the boxes on your widget                      | Toggle     | Yes      |
| >> `autoalign`          | Override the following widget alignments                       | Toggle     | Yes      |
| >> `widgetorient`       | Set horizontal or vertical orientation of widget               | Toggle     | Yes      |
| >> `box1pos`            | Set position of Box 1                                          | List       | Yes      |
| >> `box1size`           | Override Box 1 size set from Flow. Disable flow                | 0-720      | Yes      |
| **`colors`**            | **Override preset theme colors**                               | **Folder** | **Yes**  |
| > `themeinfo`           | User custom theme instructions                                 | Note       | No       |
| > `reset`               | Reset all colors in folder                                     | Toggle     | Yes      |
| > `back1`               | Background color 1                                             | Hex Color  | Yes      |
| > `back2`               | Background color 2                                             | Hex Color  | Yes      |
| > `objiconon`           | Object Icon - State on                                         | Hex Color  | Yes      |
| > `objiconoff`          | Object Icon - State off                                        | Hex Color  | Yes      |
| > `objringon`           | Object Ring - State on                                         | Hex Color  | Yes      |
| > `objringoff`          | Object Ring - State off                                        | Hex Color  | Yes      |
| > `border`              | Border color                                                   | Hex Color  | Yes      |
| **`colors/colorgen`**   | **Generate colors for your widget from the system**            | **Folder** | **Yes**  |
| >> `syscolorset`        | Select a color tone                                            | List       | Yes      |
|                         | *Accent1-3, Neutral1-3*                                        | List       | Yes      |
| >> `adjcolor`           | Strength of color tone - 0 = White, 100 = Black                | 0-100      | Yes      |
| >> `opacity`            | Opacity Adjustment - 0 = Transparent, 255 = Solid              | 0-255      | Yes      |
| >> `output`             | Result of your custom color. Copy to "colors" folder           | Hex Color  | Yes      |
| **`colors/coloreditor`**| **Edit colors for your widget from the theme or wallpaper**    | **Folder** | **Yes**  |
| >> `editcolor`          | Select the color to edit                                       | List       | Yes      |
|                         | *Back1, Back2, Icon Off/On, Ring Off/On, Border, Wallpaper 1-3*| List       | Yes      |
| >> `wallcolorset`       | Select a color filter                                          | List       | Yes      |
|                         | *Saturation, Brightness, Complimentary color*                  | List       | Yes      |
| >> `adjcolor`           | Strength of color filter                                       | 0-100      | Yes      |
| >> `opacity`            | Opacity Adjustment - 0 = Transparent, 255 = Solid              | 0-255      | Yes      |
| >> `output`             | Result of your custom color. Copy to "colors" folder           | Hex Color  | Yes      |
| **`entities`**          | **Home Assistant entity names**                                | **Folder** | **Yes**  |
| > `entitynote`          | User entity instructions                                       | Note       | No       |
| > `entity1`             | Device 1 Entity ID                                             | entity_id  | Yes      |
| > `entity2`             | Device 2 Entity ID                                             | entity_id  | Yes      |
| > `entity3`             | Device 3 Entity ID                                             | entity_id  | Yes      |
| > `entity4`             | Device 4 Entity ID                                             | entity_id  | Yes      |
| > `entity5`             | Device 5 Entity ID                                             | entity_id  | Yes      |
| > `entity6`             | Device 6 Entity ID                                             | entity_id  | Yes      |
| **`core`**              | **Core**                                                       | **Folder** | **No**   |
| > `curpage`             | Current page for paged templates                               | X          | No       |
| **`core/size`**         | **Widget size**                                                | **Folder** | **No**   |
| >> `wgtwidth`           | Widget width                                                   | X          | No       |
| >> `wgtheight`          | Widget height                                                  | X          | No       |
| >> `box2width`          | Width of Box 2                                                 | X          | No       |
| >> `box2height`         | Height of Box 2                                                | X          | No       |
| >> `box2maxobj`         | Number of objects that will fit in Box 2 (per row)             | X          | No       |
| **`core/tasker`**       | **Tasker**                                                     | **Folder** | **No**   |
| >> `fromtasker`         | State information sent from tasker to apply to "json/device"   | X          | No       |
| >> `temp`               | Temp storage for Tasker > KWGT flow                            | X          | No       |
| **`core/status`**       | **Status System**                                              | **Folder** | **No**   |
| >> `currentstatus`      | Current status or last status displayed                        | X          | No       |
| >> `setstatus`          | Send error id (##) to this to activate error                   | X          | No       |
| >> `statustimer`        | Time to display Status message                                 | X          | No       |
| **`func`**              | **Global functions**                                           | **Folder** | **No**   |
| > `jsonmain`            | Load JSON data for following 3 global variables                | X          | No       |
| > `jsonstate`           | Get the current state of devices. Call with ".box1.row2.0"     | X          | No       |
| > `jsonobj`             | Get object information. Call with ".box1.row2.0"               | X          | No       |
| > `jsontemp`            | Get template information. Call with ".box1.row2.0"             | X          | No       |
| > `alignment`           | Variables for use in alignment globals and objects             | X          | No       |
| > `theme`               | Variables for use in theme globals and objects                 | X          | No       |
| > `themecolors`         | Variables for color outputs                                    | X          | No       |
| > `devjsonrem`          | Device State - Remove entity                                   | X          | No       |
| > `devjsonadd`          | Device State - Add newlines and spaces to JSON                 | X          | No       |
| > `devjsonindent`       | Device State - Add indentation to JSON                         | X          | No       |
| > `devjsonnew`          | Device State - Add new entity to bottom of list                | X          | No       |
| **`json`**              | **JSON databases for widget**                                  | **Folder** | **Yes**  |
| > `device`              | Device states database                                         | JSON       | No       |
| > `theme`               | Theme database                                                 | JSON       | Advanced |
| > `template`            | Template database                                              | JSON       | Advanced |
| > `status`              | Status and error database                                      | JSON       | No       |
|  `setupmode`            | Debugging - Enable showing setup data                          |            | Yes      |
|                         |                                                                |            |          |

Legend: ">" indicates the folder tree level, "**Bold**" indicates folders, `global` indicates global name

