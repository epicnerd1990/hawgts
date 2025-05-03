# This file is meant for the developer for reference only and only for viewing in the code editor, not a rendering preview. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

###### #####################################
###### TO-DO to get to V1 release
###### #####################################
This is a floating list used to keep track of where everything is at while developing the core of the widget.
It contains lists, resources, plans and data lists


###### Legend for to-do list
- This task is done in KWGT Editor or both
- `This task is done in preset.json`
- ~~This task is complete~~
- `globalname` `localvarname` `flowname`
- "jsonkeyname"
- **Notes** **10% complete**


###### Shortcuts and scripts for KWGT use
- "Ctrl+Shift+x": Copy selected code with lines and whitespace removed
- "Ctrl+Shift+c": Copy selected code with lines and whitespace removed.
                  Additionally escapes for JSON - for copying into preset.json

- Bash:
    - "kwgt_check":   JSON-lint preset.json
    - "kwgt_globals": Output global list in the order they appear in the JSON. Terminal Output
    - "kwgt_pkg":     (below) Cleans and checks preset.json, packages it and sends to dev device
```
kwgt_pkg() {
	# Use: `kwgt_pkg /folder/preset.json` or kwgt_pkg for default location
	if [ -z "$1" ]; then
		presetpath="/common/Projects/Android/Kustom/hawgts/widgets/DCW/preset.json"
	else
		presetpath=$1
	fi
	presetdir=$(dirname "$presetpath")

	# Check the JSON using JSONLINT
	jsonlint "$presetpath"
	echo "Passed JSON test if JSON it output above"

	# Reorder the global list to ensure correct rendering in KWGT editor
	jq '
	.preset_root.globals_list |= (
		to_entries
		| [ .[] | .value.index = null | . ]   # clear old indexes
		| reduce range(0; length) as $i (
			.;
			.[$i].value.index = ($i + 1)
		)
		| from_entries
	)
	' "$presetpath" >"${presetpath}.tmp" && mv "${presetpath}.tmp" "$presetpath"

	# Get the version number from preset.json
	ver=$(jq -r '.preset_info.title' "$presetpath")

	# Create a new *.kwgt file to load our preset into
	\cp "${presetdir}/DCW.kwgt" "${presetdir}/DCW_${ver}.kwgt"
	echo "Re-order Global list, get version number and create DCW_${ver}.kwgt"

	# Copy preset.json into blank widget template and make file available in the widget root
	zip -D -j "${presetdir}/DCW_${ver}.kwgt" "$presetpath"
	echo "Added preset.json to DCW_${ver}.kwgt"

	# Send new *.kwgt file to testing device using KDE Connect
	kdeconnect-cli --share "${presetdir}/DCW_${ver}.kwgt" --device 2a78f8af_165d_4d8a_8735_7e86daeb24d4
	echo "Added preset.json to DCW_${ver}.kwgt. Sending file to testing device and archiving"

	# Copy built *kwgt file to an archive folder
	\cp "${presetdir}/DCW_${ver}.kwgt" "${presetdir}/../../../All Widgets/"

	# Remove old copy from $presetdir
	\rm "${presetdir}/DCW_${ver}.kwgt"
}
```

- Kode:
    - //x22 = tc(utf) for many characters like "':;()<>
UTF Hex       REGEX                  KWGT
7B {          Escape literal with \, no utf
7D }          Escape literal with \, no utf
5B [          Escape literal with \, no utf
5D ]          Escape literal with \, no utf
22 "          Escape literal with \, use utf
24 $$         Double,                Double?
0A New Line   \n,                    use utf


###### #####################################
###### TO-DO - V1 General Tasks
###### #####################################
- Complete Readme
    - Readme = basic, wiki = advanced use?
- Continue to work on Codex AI programming
- Look at YAML for lists?
- Create Template database
    - Specific templates to make:
        - "House Security" template
    - Specific Komponents to make:
        - Battery - Complete
            - Setup battery in different sizes/modes - Find way to scale?
        - Create "Komponent" Templates for different modes
- Fix and set up favourite formulas in KWGT before starting Media Control Widget **50%**

###### #####################################
###### TO-DO - Features to complete for V1
###### #####################################

- Finish implementing Templates V3 and alignment system cleanup **90%**
    - Complete templates for all devices **10%**
    - ~~Fix remaining glitches and alignment issues~~ **100%**
    - Add support for battery items?
    - Add support for custom dials and displays - or just make these komponents?
- JSON System completion **70%**
    - Look into file-based JSON template
    - Allow user to select their own custom JSON using flow and file picker? Flow could remove whitespace and write to `json/template`?
    - Could also document the template syntax on github wiki and users can append to the template json for custom layouts.
        - Provide basic samples and a copy of template.json for reference
    - Could use "Send Data" option in flows or WebGet()
    - File permissions?
