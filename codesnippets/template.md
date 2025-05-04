# This file is meant for the developer for reference only and only for viewing in the code editor, not a rendering preview. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

###### #####################################
###### Global Template code sets
###### #####################################
See samples.json for json samples and examples


###### #####################################
###### Widget loading process
###### #####################################
- Widget Activate
  - Auto-set sizing data based on widget size
  - User select Device Type and Template #
  - Template JSON set to widget

###### #####################################
###### KWGT Global template flow
###### #####################################
"Light - 1 Device, 2 Rows, 2 Boxes"

**Trigger:** `OnChange` forumla: `$gv(templateselect)`

**Action**

###### 1- Formula - 
```
  $lv(objfit, gv(core/size/box2iconmax))$
  $lv(posid, (".box2.row1." + (#objfit - 1)))$
  $gv(func/jsontemp)$
  $lv(box1vis, if(#tbox1 = 1, ", 1 Box",
                  #tbox1 = 2,  ", 2 Boxes",
                  0
               ))$



  $lv(ntdevnum,
      (if(#tdevct ~= "\d-\d",
            ("Connect " + #tdevct + " " + gv(devicetype) + " entities for this template"),
          #tdevct = 4,
            ("Connect up to 4 " + gv(devicetype) + " entities for this template, 1 per page"),
          ("Connect " + #tdevct + " " + gv(devicetype) + " entities for this template")
      )))$
  $lv(ntwgtsize,
      (if(#objkey != #obj, 
          "This template requires " + "?" + " icons to fit in Box 2, but only " + "#objfit" + " can fit.\\nResize the widget, see \"fitmoreobj\" setting below or consult Readme",
          ""
      )))$


  $gv(devicetype) + " - " + #tdevct + " Device(s), " + #trowct + " Row(s)" + #box1vis$
  $#ntdevnum$
  $#ntwgtsize$

```

###### 2 - Set Global Variable: `templateinfo`
 
###### #####################################
###### KWGT Global template functions
###### #####################################

###### func/jsonmain  - "Template JSON Keys" - JSON Call

This is the main function called by `func/jsonobj` and `func/jsonstate`.
It does the actual database calls for both `json/template` and `json/device`

Example usage: `$gv(func/jsonmain)$ $lv(posid, ".box2.row1.0")$`
`tc(json)` syntax: `templatename1.controllist.iconname.action` returns value of `action`

- Function return definitions:
- 
- `#template`: `devicetype` + `templateselect`, eg `light1`
- `#tpageon`: 0 = no pages, 1 = use pages
- `#objkey`: consolidated `controls` root shortcut for `json/template`
- `#entitykey`: consolidated `device_states` root shortcut for `json/device`

- light.light1{ box1{} box2{} }
- light.light1{ page1{ box1{} box2{} } }
light.light1.page1.box1.row1.0

```
  $lv(dbtemp, gv(json/template))$
  $lv(dbdev, gv(json/device))$
  $lv(template, ("." + gv(devicetype) + "." + (gv(devicetype) + gv(templateselect))))$
  $lv(tpageon, (tc(json, #dbtemp, (#template + ".page"))))$
  $lv(obj, "." + gv(devicetype) + ".objects")$
  $lv(curpage, gv(core/curpage))$
  $lv(objfindkey, 
        (if(#tpageon = 0, (#template + #posid),
                         (#template + ".page" + #curpage + #posid)
        ))
  )$
  $lv(objkey, (if((tc(json, #dbtemp, #objfindkey)) = "", "", 
                  (#obj + "." + (tc(json, #dbtemp, #objfindkey)))
               )))$
  $lv(entity,
    (if(#tpageon = 0, gv(entities/entity1),
                     gv(entities/("entity" + #curpage))
    ))
  )$
  $lv(entitykey, (if(#entity != "",
                     ".device_states[?(@.entity_id == '" + #entity + "')]"
              ))
  )$
  $lv(box1obj, 
      (tc(json, #dbtemp, 
        (if(#tpageon = 0, (#template + ".box1.row1.1"),
                         (#template + ".page" + #curpage + ".box1.row1.1")
        ))
      ))
  )$

```

###### func/jsontemp  - "JSON Template - Main"

This is the function called by the widget items. It's used for general template data
No #posid required

Example usage: `$gv(func/jsontemp)$`
#tpageon = #template + .page

