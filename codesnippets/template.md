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
  $lv(posid, ".box2.row2.1")$
  $gv(func/jsonstate)$
  $if(#stoutput = 1, gv(colors/colico), #00FFFFFF)$
```

###### Icon Paint Color Formula
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#stoutput = 1, gv(colors/color1), gv(colors/colico))$
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
                (((if(gv(widgetorient) = 0, gv(core/size/wgtheight), gv(box1size))) / 2) + gv(verticalpadding) - (gv(deviceiconsize) / 2)),
               gv(box1position) = TOP   | gv(box1position) = BOTTOM,
               0
           ))
  )$
  $if(#ticopos = 1 | #ticopos = 3, gv(verticalpadding), #ticopos = 2, #pos)$
```

"position_offset_x"
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

