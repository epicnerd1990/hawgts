# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

###### #####################################
###### KWGT Backend
###### #####################################
- Backend KWGT Flows and Functions
- Autosizing Code
- Status System
- App launcher
- Colors

###### #####################################
###### Flow - Box 1 Position
###### #####################################
**Trigger:** `On Change` of `$gv(theme/advanced/boxtoggle)$$si(rratio)$`

**Action**

###### 1 - Formula
```
  $gv(func/alignment)$

  $lv(pos, (if(#align = 0 & #invert = 0, "RIGHT", 
               #align = 0 & #invert = 1, "LEFT", 
               #align = 1 & #invert = 0, "TOP", 
               #align = 1 & #invert = 1, "BOTTOM"
           )))$
  $if(gv(theme/advanced/autoalign) = 1, #pos, #box1pos)$
```

###### 2 - Set Global Variable: `theme/advanced/box1pos`

###### #####################################
###### Flow - Box 1 Size
###### #####################################
**Trigger:** `On Change` of `$gv(theme/advanced/widgetorient)$$gv(devicetype)$$gv(templateselect)$`

**Action**

###### 1 - `Stop IF`: `theme/advanced$#/autoalign` = 0

###### 2 - Formula - Write to the template
```
  $gv(func/jsontemp)$
  $gv(func/alignment)$

  $lv(box1col1, 
      (tc(json, #dbtemp, 
        (if(#tpageon = 0, (#template + ".box1.row1.1"),
                         (#template + ".page" + #curpage + ".box1.row1.1")
        ))
      ))
  )$
  $lv(box1col2, 
      (tc(json, #dbtemp, 
        (if(#tpageon = 0, (#template + ".box1.row1.1"),
                         (#template + ".page" + #curpage + ".box1.row2.1")
        ))
      ))
  )$
  $lv(box1col, if(#box1col1 = "" & #box1col2 = "", 0, 1))$

  $lv(size, (if(gv(theme/advanced/box1sq)   = 1,   #wgtdir,
              (if(#tbox1    = 0,                   0,
                  #tbox1    = 1,                   #box1sm,
                  #tbox1    = 2 & #box1col  = 0,   #box1sm,
                  #tbox1    = 2 & #box1col  = 1,   #box1lg,
                  #ticopos  = 2 & #tbox1    = 2,   #wgtdir,
                  #ticopos  = 2 & #tbox1    = 1,   #box1sm,
                  #wgtdir
              ))
            )))$
  $#size$
```

###### 3 - Set Global Variable: `theme/advanced/box1size`

###### #####################################
###### Flow - External launcher
###### #####################################
**Trigger:** Manual (called from object touch)

**Action**

###### 1 - Formula
> #last = devicon, appicon1, appicon2
```
  $lv(ext, gv(external/#last))$
  $gv(func/jsonmain)$

  $lv(app,
    if(#ext = 1, ("ha.entity." + #entity),
       #ext = 2, "ha",
       #ext = 3, "ha.assist",
       #ext = 4, "kwgt",
       #ext = 5, gv(external/custom),
       ""
  ))$
  $lv(action, if(#ext = 5, "custom", "open"))$
  $"tasker://secondary?action=" + #action + "&app=" + #app$
```

###### 2 - Open URI
`#last`

Examples:
```
tasker://secondary?action=open&app=ha
tasker://secondary?action=custom&app=com.samsung.android.app
```

###### #####################################
###### Flow - Update status
###### #####################################
**Trigger:** `On Change` of `$gv(core/status/setstatus)$`

**Action**

###### 1 - Stop If
`#cat1` or `#error1` = ""

###### 2 - Formula
```
$gv(func/status)$
$tc(json, #dbstatus, ("." + #cat1 + "." + #error1 + ".error"))$
```

###### 3 - Set Global Variable `core/status/currentstatus`

###### 4 - Toggle Global Variable `core/status/statustimer`

###### #####################################
###### Status System **45%**
###### #####################################

