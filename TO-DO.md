# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

###### #####################################
###### TO-DO to get to V1 release
###### #####################################
This is a floating list used to keep track of where everything is at while developing the core of the widget.
It contains lists, resources and plans


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
###### Dev version > Release version
###### #####################################
- Remove debugging, setup data, etc globals and objects completely
- Remove Device Icon click to open editor extra item
- Create a "setup your widget by going to globals" textbox with basic setup info
- Remove "notes" key in template JSON
- Set page icons to generic for editing by user
- Include "kustom.codeworkspace" in repo again?
- Use Kustom packager to create Android "App"?
- Ensure JSON is synced between widget and *.json files
- Ensure `json/theme` and `json/template` include blank lines for pasting
- Set all non-needed values to blank. This also enables the setup screen
- `json/theme` and `json/template`
    - 2 blank lines at bottom of JSON for easy user addition - center of editor
- Rename all globals to be edit by users to 9? chars again


###### #####################################
###### TO-DO - Tasks to complete for V1
###### #####################################
- Complete Readme **60%**
    - ~~"globals.md" + "json.md"~~
    - Make readme easier to understand
- Look at YAML for lists/notes?
- Fix and set up favourite formulas in KWGT before starting Media Control Widget **50%**
- Confirm icon pack is up to date with latest icon list
- Create official build process - "build.md"?
    - Create script that creates a "globals.md" chart with name and description imported from json
    - Reference "Dev version > Release version" list above
- Finish Features below


###### #####################################
###### TO-DO - Features to complete for V1
###### #####################################
- Finish implementing Templates V3 and alignment system cleanup **90%**
    - Complete templates for all devices **10%**
    - "House Security"?
- JSON System completion **70%**
    - Look into file-based JSON template
    - Allow user to select their own custom JSON using flow and file picker?
    - Document the JSON syntax on github and users can append to the template json for custom layouts.
        - Provide basic samples and a copy of json files for reference
    - Could use "Send Data" option in flows or WebGet()
    - File permissions?
- KWGT > TASKER - Create "custom" service call **0%**
    - Use HA templates for simplicity?
- Set up and build Tasker plugin **0%**
    - Simply merge everything into one downloadable group? Taskernet
    - Tasker - "toggle" error
    - Make state updates more effective and work in more situations
        - Perhaps use Tasker to regularly check the current state of connected entities and update widget accordingly?
- Final review of code
    - Go through everything and ensure everything works in all scenarios
    - Slim down List Global code by using the index# directly to save excess local variables in functions
    - Test config and operation by following readme on multiple devices and launchers
    - Catch any stray fixed values in "preset.json"
    - ~~Review all description tags for globals~~
    - ~~Group configuration options in folders/subfolders more~~
- Flow at start to check widget size ratio
    - If its a single row, make device icon same size as regular icons? 1:8 ratio and below?
    - Check iconsize - wgtheight and resize icons if less then 10% is showing


###### #####################################
###### TO-DO - Current
###### #####################################
- `templateinfo` Flow
    - Create a for loop for `ntwgtsize`?
    - Connect to status system?
    - Merge `templateinfo` and `templatename`??
- `template.json` database creation **10%**
    - Most of "light" and first couple "climate" templates are complete
- Readme: "HA" > "Home Assistant"? or "HASS"?
- Device and App icons
    - Tasker: KWGT launch and launch to entity problems
    - Look into way of using Kustom Editor "Shortcuts" tab for these items
        - Set defaults and tell user to go to shortcuts to change?
- Codex AI CLI custom "Kustom" prompt
    - ~~Test by creating markdown table for globallists.md~~ **80% successful**
    - Get AI to generate "template.json" outline and base content for editing
        - Add more project-specific prompt info? Add reference to readme.md?
        - Ensure `<device>.objects` is populated based on globallists.md
- Themes
    - `settheme` > `theme` (conflict with `theme/`?) and `devicetype` > `device`?
    - Shadows work but seem to be trimmed at corners - launcher?
    - Fine-tune colors for each group
    - Check color editor and generator flows **KWGT GLITCH?**
