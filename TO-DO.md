###### #####################################
###### TO-DO to get to V1 release
###### #####################################
This is a floating list used to keep track of where everything is at while developing the core of the widget.


###### Legend for to-do list
- This task is done in KWGT Editor or both
- `This task is done in preset.json`
- ~~This task is complete~~
- `globalname` `localvarname`
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
- Work on Github - update repo and organize correctly
- Look over TO-DO.md and get data and information from that
- Continue to work on Codex AI programming
- Create script for pushing to device:
    > Take my copy of preset.json and add it to a *.kwgt file
    > Push using KDE Connect to dev phone
- Clean up codesnippets/* files. Make title areas collapsable in the correct way
- Create Template database
    - Specific templates to make:
        - "House Security" template
    - Specific Komponents to make:
        - Battery - Complete
            - Setup battery in different sizes/modes - Find way to scale?
        - Create "Komponent" Templates for different modes
- Fix and set up favourite formulas in KWGT before starting Media Control Widget



###### #####################################
###### TO-DO - Features to complete for V1
###### #####################################

- Finish implementing Templates V3 and alignment system cleanup **90%**
    - Complete templates for all devices **10%**
    - Fix remaining glitches and alignment issues **90%**
    - Add support for battery items?
- JSON System completion **70%**
    - Look into file-based JSON template
    - Allow user to select their own custom JSON using flow and file picker? Flow could remove whitespace and write to `json/template`?
    - Could also document the template syntax on github wiki and users can append to the template json for custom layouts.
        - Provide basic samples and a copy of template.json for reference
    - Could use "Send Data" option in flows or WebGet()
    - File permissions?

- Group configuration options in folders/subfolders more
- KWGT > TASKER - Create "custom" service call **0%**
- Device Icon option - click to open editor or open Home Assistant directly to your device
    - For paged items, open entity associated with the current page
- Make state updates more effective and work in more situations
    - The state does not update when lights are turned from one brightness to another
    - Is this because of the way the Tasker HA blueprint works?
    - Perhaps use Tasker to regularly check the current state of connected entities and update widget accordingly?
- Add "Setup" button that is in Box 2 when first opened. Clicking it goes to the github readme
- Set up and build Tasker plugin **0%**
    - Simply merge everything into one downloadable group?
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
- Should have a flow for each global thats updated with content from the template that is ONLY activated when the template changes. This will maintain user overrides of template settings until they change the template, which will then reset everything.


###### #####################################
###### TO-DO - Current
###### #####################################
- Verify icon padding works for double row widgets
- Confirm icon pack is up to date with latest icon list
- Tasker - "toggle" error
- `#curpage1` > `curpage` after confirming it works
- `"lock" in devicetype > "security"`

- Template system V2 > V3 - Last major revision **90%**
    - **Changes made from V2 - Notes only**:
        - light.light1{ box1{} box2{} }
        - light.light1{ page1{ box1{} box2{} } }
        - `entity` tag is gone. page 1-4 = entity 1-4
        - `ttype` is now "light"
        - `tname` is now "light.light1"
        - "stateon" > "onstate"  "box_#" = "box#"  "row_#" = "row#"  
        - "cmd1" "data1" = "cmd" "data"
        - "controllist" > "objects", is now always at "light.light1.objects"
        - "style" tag is now gone, merged into "light.light1"
        - If paging, device_count will always be 2-4. If not paging, widget is always 1-2
        - If paging, page# = entity#. If not paging & entity2 is set, box# = entity#
        - If "device_icon_pos" = 2 or box_1 = 0, "box_1" is ignored and can be removed from JSON
        - If "row_count" = 1, "row_2" is ignored and can be removed from JSON
        - New order of control for template settings.
            - Device count: Template only. If pages (2-4), then # of `entities/entity#` filled in
            - Box 1 size: Template
            - Row Count: Template, if "1-2" then by widget size and style?
            - Device Icon Positon: Template only
            - Device Icon choice: Template only, user can override in widget editor with font picker
        - "tbox1" -       0 = hidden, 1 = single,    2 = double
        - "dev_ico_pos" - 1 = Corner, 2 = Box 1,     3 = Hidden
        - "row_count" -   0 = none,   1 = 1 objects, 2 = 2 objects????
    - `rowswap`: global toggle? Swaps Box 1 rows and columns. Removes need for row#? Pair with `widgetalign` **Unsure**
    - V3 and alignment update test problems
        - Examine `box1size` flow - it seems to resize small when playing with 1 row. #box1obj?
            - box1size flow very fragile - **Minor tweaks made - Check in editor**
            - Examine `box2iconmax` - regular sized widget only allows 3 icons
        - Pages still not working
            - Flow works correctly - it writes the correct number to curpage
            - ~~`curpage` > number global~~ **Check to see if fix works**
            - Will page template with 3 entities only show 3 pages?
        - Test widget in all alignments and different template styles before signing off on alignment system completion
            - Find out whats happening with widget width - how is it going above 720? Is that simply from scaling?
        - Flow for setting `widgetalign` is no longer there - recover?
    - Figure out how to allow user to change page icons - iconname in `entities/*`? Check editor
    - Create a for loop for `ntwgtsize` in `templateinfo` flow
    - Complete all versions of "light" template and maybe start "climate"?


###### #####################################
###### Completed Tasks - 2025
###### #####################################
- `Change "Template" name to "Style"?` **Unsure**
- ~~`Change setstatus "name" to setstatus`~~
- Template system V2 > V3
    - ~~Templates get sorted by device type, as is current~~
    - ~~light1 and light2 are same template, different "style" and "box_1" configurations.~~
    - ~~This allows the JSON to take up less space.~~ **60% less**
    - ~~light.controllist{}  light.light1{ style{} box1{} box2{}}?~~
        - ~~`func/jsonstyle` > `func/jsontemp`~~
    - `tboxsize` > `tbox1`
    - `core/box1dir2` > `core/box1vis`
    - ~~Code all controls to call json functions directly. Deprecate globals.~~
    - `func/alignment` > `func/alignment` & `func/sizing`? **NO**
        - Add all `core/size` globals to `func/sizing`
- Rebuild alignment and auto systems
    - `func/boxsize` > `func/alignment`
    - ~~`"box1dir1" > core global, not list`~~ **Confirm adjustments made are enough**
    - ~~`"box1dir2" > core global, not list`~~ - Determined by template, `box1vis`
    - ~~`Create a "autosizing adjustment" global that combines "deviceiconshrink", "deviceappiconshrink", `~~
    - `fitmoreobj` works, but displays icons that are not enabled in the template
        - `#stoutput` needs to check if the object exists and return 0 if not.
    - ~~Implement order of control for template settings (above)~~
        - ~~Examine sizing for Box 1 based on new order of template settings~~
- ~~New text global below `templateselect`. This can show the user information about the template~~ **CURRENTLY TESTING**
    - ~~This could go along with the overhaul of the alignment and rendering system, enabling template options based on physical widget sizes~~
    - ~~Create a flow for this that updates `templateinfo`~~ **See template.md for flow**
    - This will use all of the core backened systems, such as box2minsize
- Fix object touch issue by prepending "." to "box_2.row_2.0"
- Create third json function, specifically for style information? `func/jsonstyle`. Merge from tjsontemp
- ~~"Icon Padding Set" and "Icon Count Set" flows both write to "core/size/box1iconmax"~~
- `Remove "currenttemplate"`
- `Create JSON folder in globals`
- `"boxorder" > "toggle"?`
- `"func/tjsonkeys" > func/jsoncall? jsonmain?`
- `"func/tjsonstate" > "func/jsonstate`
- `"func/tjsontem" > "func/jsonobj`
- `"tasker/totemplate" > "tasker/cmd"?`
- `"deviceiconposition"` > func/jsontem #ticopos
- `"entities" folder to top to simplify setup`
- `"pagenum"` - To fix pages
- `"curpage" not named correctly`- `Remove "devicecount"`
- Box 1 page icon visiblity: check to see if entities/entity# exists, if not, no page **This should happen naturally in existing code**
    - Mostly useful for controlling 3 lights vs 4 and not needing new templates. Page 4 is  ignored and 1-3 work as-is. This also allows 2 devices to page on a single row widget, like "climate" control 1 fan and one hvac or a/c or something. 
- "style" key group needs to be integrated and code written
    - Add: ~~Icon~~, ~~Box1 Size~~, Row count, ~~Device Icon position~~, Device count
    - ~~tjsontem - rename local variables "obxxx" eg "obicon"~~
- ~~Fix value of "json/template" in preset~~
- ~~Understand JSON syntax and the code calling it~~
    - ~~Does "devicetype" in JSON (eg light1) start at 0?~~ **No, it should start at 1 based on `templateselect`**
    - ~~Look at page JS ON code. Revise? "action" could be "page" and "data1" could be the page #?~~
- ~~Disable old "KWGT > Tasker" Flow~~
- ~~Investigate ALL new flows and document them~~
- ~~Look into "TASKER > KWGT" flow - Do we need to build "json/device" like that?~~ **Yes, unfortunatly**
- ~~Double check "preset.json" is the most up to date~~
    - ~~exported 6.0 should be compared~~
    - ~~Double check widget on page 2 of devphone - it may have some slight improvements over 6.0~~
- ~~Fix Tasker > KWGT errors - missing tasker task? check phone~~
- Once template is more complete, inspect Device icon system
    - **Deleted list global, will set from template or user can set using icon picker**
    - It appears to need a lot of work
    - Figure out if a global variable can be written to a list object
    - Look at lists and see whats up
    - Confirm Device Icon code is cleaned up after deletion of list global
- KWGT > TASKER
    - ~~Assess code - opportunities for optimization and tweaking from template update~~
    - ~~Look into "tasker/cmd" and look at editing the syntax. Should now be just ".box2.row1.0"?~~
    - ~~Clear "tasker/cmd" at end to fix problem with duplicate ~~ **Works, but unsure if it helps**
    - Add new "custom" service call
- Clean up flows
    ~~- Look into merging some small flows with each other with doubled up functions~~
    - Potential to consolidate Tasker > KWGT and func/* **NO**
    ~~- Come up with a better naming system~~
- ~~Convert kode files into md files~~
    - Also, create a single "working" kode file to use syntax highlighting in
    - Make tree with links to each section of code

###### #####################################
###### Completed Tasks - 2023
###### #####################################
- Check to see if .device1.type is being used anywhere. Name can be extracted from devicetype **Now has template use, keeping**
- Fix "currenttemplate" to be formatted and reintegrate into code **Do not use currenttemplate**
- Clean up readjson code after templates and other json are finished
- Can have same syntax as json/template
- Merge templates into new database
    - Copy working entity system from lighting template to all templates while merging
- Ask user what device type is, set icon group, choose a matching template **SEE SETUP GLOBALS**
- Check Room code next and setup different rooms. Decide how to format json
- Next, set up new JSON syntax into code and test it.
- State indicator: Working at a basic level for regular and page templates
    - `Make circle backgrounds for every icon`
    - `Add Icon Color and Icon Filter code to all icons`
- Remove code for "1.2.0" and use full json path instead.
    - Can then eliminate the converter code and insert directly into formula. Still need to keep globals
- Create a global variable that contains the current templates JSON, which is then read by the system to render the UI
- Merge Device States JSON into current template JSON and rename system? **Cant - not editable**
- Create flow to set "core/currentdeviceicon" and "currenttemplate"
    - currenttemplate: **DOES NOT OUTPUT FORMATTED, REPLAN**
        - Change readjson to read from this global instead
        - Only gets overwritten when device globals reconfigured
        - Add {} to output code
        - Offer fallback onto the json db on error
        - templatedb - use only when changing template type, move current template into currenttemplate
    - core/currentdeviceicon:
        - Set up list of device icons
        - `Put list in global (end of globaltemplate.kode)`
- Check to make sure Box 1 > 2 Boxes template has columns instead of rows? Copy from old Light template
- "Preset" folder where you can insert your HA information for preset templates
    - Eg add "light.bedroom_lights" as room 1
    - `Core added, apply to all devices in all templates. Working test: bedroom light off`
    - `Add entity5 & 6`
    - `Rename folder "entities". Rename note in folder`
    - Test to see if new code works
- Test to see if you can make a whole list global `$gv(textglobalwithformula)$` **Does not appear to work.** 
    - This will allow us to generate a list on the fly with a flow and global database of some kind.
    - Globals test1 and test2. 
- Re-build old folder structure and rename files to old names to allow github to follow sources
- Add option to set low and med brightness stsets
- Last step of KWGT > Tasker flow should reset tasker/cmd to blank and edit flow not to run if tasker/cmd is blank
- Rename Device JSON globals to specific json names
- Add type of button to template json, using ha > tasker names: on, off, toggle, light (>level?), template and custom. Add light level line also (30, 120?, 255)
- `Reorder Flows in preset`
- `Change all icon foulas - "#obtype" > "#template"`
- `"func/" > "func/"`
- `"boxdir1" > "boxdir1"?`
- `"deviceicon" > "deviceicon"`
- `cmd1 > cmd1 in templatjson`
- `"ticonid" > "ticonid"`
- `"ciconid" > "ccinid"`
- `"func" > "func"`
- `"generated" > "co"`
- `"type" > "obtype"`
- `"icon" > "ticon"`
- `"global" > "tglobal"`
- `"global" > "actio`
- `"cmd1" > "obcmd1"`
- `Install new visbility and paint codes in globaltemplate`
- `Update object code for jsonread formula split`
- Finish brightness json integration into code. This should make lights work properly.
- Change global order for new configuration **ALL BELOW DONE**
    - **USER - AUTO-WIDGET GLOBALS - Generated custom default** - "Configure Your Widget"
        - Color
        -  - How many devices to control with this widget
        - devicetype - What kind of device(s) > Sets the main device category
        - deviceicon -  Choose from several icons or custom
        - deviceiconposition - Ask user where they want the icon (only if box 1 is unused?)
        - Entity_ids  
    - **USER - CUSTOM/MANUAL WIDGET GLOBALS - Tweak custom default** - "Customize your widget"
        - boxtoggle - Set order of boxes
        - core/box1dir2 - Column count or remove Box 1 **MAKE AUTO**
        - box1size - Custom column stset
        - boxdir1 - Row count **MAKE AUTO**
        - *deviceiconname* - List global with every single fonticon in pack
            - Sort by Category and device type
            - Device Icon object can load fonticon from here.
        - box2align - Centered or aligned
        - ... Misc controls from current global homepage ...
    - **ADVANCED - Auto-Align** - "Advanced customization and alignment"
    - Widget Orientation
    - Json editing?
    - ...
- Re-examine global formulas and generated folder to make global formulas where possible
- Merge #globalcm and #global and adjust code accordingly
- Check reordered Flows
- FLOWS CAN WRITE TO GLOBALS IF MANUALLY EDITED IN JSON TO INCLUDE FOLDER NAME
    - `Go over list at top of document and make changes in JSON accordingly`
- `currenttemplate > currenttemplate`
- `Fix color2 - not in folder`
- `Edit "Template Touch Action" flow and fix pagenum global`
    - This should allow pages to work, investigate if 
- See if a lv can be passed to a flow - unlikely but worth checking
- Move Box template json to the root to avoid duplicates. Box 1 should always be static on page templates
- `Convert lists into json`
    - Making "json/error" JSON would allow the status function to be finished
- Merge Template touch flow and KWGT > Tasker flows~*CHECK*
- Figure out why pages wont display when changed
    - Finish editing template - change page and global values (light1 done)
- Create a flow to write to currentstatus and set statustimer *CHECK*
- Create a flow to write the value of "templatename". Use code currently in global - *Doesnt work*
    - Iconcount and Iconpadding are the same global?
- Loop problems to fix 
    - box2minsize - Disabled - Fix looop with iconcount and re-add to box2width/box2height
    - iconcount - Patched - Need to use deviceiconcalc, using deviceiconsize temporarily
- Figure out why stoutput is always outputing 1 - doesnt make sense
    - Problem with #range? Seems to be something deep like a loop or bug or limitation of kwgt
    - stoutput set to 0 to disable state for now

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
    colors/colornote
    colors/color1sel
    colors/color1sysc
    colors/color1fil1
    colors/color1adj
    colors/color1fil2
    colors/color1pick
    colors/color1alph
    colors/color2sel
    colors/color2sysc
    colors/color2fil1
    colors/color2adj
    colors/color2fil2
    colors/color2pick
    colors/color2alph
    colors/colico
    colors/colicoon
    colors/color2fin
    colors/color1fin
    colors/border
    colors/bordersize
    colors/bordercolor
    colors/color1
    colors/color2
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
