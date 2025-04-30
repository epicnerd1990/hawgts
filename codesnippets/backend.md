# KWGT Globals

- Backend KWGT Flows and Functions
- Autosizing Code
- Colors
- Status System


###### #####################################
###### Flow: "AUTO-ALIGN - Box 1 Position"
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

**V1**
```
  $lv(current, gv(box1position))$
  $lv(horver, (if(gv(widgetorient) = 0, "hor", "ver")))$
  $lv(inverted, (if(gv(boxtoggle) = 0, "no", "yes")))$
  $lv(pos, (if(#horver = "hor" & #inverted = "no", "RIGHT", #horver = "hor" & #inverted = "yes", "LEFT", #horver = "ver" & #inverted = "no", "TOP", #horver = "ver" & #inverted = "yes", "BOTTOM")))$
  $if(gv(autoalign) = 1, #pos, current)$
```


###### #####################################
###### Flow: "AUTO-ALIGN - Box 1 Size"
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

**V5** - Change to Flow
```
  $gv(func/boxsize)$
  $lv(tempsize, gv(box1size))$
  $lv(size, (if(gv(core/box1vis) = 0, 0,
              // col1
                gv(core/box1vis) = 1, ((#pad + gv(iconsize)) * 100 / 720),
              // col2
                gv(core/box1vis) = 2 | gv(core/box1vis) = DATA, ((#pad + (gv(iconsize) * 2) + gv(objpadding)) * 100 / 720),
                #tempsize)))$
  $if(#align = 1, gv(core/size/wgtwidth), #size >= 0, #size, 0)$
```

**V4** - ADD GLOBAL FUNCTION AND COMPLETLY REVAMP.
```
  $gv(func/boxsize)$
  $lv(size, (if(
      gv(core/box1vis) = 0, 0,
      gv(core/box1vis) >= 1, gv(box1size)
  )))$
  $if(#align = 0, gv(core/size/wgtwidth), #size >= 0, #size, 0)$
```

**V3**
 ```
 $lv(size, (if(gv(core/box1vis) = CUSTOM, gv(box1size), gv(core/box1vis))))$
  $if(gv(widgetorient) = 1, gv(core/size/wgtwidth), (#size * 720 / 100))$
```

**V2**
 ```
 $if(gv(wgtalign) = 0,
  if(gv(smboxper) = 0, gv(smboxcus),
  gv(smboxper) = 15, (gv(iconsize) + (gv(objpadding) * 2)),
  gv(smboxper) = 30, ((gv(iconsize) * 2) + gv(objpadding) + (gv(objpadding) * 2)),
  ((gv(iconsize) * 2) + gv(objpadding) + (gv(objpadding) * 2))),
  gv(core/wgwidth))$
```

**V1**
  `$if(gv(wgtalign) = 0, (gv(smboxper) * 720 / 100), (gv(core/wgwidth)))$`



###### #####################################
###### Autosizing Global Functions
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


**Unknown Version - Testing?**
```
  $lv(devicopos, gv(deviceiconposition))$
  $lv(icosize, gv(iconsize))$
  $lv(align, gv(widgetorient))$
  $lv(invert, gv(boxtoggle))$
```

**V3**
```
  $lv(align, gv(widgetorient))$
  $lv(wgtdir, if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth)))$
  $lv(iconsize, (if(gv(core/box1vis) = 1, gv(iconsize), gv(core/box1vis) = 2, ((gv(iconsize) * 2) + gv(objpadding2)))))$
  $lv(pad, (#wgtdir - #iconsize))$
```

**V2**
```
  $lv(align, gv(widgetorient))$
  $lv(wgtdir, if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth)))$
  $lv(iconsize, (if(gv(core/box1vis) = 1, gv(iconsize), gv(core/box1vis) = 2, ((gv(iconsize) * 2) + gv(objpadding2)))))$
  $lv(ratio, (((#wgtdir - #iconsize) / ((#wgtdir + #iconsize) / 2)) * 100))$
  $lv(pad, if(#ratio <= 20, (gv(objpadding) * 2), (#wgtdir - #iconsize)))$
```

**V1**
```
  $lv(align, gv(widgetorient))$
  $lv(wgtdir, if(#align = 0, gv(core/size/wgtheight), gv(core/size/wgtwidth)))$
  $lv(pad, (#wgtdir - (if(gv(boxdir1) = 1, gv(iconsize), gv(boxdir1) = 2, ((gv(iconsize) * 2) + gv(objpadding2))))))$
```


###### #####################################
###### Autosizing Global Variables
###### #####################################

## core/size/widgetwidth
`$si(rwidth) - (gv(horizontaldevicopd) * 2)$`


## core/size/widgetheight
`$si(rheight) - (gv(verticaldevicopd) * 2)$`




