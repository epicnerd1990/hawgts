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
###### Flow: KWGT Global template
###### #####################################
"Light - 1 Device, 2 Rows, 2 Boxes"

**Trigger:** `OnChange` formula: `$gv(templateselect)`

**Action**

###### 1 - Formula - 
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

###### #####################################
###### Flow: Generate Material Colors
###### #####################################

**Trigger:** `OnChange` formula: `$gv(theme/colorgen/syscolorset)$$gv(theme/colorgen/adjcolor)$$gv(theme/colorgen/opacity)$`

**Action**

###### 1 - Formula - 
```
  $gv(func/themecolors)$

  $lv(adjcolor, gv(theme/colorgen/adjcolor))$
  $lv(opacity, gv(theme/colorgen/opacity))$

  $lv(gsyscolorset, gv(theme/colorgen/syscolorset))$
  $lv(syscolorset, (if(#gsyscolorset = 1, "sysca1",
                       #gsyscolorset = 2, "sysca2",
                       #gsyscolorset = 3, "sysca3",
                       #gsyscolorset = 4, "syscn1",
                       #gsyscolorset = 5, "syscn2",
                       #gsyscolorset = 6, "syscn3",
                       "")
  ))$

  $lv(matfilter, si(#syscolorset, #adjcolor))$
  $lv(matfinal, ce(#matfilter, alpha, #opacity))$
  $#matfinal$
```

###### 2 - Set Global Variable: `theme/colorgen/output`

###### #####################################
###### Flow: Edit Colors
###### #####################################

**Trigger:** `OnChange` formula: `$gv(theme/coloreditor/editcolor)$$gv(theme/coloreditor/wallcolorset)$$gv(theme/coloreditor/adjcolor)$$gv(theme/coloreditor/opacity)$`

**Action**

###### 1 - Formula - 
```
  $gv(func/themecolors)$

  $lv(adjcolor, gv(theme/coloreditor/adjcolor))$
  $lv(opacity, gv(theme/coloreditor/opacity))$
  $lv(gwallcolorset, gv(theme/coloreditor/wallcolorset))$
  $lv(geditcolor, gv(theme/coloreditor/editcolor))$

  $lv(editcolor, (if(#geditcolor = 0, "back1",
                     #geditcolor = 1, "back2",
                     #geditcolor = 2, "objiconon",
                     #geditcolor = 3, "objiconoff",
                     #geditcolor = 4, "cobjringon",
                     #geditcolor = 5, "cobjringoff",
                     #geditcolor = 6, "border",
                     #geditcolor = 7, "wpcolor1",
                     #geditcolor = 8, "wpcolor2",
                     "")
  ))$
  $lv(wallcolorset, (if(#gwallcolorset = 1, "sat",
                        #gwallcolorset = 2, "lum",
                        #gwallcolorset = 3, "comp",
                        "")
  ))$

  $lv(editfilter, if(#geditcolor <= 6,
                    ce(#editcolor, #wallcolorset, #adjcolor),
                    (#geditcolor >= 7,
                    ce(si(#editcolor), #wallcolorset, #adjcolor)
                    "")
  ))$
  $lv(editfinal, ce(#matfilter, alpha,#opacity))$
  $#editfinal$
```

###### 2 - Set Global Variable: `theme/coloreditor/output`

###### func/themecolors
Use: `$gv(func/themecolors)$`
```
  $gv(func/theme)$

  $lv(cborder,     (tc(json, #dbtheme, ("." + #colors + ".border"))))$
  $lv(cback1,      (tc(json, #dbtheme, ("." + #colors + ".back_1"))))$
  $lv(cback2,      (tc(json, #dbtheme, ("." + #colors + ".back_2"))))$
  $lv(cobjicooff,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_off"))))$
  $lv(cobjringoff, (tc(json, #dbtheme, ("." + #colors + ".obj_ring_off"))))$
  $lv(cobjiconon,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_on"))))$
  $lv(cobjringon,  (tc(json, #dbtheme, ("." + #colors + ".obj_ring_on"))))$

// Output data with si() support
  $lv(back1, (if(gv(theme/colors/back1) != "", gv(theme/colors/back1),
                if(#cback1 ~= "#[a-fA-F0-9]*", #cback1, si(#cback1))
             ))
  )$
  $lv(back2, (if(gv(theme/colors/back2) != "", gv(theme/colors/back2),
                if(#cback2 ~= "#[a-fA-F0-9]*", #cback2, si(#cback2))
             ))
  )$
  $lv(objiconon, (if(gv(theme/colors/objiconon) != "", gv(theme/colors/objiconon),
                if(#cobjiconon ~= "#[a-fA-F0-9]*", #cobjiconon, si(#cobjiconon))
             ))
  )$
  $lv(objiconoff, (if(gv(theme/colors/objiconoff) != "", gv(theme/colors/objiconoff),
                if(#cobjiconoff ~= "#[a-fA-F0-9]*", #cobjiconoff, si(#cobjiconoff))
             ))
  )$
  $lv(objringon, (if(gv(theme/colors/objringon) != "", gv(theme/colors/objringon),
                if(#cobjringon ~= "#[a-fA-F0-9]*", #cobjringon, si(#cobjringon))
             ))
  )$
  $lv(objringoff, (if(gv(theme/colors/objringoff) != "", gv(theme/colors/objringoff),
                if(#cobjringoff ~= "#[a-fA-F0-9]*", #cobjringoff, si(#cobjringoff))
             ))
  )$
  $lv(border, (if(gv(theme/colors/border) != "", gv(theme/colors/border),
                if(#cborder ~= "#[a-fA-F0-9]*", #cborder, si(#cborder))
             ))
  )$

```

