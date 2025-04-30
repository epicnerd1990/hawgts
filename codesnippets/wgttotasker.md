# KWGT > Tasker Flows and details

######
###    Process
######
- KWGT Touch command is activated
  - Touch command writes to "tasker/cmd" (Reference below)
  - KWGT > Tasker flow activates on "tasker/cmd" change
    - Flow sends the command to tasker using the URI
    - Flow changes the visible page
  - Flow clears "tasker/cmd" **TO-DO**


######
###    Schemea
######

### Service Calls & "action" key value

light, on, off, toggle, brightness, template, custom
This is the reference for the data the KWGT touch command sends to `tasker/cmd`

HA Template sample: `{{ states.light.study_lights.attributes.brightness }}`
**`custom` is not coded in yet**

### URI Output
Syntax:  `tasker://secondary?service_call=var&entity_id=var&parameters=var:100`
Example: `tasker://secondary?service_call=light&entity_id=light.bedroom_lights&parameters=brightness:125`

### Home Asssistant template data sample
```
  {{ states('device_tracker.paulus') }}
  {{ state_attr('device_tracker.paulus', 'battery') }}
```


######
###    Flow: "Template Touch - KWGT > Tasker" (KWGT > Tasker)
######

This is the new flow used to send the signal to tasker from the widget
It takes the location data sent to the flow and matches it with the command from "action" in the JSON

**Trigger:** `Manual` by touch command

**Action**
### 1- Formula - Do all calls except template 
```
  $lv(posid, gv(core/tasker/temp))$
  $gv(func/jsonobj)$

  $lv(combine, (if(#obtype = "template",
                   ("tasker://secondary?service_call=" + #obaction + "&parameters=" + #obparam),
                   #obtype = "action",
                   ("tasker://secondary?service_call=" + #obaction + "&entity_id=" + #entity + "&parameters=" + #obparam),
                   "")))$
  $#combine$
```

Example: `tasker://secondary?service_call=light&entity_id=light.bedroom_lights&parameters=brightness:125`

### 2 - Intent URI - Output tasker command
```
  $#last$
```

### 3 - Formula - Change Page
```
  $lv(posid, gv(core/tasker/temp))$
  $gv(func/jsonobj)$
  $#obdata$

```

### 4 - Set Global Variable: `core/curpage`



######
###    Old versions of flow and functions
######

**V5** Last 2023 version - incomplete?
```
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonobj)$
  $lv(out, (if(#obtype = "global", (#obaction + "&" + #obcmd1),(#obaction))))$
  $#out$
```
```
  $lv(srvccall, tc(split, #last, "&", 0))$
  $lv(entityid, tc(split, #last, "&", 1))$
  $lv(data, tc(split, #last, "&", 2))$
  $lv(recomb, (if(#srvccall = "template", 
  ("tasker://secondary?service_call=" + #srvccall + "&parameters=" + #entityid),
  ("tasker://secondary?service_call=" + #srvccall + "&entity_id=" + #entityid + "&parameters=" + #data))))$
  $if(#last != "", #recomb)$
```


**V4.5** - Merge with KWGT > Tasker Flow
```
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonobj)$
    $lv(out, (if(#obtype = "action",
            ("tasker://secondary?service_call=" + #obaction + "&entity_id=" + #enttemp + "&parameters=" + #obcmd1),
            #obtype = "template", 
            ("tasker://secondary?service_call=" + #obaction + "&parameters=" + #entityid)
        ))
  )$
  $if(#out != "", #out)$
```

**V4**
```
  $lv(srvccall, tc(split, gv(tasker/cmd), "&", 0))$
  $lv(entityid, tc(split, gv(tasker/cmd), "&", 1))$
  $lv(data, tc(split, gv(tasker/cmd), "&", 2))$
  $lv(recomb, (if(#srvccall = "template",
  ("tasker://secondary?service_call=" + #srvccall + "&parameters=" + #entityid),
  ("tasker://secondary?service_call=" + #srvccall + "&entity_id=" + #entityid + "&parameters=" + #data))))$
  $if(gv(tasker/cmd) != "", #recomb)$
```

**V3.5**
```
  $lv(srvccall, tc(split, #last, "&", 0))$
  $lv(entityid, tc(split, #last, "&", 1))$
  $lv(data, tc(split, #last, "&", 2))$
  $lv(recomb, (if(#srvccall = "template", 
  ("tasker://secondary?service_call=" + #srvccall + "&parameters=" + #entityid),
  ("tasker://secondary?service_call=" + #srvccall + "&entity_id=" + #entityid + "&parameters=" + #data))))$
  $if(#last != "", #recomb)$
```

**V3** - Add to new template syntax and readjson
```
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonobj)$
  $lv(out, (if(#obtype = "action",
  (#obaction + "&" + #obcmd1),
  (#obaction)
  )
  ))$$#out$
```

**V2.5**
```
  $lv(template, gv(devicetype) + gv(templateselect))$
  $lv(type, (tc(json, gv(json/template), ("." + #template + ".box_2.row_1[0].type"))))$
  $if(#template != "" & #obtype = "action", (tc(json, gv(json/template), ("." + #template + ".box_2.row_1[0].action"))), #template)$
```

**V2**
```
  $lv(type, gv(devicetype) + gv(templateselect))$
  $tc(json, gv(json/template), ("." + #obtype + ".box_2.row_1[0].action"))$
```

**V1.5** - ADDED TEMPLATE SUPPORT
```
  $lv(srvccall, tc(split, gv(tasker/hactrl), "&", 0))$
  $lv(entityid, tc(split, gv(tasker/hactrl), "&", 1))$
  $lv(data, tc(split, gv(tasker/hactrl), "&", 2))$
  $lv(recomb, (if(#srvccall = "template", 
  ("tasker://secondary?service_call=" + #srvccall + "&parameters=" + #entityid),
  ("tasker://secondary?service_call=" + #srvccall + "&entity_id=" + #entityid + "&parameters=" + #data))))$$#recomb$
```

**V1**
```
  $lv(srvccall, tc(split, gv(tasker/hactrl), "&", 0))$
  $lv(entityid, tc(split, gv(tasker/hactrl), "&", 1))$
  $lv(data, tc(split, gv(tasker/hactrl), "&", 2))$
  $lv(recomb, "tasker://secondary?service_call=" + #srvccall + "&entity_id=" + #entityid + "&parameters=" + #data)$$#recomb$
```



**Page Flow** - Merged into KWGT > Tasker
```
V2
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonobj)$
  $lv(out, (if(#obtype = "page", #obaction, #curpage1)))$$#out$


V1 - Not Working
  $lv(posid, gv(tasker/cmd))$
  $gv(func/jsonmain)$
  $lv(curpage, gv(core/curpage))$
  $lv(out, (if(#globalcm = "page", #obcmd1, #curpage1)))$$#out$
```