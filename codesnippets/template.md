# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

###### #####################################
###### Global Template code sets
###### #####################################
See samples.json for json samples and examples

###### #####################################
###### KWGT Global Functions - Template
###### #####################################

###### func/jsonmain  - "Template JSON Backend"
Main function called by `func/jsonobj`, `func/jsonstate` and `func/jsontemp`.
This preforms the actual database calls to `json/template` and `json/device`

Example usage: `$gv(func/jsonmain)$ $lv(posid, ".box2.row1.0")$`

- Function return details:
- `#template`: `devicetype` + `templateselect`, eg `light1`
- `#tpageon`: 0 = no pages, 1 = use pages
- `#objkey`: consolidated `controls` root shortcut for `json/template`
- `#entitykey`: consolidated `device_states` root shortcut for `json/device`

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

###### func/jsontemp  - "JSON Template"
Function called by the widget items. General template data
No `#posid` required

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

###### func/jsonobj  - "JSON Objects"
Function called by the widget items. Object-specific references

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

###### func/jsonstate  - "JSON State"
Function called by the widget items. `stoutput` returns the device state

Example usage: `$lv(posid, ".box2.row1.0")$$gv(func/jsonstate)$[$lv(keyname, ".custom_variable")$]`

`#stobtype`: breaks down states from template. `page, off, on, range, nostate`
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
  )))$
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
###### KWGT Global Functions - Theme
###### #####################################

###### func/themecolors
Example usage: `$gv(func/themecolors)$$#cback1$`

```
  $gv(func/theme)$
  $lv(darkmode, gv(theme/darkmode))$
  $lv(colors,
    if(#darkmode = 0, (#settheme + ".colors.light"),
                      (#settheme + ".colors.dark")
  ))$

  $lv(cback1,      (tc(json, #dbtheme, ("." + #colors + ".back_1"))))$
  $lv(cback2,      (tc(json, #dbtheme, ("." + #colors + ".back_2"))))$
  $lv(cobjiconon,  (tc(json, #dbtheme, ("." + #colors + ".obj_icon_on"))))$
  $lv(cobjiconoff, (tc(json, #dbtheme, ("." + #colors + ".obj_icon_off"))))$
  $lv(cobjringon,  (tc(json, #dbtheme, ("." + #colors + ".obj_ring_on"))))$
  $lv(cobjringoff, (tc(json, #dbtheme, ("." + #colors + ".obj_ring_off"))))$
  $lv(cborder,     (tc(json, #dbtheme, ("." + #colors + ".border"))))$


  $lv(back1,
     (if(gv(colors/back1) != "", gv(colors/back1),
         if(#cback1 ~= "^#[a-fA-F0-9]*$", #cback1,
            si(tc(split, #cback1, ",", 0), tc(split, #cback1, ",", 1)))
         ))
  )$
  $lv(back2,
     (if(gv(colors/back2) != "", gv(colors/back2),
         if(#cback2 ~= "^#[a-fA-F0-9]*$", #cback2,
            si(tc(split, #cback2, ",", 0), tc(split, #cback2, ",", 1)))
         ))
  )$
  $lv(objiconon,
     (if(gv(colors/objiconon) != "", gv(colors/objiconon),
         if(#cobjiconon ~= "^#[a-fA-F0-9]*$", #cobjiconon,
            #cobjiconon = "random", "random",
            si(tc(split, #cobjiconon, ",", 0), tc(split, #cobjiconon, ",", 1)))
         ))
  )$
  $lv(objiconoff,
     (if(gv(colors/objiconoff) != "", gv(colors/objiconoff),
         if(#cobjiconoff ~= "^#[a-fA-F0-9]*$", #cobjiconoff,
            #cobjiconoff = "random", "random",
            si(tc(split, #cobjiconoff, ",", 0), tc(split, #cobjiconoff, ",", 1)))
         ))
  )$
  $lv(objringon,
     (if(gv(colors/objringon) != "", gv(colors/objringon),
         if(#cobjringon ~= "^#[a-fA-F0-9]*$", #cobjringon,
            #cobjringon = "random", "random",
           si(tc(split, #cobjringon, ",", 0), tc(split, #cobjringon, ",", 1)))
         ))
  )$
  $lv(objringoff,
     (if(gv(colors/objringoff) != "", gv(colors/objringoff),
         if(#cobjringoff ~= "^#[a-fA-F0-9]*$", #cobjringoff,
            #cobjringoff = "random", "random",
            si(tc(split, #cobjringoff, ",", 0), tc(split, #cobjringoff, ",", 1)))
         ))
  )$
  $lv(border,
     (if(gv(colors/border) != "", gv(colors/border),
         if(#cborder ~= "^#[a-fA-F0-9]*$", #cborder,
            si(tc(split, #cborder, ",", 0), tc(split, #cborder, ",", 1)))
         ))
  )$
```

