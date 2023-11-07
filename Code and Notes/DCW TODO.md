# Device Control Widget

DCW to-do lists, notes, ideas and more

## TO-DO Current

### Now
- Find way to reintroduce box2minsize check into box2width without loop
- ~~devicosize does not get bigger inside box 1~~
- ~~`Update "paddingnote" and "--5."`~~
- ~~`Change "widgetorder" to list`~~
- ~~`Rename devicopo to deviceiconposition`~~
- ~~`Find out about box2min flow and merge into global`~~
- ~~`Fix deviceiconposition and deviceiconrotation - Make quotes work`~~
- ~~`Add check in every function that calls a List Global with a "DATA" value.`~~
    - ~~`Check will verify the Global is set correctly and not to "DATA"`~~
- ~~Update flow "AA Box 1 Position"~~
- ~~`Write rotate code that matches icon rotation main`~~
- ~~`Change deviceiconsize max to 450`~~
- ~~`Change "Regular" > "Default"`~~
- `Swap 1 and 0 in appiconhide`
- ~~Check box1dir1percent - value is 282?`~~
- ~~Device Icon rotation code~~
    - ~~New list global in --2: deviceiconrotation~~
        - ~~Rotate 90, 180, 270, Flip X/Y~~
    - ~~`Remove all existing rotation code and set rotation offset default to 180 for all icons`~~
        - ~~`Set Rotation to deviceiconrotation - Light "Lamp" as test`~~
- ~~Change Notes in folders to be as minimal and basic as possible. Add/Remove as needed~~ Done up until each template folder
- ~~Alignment~~
    - ~~Test widget in all directions~~
    - ~~Test all directions widget moves in and verify all diretions work~~
    - ~~Device Icons~~
        - ~~Verify all and add rotation to flip objects~~
        - ~~Check AC 2, Radiator and icons in different alignments~~
- ~~Revamp entire Device Icon and settings icon Global system~~
    - ~~Add Device Icon Position setting. 1 = Normal, 2 = Inside Box 1, 3 = Swap with settings icons~~
    - ~~Recode globals~~
    - ~~Code settings icons to move when swapped~~
    - ~~Code Box 1 icons to remove when device icon is in box 1?~~
    - ~~Rewrite box2min and iconcount in generate to allow for device icon movement~~
    - ~~Test box2min and iconcount once back in editor~~
- `Change color flows back to proper values after test`

### Working
- "Preset" folder where you can insert your HA information for preset templates
    - Eg add "light.bedroom_lights" as room 1
- **TESTING** Try fixing loading problems with colors by writing colors from a flow to a global or from a global to another global to keep a fixed copy while the color gets regenerated
- If above test works, try setting "default" options to things like box size and icon alignment to avoid weird rendering issues while widget is loading changes
- Templates
    - Finish "security" 2x2 and copy to 1x2 and 1x4
    - Copy lock 2x2 from sample and make 1x2
    - Setup soundbar 1x4
    - Setup fan 2x2
    - Re-setup battery with new battery icon
    - Create "Komponent" Templates for different modes
    - Check every template for blanks that work with respective layout
    - Switch all Box 1 > 2 Boxes templates to have columns instead of rows. Copy from Light template
- Setup Mode
    - Set up "setupmode" text layout better and add a few different text options (sizing, json, etc)
    - Group in overlay groups and use visibility to display each one
    - Set up a note for visibility like the ones inside templates

### Future
- Work on README
    - Create second readme "Global Settings"
        - Go through all global settings item by item. Organize page to be accessible by tree (multiple depths?)
    - Create third readme "Box Heiaracy"
        - Tree view of all boxes/templates, with screenshots. Use headers for subgroups like "2 Rowss"
- Background color system
    - Possibly re-do based on community resources
    - Possibly re-do based on UI types and Tasker setup interface/color picker
    - Make sure it works properly if not
- Setup auto-hiding icons where needed (After templates are complete)
- Set up new tasker pipeline for custom commands for advanced users ()

### Version 2.0 features (tentative)
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