## core/size/box2width
```
  $gv(func/alignment)$
  $if(#tbox1 = 0 | #align = 1, gv(core/size/wgtwidth), (#wgtdirop - gv(box1size)))$
```

**V3** Without box2minsize check
```
  $if(gv(widgetorient) = 1 | gv(core/box1vis) = 0, 
    gv(core/size/wgtwidth), (
    gv(core/size/wgtwidth) - gv(box1size))
  )$

  $if(gv(core/box1vis) = 0, gv(verticalpadding), 0)$
  $if(gv(widgetorient) = 0, (gv(box1size) + #padding), 0)$
```

**V2**
```
  $if(gv(widgetorient) = 0, 
      (if(gv(core/size/wgtwidth) <= gv(core/size/box2minsize), 
          gv(core/size/box2minsize),
          (gv(core/size/wgtwidth) - gv(box1size)))
      ), 
  gv(core/size/wgtwidth)
  )$
```

**V1**
```
  $if(gv(widgetorient) = 0 | gv(core/box1vis) = 0,(if(gv(core/size/wgtwidth) <= gv(core/size/box2minsize),gv(core/size/box2minsize), (gv(core/size/wgtwidth) - gv(box1size)))),gv(core/size/wgtwidth))$
```


## core/size/box2height
```
  $gv(func/alignment)$
  $if(#tbox1 = 0 | #align = 0, gv(core/size/wgtheight), (#wgtdirop - gv(box1size)))$
```

**V1**
```
  $if(gv(widgetorient) = 0, gv(core/size/wgtheight), 
      (if(gv(core/box1vis) = 0, gv(core/size/wgtheight), 
      (gv(core/size/wgtheight) - gv(core/box1height))
    ))
  )$
```


## core/size/box2iconmax
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

**V4** - Added option to disable via toggle
```
  $lv(icon, gv(iconsize) + gv(objpadding))$
  $lv(boxsize1, (if(gv(widgetorient) = 0, gv(core/size/box2width), gv(core/size/box2height))))$
  $lv(boxsize, (#boxsize1 - gv(deviceiconsize)) / (#icon + gv(objpaddingbox2)))$
  $if(#boxsize <= 2, 2, tc(split, #boxsize, ".", 0))$
```

**V3** - Clean up code
```
  $lv(icon, gv(iconsize) + gv(objpadding))$
  $lv(devicosi, if(gv(deviceiconshrink) = 1, gv(appiconsize), gv(deviceappiconpadding) = 0, 0, gv(deviceiconsize)))$
  $lv(boxsize1, if(gv(widgetorient) = 0, gv(core/size/box2width), gv(core/size/box2height)))$
  $lv(boxsize2, (#boxsize1 - #devicosi) / #icon)$
  $if(#boxsize2 <= 2, 2, (tc(cut, mu(abs,#boxsize2), 1)))$
```

**V2**
 ```
 $lv(icon, gv(iconsize) + gv(objpadding))$
  $lv(devicosi, if(gv(icodevge) gv(icodev), gv(icodev), gv(icosm)))$
  $lv(devicopd, if(icopadr = 0, #devicosi), 0)$
  $lv(boxsize1, if(gv(wgtalign) = 0, gv(core/lgboxw), gv(core/lgboxh)))$
  $lv(boxsize2, #boxsize1 - #devicopd)$
  $lv(count, #boxsize2 / #icon)$
  $if(#count <= 2, 2, tc(cut, mu(abs,#count), 1))$

  $/* core number of icons able to be shown in Box 2
  Calculate icon sizes, devicopd and widget alignment */$
  $lv(icon, gv(iconsize) + gv(objpadding))$
  $lv(devicosi, if(gv(icodevge) = gv(icodev), gv(icodev), gv(icosm)))$
  $lv(devicopd, if(icopadr = 0, #devicosi), 0)$
  $lv(boxsize1, if(gv(wgtalign) = 0, gv(core/lgboxw), gv(core/lgboxh)))$

  $/* Subtract settings icons, unless they're hidden.
  Get final number: Icon size / Box - settings */$
  $lv(boxsize2, #boxsize1 - #devicopd)$
  $lv(count, #boxsize2 / #icon)$
  $/* mu(abs) removes decimal point. This causes the count to round up and formats it for use.*/$
  $if(#count <= 2, 2, tc(cut, mu(abs,#count), 1))$
```

**V1**
```
  $/* core number of icons able to be shown
  Calculate icon sizes, devicopd and widget alignment */
  lv(icon, gv(iconsize) + gv(objpadding))$$lv(lgbox, if(gv(wgtalign) = 0, gv(core/lgboxw), gv(core/lgboxh)))$

  $/* Subtract settings icons, unless they're gone.
  Get final number: Icon size / Box - settings */
  lv(adjustment, if(gv(icopadr) = 0, gv(icosm), 0))$
  $lv(boxsize, #lgbox - #adjustment)$
  $lv(count, #boxsize / #icon)$
  $if(#count <= 2, 2, tc(cut, mu(abs,#count), 1))$
```

