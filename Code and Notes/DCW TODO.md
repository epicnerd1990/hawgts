# Device Control Widget

DCW to-do lists, notes, ideas and more

## TO-DO Current

### Now
- ~~Add code back to preset and debug new formulas~~
- ~~Box restructuring based on flow chart~~
- ~~`Fix generated/boxsize name`~~
- ~~`Fix box1dir2 text (fixed in notes)`~~
- ~~`Fix box2align note (fixed in notes)`~~
- `Fix readme` **Potentially Fixed, check**
- ~~`Reverse advanced/auto-align`~~
- ~~`Fix "devicecount" notes to remove config selection`~~
- ~~`"Select" > "Select", "Row" > "Rows', "2 Boxes" > "2 Boxes", "Device" > "Devices"`~~
- ~~`Update notes with a 1st item to have a different value. Maybe "-- CLICK FOR SETUP NOTE --"?`~~
- ~~`Add section after end of setup in globals for post-configuration`~~` - Move device icon click, app icons, etc`
- ~~`Move 3 main folders to top top of globals`~~
- ~~`Create global formulas for box 1 column and row alignments, padding, etc`~~
- ~~`Remove "1-device" and "2-device" globals`~~
- ~~`Rename box1dir2`~~
- ~~`Move any global that a flow updates out of folders - Remove advanced folder?`~~ Check Color Flows in Editor
- ~~`Rename Flows to be easily recognisable`~~
- `Update "paddingnote" and "--5."`
- `Change "widgetorder" to list`
- `Change color flows back to proper values after test`
- `Add check in every function that calls a List Global with a "DATA" value.`
    - `Check will verify the Global is set correctly and not to "DATA"`
    - `Use Global function with preset error message with global set from check`
        - "Error: Global "#glbname" not set correctly"
- ~~Change Notes in folders to be as minimal and basic as possible. Add/Remove as needed~~ Done up until each template folder
- ~~Create "Enabled" and "Disabled" text objects for in boxes - based on Global settings~~
- ~~Enable optional row padding to all elements (ex. light rm 1) + swap row and icon padding based on alignment~~
- ~~Copy margin formula on light room 1 and enable on all other double rows~~
- Investigate Box 2 size problems

### Working
- Alignment (Use "flip" value for some icons where it works.)
    - Test widget in all directions
    - Test all directions widget moves in and verify all diretions work
    - Device Icons
        - Verify all and add rotation to flip objects
        - Check AC 2, Radiator and icons in different alignments
- ~~`Create "rratio" global that saves the rratio when the widget first opens, then all flows look to it for changes (compare to rratio). on change, do flows, then update global`~~
- Restructure Boxes to flow chart
    - ~~Create"box1dir2" global which is a row count~~
        - ~~`Change row count checks to new global`~~
        - ~~Use for visibility of new box organization~~
    - ~~`Modify box globals to be more user-friendly and simple`~~
- Try fixing loading problems with colors by writing colors from a flow to a global or from a global to another global to keep a fixed copy while the color gets regenerated <**TESTING NOW**>

### Future
- Templates
    - Finish "security" 2x2 and copy to 1x2 and 1x4
    - Copy lock 2x2 from sample and make 1x2
    - Setup soundbar 1x4
    - Setup fan 2x2
    - Re-setup battery with new battery icon
    - Create "Komponent" Templates for different modes
    - Check every template for blanks that work with respective layout
    - Switch all Box 1 > 2 Boxes templates to have columns instead of rows. Copy from Light template
- "Preset" folder where you can insert your HA information for preset templates
    - Eg add "light.bedroom_lights" as room 1
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




## TO-DO Project
### KWGT
- Add TV small widget panels to DCW (after finishing big TV widget?)
- Write a "setup" flow that presets type, box count, col count and row count based on widget size, dpi, screen info, etc
    - "large", "med", "small" modes?
- Add "Setup" button that is in Box 2 when first opened. Clicking it goes to the github readme
- Fix and set up favourite formulas in KWGT before starting Media Control Widget

### TASKER
- Allow tasker and KWGT to process all data at once (losing some state updates)
- Create user-friendly message when HA is unavaliable
    - Use new setup GUI
    - Or use status bar? KWGT code complete, send string to kwgt br: "error"
- ~~Add "Status" bar at bottom of widget with small icon and text.~~ Use following errors:
    - HA Not running
    - Cannot get status update
    - State not returned after change
    - JSON Error
    - Tasker Error

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
    - Solar Panels
- Stick in the corner of the screen
    - Snowflake
    - Alert (x2?)
- Add chain to lock icon

### Rename
- `All "ico" Icons to "dev"`
- `Look into sort again and find way to group icons better`
    - `"material" > "mdi" and append "-google"?`

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