###### func/theme 
Example usage: `$gv(func/theme)$$#border$`
Only call the local variables with no prefix for valid data.

```
  $lv(dbtheme,    gv(json/theme))$
  $lv(settheme,   gv(settheme))$

  $lv(tborder,    (tc(json, #dbtheme, ("." + #settheme + ".border_width"))))$
  $lv(tradius,    (tc(json, #dbtheme, ("." + #settheme + ".corner_radius"))))$
  $lv(tpadwgt,    (tc(json, #dbtheme, ("." + #settheme + ".pad_widget"))))$
  $lv(tpadobj,    (tc(json, #dbtheme, ("." + #settheme + ".pad_obj"))))$
  $lv(tpadside,   (tc(json, #dbtheme, ("." + #settheme + ".pad_sides"))))$
  $lv(tdevico,    (tc(json, #dbtheme, ("." + #settheme + ".dev_ico_ring"))))$
  $lv(tringsha,   (tc(json, #dbtheme, ("." + #settheme + ".ring_shape"))))$
  $lv(ticonsize,  (tc(json, #dbtheme, ("." + #settheme + ".ring_percent"))))$
  $lv(tringrnd,   (tc(json, #dbtheme, ("." + #settheme + ".ring_random"))))$
  $lv(tshadow,    (tc(json, #dbtheme, ("." + #settheme + ".shadow"))))$
  $lv(twgtopa,    (tc(json, #dbtheme, ("." + #settheme + ".wgt_opacity"))))$

  $lv(gborder,    gv(theme/bordersize))$
  $lv(gradius,    gv(theme/cornerradius))$
  $lv(gpadwgtver, gv(theme/wgtpaddingver))$
  $lv(gpadwgthor, gv(theme/wgtpaddinghor))$
  $lv(gpadobjhor, gv(theme/objpaddinghor))$
  $lv(gpadobjver, gv(theme/objpaddingver))$
  $lv(gpadside,   gv(theme/objpaddingside))$
  $lv(gringsha,   gv(theme/ringshape))$
  $lv(giconsize,  gv(theme/iconsize))$
  $lv(gringrnd,   gv(colors/ringrandom))$
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
  $lv(iconsize,   (if(#giconsize,  (if((#giconsize * 1)  <= 0, #ticonsize, #giconsize)),  #ticonsize)))$
  $lv(ringrnd,    (if(#gringrnd,   (if((#gringrnd * 1)   <= 0, #tringrnd,  #gringrnd)),   #tringrnd)))$
  $lv(shadow,     (if(#gshadow,    (if((#gshadow * 1)    <= 0, #tshadow,   #gshadow)),    #tshadow)))$
  $lv(wgtopa,     (if(#gwgtopa,    (if((#gwgtopa * 1)    <= 0, #twgtopa,   #gwgtopa)),    #twgtopa)))$

  $lv(squircle, (#iconsize / 70) * 100)$
  $lv(colorcount, if(#ringrnd = 1, tc(reg, fl(0, 20, "i+1", gv(core/randomring), ","), "[\d+,]*,(\d+),*", "$1")))$
  $lv(colorcheck, if(#colorcount ~= "[,*]" & #ringrnd = 1, 0, #colorcount))$
```

###### #####################################
###### Flow: KWGT Global template
###### #####################################
"Light - 1 Device, 2 Rows, 2 Boxes"

**Trigger:** `OnChange` formula: `$gv(templateselect)`

**Action**

###### 1 - Formula
```
  $lv(posid, (".box2.row1." + (#box2maxobj - 1)))$
  $gv(func/alignment)$
  $lv(box1vis,
    if(#tbox1 = 1, ", 1 Box",
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
        "This template requires " + "?" + " icons to fit in Box 2, but only " + "#box2maxobj" + " can fit.\\nResize the widget, see \"fitmoreobj\" setting below or consult Readme",
        ""
    )))$

  $gv(devicetype) + " - " + #tdevct + " Device(s), " + #trowct + " Row(s)" + #box1vis$
  $#ntdevnum$
  $#ntwgtsize$
```

###### 2 - Set Global Variable: `templateinfo`
 
###### #####################################
###### Flow: Generate Material Colors
###### #####################################

**Trigger:** `OnChange` formula: `$gv(colors/colorgen/syscolorset)$$gv(colors/colorgen/adjtone)$$gv(colors/colorgen/opacity)$`

**Action**