- Confirm icon pack is up to date with latest icon list
- Group configuration options in folders/subfolders more
- KWGT > TASKER - Create "custom" service call **0%**
    - Use HA templates for simplicity?
- Set up and build Tasker plugin **0%**
    - Simply merge everything into one downloadable group?
    - Tasker - "toggle" error
    - Make state updates more effective and work in more situations
        - The state does not update when lights are turned from one brightness to another
        - Is this because of the way the Tasker HA blueprint works?
        - Perhaps use Tasker to regularly check the current state of connected entities and update widget accordingly?

- Should have a flow for each global thats updated with content from the template that is ONLY activated when the template changes. This will maintain user overrides of template settings until they change the template, which will then reset everything.


###### #####################################
###### TO-DO - Current
###### #####################################
- `templateinfo` Flow
    - Create a for loop for `ntwgtsize`?
    - Connect to status system?
- Complete all versions of "light" template and maybe start "climate"

- Add "Setup" button that is in Box 2 when first opened. Clicking it goes to the github readme
- Device and App icons
    - Confirm all code works
    - Device Icon: click to open editor or open Home Assistant directly to your device
    - Show/hide app icon, 1st = HA, 2nd = Custom
    - Look at "Shortcuts" next to "Flows" in editor
- "Status" system completion **10%**
    - Or use status bar? KWGT code complete, send string to kwgt br: "error" once status is working
    - Use following errors:
        - HA Not running
        - Cannot get status update
        - State not returned after change
        - JSON Error
        - Tasker Error
- Go over color system - Create "themes"
    - Implement Tasker color picker?
    - Create "themes" like One UI, material, HA card, etc. Template independant
    - Store data in JSON
    - Possibly re-do based on community resources
    - Possibly re-do based on UI types and Tasker setup interface/color picker
    - Utilize lists to make the UI user-friendly?
    - Flows temporarily disabled in 6.3
