# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

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
  $lv(objfit, gv(core/size/box2maxobj))$
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
###### KWGT Global Functions - Template
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

**Trigger:** `OnChange` formula: `$gv(colors/colorgen/syscolorset)$$gv(colors/colorgen/adjcolor)$$gv(colors/colorgen/opacity)$`

**Action**

###### 1 - Formula - 
```
  $gv(func/themecolors)$

  $lv(adjcolor, gv(colors/colorgen/adjcolor))$
  $lv(opacity, gv(colors/colorgen/opacity))$

  $lv(gsyscolorset, gv(colors/colorgen/syscolorset))$
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

###### 2 - Set Global Variable: `colors/colorgen/output`

###### #####################################
###### Flow: Edit Colors
###### #####################################

**Trigger:** `OnChange` formula: `$gv(colors/coloreditor/editcolor)$$gv(colors/coloreditor/wallcolorset)$$gv(colors/coloreditor/adjcolor)$$gv(colors/coloreditor/opacity)$`

**Action**

###### 1 - Formula - 
```
  $gv(func/themecolors)$

  $lv(adjcolor, gv(colors/coloreditor/adjcolor))$
  $lv(opacity, gv(colors/coloreditor/opacity))$
  $lv(gwallcolorset, gv(colors/coloreditor/wallcolorset))$
  $lv(geditcolor, gv(colors/coloreditor/editcolor))$

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
                    ce(fl(0,0,0,"#editcolor"), #wallcolorset, #adjcolor),
                    (#geditcolor >= 7,
                    ce(si(#editcolor), #wallcolorset, #adjcolor)
                    "")
  ))$
  $lv(editfinal, ce(#editfilter, alpha,#opacity))$
  $#editfinal$
```

###### 2 - Set Global Variable: `colors/coloreditor/output`

###### #####################################
###### KWGT Global Functions - Theme
###### #####################################

###### func/themecolors
Use: `$gv(func/themecolors)$`
```
  $gv(func/theme)$
  $lv(darkmode, gv(theme/darkmode))$
  $lv(colors, if(#darkmode = 0, (#settheme + ".colors.light"),
                                (#settheme + ".colors.dark")
  ))$

  $lv(cborder,     (tc(json, #dbtheme, ("." + #colors + ".border"))))$
  $lv(cback1,      (tc(json, #dbtheme, ("." + #colors + ".back_1"))))$
  $lv(cback2,      (tc(json, #dbtheme, ("." + #colors + ".back_2"))))$
  $lv(cobjiconoff,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_off"))))$
  $lv(cobjringoff, (tc(json, #dbtheme, ("." + #colors + ".obj_ring_off"))))$
  $lv(cobjiconon,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_on"))))$
  $lv(cobjringon,  (tc(json, #dbtheme, ("." + #colors + ".obj_ring_on"))))$


  $lv(back1, (if(gv(colors/back1) != "", gv(colors/back1),
                if(#cback1 ~= "^#[a-fA-F0-9]*$", #cback1, 
                si(tc(split, #cback1, ",", 0), tc(split, #cback1, ",", 1)))
             ))
  )$
  $lv(back2, (if(gv(colors/back2) != "", gv(colors/back2),
                if(#cback2 ~= "^#[a-fA-F0-9]*$", #cback2, 
                si(tc(split, #cback2, ",", 0), tc(split, #cback2, ",", 1)))
             ))
  )$
  $lv(objiconon, (if(gv(colors/objiconon) != "", gv(colors/objiconon),
                if(#cobjiconon ~= "^#[a-fA-F0-9]*$", #cobjiconon, 
                si(tc(split, #cobjiconon, ",", 0), tc(split, #cobjiconon, ",", 1)))
             ))
  )$
  $lv(objiconoff, (if(gv(colors/objiconoff) != "", gv(colors/objiconoff),
                if(#cobjiconoff ~= "^#[a-fA-F0-9]*$", #cobjiconoff, 
                si(tc(split, #cobjiconoff, ",", 0), tc(split, #cobjiconoff, ",", 1)))
             ))
  )$
  $lv(objringon, (if(gv(colors/objringon) != "", gv(colors/objringon),
                if(#cobjringon ~= "^#[a-fA-F0-9]*$", #cobjringon, 
                si(tc(split, #cobjringon, ",", 0), tc(split, #cobjringon, ",", 1)))
             ))
  )$
  $lv(objringoff, (if(gv(colors/objringoff) != "", gv(colors/objringoff),
                if(#cobjringoff ~= "^#[a-fA-F0-9]*$", #cobjringoff, 
                si(tc(split, #cobjringoff, ",", 0), tc(split, #cobjringoff, ",", 1)))
             ))
  )$
  $lv(border, (if(gv(colors/border) != "", gv(colors/border),
                if(#cborder ~= "^#[a-fA-F0-9]*$", #cborder, 
                si(tc(split, #cborder, ",", 0), tc(split, #cborder, ",", 1)))
             ))
  )$
```

###### func/theme 
Use: `$gv(func/theme)$`
```
  $lv(dbtheme, gv(json/theme))$

  $lv(settheme, (if(gv(settheme) = 0, "OneUI",
                    gv(settheme) = 1, "Material",
                    gv(settheme) = 2, "Basic",
                    gv(settheme) = 3, "Custom",
                    "0")
  ))$
  $lv(pastel, ce(tc(json, #dbtheme, (".pastel." + mu(rnd, 0, 18))), sat, 20))$
  $lv(themename, (tc(json, #dbtheme, ("." + #settheme))))$

  $lv(tborder,   (tc(json, #dbtheme, ("." + #settheme + ".border_width"))))$
  $lv(tradius,   (tc(json, #dbtheme, ("." + #settheme + ".corner_radius"))))$
  $lv(tpadwgt,   (tc(json, #dbtheme, ("." + #settheme + ".pad_widget"))))$
  $lv(tpadobj,   (tc(json, #dbtheme, ("." + #settheme + ".pad_obj"))))$
  $lv(tpadside,  (tc(json, #dbtheme, ("." + #settheme + ".pad_sides"))))$
  $lv(tdevico,   (tc(json, #dbtheme, ("." + #settheme + ".dev_ico_ring"))))$
  $lv(tringsha,  (tc(json, #dbtheme, ("." + #settheme + ".ring_shape"))))$
  $lv(tringsize, (tc(json, #dbtheme, ("." + #settheme + ".ring_percent"))))$
  $lv(tshadow,   (tc(json, #dbtheme, ("." + #settheme + ".shadow"))))$
  $lv(twgtopa,   (tc(json, #dbtheme, ("." + #settheme + ".wgt_opacity"))))$

  $lv(gborder,    gv(theme/bordersize))$
  $lv(gradius,    gv(theme/cornerradius))$
  $lv(gpadwgtver, gv(theme/verticalpadding))$
  $lv(gpadwgthor, gv(theme/horizontalpadding))$
  $lv(gpadobjhor, gv(theme/objpaddinghor))$
  $lv(gpadobjver, gv(theme/objpaddingver))$
  $lv(gpadside,   gv(theme/objpaddingside))$
  $lv(gringsha,   gv(theme/ringshape))$
  $lv(gringsize,  gv(theme/ringsize))$
  $lv(gshadow,    gv(theme/shadow))$
  $lv(gwgtopa,    gv(theme/opacity))$

  $lv(border,     (if(#gborder,    (if((#gborder * 1)    <= 0, #tborder,   #gborder)),    #tborder)))$
  $lv(radius,     (if(#gradius,    (if((#gradius * 1)    <= 0, #tradius,   #gradius)),    #tradius)))$
  $lv(padwgtver,  (if(#gpadwgtver, (if((#gpadwgtver * 1) <= 0, #tpadwgt,   #gpadwgtver)), #tpadwgt)))$
  $lv(padwgthor,  (if(#gpadwgthor, (if((#gpadwgthor * 1) <= 0, #tpadwgt,   #gpadwgthor)), #tpadwgt)))$
  $lv(padobjhor,  (if(#gpadobjhor, (if((#gpadobjhor * 1) <= 0, #tpadobj,   #gpadobjhor)), #tpadobj)))$
  $lv(padobjver,  (if(#gpadobjver, (if((#gpadobjver * 1) <= 0, #tpadobj,   #gpadobjver)), #tpadobj)))$
  $lv(padobjside, (if(#gpadside,   (if((#gpadside * 1)   <= 0, #tpadside,  #gpadside)),   #tpadside)))$
  $lv(ringsha,    (if(#gringsha,   (if((#gringsha * 1)   <= 0, #tringsha,  #gringsha)),   #tringsha)))$
  $lv(ringsize,   (if(#gringsize,  (if((#gringsize * 1)  <= 0, #tringsize, #gringsize)),  #tringsize)))$
  $lv(shadow,     (if(#gshadow,    (if((#gshadow * 1)    <= 0, #tshadow,   #gshadow)),    #tshadow)))$
  $lv(wgtopa,     (if(#gwgtopa,    (if((#gwgtopa * 1)    <= 0, #twgtopa,   #gwgtopa)),    #twgtopa)))$

  $lv(squircle, (#ringsize / 70) * 100)
```


```
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
  $if(#objid = "" | gv(core/size/box2maxobj) < 3, REMOVE, ALWAYS)$
```

###### Icon visiblity formula - Box 1, Row 2, Icon 1 (pages)
```
  $lv(posid, ".box1.row2.0")$
  $gv(func/jsonobj)$
  $lv(entity, "entities/entity" + #obdata)$

  $if(#obid = "0", REMOVE,
      #obid = "1" & #obtype = "page" & gv(#entity) = "", REMOVE, ALWAYS)$

```

###### Circle
- Paint Color Formula
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $gv(func/themecolors)$
  $if(#stoutput = 1 & #themename =  "OneUI", #pastel,
      #stoutput = 1 & #themename != "OneUI", #objringon,
      #objringoff)$
```

- Shape Width
```
$gv(func/theme)$
$(gv(theme/iconsize) * #ringsize / 100) + gv(theme/iconsize)$
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
  $if(#stoutput = 1, gv(colors/back1), gv(colors/objiconon))$
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

  $if(gv(theme/advanced/box1pos) = RIGHT,
          (if(#ticopos = 1, TOPLEFT,
              #ticopos = 2, CENTERRIGHT,
              #ticopos = 3, BOTTOMLEFT
          )),
      gv(theme/advanced/box1pos) = LEFT, 
          (if(#ticopos = 1, TOPRIGHT,
              #ticopos = 2, CENTERLEFT,
              #ticopos = 3, BOTTOMRIGHT
          )),
      gv(theme/advanced/box1pos) = TOP,
          (if(#ticopos = 1, BOTTOMLEFT,
              #ticopos = 2, TOP,
              #ticopos = 3, BOTTOMRIGHT
          )),
      gv(theme/advanced/box1pos) = BOTTOM,
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

  $lv(pos, (if(gv(theme/advanced/box1pos) = RIGHT | gv(theme/advanced/box1pos) = LEFT,
                (((if(gv(theme/advanced/widgetorient) = 0, gv(core/size/wgtheight), gv(theme/advanced/box1size))) / 2) +
                #padwgtver - (gv(theme/deviceiconsize) / 2)),
               gv(theme/advanced/box1pos) = TOP   | gv(theme/advanced/box1pos) = BOTTOM,
               0
           ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, padwgtver, #ticopos = 2, #pos)$
```

"position_offset_x"
```
  $gv(func/jsontemp)$
  $gv(func/theme)$

  $lv(pos, (if(gv(theme/advanced/box1pos) = RIGHT | gv(theme/advanced/box1pos) = LEFT,
               0,
               gv(theme/advanced/box1pos) = TOP   | gv(theme/advanced/box1pos) = BOTTOM,
                 (((if(gv(theme/advanced/widgetorient) = 0, gv(core/size/wgtheight), gv(theme/advanced/box1size))) / 2) +
                 #padwgtver - (gv(theme/deviceiconsize) / 2))
            ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, #padwgtver, #ticopos = 2, #pos)$
```

###### #####################################
###### Global Lists
###### #####################################

###### `templateselect`
```
DATA##"Select the template you'd like to use for the device selected above",
1##Template 1,
2##Template 2,
3##Template 3,
4##Template 4,
5##Template 5,
6##Template 6
```

###### `devicetype`
```
DATA##"Select the type of device you'd like to control",
light##Lights,
climate##Climate,
tv##Television,
sound##Sound System,
security##Security,
lock##Doors and Locks,
power##Charging and Battery,
remote##Remote control,
appliance##Home Appliances,
home##Home Features?,
electronics##Personal Electronics,
maintence##Home Maintence,
other##Others
```