```
  $gv(func/jsonmain)$
  $lv(tname,    (tc(json, #dbtemp, (#template  + ".name"))))$
  $lv(ticon,    (tc(json, #dbtemp, (#template  + ".icon"))))$
  $lv(tbox1,    (tc(json, #dbtemp, (#template  + ".box1_size"))))$
  $lv(trowct,   (tc(json, #dbtemp, (#template  + ".row_count"))))$
  $lv(ticopos,  (tc(json, #dbtemp, (#template  + ".device_icon_pos"))))$
  $lv(tdevct,   (tc(json, #dbtemp, (#template  + ".device_count"))))$
```

###### func/jsonobj  - "JSON Template - Objects"

This is the function called by the widget items. It's used for object-specific template data

Example usage: `$gv(func/jsonobj)$ $lv(posid, ".box2.row1.0")$`

`#obaction`: action, page, template
`#obid`: use for confirming if object exists for visibility. 0 = doesnt exist

```
  $gv(func/jsonmain)$
  $lv(obid,      (if(#objkey != "", 1, 0)))$
  $lv(obtype,    (tc(json, #dbtemp, (#objkey + ".type"))))$
  $lv(obicon,    (tc(json, #dbtemp, (#objkey + ".icon"))))$
  $lv(obaction,  (tc(json, #dbtemp, (#objkey + ".action"))))$
  $lv(obcmd,     (tc(json, #dbtemp, (#objkey + ".cmd"))))$
  $lv(obdata,    (tc(json, #dbtemp, (#objkey + ".data"))))$
  $lv(obparam,   (if(#obaction = "light", (#obcmd + ":" + #obdata),
                     #obaction = "template", #obdata,
                     ""
                 )))$

```

###### func/jsonstate  - "JSON State" - JSON Device States

This is the function called by the widget items. It's used for all the device states

Example usage: `$lv(posid, ".box2.row1.0")$$gv(func/jsonstate)$[$lv(keyname, ".custom_variable")$]`

`#stobtype`: breaks down states from template.
           page, off, on, range, nostate, 0 **0 is deprecated?, remove soon**
`#stoutput`: 0 = device off, 1 = device on

*"keyname" is optional, future use for custom states*
```
  $gv(func/jsonmain)$

  $lv(obtype,    	 (tc(json, #dbtemp, (#objkey + ".type"))))$
  $lv(obonstate, 	 (tc(json, #dbtemp, (#objkey + ".stateon"))))$
  $lv(oblevelh,  	 (if(#obonstate != "", (tc(split, #obonstate, ">", 1)))))$
  $lv(oblevell,  	 (if(#obonstate != "", (tc(split, #obonstate, ">", 0)))))$

  $lv(ststate,  	 (tc(json, #dbdev,  (#entitykey + ".state"))))$
  $lv(stlevelv,    (tc(json, #dbdev,  (#entitykey + ".brightness"))))$
  $lv(stlevel, 	   (if(#stlevelv != "", #stlevelv, 0)))$
  $lv(stcolor,  	 (tc(json, #dbdev,  (#entitykey + ".color"))))$
  $lv(stcustom, 	 (tc(json, #dbdev,  (#entitykey + "." + #keyname))))$

  $lv(stobtype, (if(
                #obonstate ~= "\d+>\d+" & #obtype = "action" & #ststate != "off", "range",
                #obonstate ~= "page\d"  & #obtype = "page",   										"page",
                #obonstate = "off"      & #obtype = "action", 										"off",
                #obonstate = "0"        & #obtype = "action", 										"off",
                #obonstate = "on"       & #obtype = "action", 										"on",
                #obonstate = ""         | #obtype = "",       										"nostate",
                #obonstate
  ))
  )$
  $lv(stoutput, (if(#stobtype != "range",
                  (if(#ststate != "",
                      if(#stobtype = "page"    & #tpageon =        1 &
                          ("page" + #curpage1) = #obonstate,       1,
                          #stobtype = "on"     & #ststate = "on",  1,
                          #stobtype = "off"    & #ststate = "off", 1,
                          #stobtype = "nostate",                   0,
                          0
                      ), 0
                  )),
                  (if(#stobtype = "range" & #stlevel >= #oblevell & #stlevel <= #oblevelh, 1, 0))
                ))
  )$
```