- Theme system notes:
    - User Options
        > System dark mode - write to global, override with toggle?
        > Choose theme style
            > If not "Custom", set all attributes
            > If "Custom", use existing globals to configure (Make --3 "theme globals"?)
            - Do not allow custom attributes to be overwritten by flows unless user selects factory theme
    - Existing code:
        > User select color src (wallpaper, material, etc)
        > User select color accent `$si(sysca1, 50)$`
        > User select color filter `$ce(#FF0000, sat, 50)$`
        > User set filter strength and mask
        > User overide color/manual input
        > User set color transparency `$ce(#FF0000, alpha, 50)$`
    - New code: `theme/`
        > User selects theme style
        - Custom options `theme/colors`
            > User select color accent (allow adjustment of sysca# tone) and filter
            > User set filter strength and mask
            > User overide color/manual input for all colors
            > User set color transparency
            > User set border size and on/off
            > User set color transparency
    - Dark/light mode?
        - `$si(darkmode)$` System Dark mode
        - `$si(darkwp)$`   Dark colored wallpaper
        - `$si(wpcolor1)$` Primary wallpaper color extracted
        - `$si(wpcolor2)$` Secondary wallpaper color extracted
        - `$si(sysca1, 50)$` Material/OneUI Accent  #1 @ 50% tone (0=white, 100=black)
        - `$si(sysca2, 80)$` Material/OneUI Accent  #2 @ 80% tone
        - `$si(syscn1, 20)$` Material/OneUI Neutral #1 @ 50% tone
    - Colors:
        - Primary: Box 1, State active obj, device icon ball color, 
        - Secondary: Box 2, state off obj, 
        - Neutral: Box border, Box 1 + 2, , device icon color, object icon color


    - Use existing code where possible but remove duplicates - no need for color1 and color2
    - Look into removing `theme/color#fil2`, `theme/color#fil1`, `theme/color#adj`
    - `theme/settheme` > `theme/settheme`
    - Merge `theme/color1sync` & `theme/color2sync`
    - Merge `theme/color1alph` & `theme/color2alph`
    - Create `theme/color#` x 4, paired to theme
    - Write theme json code in templates.md
    - Look at material color spec to understand color tone in ".OneUI.colors{}"

__Attributes         One UI                 HA/Material            Basic                 Custom__
Color 1 (Primary):   Material Accent #1     Material Accent #1     Wallpaper             Color Picker
Color 2 (Secondary):.Material Accent #2     Material Accent #2     Wallpaper             Color Picker
Color 3 (Contrast):  Material Neutral #3    Material Neutral #3    White/Black           Color Picker
Border width:        1dp                    0                      0                     `theme/bordersize`
Padding - Outside:   16dp                   10dp                   10kpx                 `horizontalpadding`, `verticalpadding`
Padding - Icons:     4dp                                                                 `objpadding`, `objpadding2`
Padding - Edges:     16dp                   16dp                   10kpx                 `objpaddingbox2`
Corner Radius:                                                                           `cornerradius`
Icon Color:          All icons pastel       Only active coloredd   White/Black           `color/color1`, `color/color2`
Device Icon border:  None                   Corner ball            None                  New

> Globals to move into `theme`
`theme/color1`, `theme/color2`, `theme/*`, `horizontalpadding`, `verticalpadding`, `objpadding`, `objpadding2`,
`objpaddingbox2`, `cornerradius`,

> Globals in theme/
`theme/settheme`, `theme/color2sel`, `theme/color1sysc`, `theme/color2sysc`, `theme/color1fil1`, `theme/color2fil1`,
`theme/color1adj`, `theme/color2adj`, `theme/color1fil2`, `theme/color2fil2`, `theme/color1pick`, `theme/color1pick`,
`theme/color1alph`, `theme/color1alph`, `theme/color1fin`, `theme/color2fin`

`theme/colico`, `theme/colicoon`
`theme/border`, `theme/bordercolor`

```
// Theme JSON database

// See `samples.json` for active building
```


###### #####################################
###### Completed Tasks
###### #####################################
- Changing state now only works after 2 taps, not one. Look at KWGT > Tasker flow.
- `"lock" in devicetype > "security"`



###### #####################################
###### Feature Wishlist (V1+)
###### #####################################

- Create a kustom flow that formats json from no formatting to human readable **Universally**
    - Use for loops and awareness of {} level, arrays and commas.
    - Wipe all whitespace first to get json to consistant state
    - Provide custom HA calls for lights and potentially other devices? Or use templates instead?
- Create a flow loop that writes new lv() - Can reduce stacks of defined lv()
- Make widget a Komponent that can be added to a KWLP wallpaper
- Integrate all options into the globals.
- Setup Mode
    - Set up "setupmode" text layout better and add a few different text options (sizing, json, etc)
    - Group in overlay groups and use visibility to display each one
    - Set up a note for visibility like the ones inside templates
- Add Row 3
- Change tasker/cmd data to process json instead of "&" syntax?
    - Can write template json in KWGT to send to tasker
- Integerate TV templates into widget once TV Remote is complete


###### #####################################
###### Dev version > Release version
###### #####################################

- Move service global folders to bottom from top
- Remove debugging, setup data, etc globals and objects completely
- Remove Device Icon click to open editor option?
- Create a "setup your widget by going to globals" textbox with basic setup info
- Remove "notes" key in template JSON


###### #####################################
###### All Globals
###### #####################################
6.0:                            4.9:                    Task:                                       Notes:
core
    core/size
        core/size/wgtwidth      generate/wgtwidth       Flow Generated Width
        core/size/wgtheight     generate/wgtheigh       Flow Generated Height
        core/size/box2width     generate/box2wid        Flow Generated Box 2 Width
        core/size/box2height    generate/box2hei        Flow Generated Box 2 Height
        core/size/box1col                               Box 1 icon count                            Review
                                generate/box1hei                                                    DEPRECATED
        core/size/box2minsize   generate/box2min        Box 2 minimum size
        core/size/box1iconmax                           Box Icon sizes
        core/size/box2iconmax   generate/icocount       Box Icon sizes
    core/currentstatus                                                                              Status system - not complete
    core/setstatus                                                                                  Status system - not complete
    core/statustimer                                                                                Status system - not complete
    core/curpage                generate/pagenum        Page number for multi-page widgets
    core/stackalign             formulas/icostack       Formula for stack groups and such           Deprecate soon?
    core/boxalign               formulas/icopos         Formula for box1 positioning
    core/contentalign                                                                               UNUSED - DEPRECATE?
    core/color1                 generate/col1
    core/color2                 generate/col2
tasker
    tasker/data                 tasker/info             Recent command from Tasker
    core/tasker/fromtasker           tasker/stateupd         tasker/data to Device JSON
    core/tasker/fromcolorpic         tasker/colpic           Unused Tasker color picker                  Review with color system redesign
    tasker/cmd                                   Button press data > template JSON           Confirm template code
                                tasker/sendtoha         Data sent to tasker from widget. Flow uses  DEPRECATED
                                tasker/statedat         Device state DB                             DEPRECATED
func
    func/jsonmain              formulas/devstate       LV - Main JSON read
    func/func/jsonstate             formulas/devstate       LV - Device State data (in-code use)
                                formulas/lgtlevel                                                   DEPRECATED - Merged into "tjsonkeys"
                                formulas/lgtcolor                                                   DEPRECATED - Merged into "tjsonkeys"
    func/jsonobj               formulas/devstate       LV - Template data (in-code use)
    func/devjsonrem                                 TASKER > KWGT FLOW - Remove entity from list
    func/devjsonadd                                TASKER > KWGT FLOW                          Review
    func/devjsonindent                                TASKER > KWGT FLOW                          Review
    func/devjsonnew                                 TASKER > KWGT FLOW - Add new entity to list
    func/alignment                                        Generate how many icons can fit in Box 2
readme
setupmode                       setupmde                Display JSON data for debugging             DEBUGGING
-- 1.
colors
    theme/colornote
    theme/settheme
    theme/color1sysc
    theme/color1fil1
    theme/color1adj
    theme/color1fil2
    theme/color1pick
    theme/color1alph
    theme/color2sel
    theme/color2sysc
    theme/color2fil1
    theme/color2adj
    theme/color2fil2
    theme/color2pick
    theme/color2alph
    theme/colico
    theme/colicoon
    theme/color2fin
    theme/color1fin
    theme/border
    theme/bordersize
    theme/bordercolor
    theme/color1
    theme/color2
                     devcount                Op: Select # of devices to control
                                1-device                1 device style options (rows, etc)          DEPRECATED
                                2-device                2 device style options (rows, etc)          DEPRECATED
devicetype                                              Op: Select Device type from template data   NEW
deviceicons                     devico
templateselect                                          Op: Select template # to use (1-6)          NEW
templatename                                            Display Template name selected              NEW
entities
    entities/entitynote
    entities/entity1
    entities/entity2
    entities/entity3
    entities/entity4
    entities/entity5
    entities/entity6
-- 2.
deviceiconposition              devicovi                Op: Device icon position                    Merged both together
deviceiconrotation                                      Op: Device icon rotation
deviceiconsize                                          Op: Device icon size
boxtoggle                                                Op: Box 1 > Box 2
core/box1vis                        generate/box1wid        Op: Width of Box 1.                         Switched from auto to overrideable
box1size                                                Op: Manual override of "core/box1vis"
box2align                       box2alig                Op: Align custom content in Box 2           DEPRECATE?
iconsize                        icosize                 Op: Choose main icon size
appiconvis                      appicovi                Op: Choose app icon visibility
appiconsize                     appico                  Op: Choose app icon size
cornerradius                    boxrad                  Op: Widget corner radius
objpadding              icopad                  Op: Icon padding
objpadding2                icopad                  Op: Icon padding
objpaddingbox2                                          Op: Icon padding to Box 1
                                icodevszsc
                                icodevsz
verticalpadding                 verpad                  Op: Widget vertical padding
horizontalpadding               horpad                  Op: Widget horizontal padding
-- 3.
currenttemplate                                         Display the name of the current template
boxdir1
autoalign                       autoalig
widgetorient                    wgtalign
box1position                    position                Op: Set position/alignment of Box 1 icons.  Merge with "box2align"?
deviceiconshrink                                        Op: Shrink Device icon to App icon size
deviceappiconpadding            apicopad                Op: Override icon count calc for box 2      DEPRECATE??
deviceopeneditor                openedit                Op: Open editor on device icon click
-- 4.
json/error                                               JSON - Status                               Not working
json/device                                              JSON - Devicestates.json                    Generated
json/template                                            JSON - Templates.json


###### #####################################
###### All Flows
###### #####################################
Template Touch - KWGT > Tasker
Current Template Global Set
Template DB & Device Icon Set
KWGT > Tasker
Tasker > KWGT DB
Tasker > Status
AA - Box 1 Position
AA - Box 1 Size
Color 1 Set
Color 2 Set
Icon Padding Set
Icon Count Set
Template Name


###### #####################################
###### All Global Functions
###### #####################################

`func/jsonmain`
`#dbtemp`                   Template JSON


###### #####################################
###### Icon Pack
###### #####################################

###### Add to hawgts
- Schedule/calender
- Pool or Hot Tub room/controls
- A/C off

###### Change
- Sit on edge of screen like TV
    - Soundbar-mod
    - Projector Screen 
    - Lawn Mower
    - Sprinklers
    - Solar Panels
    - Radiant Heat
    - Battery
    - Alert
    - Wind Power
- Stick in the corner of the screen
    - Snowflake
    - Alert (x2?)
- Flip
    - Sprinkler
    - Wind Power
- Add chain to lock icon

###### Rename
- `All "ico" Icons to "dev"`
- `Look into sort again and find way to group icons better`
    - `"material" > "mdi" and append "-google"? or remove all prefix and append instead?`
- Identify all icons modified to fit in the corner. ".corner.mdi"?
