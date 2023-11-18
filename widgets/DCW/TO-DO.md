# Device Control Widget

DCW to-do lists, notes, ideas and more

## TO-DO Current

### Ongoing and Difficult
- Loop problems to fix 
    - box2minsize - Disabled - Fix looop with iconcount and re-add to box2width/box2height
    - iconcount - Patched - Need to use deviceiconcalc, using deviceiconsize temporarily
- ~~Add a border option~~
- ~~Set up new tasker pipeline for custom commands for advanced users ()~~
- Work on Readme's
- Flows are unable to write to globals in folders. When fixed,
    - Reorganize Globals by folders
        - Roughly sort by numbers
        - Merge -- 2-4. into one folder, keeping subheading list globals. Make 6 one folder
        - JSON at bottom with setup mode toggle
        - Add note at the top of each folder
    - Change iconcount flow to write to iconcount and remove "temp" global
    - Complete color test
        - Write directly to colors in color folder
         - `Change color flows back to proper values after test`
    - Set "default" options to things like box size and icon alignment to avoid weird rendering issues while widget is loading changes after successful color test


### Current
- ~~`"Rm" > "Room", "bordercolor" > "bordercolorlor", "bordersize" > "bordersizeze"`~~
- ~~`Swap 1 and 0 in appiconvis and remove duplicate`~~
- ~~`Remove "based on globals" from folder status text items`~~
- ~~`Verify all mentions of "colcount" are preceeded by formulas`~~
- ~~`Copy Box 2 > 1 Box, 2 Row code from snippets into each Box/Row type in Box 2`~~
- ~~`Copy Box 1 > 1 Box, 1 Row code from snippets into each Box/Row type in Box 1`~~
- ~~`Add Add ">>>" Code to main from Snippets and Auto-sizing`~~
- ~~`Move -- 3. to after boxorder?`~~
- ~~``deviceiconcat > deviceiconcat`~~
- ~~`Move box1dir1 above box1dir2`~~
- ~~`Change widgetorient to a list`~~ Can't, using on/off formulas. Add a flow if it becomes neccessary
- ~~`Column to Columns (box1dir2 list)`~~
- ~~iconpaddinbox seems to not be accounted for in iconcount~~
- ~~Fix iconcount loop in vertical mode~~
- ~~Fix Box 1 position in 1x4 mode~~
- ~~When setting box1dir2 to "Remove", both Boxs disappear~~
    - ~~Box 1 content still shows~~ 
    - ~~On the 2x2 widget. box1height/box1width seem to be the problem~~
    - ~~Also, Box 1 visibility seems to be incorrect in this state~~
    - `All fixed in 2x2 Security/Lock, edited code, might have missed something. Verify after code`
- ~~Add check for [Enabled] tags in Box 1 to hide when box 1 is hidden~~
- ~~Augment auto-align flow or make new one that removes box 2 after ratio is 1? and widgetsize is smaller than 3x(2x2) or (2x2)x3~~
- ~~Fix incorrect icon direction in 3x1 display~~
    - ~~Vertical widget > Box 2 > 2 Boxes 1 Row > 1 Device~~
- ~~Replace wrong code in Icon Padding Flow~~
- ~~Verify new boxalign formulas and flows work with vertical box1size~~
- ~~Changing box1dir2 somehow makes box1width/height grow to ~3 icons tall~~
- ~~Fix box1size flow - outputting wrong~~
    - ~~Working on box1size flow and global formulas - box1dir1 and box1dir2~~
    - ~~Temp 1 column support added~~
    - ~~Make pad calc work for 1 and 2 rows and 1 and 2 columns~~
- ~~Fix deviceiconcalc - Change check for row 2 for a check of the height of widget and settings icon visibility. Shrink if widgetwidth <= deviceiconsize + iconpaddingstack * 2~~
- ~~Check Box 1 hidden and mini sizes before continuing~~
- ~~`Entities to go between --4 and --5?`~~
- ~~`box1size > box1dir1size`~~
- ~~"Preset" folder where you can insert your HA information for preset templates~~
    - ~~Eg add "light.bedroom_lights" as room 1~~
    - ~~`Core added, apply to all devices in all templates. Working test: bedroom light off`~~
    - ~~`Add entity5 & 6`~~
    - ~~`Rename folder "entities". Rename note in folder`~~
    - `Apply to remaining templates as they are edited`
    - ~~Test to see if new code works~~
- ~~`Remove box1width and box1height from formulas. Integrate box1height align calc into the few calls it has`~~
- ~~`Fix template alignments that contain "Vertical_left" to "Vertical_Center" (some)`~~
- ~~`Rename appiconvis and deviceiconcalc > deviceiconcalc?`~~
- ~~`Rewrite and rename appiconvis. Show is default, 0. Reword as "app icon visibility"`~~
- ~~`Update note items in lists from code database`~~
- Test to see if you can make a whole list global `$gv(textglobalwithformula)$`
    - This will allow us to generate a list on the fly with a flow and global database of some kind.
- ~~Iconcount problems~~
    - ~~Fix iconcount boxsize2 and boxsize3 - not calculating properly - sometimes - From initial start?~~
    - ~~Try as a flow instead maybe?~~
    - ~~Returning 2 in single box mode~~
- ~~iconpadingbox not returned when box1dir2 is set to custom **CHECK - POTENTIALLY MAKE NEW**~~
- Add 3+ device, 1 box mode? Could have 4 devices as toggles in a grid
- ~~Update colcount to kpx~~

### Future
- Work on README
    - ~~Create second readme "Global Settings"~~
        - Go through all global settings item by item. Organize page to be accessible by tree (multiple depths?)
    - Create third readme "Box Heiaracy"
        - Tree view of all boxes/templates, with screenshots. Use headers for subgroups like "2 Rowss"
- Set up [DELETE] system where once the user is complete, they can delete all marked widget items
    - Make sure user knows to delete unused icon overlay groups from templates
- Background color system
    - Possibly re-do based on community resources
    - Possibly re-do based on UI types and Tasker setup interface/color picker
    - Make sure it works properly if not
- Setup auto-hiding icons where needed (After templates are complete)
- Add all Device Icon types and all templates to Globals
    - Device Icon: Current list = Category, New list = Category options - use global variables?
    - Integrate full check for how many rows/icons are showing and show icons accordingly in Box 2 to all templates (with missing in Box 1 code)
- Setup Mode
    - Set up "setupmode" text layout better and add a few different text options (sizing, json, etc)
    - Group in overlay groups and use visibility to display each one
    - Set up a note for visibility like the ones inside templates
- Setup inital flow that sets devicecount, box1dir2, box1dir1 
    - Also a flow that adjusts things automatically. eg change to 3 devices, automatically set to 2 columns, etc.
    - Check: Save temp global values, get widget info, run if different. If not, use temp global??
    - Rows, Box 1 columns, Device count, etc
    - Run iconcount calc?
    - Maybe merge into the Box 1 size code that runs every time widget resizes? For example, if you resize from 2 rows to 1 row, change global to 1 row, etc
    - Merge colcount into this
- Add code to shrink iconsize somehow if the box is smaller than the icon sizes?
- Create a 
- Create Custom GPT for Widget design and global/settings layout
- Convert kode files into md files
    - Also, create a single "working" kode file to use syntax highlighting in
    - Make tree with links to each section of code
- `Move "Hidden" Device Icon setting from deviceiconposition to deviceiconcat?`


### Templates
- Look at security 2x2 - "House Security" template?
- Copy lock 2x2 from sample and make 1x2
- Setup soundbar 1x4
- Setup fan 2x2
- Re-setup battery with new battery icon
- Setup battery in different sizes/modes - Find way to scale?
- Create "Komponent" Templates for different modes
- Check every template for blanks that work with respective layout
- Switch all Box 1 > 2 Boxes templates to have columns instead of rows. Copy from Light template

### Global Changes > Readme:
- Added haent
### Working
- Work on Readme's

### Version 2.0 features*
- Make widget a Komponent that can be added to a KWLP wallpaper
- Integrate all options into the globals.


## TO-DO Project
### KWGT
- Add TV small widget panels to DCW (after finishing big TV widget?)
- Write a "setup" flow that presets type, box count, col count and row count based on widget size, dpi, screen info, etc
    - "large", "med", "small" modes?
- Add "Setup" button that is in Box 2 when first opened. Clicking it goes to the github readme
- Fix and set up favourite formulas in KWGT before starting Media Control Widget

### TASKER
- Allow tasker and KWGT to process all data at once (losing some state updates maybe?)
- Create user-friendly message when HA is unavaliable
    - Use new setup GUI
    - Or use status bar? KWGT code complete, send string to kwgt br: "error"
- ~~Add "Status" bar at bottom of widget with small icon and text.~~ Use following errors:
    - HA Not running
    - Cannot get status update
    - State not returned after change
    - JSON Error
    - Tasker Error
- Set up and build Tasker plugin. Be sure to add setup features from JOAPPS tasker and potentially integreate KWGT setup into this?

### END OF PROJECT
- Make more templates
- Complete readme
- Make copies of every “fonticon” image and organize by device type in the templates folder
- Clean up code and optimize everything
- Setup auto-hiding icons where needed


## Icons
### Add to hawgts
- Schedule/calender
- Pool or Hot Tub room/controls

### Change
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

### Rename
- `All "ico" Icons to "dev"`
- `Look into sort again and find way to group icons better`
    - `"material" > "mdi" and append "-google"? or remove all prefix and append instead?`
- Identify all icons modified to fit in the corner. ".corner.mdi"?

## Future Features/Addons
- Integerate TV templates into widget once TV Remote is complete
- Try using web get in flows?
    ```
    $lv(json, wg("kfile:///org.kustom.provider/statedata.json", txt))$$#json$
    $tc(json, #json, "['light.bedroom_lights']['state']")$\
    ```

## VS Code terminal commands
- Commands:
    - `kwgt_reorder` - Reorder index count in separate JSON
    - `kwgt_check` - Checks for JSON errors
    - `kwgt_globals` - Output global list in the order they appear in the JSON. Terminal Output

## Kodine Tricks
- //? = tc(utf) for many characters like "':;()<>

## Legend
- This item is done in KWGT Editor

- `This item is done in preset.json`

- ~~This item is complete~~

### Chat GPT Changes

## Folder reorganizing
Certainly! Based on your repository's structure and the provided details, here's an exhaustive restructuring:

### Root Level
- `.vscode` (Configuration files for Visual Studio Code)
- `.resources` (Images specifically for the README)
- `README.md` (Main project overview)
- `fave_formulas.json` (To be deleted as mentioned)
- `index-reordering.tmp.json` (To be deleted as mentioned)

### Folders and Their Contents
- `docs/`
  - `setup/` (Setup instructions and related documentation)
  - `snippets_and_notes/` (Code snippets and to-do lists)
- `widgets/`
  - `dcw/` (Main app code for the Device Control Widget)
- `scripts/` (Scripting and automation files)

### Proposed Actions
- Create `docs/` and within it, `setup/` and `snippets_and_notes/`.
- Rename and move `Device Control Widget` to `widgets/dcw/`.
- Rename `Editing Scripts` to `scripts/`.
- Leave `.vscode`, `.resources`, and `README.md` at the root level.
- Delete `fave_formulas.json` and `index-reordering.tmp.json` as they are orphan files.

This structure aligns with the functional approach and organizes files based on their purpose and content, offering a clearer and more maintainable repository layout.