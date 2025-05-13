# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

###### #####################################
###### KWGT > Tasker Flows and details
###### #####################################

###### #####################################
###### Process
###### #####################################
- KWGT Touch command is activated
  - Touch command writes to "tasker/cmd" (Reference below)
  - KWGT > Tasker flow activates on "tasker/cmd" change
    - Flow sends the command to tasker using the URI
    - Flow changes the visible page
  - Flow clears "tasker/cmd" **TO-DO**


###### #####################################
###### Schemea and notes
###### #####################################

###### Service Calls & "action" key value

light, on, off, toggle, brightness, template, custom
This is the reference for the data the KWGT touch command sends to `tasker/cmd`

HA Template sample: `{{ states.light.study_lights.attributes.brightness }}`
**`custom` is not coded in yet**

###### URI Output
Syntax:  `tasker://secondary?service_call=var&entity_id=var&parameters=var:100`
Example: `tasker://secondary?service_call=light&entity_id=light.bedroom_lights&parameters=brightness:125`

###### Home Asssistant template data sample
```
  {{ states('device_tracker.paulus') }}
  {{ state_attr('device_tracker.paulus', 'battery') }}
```

###### #####################################
###### Flow: Obj - Activate
###### #####################################
This is the flow activated from the touch event
It outputs either the tasker URI info or the page number for changing the page
This cannot be merged into following flows due to problems with writing more than one global

**Trigger:** `Manual` by touch command, `.box2.row1.0` sent via #last

**Action**

###### 1- Formula - Do all calls except template 
```
  $lv(posid, #last)$
  $gv(func/jsonobj)$

  $lv(uri, (if(#obtype = "template",
                   ("tasker://secondary?service_call=" + #obaction + "&parameters=" + #obparam),
                   #obtype = "action",
                   ("tasker://secondary?service_call=" + #obaction + "&entity_id=" + #entity + "&parameters=" + #obparam),
                   "")))$
  $if(#uri = "" & #obtype = "page", #obdata, #uri)$
```

###### 2 - Set Global Variable: `$gv(core/tasker/temp)$`

###### #####################################
###### Flow: Obj - KWGT > Tasker
###### #####################################
Send command URI to tasker from the widget

**Trigger:** `On Change` of `$gv(core/tasker/temp)$`

**Action**

###### 1 - Intent URI - Output tasker command
```
  $lv(uri, gv(core/tasker/temp))$
  $if(#uri ~= "\b\d\b", "", #uri)$
```

###### #####################################
###### Flow: Obj - Page Change
###### #####################################
Change current page

**Trigger:** `On Change` of `$gv(core/tasker/temp)$`

**Action**

###### 1 - Formula - Change Page
```
  $lv(uri, gv(core/tasker/temp))$
  $lv(page, gv(core/curpage))$
  $if(#uri ~= "\d", #uri, #page)$
```

###### 2 - Set Global Variable: `core/curpage`