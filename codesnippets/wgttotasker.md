# This file is meant for the developer for reference only and only for viewing in the code editor, not a rendering preview. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

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
###### Flow: KWGT > Tasker
###### #####################################
This is the new flow used to send the signal to tasker from the widget
It takes the location data sent to the flow and matches it with the command from "action" in the JSON

**Trigger:** `Manual` by touch command

**Action**

###### 1- Formula - Do all calls except template 
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

Example output: `tasker://secondary?service_call=light&entity_id=light.bedroom_lights&parameters=brightness:125`

###### 2 - Intent URI - Output tasker command
```
  $#last$
```

###### 3 - Formula - Change Page
```
  $lv(posid, gv(core/tasker/temp))$
  $gv(func/jsonobj)$
  $#obdata$

```

###### 4 - Set Global Variable: `core/curpage`