- "Status" system completion **45%** **TEST**
    - Add "Stop If" to Status Flow if `#cat1` or `#error1` = ""
    - Send string to kwgt br: "error"
    - Integrate "Setup" button?
    - Integrate `templateinfo` button above?
    - "Hide/Ignore Error" button?
    - Create functions to identify problems in `func/status` to test
    - Build-in an initial setup mode
        - Use `devicetype` = DATA to hide objects and show setup
        - Create "setup" template?
        - Include link to the github readme?
    - Arrow to direct user to "Globals" tab?
    - ~~Update globals.md~~
- "globallists.md" > "docs/iconlist.md"
    - ~~Write short intro~~
    - ~~Create markdown table~~ **Codex AI**

###### #####################################
###### Completed Tasks
###### #####################################
- Theme system overhaul and upgrade
     - Test Colors, Editor and Generator
        - Big problem with `$si(#var)$` not parsing. Look into solutions
            - `$si(#var)` not working. `$si(#var, #var)$` works. Split in code
        - `colorgen/adjcolor` is backwards
        - `colorgen/opacity` change to 200 default
        - Suggest user makes final adjustments in color picker before copying from colorgenerator or coloreditor
            - Add note in `colorgen` and `coloreditor`?
        - `coloredit/opacity` is only 0-100
        - color editor not pulling colors correctly **TEST**
        - `coloreditor` > `coloredit`
        - `colors/coloredit/wallcolorset` > `colors/coloredit/filter`
        - `colors/coloredit/adjcolor` > `colors/coloredit/adjfilter`
        - Test opacity, shadows and borders
        - Pastels do not work
    - "light" object color data - check system and find way to indicate on widget
    - Swap widget opacity direction? **Cannot, by design**
    - `box1align` in `func/alignment` > box1anchor
    - `func/themecolors` not allowing color hex values in JSON??
        - Border size only working on Box 1
    - `Add widget shadow` **TEST**
        - Get shadows working 
    - `theme/ringshape` > List with blank first entry? **TEST**
    - `objpaddinghor` only working on box 1
    - Add `objpaddingside` to `box1size` so it adjusts as well
    - Look at border code - should not need formula for switch?
    - Implement Tasker color picker? **NO**
    - Create "themes" like One UI, material, HA card, etc. Template independant
    - Store data in JSON
    - Possibly re-do based on community resources
    - Possibly re-do based on UI types and Tasker setup interface/color picker
    - Utilize lists to make the UI user-friendly?
    - Look at material color spec to understand color tone in ".OneUI.colors{}"
    - Finish moving preset.json code to new theme func
        - Remove formulas from globals meant for custom
    - Use existing code where possible but remove duplicates - no need for color1 and color2
    - Device icon color > `#objiconoff`
    - Merge `theme/color1sync` & `theme/color2sync`
    - Merge `colors/coloredit/opacity` & `theme/color2alph
    - Create `theme/color#` x 4, paired to theme
    - Write theme json code in templates.md
    - `Update `theme/color/*` from `colors/coloredit` or make user c&p?`~
   - `"Fill In" "custom" theme values from theme to global` - option? **Potentially not**
        - On "Custom" select? If user goes back to regular theme, settings would persist
        - Switch at top of `colors/coloredit`?
    - `Write flow for "colors/coloredit"`
    - Look at each theme in JSON
        - Verify configurations seem to match chart below and Material/OneUI specs
        - `Convert "dp" to "kpx`"
        - Figure out using `$$` in JSON - in JSON will work `$si(#jsonval)$` (sysca1, 50)
    - `Figure out pastel icon colors for OneUI` ".OneUI.colors.pastel{}"
        - `Add new circle icon color code in template.md to all objects`
    - Remove "10" mode from theme globals so they arent in formula mode
    - `#padwgthor` and `#padwgtver` are backwards
    - Several object formulas set to `#tradius` not `#radius`
        - Double check stack group margins in editor
    - Box border strokes incorrect
    - Icon ring meant to be 25% bigger then icon size  in material - implement ring size
    - `"State" title on rings > "Ring"`
    - `Make circle shape configurable with "ring_shape"`
    - `Implement Opacity code into background`
    - Create "Clear Custom Values" option - Flow
    - ~~Auto-shrink icons to fit ring size instead of iconsize being bigger then icon~~
    - Find new way to do pastels that maintains their color once set
        - ~~Run number generater inside object formula directly~~
        - ~~`Change "pastel" to "randomring" systemwide`~~
        - ~~`Insert new code in "preset.json"`~~
        - ~~Create `core/pastel` (> `core/randomring`)~~
        - ~~`Create template key for colorlist feature`~~
- Changing state now only works after 2 taps, not one. Look at KWGT > Tasker flow.
- `"lock" in devicetype > "security"`
- `"Icon #" > "Object #" in editor`
- `theme/objpaddingbox2` > `theme/objpaddingside`
- Some entities missing after theme work
- "Status" system
    - `Create and populate `func/status` from backend.md`
- Device and App icons
    - `appiconvis` backwards
    - Design: 
        - `external/appiconsize` for icon size
        - list: `deviceicontap` choose editor, ha, ha assist, none or other to open
        - list: `appicon1` choose editor, ha, ha assist, none or other to open
        - list: `appicon2` choose editor, ha, ha assist, none or other to open
        - Link icons to these actions for appicons
        - Tasker "Secondary" URI
    - Device and App icon postion problems
    - `Create `external/custom` for users to enter a custom android app to open`
    - Update globals.md
- All of `core/size` merged into `func/alignment`
    - Update all references to `core/size` in preset.json
- globallists.md
    - ~~Write short intro~~
    - ~~Create markdown table~~ **Codex AI**
- Clear `core/tasker/temp` after flow
- json.md #2 - add suggestion on best way to do this
- Include a theme.json and template.json in /docs and link to json.md
- Move service global folders to bottom from top


###### #####################################
###### Future features and plans (V1+)
###### #####################################
- Create a kustom flow that formats json from no formatting to human readable **Universally**
    - Use for loops and awareness of {} level, arrays and commas.
    - Wipe all whitespace first to get json to consistant state
    - Provide custom HA calls for lights and potentially other devices? Or use templates instead?
- Create a flow loop that writes new lv() - Can reduce stacks of defined lv()
- Integrate all options into the globals.
- Setup Mode
    - Set up "setupmode" text layout better and add a few different text options (sizing, json, etc)
    - Group in overlay groups and use visibility to display each one
    - Set up a note for visibility like the ones inside templates
- Add Row 3
- Change tasker/cmd data to process json instead of "&" syntax?
    - Can write template json in KWGT to send to tasker
- Integerate TV templates into widget once TV Remote is complete
- Create Device Icon ring
    - Merge with device icon touch
    - Ensure it works in all positions
- Komponents
    - Make widget a Komponent that can be added to a KWLP wallpaper
    - Add support for custom dials and displays
        - Climate dial, Security panel?, Battery meter
- Override templates like themes?
- Find way to communicate with Tasker better - Send Data option in flows maybe?
    - Even better, skip Tasker altogether - Find way to use HA API directly in widget?
        - Make HA service calls using homeassistant:// intent
        - This would allow the widget to not need any setup, other then the HA app configured
        - No API keys or Tasker needed, No HA config at ALL
        - Can I get state information???
            - Use HA app and wg()? Can I get auth that way?
        - https://companion.home-assistant.io/docs/integrations/url-handler/#call-service
- "Pastel" OneUI feature to be expanded to random color option for any theme

###### #####################################
###### Icon Pack
###### #####################################

###### Add to hawgts
- Schedule/calender
- Pool or Hot Tub room/controls
- A/C off
- HA "Assist" assistant "app-home-assistant-assist"
- KWGT icon? allowed?

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
