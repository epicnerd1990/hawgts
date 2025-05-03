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
  $lv(align,    gv(widgetorient))$
  $lv(override, gv(autoalign))$
  $lv(invert,   gv(boxtoggle))$

  $lv(objpad1,    gv(theme/objpadding))$
  $lv(objpad2,    gv(theme/objpadding2))$
  $lv(objboxpad2, gv(theme/objpaddingbox2))$

  $lv(wgtwidth1, (if(#align = 0, si(rheight), si(rwidth))))$
  $lv(wgtdir,   (if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth  ))))$
  $lv(wgtdirop, (if(#align = 0, gv(core/size/wgtwidth),  gv(core/size/wgtheight ))))$
  $lv(iconcalc, (gv(iconsize) * 2) + #objpad1)$
  $lv(pad,      (#wgtdir - #iconcalc))$

  $lv(box1sm, (gv(iconsize) + (#objboxpad2 * 2) * 100 / #wgtwidth1))$
  $lv(box1lg, (((#objboxpad2 * 2) + #iconcalc)))$
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

  $lv(margin1, if(#align = 0, "#objpad2" , "#objpad1"))$
  $lv(margin2, if(#align = 0, "#objpad1" , "#objpad2"))$
```

###### #####################################
###### Autosizing Global Variables
###### #####################################

###### core/size/wgtwidth
`$si(rwidth) - (gv(theme/horizontalpadding) * 2)$`

###### core/size/wgtheight
`$si(rwidth) - (gv(theme/verticalpadding) * 2)$`

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
  $lv(box2dir,  (if(#align = 0, gv(core/size/box2width), gv(core/size/box2height))))$
  $lv(icon, gv(iconsize) + #objpad1)$
  $lv(boxsize, (#box2dir - gv(deviceiconsize)) / (#icon + #objpad1))$
  $if(gv(fitmoreobj) = 0,
        (if(#boxsize <= 2, 2, mu(round, #boxsize))),
        10
     )$
```

###### #####################################
###### Status System **10%**
###### #####################################

TEST 2 - Change to JSON
```
  $lv(dbstatus, gv(errordb))$
  $lv(stnew, gv(currentstatus))$
  $lv(stname,   (tc(json, #dbstatus, (".status." + #stnew + ".error_name"))))$
  $lv(stdetail, (tc(json, #dbstatus, (".status." + #stnew + ".error_content"))))$
  $lv(out, #stdetail)$
```

###### #####################################
###### Color System **60%**
###### #####################################

###### theme/colors/back1
```
  $lv(theme, (if(#ctheme = 0, "OneUI",
                 #ctheme = 1, "Material",
                 #ctheme = 2, "Basic",
                 #ctheme = 3, "Custom",
                 "0"
              )))$
  Source: $#theme$

  $lv(filter, (if(gv(theme/color1fil1) = 1, "sat",
                  gv(theme/color1fil1) = 2, "lum",
                  gv(theme/color1fil1) = 3, "comp",
                  "sat"
              )))$
  Filter: $#filter$

  $lv(coltone, (if(gv(theme/color1sysc) = 0, "sysca1",
                   gv(theme/color1sysc) = 1, "sysca2",
                   gv(theme/color1sysc) = 2, "sysca3",
                   gv(theme/color1sysc) = 3, "syscn1",
                   gv(theme/color1sysc) = 4, "syscn2",
                   gv(theme/color1sysc) = 5, "syscn3",
                   "sysca1"
                )))$
  Color Tone: $#coltone$

  $lv(addsub, (if(gv(theme/color1fil2) = 0, "", 
                  gv(theme/color1fil2) = 1, "a",
                  gv(theme/color1fil2) = 2, "r",
                  ""
              )))$
  Addsub: $#addsub$

// Use `#filter` and `theme/color#adj` to change color
  $if(gv(theme/color1fil1) > 0 & (#theme = "wpcolor1" | #theme = "wpcolor2"), 
        lv(color1, ce(si(#theme), #filter, gv(theme/color1adj)
      )))$
  Wallpaper Color: $#color1$

  $if(gv(theme/color1fil1) > 0 & #theme = "sys",
        lv(color2, si(#coltone, gv(theme/color1adj)
      )))$
  Sysui: $#color2$

  $if(gv(theme/color1fil1) = 0 | gv(theme/settheme) = 3, lv(col3, gv(theme/color1pick)))$ 
  Colpick: $#col3$

  $lv(color, (if(gv(theme/settheme) <= 1, #color1,
                 gv(theme/settheme)  = 2, #color2,
                 gv(theme/settheme) = 3, #col3
              )))$
  $#color$

  $lv(final, ce(#color, alpha, gv(theme/color1alph)))$
  Final color: $#final$
```

**V1**
```
  $lv(source, (if(gv(theme/settheme) = 0, "wpcolor1",
                  gv(theme/settheme) = 1, "wpcolor2",
                  gv(theme/settheme) = 2, "sys",
                  "0"
              )))$
  Source: $#source$

  $lv(filter, (if(gv(theme/color1fil1) = 1, "sat",
                  gv(theme/color1fil1) = 2, "lum",
                  gv(theme/color1fil1) = 3, "comp",
                  "sat"
              )))$
  Filter: $#filter$

  $lv(coltone, (if(gv(theme/color1sysc) = 0, "sysca1",
                   gv(theme/color1sysc) = 1, "sysca2",
                   gv(theme/color1sysc) = 2, "sysca3",
                   gv(theme/color1sysc) = 3, "syscn1",
                   gv(theme/color1sysc) = 4, "syscn2",
                   gv(theme/color1sysc) = 5, "syscn3",
                   "sysca1"
                )))$
  Color Tone: $#coltone$

  $lv(addsub, (if(gv(theme/color1fil2) = 0, "", 
                  gv(theme/color1fil2) = 1, "a",
                  gv(theme/color1fil2) = 2, "r",
                  ""
              )))$
  Addsub: $#addsub$

  $if(gv(theme/color1fil1) > 0 & (#source = "wpcolor1" | #source = "wpcolor2"), 
        lv(color1, ce(si(#source), #filter, gv(theme/color1adj)
      )))$
  Wallpaper Color: $#color1$

  $if(gv(theme/color1fil1) > 0 & #source = "sys",
        lv(color2, si(#coltone, gv(theme/color1adj)
      )))$
  Sysui: $#color2$

  $if(gv(theme/color1fil1) = 0 | gv(theme/settheme) = 3, lv(col3, gv(theme/color1pick)))$ 
  Colpick: $#col3$

  $lv(color, (if(gv(theme/settheme) <= 1, #color1,
                 gv(theme/settheme)  = 2, #color2,
                 gv(theme/settheme) = 3, #col3
              )))$
  $#color$

  $lv(final, ce(#color, alpha, gv(theme/color1alph)))$
  Final color: $#final$
```


###### theme/colors/back2
```
$lv(source, (if(gv(theme/color2sel) = 0, "wpcolor1", gv(theme/color2sel) = 1, "wpcolor2", gv(theme/color2sel) = 2, "sys", "0")))$
$lv(filter, (if(gv(theme/color2fil1) = 1, "sat", gv(theme/color2fil1) = 2, "lum", gv(theme/color2fil1) = 3, "comp", "sat")))$
$lv(coltone, (if(gv(theme/color2sysc) = 0, "sysca1", gv(theme/color2sysc) = 1, "sysca2", gv(theme/color2sysc) = 2, "sysca3", gv(theme/color2sysc) = 3, "syscn1", gv(theme/color2sysc) = 4, "syscn2", gv(theme/color2sysc) = 5, "syscn3", "sysca1")))$
$lv(addsub, (if(gv(theme/color2fil2) = 0, "", gv(theme/color2fil2) = 1, "a", gv(theme/color2fil2) = 2, "r", "")))$

$if(gv(theme/color2fil1) > 0 & (#source = "wpcolor1" | #source = "wpcolor2"), lv(color1, ce(si(#source), #filter, gv(theme/color2adj))))$
$if(gv(theme/color2fil1) > 0 & #source = "sys", lv(color2, si(#coltone, gv(theme/color2adj))))$
$if(gv(theme/color2fil1) = 0 | gv(theme/color2sel) = 3, lv(col3, gv(theme/color2pick)))$

$lv(color, (if(gv(theme/color2sel) <= 1, #color1, gv(theme/color2sel) = 2, #color2, gv(theme/color2sel) = 3, #col3)))$
$lv(final, ce(#color, alpha, gv(theme/color2alph)))$
$#final$
```

###### theme/settheme

```
0##Material,
1##OneUI,
2##Basic,
3##Custom
```

**V1**
```
0##Wallpaper #1,
1##Wallpaper #2,
2##System (MaterialYou),
3##Manual (Color picker)
```

###### theme/color1sysc, theme/color2sysc
System and Material UI only
```
0##Accent #1,
1##Accent #2,
2##Accent #3,
3##Neutral #1,
4##Neutral #2,
5##Neutral #3
```

###### theme/color1fil1, theme/color2fil1
```
0##None,
1##Saturation,
2##Brightness,
3##Complimentary Color
```

###### theme/color1adj, theme/color2adj
- Filter strength for `theme/color#fil1`
- 0 - 100


###### theme/color1fil2, theme/color2fil2
- Control above filter for `theme/color#fil1`
```
0##None,
1##Add,
2##Remove
```

###### theme/color1pick, theme/color1pick
- Manually set color using KWGT picker

###### theme/color1alph, theme/color1alph
- Adjust set color opacity
- 0 - 255

###### theme/colors/objiconon
- Icon color - KWGT picker

###### theme/colicoon
- Color of icon when state is on - dark saturation of color1?
```
  $ce(si(wpcolor1, comp), sat, 60)$
```

###### theme/color1fin, theme/color2fin
- Color 1/Color 2 final - KWGT picker

###### theme/border
- Toggle On/Off

###### theme/colors/border
- Border  color - KWGT picker (fix)