###### func/status
`$gv(func/status)$`
```
  $lv(dbstatus, gv(json/status))$
  $lv(status, gv(core/tasker/status))

  $lv(posid, (".box2.row1." + (#box2maxobj - 1)))$
  $gv(func/jsonobj)$

  $gv(func/themecolors)$

  $lv(comm1, if(#status != "" | #status != "br(tasker, status)", 1, 0))$
  $lv(comm2, if(#status != "" | #status != "br(tasker, status)", 1, 0))$
  $lv(comm3, if(#status != "" | #status != "br(tasker, status)", 1, 0))$
  $lv(widget1,
      if(#tname   = "" | #ticon   = "" | #tbox1   = "" |
         #trowct  = "" | #ticopos = "" | #tdevct  = "" |
         #obid    = "" | #obtype  = "" | #obicon  = "" |
         #obaction= "" | #obcmd   = "" | #obdata  = "" |
         #obparam = "",
        1, 0
  ))$
  $lv(widget2,
      if(#obtype    = "" | #obonstate = "" | #ststate = "" |
         #stobtype  = "" | #stoutput = ""  | gv(json/device) = "",
        1, 0
  ))$
  $lv(widget3,
      if(#template = "" | #tpageon   = "" | #obj     = "" |
         #curpage  = "" | #objfindkey= "" | #objkey  = "" |
         #entity   = "" | #entitykey = "" | #box1obj = "",
        1, 0
  ))$
  $lv(widget4,   "")$
  $lv(template1, if(#obid = 0, 1, 0))$
  $lv(template2, "")$
  $lv(theme1,
        if(#back1   = "" | #back2     = "" | #objiconon  = "" |
         #objiconoff= "" | #objringon = "" | #objringoff = "" |
         #border    = "",
         1, 0
  ))$
  $lv(theme2,    "")$
  $lv(theme3, if(#dbtheme = "" | #settheme = "", 1, 0))$
  $lv(setup1, if(gv(devicetype) = "DATA", 1, 0))$

  $lv(error,
    (if(#comm1 = 1,     (if(#error = "", #error + ";") + "comm,1"),
        #comm2 = 1,     (if(#error = "", #error + ";") + "comm,2"),
        #comm3 = 1,     (if(#error = "", #error + ";") + "comm,3"),
        #widget1 = 1,   (if(#error = "", #error + ";") + "widget,1"),
        #widget2 = 1,   (if(#error = "", #error + ";") + "widget,2"),
        #widget3 = 1,   (if(#error = "", #error + ";") + "widget,3"),
        #widget4 = 1,   (if(#error = "", #error + ";") + "widget,4"),
        #template1 = 1, (if(#error = "", #error + ";") + "template,1"),
        #template2 = 1, (if(#error = "", #error + ";") + "template,2"),
        #theme1 = 1,    (if(#error = "", #error + ";") + "theme,1"),
        #theme2 = 1,    (if(#error = "", #error + ";") + "theme,2"),
        #setup1 = 1,    (if(#error = "", #error + ";") + "setup,1")
  )))$

  $lv(error1, tc(split, #error,  ";", 0))$
  $lv(cat1,   tc(split, #error1, ",", 0))$
  $lv(num1,   tc(split, #error1, ",", 1))$

```

###### #####################################
###### External launcher
###### #####################################

###### external/devicon external/appicon1 external/appicon2
> No "0##Hidden" entry in `external/devicon`
```
0##Hidden,
1##This device in Home Assistant App,
2##Home Assistant App,
3##Home Assistant Assist,
4##KWGT Editor (Great while configuring widget),
5##Custom
```

###### "icon_icon"
```
  $gv(func/jsontemp)$
  $lv(ext, gv(external/appicon1))$
  $if(#ext = 1, #ticon,
      #ext = 2, "app-home-assistant",
      #ext = 3, "app-home-assistant-assist",
      #ext = 4, "mdi-cog",
      #ext = 5, #ticon,
      ""
  )$
```

###### #####################################
###### Alignment Global Function
###### #####################################

###### func/alignment - Functions for widget objects to reference sizing data
`$gv(func/alignment)$`
```
  $gv(func/theme)$
  $gv(func/jsontemp)$

  $lv(align,     gv(theme/advanced/widgetorient))$
  $lv(override,  gv(theme/advanced/autoalign))$
  $lv(invert,    gv(theme/advanced/boxtoggle))$
  $lv(box1pos,   gv(theme/advanced/box1pos))$
  $lv(box1size,  gv(theme/advanced/box1size))$
  $lv(objsize,   gv(theme/objsize))$

  $lv(wgtwidth,  (si(rwidth) - (#padwgtver * 2)))$
  $lv(wgtheight, (si(rheight) - (#padwgthor * 2)))$
  $lv(wgtdir,    (if(#align = 0, #wgtheight, #wgtwidth )))$
  $lv(wgtdirop,  (if(#align = 0, #wgtwidth,  #wgtheight)))$
  $lv(devobjpos, (if(#align = 0, #wgtheight, #box1size)))$
  $lv(objiconsize,(#objsize - (#objsize * #iconsize / 100)))$
  $lv(devposcalc,((#devobjpos / 2) + #padwgtver - (gv(theme/deviceiconsize) / 2)))$

  $lv(box2obj,   (#objsize + #padobjhor))$
  $lv(box2width,
    (if(#tbox1 = 0 | #align = 1, #wgtwidth, (#wgtdirop - #box1size))))$
  $lv(box2height,
    (if(#tbox1 = 0 | #align = 0, #wgtheight, (#wgtdirop - #box1size))))$
  $lv(box2dir,   (if(#align = 0, #box2width, #box2height)))$
  $lv(box2size,  (#box2dir - gv(theme/deviceiconsize)) / (#box2obj + #padobjhor))$
  $lv(box2maxobj,(if(gv(fitmoreobj) = 0,
        (if(#box2size <= 2, 2, mu(round, #box2size))), 10
     )))$

  $lv(box1sm, (#objsize + (#padobjside * 2) * 100 / #wgtdir))$
  $lv(box1lg, (((#objsize + #padobjside) * 2) + #padobjhor))$

  $lv(L, "LEFT")$   $lv(R, "RIGHT")$ $lv(T, "TOP")$
  $lv(B, "BOTTOM")$ $lv(C, "CENTER")$

  $lv(box1anchor,
    (if(#box1pos = #R, (#C + #R), #box1pos = #L, (#C + #L),
        #box1pos = #T, #T,        #box1pos = #B, #B
  )))$
  $lv(box1center,
    (if(#box1pos = #R, ("VERTICAL_" + #R), #box1pos = #L, ("VERTICAL_" + #L),
        #box1pos = #T, ("VERTICAL_" + #T), #box1pos = #B, ("VERTICAL_" + #B)
  )))$

  $lv(devicopos,
    if(#box1pos = #R,
        (if(#ticopos = 1, (#T + #L), #ticopos = 2, (#C + #R),
            #ticopos = 3, (#B + #L)
        )),
       #box1pos = #L,
        (if(#ticopos = 1, (#T + #R), #ticopos = 2, (#C + #L),
            #ticopos = 3, (#B + #R)
        )),
       #box1pos = #T,
        (if(#ticopos = 1, (#B + #L), #ticopos = 2, #T,
            #ticopos = 3, (#B + #R)
        )),
       #box1pos = #B,
        (if(#ticopos = 1, (#T + #L), #ticopos = 2, #B,
            #ticopos = 3, (#T + #R)
        ))
    ))$
  $lv(appicopos,
    (if(#box1pos = #R, (#B + #L),
        #box1pos = #L, (#B + #R),
        #box1pos = #T, (#B + #R),
        #box1pos = #B, (#T + #R)
    )))$
  $lv(devicoalign,
    (if(#box1pos = #R | #box1pos = #L, 1,
        #box1pos = #T | #box1pos = #B, 0
    )))$


  $lv(pad1, if(#align = 0, "#padobjver" , "#padobjhor"))$
  $lv(pad2, if(#align = 0, "#padobjhor" , "#padobjver"))$
  $lv(pad3, if(#align = 0, "#padwgtver" , "#padwgthor"))$
```

