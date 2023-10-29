# Device Control Widget

DCW to-do lists, notes, ideas and more

## TO-DO Current

### Now
- Add code back to preset and debug new formulas
- Start box restructuring based on flow chart

### Working
- Alignment (Use "flip" value for some icons where it works.)
    - Test widget in all directions
    - Test all directions widget moves in (updated code before but couldnt test)
    - Enable optional row padding to all elements (ex. light rm 1) + swap row and icon padding based on alignment
    - Copy margin formula on light room 1 and enable on all other double rows`
- Device Icons - Check AC 2, Radiator and icons in different alignments
- `Create "rratio" global that saves the rratio when the widget first opens, then all flows look to it for changes (compare to rratio). on change, do flows, then update global`
- Restructure Boxes to flow chart
    - ~~Create"widgetitems" global which is a row count~~
        - `Add check in various codes for row count to simplify code``
        - Use for visibility of new box organization
    - `Modify box globals to be more user-friendly and simple`

### Future
- Templates
    - Finish "security" 2x2 and copy to 1x2 and 1x4
    - Copy lock 2x2 from sample and make 1x2
    - Setup soundbar 1x4
    - Setup fan 2x2
    - Re-setup battery with new battery icon
    - Create "Komponent" Templates for different modes
- Add "Status" bar at bottom of widget with small icon and text. Use following errors:
    - HA Not running
    - Cannot get status update
    - State not returned after change
    - JSON Error
    - Tasker Error


## TO-DO Project
### KWGT
- Background color system
    - Possibly re-do based on community resources
    - Possibly re-do based on UI types and Tasker setup interface/color picker
    - Make sure it works properly if not
- Add TV small widget panels to DCW (after finishing big TV widget?)
- Write a "setup" flow that presets type, box count, col count and row count based on widget size, dpi, screen info, etc
    - "large", "med", "small" modes?
- Setup auto-hiding icons where needed (After templates are complete)
- Add "Setup" button that is in Box 2 when first opened. Clicking it goes to the github readme

### TASKER
- Allow tasker and KWGT to process all data at once (losing some state updates)
- Create user-friendly message when HA is unavaliable to match new setup GUI

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
