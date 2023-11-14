# Device Control Widget

DCW to-do lists, notes, ideas and more

## TO-DO Current

### New Features/Big changes
- Find way to reintroduce box2minsize check into box2width without loop
- ~~"Preset" folder where you can insert your HA information for preset templates~~
    - ~~Eg add "light.bedroom_lights" as room 1~~
    - ~~`Core added, apply to all devices in all templates. Working test: bedroom light off`~~
    - ~~`Add entity5 & 6`~~
    - ~~`Rename folder "entities". Rename note in folder`~~
- ~~Add a border option~~
- ~~Set up new tasker pipeline for custom commands for advanced users ()~~
- Work on Readme's
- **TESTING** Try fixing loading problems with colors by writing colors from a flow to a global or from a global to another global to keep a fixed copy while the color gets regenerated
    - Test note - After restart, color renders to a blue color. Add timer to start of color flow?
    - If test works, try setting "default" options to things like box size and icon alignment to avoid weird rendering issues while widget is loading changes
    - `Change color flows back to proper values after test`
- `Move "Hidden" Device Icon setting from deviceiconposition to deviceiconcat`
- Setup inital flow that sets devicecount, box1dir2, box1dir1
- Add all Device Icon types to Globals?
    - Current list = Category, New list = Category options - use global variables?
- Add every template to Globals??
    - Could make things easier for user?
    Add full check for how many rows are showing and show first icons accordingly in Box 2 to all templates
- Setup Mode
    - Set up "setupmode" text layout better and add a few different text options (sizing, json, etc)
    - Group in overlay groups and use visibility to display each one
    - Set up a note for visibility like the ones inside templates
- Create a "first run" flow that presets globals to work based on the size of the widget
    - temp global, get widget info, process, if different? then use, otherwise temp global??
    - Rows, Box 1 columns, Device count, etc
    - Potentially remove numbering system and give each folder a simple name with a note at the top



### Quick Fixes/Bugs
- ~~`"Rm" > "Room", "bordercolor" > "bordercolorlor", "bordersize" > "bordersizeze"`~~
- ~~`Swap 1 and 0 in appiconhide and remove duplicate`~~
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
- `Entities to go between --4 and --5?`
- `box1dir2percent > box1dir1size`
- `Remove box1width and box1height from formulas. Integrate box1height align calc into the few calls it has`
- Verify new boxalign formulas and flows work with vertical box1size
- Changing box1dir2 somehow makes box1width/height grow to ~3 icons tall

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

## Global Changes > Readme:
- Added haent
### Working
- Work on Readme's


### Future
- Work on README
    - ~~Create second readme "Global Settings"~~
        - Go through all global settings item by item. Organize page to be accessible by tree (multiple depths?)
    - Create third readme "Box Heiaracy"
        - Tree view of all boxes/templates, with screenshots. Use headers for subgroups like "2 Rowss"
- Background color system
    - Possibly re-do based on community resources
    - Possibly re-do based on UI types and Tasker setup interface/color picker
    - Make sure it works properly if not
- Setup auto-hiding icons where needed (After templates are complete)
- Reorganize Globals by folders
    - **Wait until new KWGT beta available, currently flows do not always write to globals inside folders**
    - Roughly sort by numbers

### Version 2.0 features*
- Make widget a Komponent that can be added to a KWLP wallpaper



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

## Kodine Tricks
- //? = tc(utf) for many characters like "':;()<>

## Legend
- This item is done in KWGT Editor

- `This item is done in preset.json`

- ~~This item is complete~~