## core/size/box2minsize **Deprecated**
```
  $gv(func/alignment)$
  $gv(func/jsontemp)$
  $lv(devico, (if(#ticopos = 2 | #ticopos = 3, 0, gv(deviceiconsize))))$
  $(#iconcalc + #objpad1 + #devico + #objboxpad2 * 2))$
```

**V3**
```
  $lv(devico, (if(gv(deviceiconposition) = 2 | gv(deviceiconposition) = 4 | gv(deviceappiconpadding) = 0, 0, gv(deviceiconsize))))$
  $(gv(iconsize) * 2) + 
    (gv(objpadding) * 2) + 
    #devico + 
    (gv(objpaddingbox2) * 2)$
```

**V2.5** Refresh code
```
  $lv(devicosi, (if(gv(deviceiconcalc) = gv(deviceiconsize), gv(deviceiconsize), gv(appiconsize))))$
  $lv(devicopd, (if(gv(deviceappiconpadding) = 1, #devicosi, 0)))$
  $((gv(iconsize) * 2) + (gv(objpadding) * 2) + #devicopd + gv(objpaddingbox2))$
```

**V2**
```
  $lv(devicosi, (if(gv(icodevge) = gv(icodev), gv(icodev), gv(icosm))))$
  $lv(devicopd, (if(icopadr = 0, #devicosi, 0)))$
  $((gv(iconsize) * 2) + gv(objpadding) + gv(objpadding) + #devicopd)$

  $/* If devicopd is hidden, remove from calculation */$
  $lv(devicosi, (if(gv(icodevge) = gv(icodev), gv(icodev), gv(icosm))))$
  $lv(devicopd, (if(icopadr = 0, #devicosi, 0)))$
  $/*Calculate the minimum size Box 2 can be */$
  $((gv(iconsize) * 2) + gv(objpadding) + gv(objpadding) + #devicopd)$
```

**V1**
```
  $lv(icoopt, if(gv(icopadr) = 0,  (gv(icodevge)), 0))$
  $((gv(iconsize) * 2) + (gv(objpadding) * 2) + gv(objpadding) + #icoopt)$
```



## core/deviceiconcalc **Deprecated - Template sets positon  now**
```
  $gv(func/alignment)$
  $lv(default, gv(deviceiconsize))$
  $lv(shrunk, (gv(appiconsize) + gv(objpadding)))$
  $lv(setting, gv(appiconsize))$
  $if(#setting = 0, #default, #setting = 1, #shrunk, 
      #setting = 2, (if(gv(boxdir1) = 1, 
                       if(gv(appiconvis) = 1, #wgtdir, #shrunk),
                       gv(core/size/box2iconmax) < 4, #shrunk, #default
                    )),
      #default
  )$
```

**V5** Optimize code
```
  $if(gv(deviceiconshrink) = 1 | gv(boxdir1) = 1, 
        (gv(appiconsize) + gv(objpadding)),
      gv(core/size/box2iconmax) < 4 & gv(deviceiconshrink) = 2, 
        (gv(appiconsize) + gv(objpadding)),
      gv(deviceiconsize)
  )$
```

**V4** Clean code up
```
  $lv(autoshr, (if(gv(core/size/box2iconmax) < 4 & gv(deviceiconshrink) = 2, 1, 0)))$
  $if(#autoshr = 1 | gv(deviceiconshrink) = 1 | gv(boxdir1) = 1, (
    gv(objpadding) + gv(appiconsize)), 
    #autoshr = 0 | gv(deviceiconshrink) = 0, gv(deviceiconsize))$
```

**V3**
```
  $lv(small, gv(objpadding) + gv(icosm))$
  $lv(large, gv(icodev))$
  $lv(autoshr, (if(gv(core/icocount) < 4 & gv(icodevsm) = 2, 1, 0)))$
  $lv(1row, (if(si(rheight) <= 150, 1, 0)))$
  $if(#autoshr = 1 | gv(icodevsm) = 1 | #1row = 1, #small, #autoshr = 0 | gv(icodevsm) = 0, #large)$
```

**V2** ADD GLOBAL SETTINGS
```
  $lv(small, gv(objpadding) + gv(icosm))$
  $lv(large, gv(icodev))$
  $lv(autosize, (if(gv(core/icocount) < 4 & gv(icodevsm) = 2, 1, 0)))$
  $if(#autosize = 1 | gv(icodevsm) = 1, #small, #autosize = 0 | gv(icodevsm) = 0, #large)$
```


