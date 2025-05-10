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
- Create official build process - "build.md"?
    - Create script that creates a "globals.md" chart with name and description imported from json
    - Check "Final To-Do" list way below
- Review all description tags for globals

###### #####################################
###### TO-DO - Features to complete for V1
###### #####################################

- Finish implementing Templates V3 and alignment system cleanup **90%**
    - Complete templates for all devices **10%**
    - ~~Fix remaining glitches and alignment issues~~ **100%**
    - Add support for battery items?
    - Add support for custom dials and displays - or just make these komponent/addons?
- JSON System completion **70%**
    - Look into file-based JSON template
    - Allow user to select their own custom JSON using flow and file picker? Flow could remove whitespace and write to `json/template`?
    - Could also document the template syntax on github wiki and users can append to the template json for custom layouts.
        - Provide basic samples and a copy of template.json for reference
    - Could use "Send Data" option in flows or WebGet()
    - File permissions?
- Confirm icon pack is up to date with latest icon list
- Group configuration options in folders/subfolders more
- Should have a flow for each global thats updated with content from the template that is ONLY activated when the template changes. This will maintain user overrides of template settings until they change the template, which will then reset everything.
- KWGT > TASKER - Create "custom" service call **0%**
    - Use HA templates for simplicity?
- Set up and build Tasker plugin **0%**
    - Simply merge everything into one downloadable group?
    - Tasker - "toggle" error
    - Make state updates more effective and work in more situations
        - The state does not update when lights are turned from one brightness to another
        - Is this because of the way the Tasker HA blueprint works?
        - Perhaps use Tasker to regularly check the current state of connected entities and update widget accordingly?
- Final review of code
    - Go through everything and ensure everything works in all scenarios
    - Slim down List Global code by using the index# directly to save excess local variables in functions
    - Test config and operation by following readme on multiple devices and launchers


###### #####################################
###### TO-DO - Current
###### #####################################
- `templateinfo` Flow
    - Create a for loop for `ntwgtsize`?
    - Connect to status system?
- Add "Setup" button that is in Box 2 when first opened
    - Clicking it goes to the github readme?
    - Arrow to direct user to "Globals" tab?
- Device and App icons
    - Confirm all code works
    - Device Icon: click to open editor or open Home Assistant directly to your device
    - Show/hide app icon, 1st = HA, 2nd = Custom
    - Look at "Shortcuts" next to "Flows" in editor
    - App icons: fixed positioning
- "Status" system completion **10%**
    - Or use status bar? KWGT code complete, send string to kwgt br: "error" once status is working
    - Use following errors:
        - HA Not running
        - Cannot get status update
        - State not returned after change
        - JSON Error
        - Tasker Error
- Complete all versions of "light" template and maybe start "climate"
- Theme system
    - Configure colors and styles correctly, testing to get correct results
        - Fine-tune colors for each group
        - ~~Big problem with `$si(#var)$` not parsing. Look into solutions or risk revamp~~
        - ~~Found solution. `$si(#var)` = not working. `$si(#var, #var)$` works. Split in code~~
        - Test opacity, shadows and borders
    - ~~Create "Clear Custom Values" option - Flow~~ 
    - Background
        - `Add widget shadow` - Testing in editor
        - ~~`Implement Opacity code into background`~~
    - `func/themecolors` not allowing color hex values in JSON??
    - Look at border code - should not need formula for switch?

###### #####################################
###### Completed Tasks
###### #####################################
- Theme system overhaul and upgrade
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
    - Merge `theme/coloreditor/opacity` & `theme/color2alph~
    - Create `theme/color#` x 4, paired to theme
    - Write theme json code in templates.md
    - `Update `theme/color/*` from `theme/coloreditor` or make user c&p?`~
   - `"Fill In" "custom" theme values from theme to global` - option? **Potentially not**
        - On "Custom" select? If user goes back to regular theme, settings would persist
        - Switch at top of `theme/coloreditor`?
    - `Write flow for "theme/coloreditor"`
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
- Changing state now only works after 2 taps, not one. Look at KWGT > Tasker flow.
- `"lock" in devicetype > "security"`
- `"Icon #" > "Object #" in editor`
- `theme/objpaddingbox2` > `theme/objpaddingside`
- Some entities missing after theme work

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
- Create Device Icon ring
    - Merge with device icon touch
    - Ensure it works in all positions


###### #####################################
###### Dev version > Release version
###### #####################################

- Move service global folders to bottom from top
- Remove debugging, setup data, etc globals and objects completely
- Remove Device Icon click to open editor option?
- Create a "setup your widget by going to globals" textbox with basic setup info
- Remove "notes" key in template JSON

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
