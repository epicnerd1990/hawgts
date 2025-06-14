# This file is meant for the developer for reference only and the content is laid out for viewing in the code editor only. Using markdown as a simple organizing format for readability, notes, folding and pseudocode

# This file contains snippets of code formatted for easy editing. This code is syncronized to "preset.json" values after each edit. Related data also included

###### #####################################
###### Tasker > KWGT Flows and related globals
###### #####################################

###### #####################################
###### Process
###### #####################################
- Tasker HA blueprint pushes state change from HA to Tasker
  - Tasker pushes state change to HA using `core/tasker/fromtasker`
  - `Tasker > KWGT` flow activates when `core/tasker/fromtasker` is updated and adds the state data to `json/device`
  - Widget reloads all status indicators each time `json/device` is updated

###### #####################################
###### Schemea
###### #####################################

###### Syntax - See json.json for full example
```
{
  "device_states": [
    {
      "entity_id": "placeholder",
      "state": "on"
    },
  ]
}
```

###### State update sample from tasker
```
{
  "entity_id": "light.bedroom_lights",
  "state": "on",
  "brightness": 125,
  "color": "255, 255, 128, 65"
},
```


###### #####################################
###### Flow: Tasker > KWGT
###### #####################################

This is the flow that adds new state data into `json/device`.
Zero formatting to fully human-readable JSON output (extra whitespace at top)

This flow uses 4 globals to run

**Trigger:** `On Change` of `$gv(tasker/fromtasker)$`

**Action**

###### 1 - Formula
```
  $lv(entity, tc(json, gv(tasker/fromtasker), ".entity_id"))$
  $lv(exists, if(tc(json, gv(json/device), "$.device_states[?(@.entity_id == '" + #entity + "')].entity_id") != "", 1, 0))$
  $lv(datawipe, tc(reg, tc(reg, gv(json/device), "\n", ""), "\s", ""))$
  $lv(jsonwipe, tc(reg, tc(reg, gv(tasker/fromtasker), "\n", ""), "\s", ""))$
  $lv(jsonrem, (if(#exists = 1, gv(func/devjsonrem), #datawipe)))$
  $lv(jsonout, tc(reg, #jsonrem, "\},\]\}", ("\}," + #jsonwipe + "\]\}")))$
  $lv(jsonline, gv(func/devjsonadd))$
  $lv(jarrcut, tc(split, tc(split, #jsonline, "[", 1), "]", 0))$
  $lv(jarredit, tc(reg,#jarrcut, "\n", tc(utf, 0A) + "    "))$
  $lv(jaddspc, gv(func/devjsonindent))$
  $lv(jremblnk, tc(reg, #jaddspc, "\s\n", ""))$$#jremblnk$
```

###### 2 - Set Global Variable: `json/device`

###### #####################################
###### Global Functions - Tasker > KWGT
###### #####################################

###### func/devjsonrem
This is the function used by the Tasker > KWGT flow to remove an entity from `json/device`

**Regex Match**
  `.*\{\"entity_id\":\"#entity\"[^\}].*\},`
  `\{"entity_id":"#entity".*?\},`

```
  $tc(reg,
    #datawipe,
    ("\{\" + tc(utf, 22) + "entity_id" + "\" + tc(utf, 22) + ":\" + tc(utf, 22) + 
    #entity + "\" + tc(utf, 22) + ".*?\},"),"")$
```


###### func/devjsonnew
This is the function used by the Tasker > KWGT flow to add a new entity to`json/device`

**Regex Match**
`},\]\}` - MATCH
`\},#jsonwipe\]\}` - REPLACE

`$tc(reg, #jsonrem, "\},\]\}", ("\}," + #jsonwipe + "\]\}"))$`


###### func/devjsonadd
This is the function used by the Tasker > KWGT flow to add new lines and in-line spaces to `json/device`

**Regex Match**
```
  \}, > \},\n
  : > ": "
  \", > \",\n
  \{ > \{\n
  \} > \n\}
  \] > \]\n
```

```
  $tc(reg,
    tc(reg,
      tc(reg,
        tc(reg,
          tc(reg,
            tc(reg,
              tc(reg,
                tc(reg,
                  #jremblnk,
                  "\},",
                  "\}," + tc(utf, 0A)
                ),
                ":",
                ": "
              ),
              "\" + tc(utf, 22) + ",",
              "\" + tc(utf, 22) + "," + tc(utf, 0A)
            ),
            "\{",
            "\{" + tc(utf, 0A)
          ),
          "\}",
          tc(utf, 0A) + "\}"
        ),
        "\[",
        "\[" + tc(utf, 0A)
      ),
      "\" + tc(utf, 22) + "device_states\" + tc(utf, 22) + ".*",
      "$0"
    ),
    "\]",
    "  ]"
  )$
```

###### func/devjsonindent
This is the function used by the Tasker > KWGT flow to add array indentation to`json/device`

**Regex Match**
```
  \s+\{\"entity_id\":\"#entity\"[^\}]*\},
  \},\]\} - MATCH
  \},#jsonwipe\]\} - REPLACE
  \}, > \},\n
  : > ": "
  \", > \",\n
  \{ > \{\n
  \} > \n\}
  \] > \]\n
  (?<=\[)[^\[\]]*(?=\]) - Match contents of array & replace with jarredit
  (?<!\[)(?<=\n|^)\s*("".*) - Add indentation for array content inside each object
```
```
  $(tc(reg, 
      tc(reg, 
        #jsonline, 
        ("(?<=\[)[^\[\]]*(?=\])"), 
        tc(utf, 0A) + "    " + #jarredit + tc(utf, 0A) + "  "
      ), 
      ("(?<!\[)(?<=\n|^)\s*(" + tc(utf, 22) + ".*)"), 
      "  $0"
  ))$
```