###### #####################################
###### Theme Globals and lists
###### #####################################

###### settheme
```
OneUI,Material,Basic
```

###### theme/deviceiconrotation
```
DATA##\"Select the rotation of the Device Icon on your Widget\",
NONE##0 Degrees,
DEG90##90 Degrees,
DEG180##180 Degrees,
DEG270##180 Degrees,
FLIP_X##Flip Vertically,
FLIP_Y##Flip Horizontally
```

###### colors
`colors/back1`
`colors/back2`
`colors/objiconon`
`colors/objiconoff`
`colors/objringon`
`colors/objringoff`
`colors/border`

###### colors/reset
```
  $if(gv(colors/back1 = "" &
         colors/back2 = "" &
         colors/objiconon = "" &
         colors/objiconoff = "" &
         colors/objringon = "" &
         colors/objringoff = "" &
         colors/border = "", 0
  ))$
```

###### colors/coloredit/editcolor
- Select color to edit
```
back1##Background 1,
back2##Background 2,
objiconoff##Icon Off,
objiconon##Icon On,
cobjringoff##Ring Off,
cobjringon##Ring On,
border##Border Color,
wpcolor1##Wallpaper Color 1,
wpcolor2##Wallpaper Color 2,
wpcolor3##Wallpaper Color 3
```

###### colors/colorgen/syscolorset
- System and Material UI only
```
0##None,
1##Accent #1,
2##Accent #2,
3##Accent #3,
4##Neutral #1,
5##Neutral #2,
6##Neutral #3
```

###### colors/coloredit/filter
- Wallpaper & imported color only
```
0##None,
1##Saturation,
2##Brightness,
3##Complimentary Color
```

###### colors/themeinfo
Use this to override color properties set by the theme JSON. All values in this folder are ignored when empty.\n\n

There is also a color generator to use your phone's system colors and an editor to modify colors\n\n

See readme for more information and JSON config

###### colors/colorgen/info
Use this color generator to get colors from your device theme and modify them\n\n

This is based on \"Material Design\" specifications. So \"Primary 80\" should be Accent #1 + 80% tone. See specifications online and readme for more details.\n\n

Note: Background color opacity can also be set in the \"theme\" folder\n\n

You will have to copy the color to where you want to use it when generated

###### colors/coloredit/info
Use this color editor to modify the existing colors used in your theme, or extracted from your wallpaper\n\n

Select the color source you want to modify. Colors are from the selected theme or your overridden color, if set. Wallpaper colors are extracted from the device wallpaper by KWGT.\n\n

Note: Background color opacity can also be set in the \"theme\" folder\n\n

You will have to copy the color to where you want to use it once complete

###### `readme`
```
See readme.md for instructions on how to connect HAWGTS with your Home Assistant system. Follow the instructions below to setup your widget with the settings in this menu\n\n

1. Choose a theme. Change any theme settings or colors you'd like to make it your own, or skip this part\n
2. Set your device type, then toggle through the templates to see which one works for your layout and launcher\n
3. Set your entity_id(s) from Home Assistant\n\n

The widget should now be able to control your configured device. Once your first widget is complete, export it from KWGT to make copies of your customized widget for other devices
```