###### Flow: Custom Colors > Globals
```



// Use `#wallfilter` and `theme/color#adj` to change color
  $if(gv(theme/color1fil1) > 0 & (#theme = "wpcolor1" | #theme = "wpcolor2"), 
        lv(color1, ce(si(#theme), #wallfilter, gv(theme/coloreditor/adjcolor)
     // lv(color1, ce(si(wpcolor1), sat, ##))
      )))$
  Wallpaper Color: $#color1$

  $if(gv(theme/color1fil1) > 0 & #theme = "sys",
        lv(color2, si(#systone, gv(theme/coloreditor/adjcolor)
     // lv(color2, si(sysca1, ##))
      )))$
  Sysui: $#color2$

  $if(gv(theme/color1fil1) = 0 | gv(settheme) = 3, lv(col3, gv(theme/color1pick)))$ 
  Colpick: $#col3$

  $lv(color, (if(gv(settheme) <= 1, #color1,
                 gv(settheme)  = 2, #color2,
                 gv(settheme) = 3, #col3
              )))$
  $#color$

  $lv(final, ce(#color, alpha, gv(theme/coloreditor/opacity)))$
  Final color: $#final$
```
###### func/themecolors
Use: `$gv(func/themecolors)$`
```
  $gv(func/theme)$

// JSON data
  $lv(cborder,     (tc(json, #dbtheme, ("." + #colors + ".border"))))$         // Border Color
  $lv(cback1,      (tc(json, #dbtheme, ("." + #colors + ".back_1"))))$         // Background 1
  $lv(cback2,      (tc(json, #dbtheme, ("." + #colors + ".back_2"))))$         // Background 2
  $lv(cobjicooff,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_off"))))$   // Icon Off
  $lv(cobjringoff, (tc(json, #dbtheme, ("." + #colors + ".obj_ring_off"))))$   // Ring Off
  $lv(cobjiconon,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_on"))))$    // Icon On
  $lv(cobjringon,  (tc(json, #dbtheme, ("." + #colors + ".obj_ring_on"))))$    // Ring On

// Output data
  $lv(back1,       (if(gv(theme/colors/back1)      != "", gv(theme/colors/back1),      #cback1)))$
  $lv(back2,       (if(gv(theme/colors/back2)      != "", gv(theme/colors/back2),      #cback2)))$
  $lv(objiconon,   (if(gv(theme/colors/objiconon)  != "", gv(theme/colors/objiconon),  #cobjiconon)))$
  $lv(objiconoff,  (if(gv(theme/colors/objiconoff) != "", gv(theme/colors/objiconoff), #cobjiconoff)))$
  $lv(objringon,   (if(gv(theme/colors/objringon)  != "", gv(theme/colors/objringon),  #cobjringon)))$
  $lv(objringoff,  (if(gv(theme/colors/objringoff) != "", gv(theme/colors/objringoff), #cobjringoff)))$
  $lv(border,      (if(gv(theme/colors/border)     != "", gv(theme/colors/border),     #cborder)))$
```
###### func/theme 
Use: `$gv(func/theme)$`

```
  $lv(dbtheme, gv(json/theme))$
  $lv(darkmode, gv(theme/darkmode))$

// Set editor globals > locals
  $lv(editcolor, gv(theme/coloreditor/editcolor))$
  $lv(adjcolor, gv(theme/coloreditor/adjcolor))$
  $lv(opacity, gv(theme/coloreditor/opacity))$

// Set editor list globals > temp locals
  $lv(gsettheme, gv(settheme))$
  $lv(gsyscolorset, gv(theme/coloreditor/syscolorset))$
  $lv(gwallcolorset, gv(theme/coloreditor/wallcolorset))$

// Set Lists to locals
  $lv(settheme, (if(#gsettheme = 0, "OneUI",
                    #gsettheme = 1, "Material",
                    #gsettheme = 2, "Basic",
                    #gsettheme = 3, "Custom",
                    "0")
  ))$
  $lv(syscolorset, (if(#gsyscolorset = 0, "sysca1",
                       #gsyscolorset = 1, "sysca2",
                       #gsyscolorset = 2, "sysca3",
                       #gsyscolorset = 3, "syscn1",
                       #gsyscolorset = 4, "syscn2",
                       #gsyscolorset = 5, "syscn3",
                       "sysca1")
  ))$
  $lv(wallcolorset, (if(#gwallcolorset = 1, "sat",
                        #gwallcolorset = 2, "lum",
                        #gwallcolorset = 3, "comp",
                        "sat")
  ))$

// Set correct color key location based on mode
  $lv(colors, if(#darkmode = 0, (#settheme + ".colors.light"),
                                (#settheme + ".colors.dark")
  ))$

// JSON data
  $lv(themename,(tc(json, #dbtheme, ("." + #settheme))))$                         // Theme Title
  $lv(tborder,  (tc(json, #dbtheme, ("." + #settheme + ".border_width"))))$       // Border width
  $lv(tradius,  (tc(json, #dbtheme, ("." + #settheme + ".corner_radius"))))$      // Corner Radius
  $lv(tpadwgt,  (tc(json, #dbtheme, ("." + #settheme + ".pad_widget"))))$         // Widget Padding
  $lv(tpadobj,  (tc(json, #dbtheme, ("." + #settheme + ".pad_object"))))$         // Object Padding
  $lv(tpadside, (tc(json, #dbtheme, ("." + #settheme + ".pad_sides"))))$          // Padding on box sides
  $lv(tdevico,  (tc(json, #dbtheme, ("." + #settheme + ".dev_ico_ring"))))$       // Ring behind Device Icon
  $lv(tshad,    (tc(json, #dbtheme, ("." + #settheme + ".shadow"))))$             // Widget Shadow
  $lv(twgtopa,  (tc(json, #dbtheme, ("." + #settheme  + ".wgt_opacity"))))$       // Widget opacity

// Output data
  $lv(border,      (if(gv(theme/bordersize)         != "", gv(theme/bordersize),          #tborder)))$
  $lv(radius,      (if(gv(theme/cornerradius)       != "", gv(theme/cornerradius),        #tradius)))$
  $lv(padwgthor,   (if(gv(theme/verticalpadding)    != "", gv(theme/verticalpadding),     #tpadwgt)))$
  $lv(padwgtver,   (if(gv(theme/horizontalpadding)  != "", gv(theme/horizontalpadding),   #tpadwgt)))$
  $lv(padobjhor,   (if(gv(theme/objpadding)         != "", gv(theme/objpadding),          #tpadobj)))$
  $lv(padobjver,   (if(gv(theme/objpadding2)        != "", gv(theme/objpadding2),         #tpadobj)))$
  $lv(padobjver,   (if(gv(theme/objpaddingbox2)     != "", gv(theme/objpaddingbox2),      #tpadside)))$
  $lv(wgtopa,      (if(gv(theme/coloreditor/opacity)!= "", gv(theme/coloreditor/opacity), #twgtopa)))$
```

###### #####################################
###### KWGT Template Icon code examples
###### #####################################

###### Icon name formula - Box 2, Row 2, Icon 0
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#objid != "0", #icon, #tempicon)$
```

###### Icon visiblity formula - Box 2, Row 2, Icon 2 (auto-hide)
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonobj)$
  $if(#objid = "" | gv(core/size/box2iconmax) < 3, REMOVE, ALWAYS)$
```

###### Icon visiblity formula - Box 1, Row 2, Icon 1 (pages)
```
  $lv(posid, ".box1.row2.0")$
  $gv(func/jsonobj)$
  $lv(entity, "entities/entity" + #obdata)$

  $if(#obid = "0", REMOVE,
      #obid = "1" & #obtype = "page" & gv(#entity) = "", REMOVE, ALWAYS)$

```

###### Circle Paint Color Formula
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $gv(func/themecolors)$
  $if(#stoutput = 1, #objringon, #objringoff)$
```

**V1**
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#stoutput = 1, gv(theme/colors/objiconon), #00FFFFFF)$
```

###### Icon Paint Color Formula
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $gv(func/themecolors)$
  $if(#stoutput = 1, #objiconon, #objiconoff)$
```

**V1**
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#stoutput = 1, gv(theme/colors/back1), gv(theme/colors/objiconon))$
```

###### Icon Color Filter Formula
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#stoutput = 1, "MULTIPLY", "NORMAL")$
```

###### Box 1, Icon 1 padding override
"position_padding_left"
```
  $lv(posid, ".box1.row2.1")$
  $gv(func/jsonobj)$
  $gv(func/alignment)$

  $if(#obid = "0" & #align = 0, "10", "0")$

```
"position_padding_top"
```
  $lv(posid, ".box1.row2.1")$
  $gv(func/jsonobj)$
  $gv(func/alignment)$

  $if(#obid = "0" & #align = 1, "10", "0")$

```

###### Stack group Alignment (Level 1)
```
  $gv(func/alignment)$
  $#boxcenter$
```

###### Stack group Margins (Level 1 + 2)
```
$gv(func/alignment)$$#margin2$
```
```
$gv(func/alignment)$$#margin1$
```

###### Device Icon properties
"position_anchor"
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

"position_offset_x"
```
  $gv(func/jsontemp)$

  $lv(pos, (if(gv(box1position) = RIGHT | gv(box1position) = LEFT,
                (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) + gv(theme/verticalpadding) - (gv(deviceiconsize) / 2)),
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
               0
           ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, gv(theme/verticalpadding), #ticopos = 2, #pos)$
```

"position_offset_x"
```
  $gv(func/jsontemp)$

  $lv(pos, (if(gv(box1position) = RIGHT | gv(box1position) = LEFT,
               0,
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
                 (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) + gv(theme/horizontalpadding) - (gv(deviceiconsize) / 2))
            ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, gv(theme/horizontalpadding),#ticopos = 2, #pos)$
```

