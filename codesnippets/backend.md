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

###### 1 - Formula - Write to the template
ADD "Stop If" in Flow UI
if `autoalign` = 0, dont proceed
```
  $gv(func/jsontemp)$
  $gv(func/alignment)$

  $lv(box1col1, 
      (tc(json, #dbtemp, 
        (if(#tpageon = 0, (#template + ".box1.row1.1"),
                         (#template + ".page" + #curpage1 + ".box1.row1.1")
        ))
      ))
  )$
  $lv(box1col2, 
      (tc(json, #dbtemp, 
        (if(#tpageon = 0, (#template + ".box1.row1.1"),
                         (#template + ".page" + #curpage1 + ".box1.row2.1")
        ))
      ))
  )$
  $lv(box1col, if(#box1col1 = "" & #box1col2 = "", 0, 1))

  $lv(size, (if(#tbox1    = 0,                   0,
                #tbox1    = 1,                   #box1sm,
                #tbox1    = 2 & #box1col  = "0", #box1sm,
                #tbox1    = 2 & #box1col  = "1", #box1lg,
                #ticopos  = 2 & #tbox1    = 2,   #wgtdir,
                #wgtdir
            )))$
  $#size$

```

###### 2 - Set Global Variable: `box1size`

###### #####################################
###### Autosizing Global Function
###### #####################################

###### func/alignment - Functions for template code to use to reference autosizing data
`$gv(func/alignment)$`
```
  $lv(align,    gv(widgetorient))$
  $lv(override, gv(autoalign))$
  $lv(invert,   gv(boxtoggle))$

  $lv(objpad1,    gv(objpadding))$
  $lv(objpad2,    gv(objpadding2))$
  $lv(objboxpad2, gv(objpaddingbox2))$

  $lv(wgtdir,   (if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth  ))))$
  $lv(wgtdirop, (if(#align = 0, gv(core/size/wgtwidth),  gv(core/size/wgtheight ))))$
  $lv(iconcalc, (gv(iconsize) * 2) + #objboxpad2)$
  $lv(pad,      (#wgtdir - #iconcalc))$

  $lv(box1sm, (gv(iconsize) + (#objpad1 * 2) * 100 / 720))$
  $lv(box1lg, ((#pad + #iconcalc)))$
  $lv(boxalign, (if(gv(box1position) = RIGHT, CENTERRIGHT,
                    gv(box1position) = LEFT,   CENTERLEFT,
                    gv(box1position) = TOP,    TOP,
                    gv(box1position) = BOTTOM, BOTTOM
  )))$
```

###### #####################################
###### Autosizing Global Variables
###### #####################################

###### core/size/widgetwidth
`$si(rwidth) - (gv(horizontaldevicopd) * 2)$`

###### core/size/widgetheight
`$si(rheight) - (gv(verticaldevicopd) * 2)$`

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
  $lv(boxsize, (#box2dir - gv(deviceiconsize)) / (#icon + #objboxpad2))$
  $if(gv(fitmoreobj) = 0,
        (if(#boxsize <= 2, 2,
         tc(split, #boxsize, ".", 0
        ))),
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

###### colors/color1
```
  $lv(source, (if(gv(colors/color1sel) = 0, "wpcolor1", gv(colors/color1sel) = 1, "wpcolor2", gv(colors/color1sel) = 2, "sys", "0")))$source: $#source$
  $lv(filter, (if(gv(colors/color1fil1) = 1, "sat", gv(colors/color1fil1) = 2, "lum", gv(colors/color1fil1) = 3, "comp", "sat")))$filter: $#filter$
  $lv(coltone, (if(gv(colors/color1sysc) = 0, "sysca1", gv(colors/color1sysc) = 1, "sysca2", gv(colors/color1sysc) = 2, "sysca3", gv(colors/color1sysc) = 3, "syscn1", gv(colors/color1sysc) = 4, "syscn2", gv(colors/color1sysc) = 5, "syscn3", "sysca1")))$color tone: $#coltone$
  $lv(addsub, (if(gv(colors/color1fil2) = 0, "", gv(colors/color1fil2) = 1, "a", gv(colors/color1fil2) = 2, "r", "")))$ addsub: $#addsub$

  $if(gv(colors/color1fil1) > 0 & (#source = "wpcolor1" | #source = "wpcolor2"), lv(color1, ce(si(#source), #filter, gv(colors/color1adj))))$wpcolor: $#color1$
  $if(gv(colors/color1fil1) > 0 & #source = "sys", lv(color2, si(#coltone, gv(colors/color1adj))))$sysui: $#color2$
  $if(gv(colors/color1fil1) = 0 | gv(colors/color1sel) = 3, lv(col3, gv(colors/color1pick)))$colpick: $#col3$

  $lv(color, (if(gv(colors/color1sel) <= 1, #color1, gv(colors/color1sel) = 2, #color2, gv(colors/color1sel) = 3, #col3)))$$#color$
  $lv(final, ce(#color, alpha, gv(colors/color1alph)))$
  Final color: $#final$
```


###### colors/color1
```

$lv(source, (if(gv(colors/color2sel) = 0, "wpcolor1", gv(colors/color2sel) = 1, "wpcolor2", gv(colors/color2sel) = 2, "sys", "0")))$
$lv(filter, (if(gv(colors/color2fil1) = 1, "sat", gv(colors/color2fil1) = 2, "lum", gv(colors/color2fil1) = 3, "comp", "sat")))$
$lv(coltone, (if(gv(colors/color2sysc) = 0, "sysca1", gv(colors/color2sysc) = 1, "sysca2", gv(colors/color2sysc) = 2, "sysca3", gv(colors/color2sysc) = 3, "syscn1", gv(colors/color2sysc) = 4, "syscn2", gv(colors/color2sysc) = 5, "syscn3", "sysca1")))$
$lv(addsub, (if(gv(colors/color2fil2) = 0, "", gv(colors/color2fil2) = 1, "a", gv(colors/color2fil2) = 2, "r", "")))$

$if(gv(colors/color2fil1) > 0 & (#source = "wpcolor1" | #source = "wpcolor2"), lv(color1, ce(si(#source), #filter, gv(colors/color2adj))))$
$if(gv(colors/color2fil1) > 0 & #source = "sys", lv(color2, si(#coltone, gv(colors/color2adj))))$
$if(gv(colors/color2fil1) = 0 | gv(colors/color2sel) = 3, lv(col3, gv(colors/color2pick)))$

$lv(color, (if(gv(colors/color2sel) <= 1, #color1, gv(colors/color2sel) = 2, #color2, gv(colors/color2sel) = 3, #col3)))$
$lv(final, ce(#color, alpha, gv(colors/color2alph)))$
$#final$
```