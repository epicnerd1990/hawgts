# This file is meant for the developer for reference only and only for viewing in the code editor, not a rendering preview. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

###### #####################################
###### Global Template code sets
###### #####################################
See samples.json for json samples and examples


###### #####################################
###### Widget loading process
###### #####################################
- Widget Activate
  - Auto-set data based on widget size
  - User select Device Count, Device Type and Template #
  - Template JSON set to widget & customtemplate
- User changes device count
  - If device count is changed after setting template > Check to see if template supports new device count
    - If yes, adjust template 
    - If no, reset devicetype and ask user to restart
- User resizes widget
  - If widget is resized after setting template > Check to see if template icons all fit (count all single-digit json keys)
    - If yes, adjust template 
    - If no, reset devicetype and ask user to restart

###### #####################################
###### KWGT Global template flow - `templateinfo`
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

Function return definitions:
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
  // Allows background global functions to use a different posid in situations where
     they need information from the template that could interfere with other calls
  $lv(curposid, if(#posid2 = "" & #posid  != "", #posid
                   #posid  = "" & #posid2 != "", #posid2))
  /// pageon > tpageon to match template lvs
  $lv(tpageon, (tc(json, #dbtemp, (#template + ".page"))))$
  // Existing - .light.light1
  $lv(template, ("." + gv(devicetype) + "." + (gv(devicetype) + gv(templateselect))))$

  // New - .light.objects
  $lv(obj, "." + gv(devicetype) + "objects")$
  $lv(curpage1, gv(core/curpage))$

  /// jsonreg/jsonpage > objfindkey - ticonid > objkey
  $lv(objfindkey, 
        (if(#tpageon = 0, (#template + #curposid),                      // .light.light1.box2.row1.0
                         (#template + ".page" + #curpage1 + #curposid)  // .light.light1.page1.box2.row2.0
        ))
  )$
  $lv(objkey, (#obj + "." + (tc(json, #dbtemp, #objfindkey))))$         // .light.objects.bedroom

// Set entity - tentity/ciconid > entitykey
  $lv(entity,                                                           // light.bedroom_lights
    (if(#tpageon = 0, gv(entities/entity1),
                     gv(entities/("entity" + #curpage1))
    ))
  )$
  $lv(entitykey, (if(#entity != "",                                     // .device_states.entity_id = #entity
                     tc(json, #dbdev, (".device_states[?(@.entity_id == '" + #entity + "')]"))
              ))
  )$

```

**Current Clean 04-27-25**
```
  $lv(dbtemp, gv(json/template))$
  $lv(dbdev, gv(json/device))$
  $lv(template, ("." + gv(devicetype) + "." + (gv(devicetype) + gv(templateselect))))$
  $lv(tpageon, (tc(json, #dbtemp, (#template + ".page"))))$
  $lv(obj, "." + gv(devicetype) + ".objects")$
  $lv(curpage1, gv(core/curpage))$
  $lv(objfindkey, 
        (if(#tpageon = 0, (#template + #posid),
                         (#template + ".page" + #curpage1 + #posid)
        ))
  )$
  $lv(objkey, (if((tc(json, #dbtemp, #objfindkey)) = "", "", 
                  (#obj + "." + (tc(json, #dbtemp, #objfindkey)))
               )))$
  $lv(entity,
    (if(#tpageon = 0, gv(entities/entity1),
                     gv(entities/("entity" + #curpage1))
    ))
  )$
  $lv(entitykey, (if(#entity != "",
                     ".device_states[?(@.entity_id == '" + #entity + "')]"
              ))
  )$
  $lv(box1obj, 
      (tc(json, #dbtemp, 
        (if(#tpageon = 0, (#template + ".box1.row1.1"),
                         (#template + ".page" + #curpage1 + ".box1.row1.1")
        ))
      ))
  )$

```

**V2** - Template V2 - 2023
```
  $lv(dbtemp, gv(json/template))$
  $lv(dbdev, gv(json/device))$
  $lv(template, gv(devicetype) + gv(templateselect))$
  $lv(curpage1, gv(core/curpage))$
  $lv(pageon, (tc(json, #dbtemp, ("." + #template + ".page"))))$
  $lv(jsonreg, ("." + #template + ".controllist." + (tc(json, #dbtemp, ("." + #template + #posid)))))$
  $lv(jsonpage, ("." + #template + ".page" + #curpage1 + ".controllist." + 
                (tc(json, #dbtemp, ("." + #template + #posid)))
  ))$
  $lv(ticonid, 
          (if(#pageon = 0 & #jsonreg ~= "^\.\w+\.controllist\.\w+$$", #jsonreg,
          #pageon = 1 & #jsonpage ~= "^\.\w+\.page\d\.controllist\.\w+$$", #jsonpage
          ))
  )$
  $lv(entity, (tc(json, #dbtemp, (#ticonid + ".entity"))))$
  $lv(enttemp, 
          (if(#entity ~= "entity\d", gv(entities/#entity),
            #entity ~= ".*\..*", #entity
          ))
  )$
  $lv(tentity, (if(#entity != "", (
        ".device_states[?(@.entity_id == '" + #enttemp + "')]")
      ))
  )$
  $lv(ciconid, (if(#tentity != "", 
                if(tc(json, gv(json/device), #tentity) != "", #tentity)
              ))
  )$
```
**V1.5**
```
  $lv(db, gv(json/template))$
  $lv(template, gv(devicetype) + gv(templateselect))$
  $lv(pageon, (tc(json, #db, ("." + #template + ".page"))))$
  $lv(jsonreg, ("." + #template + ".controllist." + (tc(json, #db, ("." + #template + #posid)))))$
  $lv(jsonpage, ("." + #template + ".page" + gv(core/curpage) + ".controllist." +(tc(json, #db, ("." + #template + ".page" + gv(core/curpage) + #posid)))))$
  $lv(ciconid,(
      if(#pageon = 0 & #jsonreg ~= "^\.\w+\.controllist\.\w+$", #jsonreg,
        #pageon = 1 & #jsonpage ~= "^\.\w+\.page\d\.controllist\.\w+$", #jsonpage
        )))$
  $lv(ticonid, (
    if(tc(reg, gv(json/device), #obaction, #obaction) != "",
      tc(json, gv(json/device), ".device_states[?(@.entity_id == '" + #obaction + "')]"))))$
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


**V1** Template V2 - 2023
```
  $gv(func/jsonmain)$
  $lv(tstyle,   (#template + ".style"))$
  $lv(tname,    (tc(json, #dbtemp, ("." + #template + ".name"))))$
  $lv(ticon,    (tc(json, #dbtemp, (#tstyle  + ".icon"))))$
  $lv(tbox1,    (tc(json, #dbtemp, (#tstyle  + ".box1_size"))))$
  $lv(trowct,   (tc(json, #dbtemp, (#tstyle  + ".row_count"))))$
  $lv(ticopos,  (tc(json, #dbtemp, (#tstyle  + ".device_icon_pos"))))$
  $lv(tdevct,   (tc(json, #dbtemp, (#tstyle  + ".device_count"))))$
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

**V3** Template V2 - 2023
```
  $gv(func/jsonmain)$
  $lv(obtype,    (tc(json, #dbtemp, (#ticonid + ".type"))))$
  $lv(obicon,    (tc(json, #dbtemp, (#ticonid + ".icon"))))$
  $lv(obaction,  (tc(json, #dbtemp, (#ticonid + ".action"))))$
  $lv(obcmd1,    (tc(json, #dbtemp, (#ticonid + ".cmd1"))))$
  $lv(obdata1,   (tc(json, #dbtemp, (#ticonid + ".data1"))))$
  $lv(obparam,   (if(#obaction = "light", (#obcmd1 + ":" + #obdata1),
                     #obaction = "template", #obdata1,
                     ""
                 )))$
  $lv(obid,      (if(#ticonid != "", 1, 0)))$

```

**V2**
```
  $gv(func/jsonmain)$
  $lv(tempname, (tc(json, #db, ("." + #template + ".name"))))$
  $lv(tempicon, (tc(json, #db, ("." + #template + ".icon"))))$
  $lv(type, (tc(json, #db, (#ciconid + ".type"))))$
  $lv(icon, (tc(json, #db, (#ciconid + ".icon"))))$
  $lv(global, (tc(json, #db, (#ciconid + ".action"))))$
  $lv(cmd1, (tc(json, #db, (#ciconid + ".cmd1")) + ":" + tc(json, #db, (#ciconid + ".data1"))))$
```
```
  $lv(posid, ".box1.row2.0")$$gv(func/jsonmain)$
  $lv(tempname, (tc(json, #db, ("." + #template + ".name"))))$
  $lv(tempicon, (tc(json, #db, ("." + #template + ".icon"))))$
  $lv(type, (tc(json, #db, (#ciconid + ".type"))))$
  $lv(icon, (tc(json, #db, (#ciconid + ".icon"))))$
  $lv(global, (tc(json, #db, (#ciconid + ".action"))))$
  $lv(cmd1, (tc(json, #db, (#ciconid + ".cmd1")) + ":" + tc(json, #db, (#ciconid + ".data1"))))$
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
**V2** - Template V2 - 2023
```
  $gv(func/jsonmain)$

  $lv(obtype,    	 (tc(json, #dbtemp, (#ticonid + ".type"))))$
  $lv(obonstate, 	 (tc(json, #dbtemp, (#ticonid + ".stateon"))))$
  $lv(ststate,  	 (tc(json, #dbdev,  (#ciconid + ".state"))))$
  $lv(stlevelv,    (tc(json, #dbdev,  (#ciconid + ".brightness"))))$
  $lv(stlevel, 	   (if(#stlevelv != "", #stlevelv, 0)))$
  $lv(stcolor,  	 (tc(json, #dbdev,  (#ciconid + ".color"))))$
  $lv(stcustom, 	 (tc(json, #dbdev,  (#ciconid + "." + #keyname))))$
  $lv(oblevelh,  	 (if(#obonstate != "", (tc(split, #obonstate, ">", 1)))))$
  $lv(oblevell,  	 (if(#obonstate != "", (tc(split, #obonstate, ">", 0)))))$
  $lv(stobtype, (if(#obonstate ~= "\d+>\d+" & #obtype = "action" &
                #ststate != "off",                                              "range",
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
                      if(#stobtype = "page"   & #pageon =  1 &
                          ("page" + #curpage1) = #obonstate,     1,
                          #stobtype = "on"    & #ststate = "on",  1,
                          #stobtype = "off"   & #ststate = "off", 1,
                          #stobtype = "nostate",                  0,
                          0
                      ), 0
                  )),
                  (if(#stobtype = "range" & #stlevel >= #oblevell & #stlevel <= #oblevelh, 1, 0))
                  ))
  )$
```

**V1.5**
```
$lv(posid, ".box2.row1.0")$
$gv(func/jsonmain)$

$lv(type, (tc(json, #db, (#ciconid + ".type"))))$
$lv(state, (#ticonid + ".state"))$
$lv(stlevel, (#ticonid + ".brightness"))$
$lv(stcolor, (#ticonid + ".color"))$
$lv(stcustom, (#ticonid + "." + #keyname))$
$lv(sobaction, (tc(json, #db, (#ciconid + ".stateon"))))$
$lv(stcurr, (if(#sobaction ~= "\d+>\d+", "range",#sobaction ~= "page\d",   "page",#sobaction = "off",      "off",#sobaction = "",         "nostate",0)))$$lv(stoutput, (if((#ticonid + ".stateon") = on,if((#stcurr = "range") & (#level >= (tc(split, #sobaction, ">", 0))) & (#level <= (tc(split, #sobaction, ">", 0))), 0,(#stcurr = "page"), 1,0),if(#stcurr = off , 1))))$
```

**V1**
```
  $lv(posid, ".box2.row1.0")$
  $lv(dbtemp, gv(json/template))$
  $lv(template, gv(devicetype) + gv(templateselect))$
  $lv(pageon, (tc(json, #dbtemp, ("." + #template + ".page"))))$
  $lv(jsonreg, ("." + #template + ".controllist." + (tc(json, #dbtemp, ("." + #template + #posid)))))$
  $lv(jsonpage, ("." + #template + ".page" + gv(core/curpage) + ".controllist." + 
  (tc(json, #dbtemp, ("." + #template + ".page" + gv(core/curpage) + #posid)))
  ))$
  $lv(ticonid, 
  (if(#pageon = 0 & #jsonreg ~= "^\.\w+\.controllist\.\w+$", #jsonreg,
  #pageon = 1 & #jsonpage ~= "^\.\w+\.page\d\.controllist\.\w+$", #jsonpage
  ))
  )$
  $lv(tempname, (tc(json, #dbtemp, ("." + #template + ".name"))))$
  $lv(tempicon, (tc(json, #dbtemp, ("." + #template + ".icon"))))$
  $lv(type, (tc(json, #dbtemp, (#ticonid + ".type"))))$
  $lv(icon, (tc(json, #dbtemp, (#ticonid + ".icon"))))$
  $lv(global, (tc(json, #dbtemp, (#ticonid + ".action"))))$
  $lv(cmd1, (tc(json, #dbtemp, (#ticonid + ".cmd1")) + ":" + tc(json, #dbtemp, (#ticonid + ".data1"))))$
  $lv(ciconid, (if(tc(reg, gv(json/device), #entity, #entity) != "",
  tc(json, gv(json/device), ".device_states[?(@.entity_id == '" + #entity + "')]")
  ))
  )$
  $lv(state, (#ciconid + ".state"))$
  $lv(stlevel, (#ciconid + ".brightness"))$
  $lv(stcolor, (#ciconid + ".color"))$
  $lv(stcustom, (#ciconid + "." + #keyname))$
  $lv(obonstate, (tc(json, #dbtemp, (#ticonid + ".stateon"))))$
  $lv(stobtype, (if(#obonstate ~= "\d+>\d+", "range",
  #obonstate = "page\d",   "page",
  #obonstate = "off",      "off",
  #obonstate = "",         "nostate",
  0
  )))$
  $lv(stoutput, (
  if((#ciconid + ".stateon") = on,
  if((#stobtype = "range") & (#level >= (tc(split, #obonstate, ">", 0))) & (#level <= (tc(split, #obonstate, ">", 0))), 0,
  (#stobtype = "page") & (("page" + gv(core/curpage)) = #obonstate), 1, 
  0
  ),
  if(#stobtype = off, 1)
  )
  ))$
  $#stoutput$
```

**V1** Original Global Formulas before this single global function
```
  $lv(state, (if(
    tc(reg, gv(json/device), #toglbnam, #toglbnam) != "",
    tc(json, gv(json/device), ".device_states[?(@.entity_id == '" + #toglbnam + "')].state")
  )))$

  $lv(level, (if(
    tc(reg, gv(json/device), #toglbnam, #toglbnam) != "",
    tc(json, gv(json/device), ".device_states[?(@.entity_id == '" + #toglbnam + "')].brightness")
  )))$

  $lv(color, (if(
    tc(reg, gv(json/device), #toglbnam, #toglbnam) != "",
    tc(json, gv(json/device), ".device_states[?(@.entity_id == '" + #toglbnam + "')].color")
  )))$

  $lv(custom, (if(
    tc(reg, gv(json/device), #toglbnam, #toglbnam) != "",
    tc(json, gv(json/device), (".device_states[?(@.entity_id == '" + #toglbnam + "')]." + #keyname))
  )))$
```

###### #####################################
###### Flow: "Template DB & Device Icon Set" **NOT WORKING**
###### #####################################

This is the flow that writes the name of the current selected template to `currenttemplate`
and sets the device icon from `jsontemplate`. It merges the flows for consolidation

**Trigger:** `On Change` of `$gv(devicetype)$$gv(templateselect)$`

**Action**

###### 1 - Formula
```
  $lv(template, gv(devicetype) + gv(templateselect))$
  $lv(current, (tc(json, gv(json/template), ("." + #template))))$
  $#current$
```

###### 2 - Set Global Variable: `currenttemplate`

###### 3 - Formula
```
  $lv(template, gv(devicetype) + gv(templateselect))$
  $lv(current, (tc(json, gv(json/template), ("." + #template + ".icon"))))$
  $#current$
```

###### 4 - Set Global Variable: `deviceicon`


**V2**
`On Change` of `$gv(tasker/cmd)$`

```
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonmain)$
  $tc(json, gv(json/template), (\".\" + #template)$
```
Set `currenttemplate`


**V1** - Flow - "Current Template Global Set"
`On Change` of core/curpage

```
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonmain)$
  $tc(json, gv(json/template), ("." + #template)$
```

Set `currenttemplate`

**V1** - `core/currentdeviceicon`
```
  $gv(func/jsonobj)$
  $#tempicon$
```


###### #####################################
###### KWGT Template Icon code examples
###### #####################################

###### Icon name formula - Box 2, Row 2, Icon 0
**Find better way to see if icon is available then checking for an iconid**

```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#objkey != "0", #icon, #tempicon)$
```

**V2**
```
  $lv(type, gv(devicetype) + gv(templateselect))$
  $tc(json, gv(json/template), ("." + #obtype + ".box_2.row_1[0].icon"))$`
```

**V1**
  `$tc(json, gv(json/template), ("." + gv(core/tempname) + ".box_2.row_1[0].icon"))$`

###### Icon visiblity formula - Box 2, Row 2, Icon 2
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#objkey = "" | gv(core/size/box2iconmax) < 3, REMOVE, ALWAYS)$
```

**V3**
```
$lv(posid, ".box2.row1.1")$
$gv(func/jsonobj)$
$if(#objkey = "", REMOVE, ALWAYS)$
```


**V2** - No auto-hide
```

  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonobj)$
  $if(#obtype = "", REMOVE, ALWAYS)$
```

**V1**  
  `$if(gv(core/size/box2iconmax)  < 2, REMOVE, ALWAYS)$`

###### Circle Paint Color Formula
```
  $lv(posid, ".box2.row2.1")$
  $gv(func/jsonstate)$
  $if(#stoutput = 1, gv(colors/colico), #00FFFFFF)$
```

**V1**
```
  $lv(glbname, "light.bedroom_lights")$
  $if(gv(func/devicestate) = off, gv(core/colico), #00FFFFFF)$
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

###### Old Device states

ROOM SELECT (CIRCLE COLOR)
`$if(gv(core/curpage) = 1, gv(core/colico), #00FFFFFF)$`
ICON COLOR
`$if(gv(core/curpage) = 3, gv(colors/color1), gv(colors/colico))$`
FILTER
`$if(gv(core/curpage) = 3, "MULTIPLY", "NORMAL")$`

###### Box 1 Type

```
$if(gv() = 2, REMOVE, if(gv(devicecount) = 1, ALWAYS, REMOVE))$

$gv(func/jsonobj)$
$if(#ticopos = \"2\", REMOVE, ALWAYS)$

```