###### 1 - Formula
```
  $gv(func/themecolors)$

  $lv(adjcolor, gv(colors/colorgen/adjtone))$
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

**Trigger:** `OnChange` formula: `$gv(colors/coloredit/editcolor)$$gv(colors/coloredit/filter)$$gv(colors/coloredit/adjfilter)$$gv(colors/coloredit/opacity)$`

**Action**

###### 1 - Formula
```
  $gv(func/themecolors)$

  $lv(adjfilter, gv(colors/coloredit/adjfilter))$
  $lv(opacity, gv(colors/coloredit/opacity))$
  $lv(gfilter, gv(colors/coloredit/filter))$
  $lv(editcolor, gv(colors/coloredit/editcolor))$

  $lv(filter, (if(#gfilter = 1, "sat",
                        #gfilter = 2, "lum",
                        #gfilter = 3, "comp",
                        "")
  ))$

  $lv(getcolor, (tc(json, #dbtheme, ("." + #colors + #editcolor))))$

  $lv(editfilter, if(#editcolor ~= "wpcolor\d",
                    ce(si(#editcolor), #filter, #adjfilter),
                    ce(#getcolor, #filter, #adjfilter),
  ))$

  $lv(editfinal, ce(#editfilter, alpha, #opacity))$
  $#editfinal$
```

###### 2 - Set Global Variable: `colors/coloredit/output`

###### #####################################
###### KWGT Template Icon code samples
###### #####################################

###### Objects
- icon_icon
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $if(#objid != "0", #icon, #tempicon)$
```

- icon_size
```
  $gv(func/alignment)$
  $#objiconsize$
```

- paint_color
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonstate)$
  $gv(func/themecolors)$
  $if(#stoutput = 1, #objiconon, #objiconoff)$
```

- Object visiblity formula (auto-hide)
```
  $lv(posid, ".box2.row2.0")$
  $gv(func/jsonobj)$
  $gv(func/alignment)$
  $if(#obid = "0" | #box2maxobj < 3, REMOVE, ALWAYS)$
```

- Object visiblity formula (pages)
```
  $lv(posid, ".box1.row2.0")$
  $gv(func/jsonobj)$
  $lv(entity, "entities/entity" + #obdata)$

  $if(#obid = "0", REMOVE,
      #obid = "1" & #obtype = "page" & gv(#entity) = "", REMOVE, ALWAYS)$
```

###### Rings
- paint_color
```
  $lv(posid, ".box1.row2.1")$
  $gv(func/jsonstate)$
  $gv(func/themecolors)$
  $if(#ringcolor = "", lv(ringcolor, ce(tc(json, #dbtheme, (".randomring." + mu(rnd, 0, #colorcount))), sat, 20)))$

  $if(#objringoff = "random" & #ringrnd = 1 & #colorcheck != 0, #ringcolor,
      if(#stoutput = 1, #objringon,
         #objringoff)
  )$
```

- position_padding_left
```
  $lv(posid, ".box1.row2.1")$
  $gv(func/jsonobj)$
  $gv(func/alignment)$

  $if(#obid = "0" & #align = 0, "10", "0")$
```

- position_padding_top
```
  $lv(posid, ".box1.row2.1")$
  $gv(func/jsonobj)$
  $gv(func/alignment)$

  $if(#obid = "0" & #align = 1, "10", "0")$
```

###### Device Icon properties
- position_anchor
```
  $gv(func/alignment)$$#devicopos$
```

- position_offset_x
```
  $gv(func/alignment)$

  $lv(pos, if(#devicoalign = 1, #devposcalc, 0))$
  $if(#ticopos = 1 | #ticopos = 3, #pad3, #ticopos = 2, #pos)$
```

- position_offset_y
```
  $gv(func/alignment)$

  $lv(pos, if(#poscalc = 0, #poscalc, 0))$
  $if(#ticopos = 1 | #ticopos = 3, #pad3, #ticopos = 2, #pos)$
```

- paint_color
```
$gv(func/themecolors)$
$lv(posid, ".box1.row1.0")$
$gv(func/jsonstate)$

$if(gv(devicetype) = "light" & #stcolor != "", #stcolor, #objiconoff)$
```

###### App Icon properties
- position_anchor
```
  $gv(func/alignment)$$#appicopos$
```

- position_offset_x
```
  $gv(func/alignment)$

  $lv(pos, if(#devicoalign = 1, #devposcalc, 0))$
  $(if(#ticopos = 1 | #ticopos = 3, #pad3, #ticopos = 2, #pos)) + #padobjside$
```

- position_offset_y
```
  $gv(func/alignment)$

  $lv(pos, if(#poscalc = 0, #poscalc, 0))$
  $(if(#ticopos = 1 | #ticopos = 3, #pad3, #ticopos = 2, #pos)) + #padobjside$
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