```
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

  $lv(settheme, (if(gv(settheme) = 0, "OneUI",
                    gv(settheme) = 1, "Material",
                    gv(settheme) = 2, "Basic",
                    gv(settheme) = 3, "Custom",
                    "0")
  ))$

// Randomly select one of the samsung colors from the JSON
// and output a pastel version to match OneUI colors
  $lv(pastel, ce(tc(json, #dbtheme, (".pastel." + mu(rnd, 1, 18))), sat, 20))$

// Raw JSON values
  $lv(themename,(tc(json, #dbtheme, ("." + #settheme))))$
  $lv(tborder,  (tc(json, #dbtheme, ("." + #settheme + ".border_width"))))$
  $lv(tradius,  (tc(json, #dbtheme, ("." + #settheme + ".corner_radius"))))$
  $lv(tpadwgt,  (tc(json, #dbtheme, ("." + #settheme + ".pad_widget"))))$
  $lv(tpadobj,  (tc(json, #dbtheme, ("." + #settheme + ".pad_object"))))$
  $lv(tpadside, (tc(json, #dbtheme, ("." + #settheme + ".pad_sides"))))$
  $lv(tdevico,  (tc(json, #dbtheme, ("." + #settheme + ".dev_ico_ring"))))$
  $lv(tringsha, (tc(json, #dbtheme, ("." + #settheme + ".ring_shape"))))$
  $lv(tshad,    (tc(json, #dbtheme, ("." + #settheme + ".shadow"))))$
  $lv(twgtopa,  (tc(json, #dbtheme, ("." + #settheme + ".wgt_opacity"))))$

// Output data accounting for custom overrides
  $lv(border,      (if(gv(theme/bordersize)         != "", gv(theme/bordersize),          #tborder)))$
  $lv(radius,      (if(gv(theme/cornerradius)       != "", gv(theme/cornerradius),        #tradius)))$
  $lv(padwgthor,   (if(gv(theme/verticalpadding)    != "", gv(theme/verticalpadding),     #tpadwgt)))$
  $lv(padwgtver,   (if(gv(theme/horizontalpadding)  != "", gv(theme/horizontalpadding),   #tpadwgt)))$
  $lv(padobjhor,   (if(gv(theme/objpadding)         != "", gv(theme/objpadding),          #tpadobj)))$
  $lv(padobjver,   (if(gv(theme/objpadding2)        != "", gv(theme/objpadding2),         #tpadobj)))$
  $lv(padobjside,  (if(gv(theme/objpaddingside)     != "", gv(theme/objpaddingside),      #tpadside)))$
  $lv(ringsha,     (if(gv(theme/ringshape)          != "", gv(theme/ringshape),           #tringsha)))$
  $lv(wgtopa,      (if(gv(theme/coloreditor/opacity)!= "", gv(theme/coloreditor/opacity), #twgtopa)))$
```

```
// JSON data with titles for reference
  $lv(themename,(tc(json, #dbtheme, ("." + #settheme))))$                         // Theme Title
  $lv(tborder,  (tc(json, #dbtheme, ("." + #settheme + ".border_width"))))$       // Border width
  $lv(tradius,  (tc(json, #dbtheme, ("." + #settheme + ".corner_radius"))))$      // Corner Radius
  $lv(tpadwgt,  (tc(json, #dbtheme, ("." + #settheme + ".pad_widget"))))$         // Widget Padding
  $lv(tpadobj,  (tc(json, #dbtheme, ("." + #settheme + ".pad_object"))))$         // Object Padding
  $lv(tpadside, (tc(json, #dbtheme, ("." + #settheme + ".pad_sides"))))$          // Padding on box sides
  $lv(tdevico,  (tc(json, #dbtheme, ("." + #settheme + ".dev_ico_ring"))))$       // Ring behind Device Icon
  $lv(tringsha, (tc(json, #dbtheme, ("." + #settheme + ".ring_shape"))))$         // Shape of object rings
  $lv(tshad,    (tc(json, #dbtheme, ("." + #settheme + ".shadow"))))$             // Widget Shadow
  $lv(twgtopa,  (tc(json, #dbtheme, ("." + #settheme + ".wgt_opacity"))))$        // Widget opacity
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
  $if(#stoutput = 1 & #themename =  "OneUI", #pastel,
      #stoutput = 1 & #themename != "OneUI", #objringon,
      #objringoff)$
```

**V1**
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $gv(func/themecolors)$
  $if(#stoutput = 1, #objringon, #objringoff)$
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
  $gv(func/theme)$

  $lv(pos, (if(gv(box1position) = RIGHT | gv(box1position) = LEFT,
                (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) +
                #padwgtver - (gv(deviceiconsize) / 2)),
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
               0
           ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, padwgtver, #ticopos = 2, #pos)$
```

"position_offset_x"
```
  $gv(func/jsontemp)$
  $gv(func/theme)$

  $lv(pos, (if(gv(box1position) = RIGHT | gv(box1position) = LEFT,
               0,
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
                 (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) +
                 #padwgtver - (gv(deviceiconsize) / 2))
            ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, #padwgtver, #ticopos = 2, #pos)$
```