**V1** BASIC RESIZE
```
  $lv(small, gv(objpadding + gv(icosm)))$
  $lv(auto, (if((gv(core/icocount) < 4 | gv(icodevsm) > 0), #small)))$
  $if(gv(icodevsm) = 0, gv(icodev), #auto)$
```


## core/size/box1col    **Deprecated to template "box1_size"**
## core/box1vis         **Deprecated to template "box1_size"**
`core/box1vis`: 0 = Remove, 1 = Show
```
  $gv(func/alignment)$
  $if(gv(core/box1vis) = 0, 0, 
    (if(gv(box1size) >= #iconsize, 2,
        gv(box1size) = 0, 0, 1)))$
```

**V4**
```
  $gv(func/boxsize)$
  $lv(size, (if(
      gv(core/box1vis) = 0, 0,
      gv(core/box1vis) >= 1, gv(box1size)
  )))$
```

**V3**
```
  $lv(size, (if(gv(core/box1vis) = CUSTOM, gv(box1size), gv(core/box1vis))))$
  $if(gv(widgetorient) = 0, gv(core/size/wgtheight), (#size * 720 / 100))$
```

**V2**
```
  $if(gv(widgetorient) = 1, if(gv(smboxper) = 0, gv(smboxcus), (gv(smboxper) * 720 / 100)), gv(core/wgheight))$
```

**V1**
  `$if(gv(widgetorient) = 0, gv(core/wgheight), (gv(smboxper) * 720 / 100))$`


## STATUS SYSTEM
TEST 2 - Change to JSON
  $lv(dbstatus, gv(errordb))$
  $lv(stnew, gv(currentstatus))$
  $lv(stname,   (tc(json, #dbstatus, (".status." + #stnew + ".error_name"))))$
  $lv(stdetail, (tc(json, #dbstatus, (".status." + #stnew + ".error_content"))))$
  $lv(out, #stdetail)$

TEST 1 - Failed
$lv(global, "deviceiconrotation")$
$if(gv(deviceiconrotation) = DATA, NONE, gv(deviceiconrotation))$
$if(gv(deviceiconrotation) = DATA, lv(status, "1"), lv(status, "0"))$
$gv(core/currentstatus)$

## autotemplate **Weird Test, unused**
```
  $lv(rwct, gv(rowcount))$
  $lv(devct, #devct)$
  $lv(boxsz, gv(box1size))$
  $lv(icct, gv(iconcount))$
  $if(#icct <= 2, 
        if(#rwct = 1 & #devct = 1, template#1,
           #rwct = 2 & #devct = 1, template#1
        ),
      #icct > 2 & #icct < 4, 
        if(#rwct = 1 & #devct = 1 & boxsz = 1, template#1,
           #rwct = 2 & #devct = 1 & boxsz = 1, template#1
        ),
      #icct > 2 & #icct < 4 & boxsz = 1, 
        if(#rwct = 1 & #devct = 1 & boxsz = 1, template#1,
           #rwct = 2 & #devct = 1 & boxsz = 1, template#1
        ),
      #icct >= 4 & boxsz = 2, 
        if(#rwct = 1 & #devct = 1 & boxsz = 2, template#4,
           #rwct = 2 & #devct = 1 & boxsz = 2, template#4
        )
  )$
```

## colors/color1
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


## colors/color1
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

## Device Icon properties
position_anchor
```
  $gv(func/jsontemp)$

  $if(gv(box1position) = RIGHT,
          (if(#ticopos = 1, TOPLEFT,
              #ticopos = 2, CENTERRIGHT,
              #ticopos = 3, BOTTOMLEFT
          )),
      gv(box1position) = LEFT, 
          (if(#ticopos = 1, TOPRIGHT,
              #ticopos = 2, CENTERLEFT,
              #ticopos = 3, BOTTOMRIGHT
          )),
      gv(box1position) = TOP,
          (if(#ticopos = 1, BOTTOMLEFT,
              #ticopos = 2, TOP,
              #ticopos = 3, BOTTOMRIGHT
          )),
      gv(box1position) = BOTTOM,
          (if(#ticopos = 1, TOPLEFT,
              #ticopos = 2, BOTTOM,
              #ticopos = 3, TOPRIGHT
          ))
  )$
```

position_offset_x
```
  $gv(func/jsontemp)$

  $lv(pos, (if(gv(box1position) = RIGHT | gv(box1position) = LEFT,
                (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) + gv(verticalpadding) - (gv(deviceiconsize) / 2)),
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
               0
           ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, gv(verticalpadding), #ticopos = 2, #pos)$
```

position_offset_x
```
  $gv(func/jsontemp)$

  $lv(pos, (if(gv(box1position) = RIGHT | gv(box1position) = LEFT,
               0,
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
                 (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) + gv(horizontalpadding) - (gv(deviceiconsize) / 2))
            ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, gv(horizontalpadding),#ticopos = 2, #pos)$
```
