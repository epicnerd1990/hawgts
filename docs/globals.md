# Home Assistant control widgets for Android devices (HAWGT)
**SETUP TREE AND CLICKS TO TITLES**
## Global settings of widget
The purpose of this file is to give detailed explanations of what each global setting does. Many are obvious, but advanced users may find parts of this helpful for creating more custom widgets

\* = List item  
x = Locked

## Globals:
#### readme
Short overview of widget setup
#### setupmode
Setup mode is an overlay with widget information and stats. Advanced users will like it for fine tuning the <DCW> UI.
## --1. Colors
> Color subfolders here

## -- 2.
### devicecount
Select how many devices you'd like to control with this widget.  
- **1 Device**  

- **2 Devices**  

- **3 Devices**  
To control more than 2 devices, 3 Device templates use pages to fit more icons. The icons in Box 1 act as page selectors and the icons in Box 2 are the controls for that page. For example, in the Light template, Box 1 is the room selector and Box 2 offers controls like on, off, brightness and color for that room
> Affects: Templates, Box 1 interface
### box1dir1
(Select how many Rows to display (or columns if widget is vertical))  
Select how many rows (horizontal widget) or columns (vertical widget) to show on the widget.
- **1 Row**
- **2 Rows**
### box1dir2  
Configure the size of Box 1 against Box 2. Note that "Columns" below are actually "Rows" if the widget is vertical.
- **Remove (Single Box Mode)**  
Enable single box mode. This removes Box 1 and limits templates to single box options. Good for tiny widgets or lots of controls with no need to separate functions
> Affects: Avaliable templates and the number of icons that fit in Box 2
- **1 Column (Auto-Center)**  
Single column mode for devices with only 1 or two functions that are important or worth distinguishing 
- **2 Columns (Auto-Center)**  
Double column mode for devices with more than 2 important functions or to display the Device Icon more prominatly
- **Custom Size (Below)**  
Select this option to manually adjust the size of Box 1. See [box1size] below for more details and [Sizing Box 1](READMEURL) for specifics about Box 1
> Affects: iconpaddingbox
### box1size > box1size
(Set if you selected "Custom" above)  
This is the always the current size of Box 1 in kpx. It's updated when one of the Auto-Center options in box1dir2. If Custom is selected, the current number is the auto-centered calculation from the previous setting. See the section on [Sizing Box 1](READMEURL)
> Affects: iconpaddingbox, Box 1 > Column 2 visibility
### boxorder
*Set the order of the boxes on your widget
### appiconvis
Hide the App Icons
#### deviceiconcat
*Select the device icon to display in the corner of the widget or hide it in the next option
#### deviceiconposition
*Select the location of the Device Icon on your Widget
#### deviceiconrotation
*Select the rotation of the Device Icon on your Widget

### -- 3.
#### deviceiconsize
Set the normal (default) size of the Device Icon. Auto-shrinking options avaliable below.
Default: 120kpx
#### iconsize
Set the size of the widget icons.
Default: 80kpx
#### appiconsize
Set the size of the small app-launcher icon(s).
Default: 32kpx
#### cornerradius
Set the radius of the rounded corners.
Default: 10kpx

### -- 4.
#### iconpaddinginstack
Set the icon padding inside each row/column.
Default: 20kpx
#### iconpaddingstack
Set the icon padding between each row/column.
Default: 20kpx
#### iconpaddingbox
Set the icon padding from the edge of Box 2.
Default: 10kpx
#### paddingnote
The following options work best when your launchers "Remove Padding" option is set on this widget. This will allow tight boundaries if you have multiple widgets.
#### verticalpadding
Select the Vertical widget padding size
Default: 6kpx
#### horizontalpadding
Select the Horizontal widget padding size\nDefault: 3kpx

### -- 5.
*Extra Options

Use the following settings to adjust "Automatic" widget functions

- Auto-align - Disable and set alignment and box position manually. Use the follow two settings to manually set alignment
- Box 2 Alignment - Center icons in Box 2 instead of aligning to Box 1
- Device Icon Shrinking options
- Remove Device Icon from padding and sizing calculations
- Remove Device Icon touch to open editor
#### autoalign
Auto-align On/Off. Use next 2 options to manually set alignment
#### widgetorient
Set horizontal or vertical orientation of widget.
#### box1position
*Set position of Box 1
#### box2align
Select alignment of Box 2 content
#### deviceiconshrink
Select Shrinking options for the Device Icon.
*Select alignment of Box 2 content. Use for:\n- Templates without icons (like komponents)
Default: Auto-Shrink
#### deviceappiconpadding
Use padding of Device/App icons from Box 2 in internal calculations. Disable to allow more icons to fit in Box 2. Overlap may occur
#### deviceopeneditor
Enable device icon click to open KWGT Editor. Diable once widget is set up
#### x jsondb
jsondb is the widget status database. State changes that are set up in the HA Blueprint will be populated here. Do Not Edit!





### Full Global List extracted from JSON
generated
generated/widgetwidth
generated/widgetheight
box1size
generated/box1height
generated/box2width
generated/box2height
generated/box2minsize
generated/widgetratio
generated/iconcount
generated/deviceiconcalc
generated/pagenum
generated/status
generated/statusti
generated/statuslist
generated/color1
generated/color2
tasker
tasker/data
tasker/totasker
tasker/fromtasker
tasker/fromcolorpic
formulas
formulas/devicestate
formulas/lightlevel
formulas/lightcolor
formulas/jsonrementity
formulas/jsonaddentity
formulas/jsonaddnewline
formulas/jsonaddindents
formulas/stackalign
formulas/boxalign
formulas/boxsize
formulas/colcount
readme
setupmode
jsondb
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
-- 2.
devicecount
box1dir2
box1size
box1dir1
boxorder
appiconvis
deviceiconcat
deviceiconposition
deviceiconrotation
-- 3.
deviceiconsize
iconsize
appiconsize
cornerradius
-- 4.
iconpaddinginstack
iconpaddingstack
iconpaddingbox
paddingnote
verticalpadding
horizontalpadding
-- 5.
autoalign
widgetorient
box1position
box2align
deviceiconshrink
deviceappiconpadding
deviceopeneditor
tc1
tc2
