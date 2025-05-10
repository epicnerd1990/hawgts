# This file is meant for the developer for reference only and only for viewing in the code editor, not a rendering preview. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

###### #####################################
###### KWGT Backend
###### #####################################
- Backend KWGT Flows and Functions
- Autosizing Code
- Status System
- Colors

###### #####################################
###### Flow: "AA - Box 1 Position"
###### #####################################
**Trigger:** `On Change` of `$gv(boxtoggle)$$si(rratio)$`

**Action**

###### 1 - Formula
```
  $gv(func/alignment)$

  $lv(pos, (if(#align = 0 & #invert = 0, "RIGHT", 
               #align = 0 & #invert = 1, "LEFT", 
               #align = 1 & #invert = 0, "TOP", 
               #align = 1 & #invert = 1, "BOTTOM"
           )))$
  $if(gv(autoalign) = 1, #pos, gv(box1position))$
```

###### 2 - Set Global Variable: `box1position`

###### #####################################
###### Flow: "AA - Box 1 Size"
###### #####################################
**Trigger:** `On Change` of `$gv(widgetorient)$$gv(devicetype)$$gv(templateselect)$$gv(core/size/wgtwidth)$$gv(core/size/wgtheight)$`

**Action**

###### 1 - `Stop IF`: `autoalign` = 0

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

  $lv(size, (if(gv(box1sq)  = 1,  #wgtdir, 
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

###### 3 - Set Global Variable: `box1size`

###### #####################################
###### Autosizing Global Function
###### #####################################

###### func/alignment - Functions for template code to use to reference autosizing data
`$gv(func/alignment)$`
```
  $gv(func/theme)$

  $lv(align,    gv(widgetorient))$
  $lv(override, gv(autoalign))$
  $lv(invert,   gv(boxtoggle))$

  $lv(wgtwidth1, (if(#align = 0, si(rheight), si(rwidth))))$
  $lv(wgtdir,   (if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth  ))))$
  $lv(wgtdirop, (if(#align = 0, gv(core/size/wgtwidth),  gv(core/size/wgtheight ))))$
  $lv(iconcalc, (gv(iconsize) * 2) + #padobjhor)$
  $lv(pad,      (#wgtdir - #iconcalc))$

  $lv(box1sm, (gv(iconsize) + (#padobjver * 2) * 100 / #wgtwidth1))$
  $lv(box1lg, (((#padobjver * 2) + #iconcalc)))$
  $lv(boxalign, (if(gv(box1position) = RIGHT,  CENTERRIGHT,
                    gv(box1position) = LEFT,   CENTERLEFT,
                    gv(box1position) = TOP,    TOP,
                    gv(box1position) = BOTTOM, BOTTOM
  )))$
  $lv(boxcenter, (if(gv(box1position) = RIGHT,  VERTICAL_RIGHT,
                     gv(box1position) = LEFT,   VERTICAL_LEFT,
                     gv(box1position) = TOP,    HORIZONTAL_TOP,
                     gv(box1position) = BOTTOM, HORIZONTAL_BOTTOM
  )))$

  $lv(margin1, if(#align = 0, "#padobjver" , "#padobjhor"))$
  $lv(margin2, if(#align = 0, "#padobjhor" , "#padobjver"))$
```

###### #####################################
###### Autosizing Global Variables
###### #####################################

###### core/size/wgtwidth
`$gv(func/theme)$$si(rwidth) - (#padwgthor * 2)$`

###### core/size/wgtheight
`$gv(func/theme)$$si(rwidth) - (#padwgtver * 2)$`

###### core/size/box2width
```
  $gv(func/alignment)$
  $if(#tbox1 = 0 | #align = 1, gv(core/size/wgtwidth), (#wgtdirop - gv(box1size)))$
```

###### core/size/box2height
```
  $gv(func/alignment)$
  $if(#tbox1 = 0 | #align = 0, gv(core/size/wgtheight), (#wgtdirop - gv(box1size)))$
```

###### core/size/box2iconmax
```
  $gv(func/alignment)$
  $gv(func/theme)$
  $lv(box2dir,  (if(#align = 0, gv(core/size/box2width), gv(core/size/box2height))))$
  $lv(icon, ((gv(iconsize) * #ringsize / 100) + gv(iconsize) + #padobjhor))$
  $lv(boxsize, (#box2dir - gv(deviceiconsize)) / (#icon + #padobjhor))$
  $if(gv(fitmoreobj) = 0,
        (if(#boxsize <= 2, 2, mu(round, #boxsize))),
        10
     )$
```

###### #####################################
###### Status System **10%**
###### #####################################
```
  $lv(dbstatus, gv(errordb))$
  $lv(stnew, gv(currentstatus))$
  $lv(stname,   (tc(json, #dbstatus, (".status." + #stnew + ".error_name"))))$
  $lv(stdetail, (tc(json, #dbstatus, (".status." + #stnew + ".error_content"))))$
  $lv(out, #stdetail)$
```

###### #####################################
###### Theme System
###### #####################################

###### settheme
```
0##OneUI,
1##Material,
2##Basic,
3##Custom
```

###### theme/colors
`theme/colors/back1`
`theme/colors/back2`
`theme/colors/objiconon`
`theme/colors/objiconoff`
`theme/colors/objringon`
`theme/colors/objringoff`
`theme/colors/border`

###### theme/colors/reset
```
  $if(gv(theme/colors/back1 = "" &
         theme/colors/back2 = "" &
         theme/colors/objiconon = "" &
         theme/colors/objiconoff = "" &
         theme/colors/objringon = "" &
         theme/colors/objringoff = "" &
         theme/colors/border = "", 0
  ))$
```

###### theme/coloreditor/editcolor
- Select color to edit
```
0##Background 1,
1##Background 2,
2##Icon Off,
3##Icon On,
4##Ring Off,
5##Ring On,
6##Border Color,
7##Wallpaper Color 1,
8##Wallpaper Color 2
```

###### theme/colorgen/syscolorset
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

###### theme/coloreditor/wallcolorset
- Wallpaper & imported color only
```
0##None,
1##Saturation,
2##Brightness,
3##Complimentary Color
```

###### theme/coloreditor/adjcolor, theme/colorgen/adjcolor
- Filter/tone strength for `syscolorset` `wallcolorset`
- 0 - 100

###### theme/coloreditor/opacity theme/colorgen/opacity
- Adjust set color opacity
- 0 - 255

###### theme/coloreditor/output theme/colorgen/output
- Output of color editor/generator

###### theme/darkmode
- Switch: 1 = Dark mode

###### theme/border
- Toggle On/Off

###### theme/bordersize
- Set size of border around box

###### theme/objpadding
- Padding across "rows"

###### theme/objpadding2
- Padding across "columns"

###### theme/objpaddingside
- Padding from edge of box to first icon

###### theme/verticalpadding
- Vertical padding size

###### theme/horizontalpadding
- Horizontal padding size

###### theme/cornerradius
- Rounded corner radius