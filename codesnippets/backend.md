# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

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
  $if(gv(theme/advanced/autoalign) = 1, #pos, gv(theme/advanced/box1pos))$
```

###### 2 - Set Global Variable: `theme/advanced/box1pos`

###### #####################################
###### Flow: "AA - Box 1 Size"
###### #####################################
**Trigger:** `On Change` of `$gv(theme/advanced/widgetorient)$$gv(devicetype)$$gv(templateselect)$$gv(core/size/wgtwidth)$$gv(core/size/wgtheight)$`

**Action**

###### 1 - `Stop IF`: `theme/advanced/autoalign` = 0

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

  $lv(size, (if(gv(theme/advanced/box1sq)  = 1,  #wgtdir, 
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
###### Autosizing Global Function
###### #####################################

###### func/alignment - Functions for template code to use to reference autosizing data
`$gv(func/alignment)$`
```
  $gv(func/theme)$

  $lv(align,    gv(theme/advanced/widgetorient))$
  $lv(override, gv(theme/advanced/autoalign))$
  $lv(invert,   gv(theme/advanced/boxtoggle))$

  $lv(wgtwidth1, (if(#align = 0, si(rheight), si(rwidth))))$
  $lv(wgtdir,   (if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth  ))))$
  $lv(wgtdirop, (if(#align = 0, gv(core/size/wgtwidth),  gv(core/size/wgtheight ))))$
  $lv(iconcalc, (gv(theme/iconsize) * 2) + #padobjhor)$
  $lv(pad,      (#wgtdir - #iconcalc))$

  $lv(box1sm, (gv(theme/iconsize) + (#padobjver * 2) * 100 / #wgtwidth1))$
  $lv(box1lg, (((#padobjver * 2) + #iconcalc)))$
  $lv(boxalign, (if(gv(theme/advanced/box1pos) = RIGHT,  CENTERRIGHT,
                    gv(theme/advanced/box1pos) = LEFT,   CENTERLEFT,
                    gv(theme/advanced/box1pos) = TOP,    TOP,
                    gv(theme/advanced/box1pos) = BOTTOM, BOTTOM
  )))$
  $lv(boxcenter, (if(gv(theme/advanced/box1pos) = RIGHT,  VERTICAL_RIGHT,
                     gv(theme/advanced/box1pos) = LEFT,   VERTICAL_LEFT,
                     gv(theme/advanced/box1pos) = TOP,    HORIZONTAL_TOP,
                     gv(theme/advanced/box1pos) = BOTTOM, HORIZONTAL_BOTTOM
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
  $if(#tbox1 = 0 | #align = 1, gv(core/size/wgtwidth), (#wgtdirop - gv(theme/advanced/box1size)))$
```

###### core/size/box2height
```
  $gv(func/alignment)$
  $if(#tbox1 = 0 | #align = 0, gv(core/size/wgtheight), (#wgtdirop - gv(theme/advanced/box1size)))$
```

###### core/size/box2maxobj
```
  $gv(func/alignment)$
  $gv(func/theme)$
  $lv(box2dir,  (if(#align = 0, gv(core/size/box2width), gv(core/size/box2height))))$
  $lv(icon, ((gv(theme/iconsize) * #ringsize / 100) + gv(theme/iconsize) + #padobjhor))$
  $lv(boxsize, (#box2dir - gv(theme/deviceiconsize)) / (#icon + #padobjhor))$
  $if(gv(fitmoreobj) = 0,
        (if(#boxsize <= 2, 2, mu(round, #boxsize))),
        10
     )$
```

###### #####################################
###### Status System **40%**
###### #####################################

###### func/status
```
  $lv(dbstatus, gv(json/status))$

// template1
  $lv(objfit, gv(core/size/box2maxobj))$
  $lv(posid, (".box2.row1." + (#objfit - 1)))$
  $gv(func/jsontemp)$


// 1 = error, 0 = none
  $lv(comm1,     (if(# = #, 1, 0)))$
  $lv(comm2,     (if(# = #, 1, 0)))$
  $lv(comm3,     (if(# = #, 1, 0)))$
  $lv(template1, (if(#obid = 0, 1, 0)))$
  $lv(template2, (if(# = #, 1, 0)))$
  $lv(theme1,    (if(# = #, 1, 0)))$
  $lv(theme2,    (if(# = #, 1, 0)))$
  $lv(widget1,   (if(# = #, 1, 0)))$
  $lv(widget2,   (if(# = #, 1, 0)))$
  $lv(widget3,   (if(# = #, 1, 0)))$

// Load current errors
  $if(#comm1,    lv(error, (#error + "," + "comm;1" + #comm1)),
      #comm2,    lv(error, (#error + "," + "comm;2" + #comm2)),
      #comm3,    lv(error, (#error + "," + "comm;3" + #comm3)),
      #widget1,  lv(error, (#error + "," + "widget;1" + #widget1)),
      #widget2,  lv(error, (#error + "," + "widget;2" + #widget2)),
      #widget3,  lv(error, (#error + "," + "widget;3" + #widget3))
      #template1,lv(error, (#error + "," + "template;1" + #template1)),
      #template2,lv(error, (#error + "," + "template;2" + #template2)),
      #theme1,   lv(error, (#error + "," + "theme;1" + #theme1)),
      #theme2,   lv(error, (#error + "," + "theme;2" + #theme2)),
  )$

// Split data for use
  $lv(error1, tc(split, #error,  ",", 0))$
  $lv(cat1,   tc(split, #error1, ";", 0))$
  $lv(num1,   tc(split, #error1, ";", 1))$
  $lv(error2, tc(split, #error,  ",", 1))$
  $lv(cat2,   tc(split, #error2, ";", 0))$
  $lv(num2,   tc(split, #error2, ";", 1))$

// Output error message
  $lv(errorout1, tc(json, #dbstatus, ("." + #cat1 + "." + #error1 + ".error")))$
  $lv(errorout2, tc(json, #dbstatus, ("." + #cat2 + "." + #error2 + ".error")))$

```


**V2** fl() test
```
  $lv(dbstatus, gv(json/status))$

// "template.1.error"
  $lv(objfit, gv(core/size/box2maxobj))$
  $lv(posid, (".box2.row1." + (#objfit - 1)))$
  $gv(func/jsontemp)$

// "template.1.error"
  $lv(temp1, (if(#obid = 0, (tc(json, #dbstatus, (".template.1.error"), 1)))))$
  $lv(temp2, (if(#obid = 0, (tc(json, #dbstatus, (".template.2.error"))))))$
  $lv(temp3, (if(#obid = 0, (tc(json, #dbstatus, (".template.3.error"))))))$

  $lv(cat, "temp")$

  $lv(i, 1)$
  $lv(erroroutput, fl(1, 3, "i + 1", 
    (lv(temp, "#temp" + i) + 
     if(gv(core/statusname, #temp) = 1, #temp)
    ),
  ","))$


  $lv(erroroutput, fl(1, 3, "i + 1", 
    (lv(temp, "#temp" + i) +
     lv(form, gv(core/statusname, #temp)) +
     lv(run, fl(0,0,0, #form)) +
     if(#run = 1, i, 0)
    )))$
  $#erroroutput$
  
    $lv(error, (tc(json, #dbstatus, ("." + #cat + "." + #erroroutput + ".error"), 1)))
  $#error$
```

`gv(core/statusname)`
```
temp1##if(#obid = 0, 1, 0)
temp2##if(#trowct = 2, 1, 0)
temp3##temp3
```

**V1 - 2023**
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
0##OneUI,1##Material,2##Basic
```

###### theme/colors
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

###### colors/coloreditor/editcolor
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
8##Wallpaper Color 2,
9##Wallpaper Color 3
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

###### colors/coloreditor/wallcolorset
- Wallpaper & imported color only
```
0##None,
1##Saturation,
2##Brightness,
3##Complimentary Color
```

###### `theme/deviceiconrotation` 
```
DATA##\"Select the rotation of the Device Icon on your Widget\",
NONE##0 Degrees,
DEG90##90 Degrees,
DEG180##180 Degrees,
DEG270##180 Degrees,
FLIP_X##Flip Vertically,
FLIP_Y##Flip Horizontally
```

###### `appiconvis`
```
DATA##\"Set visbility of App Icons. Hiding these when using one row will make the device icon to fit the widget height.\",
0##Show App Icons,
1##Hide App Icons
```