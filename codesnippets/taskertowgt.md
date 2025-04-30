# TASKER > KWGT FLOWS Flows and details

######
###    Process
######
- Tasker HA blueprint pushes state change from HA to Tasker
  - Tasker pushes state change to HA using `tasker/stateupd`
  - `Tasker > KWGT` flow activates when `tasker/stateupd` is updated and adds the state data to `json/device`
  - Widget reloads all status indicators each time `json/template` is updated?



######
###    Schemea
######


### Syntax - See json.json for full example
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

### State update sample from tasker
```
{
  "entity_id": "light.bedroom_lights",
  "state": "on",
  "brightness": 125,
  "color": "255, 255, 128, 65"
},
```


######
###    Flow: "Current Template Global Set" (TASKER > KWGT)
######

This is the flow that adds new state data into `json/device`.
Zero formatting to fully human-readable JSON output (extra whitespace at top)

This flow uses 4 globals to run

**Trigger:** `On Change` of `$gv(tasker/fromtasker)$`

**Action**
### 1 - Formula
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

### 2 - Set Global Variable: `json/device`


V11 Functions

>>> Copy code from widget and verify it is correct. Prefer widget code

## func/devjsonrem
This is the function used by the Tasker > KWGT flow to remove an entity from `json/device`

#### Regex Match
  `.*\{\"entity_id\":\"#entity\"[^\}].*\},`
  `\{"entity_id":"#entity".*?\},`

```
  $tc(reg,
    #datawipe,
    ("\{\" + tc(utf, 22) + "entity_id" + "\" + tc(utf, 22) + ":\" + tc(utf, 22) + 
    #entity + "\" + tc(utf, 22) + ".*?\},"),"")$
```


## func/devjsonnew **DEPRECEATED?**
This is the function used by the Tasker > KWGT flow to add a new entity to`json/device`

#### Regex Match
`},\]\}` - MATCH
`\},#jsonwipe\]\}` - REPLACE

`$tc(reg, #jsonrem, "\},\]\}", ("\}," + #jsonwipe + "\]\}"))$`


## func/devjsonadd
This is the function used by the Tasker > KWGT flow to add new lines and in-line spaces to `json/device`

#### Regex Match
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

# func/devjsonindent
This is the function used by the Tasker > KWGT flow to add array indentation to`json/device`

#### Regex Match
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

######
###    Old versions of flow and functions
######

**V11** - Last original from before rebuilding Tasker> KWGT 2025
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

**V10** - WORKING - TASKER DATA > HUMAN READABLE (Extra whitespace at top)
```
  $lv(entity, tc(json, gv(tasker/fromtasker), ".entity_id"))$
  $lv(exists, if(tc(json, gv(json/device), "$.device_states[?(@.entity_id == '" + #entity + "')].entity_id") != "", 1, 0))$
  $lv(datawipe, tc(reg, tc(reg, gv(json/device), "\n", ""), "\s", ""))$
  $lv(jsonwipe, tc(reg, tc(reg, gv(tasker/fromtasker), "\n", ""), "\s", ""))$
  $lv(jsonrem, (if(#exists = 1, gv(func/devjsonrem), #datawipe)))$
  $lv(jsonout, gv(func/devjsonadd))$
  $lv(jsonline, gv(func/devjsonnewline))$
  $lv(jarrcut, tc(split, tc(split, #jsonline, "[", 1), "]", 0))$
  $lv(jarredit, tc(reg,#jarrcut, "\n", tc(utf, 0A) + "    "))$
  $lv(jaddspc, gv(func/devdevjsonindentent))$
  $lv(jremblnk, tc(reg, #jaddspc, "\s\n", ""))$$#jremblnk$
```
**V10** - GLOBAL functions

### $func/devjsonrem$
Remove entity from array
Regex: `\s+\{\"entity_id\":\"#entity\"[^\}]*\},`
```
  $tc(reg, 
    (#datawipe), 
    ("\s+\{\" + tc(utf, 22) + "entity_id" + "\" + tc(utf, 22) + ":\" + tc(utf, 22) + #entity + "\" + tc(utf, 22) + "[^\}]*\},"), 
    ""
  )$
```

### $func/devjsonadd$
Add new entity to JSON
Regex:
`\},]\}` - MATCH
`\},#jsonwipe]\}` - REPLACE

`$tc(reg, #jsonrem, "\},\]\}", ("\}," + #jsonwipe + "\]\}"))$`

### $func/devjsonnewline$
Add new lines and in-line spaces to JSON

#### Regex Match
```
  // \}, > \},\n
  // : > ": "
  // \", > \",\n
  // \{ > \{\n
  // \} > \n\}
  // \[ > \[\n
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
                  #jsonout, 
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

### $func/devdevjsonindentent$
Add array intendation
Regex:
`(?<=\[)[^\[\]]*(?=\])` - Match contents of array & replace with jarredit
`(?<!\[)(?<=\n|^)\s*("".*)` - Add indentation for array content inside each object

```
  $tc(reg, 
    tc(reg, 
      #jsonline, 
      ("(?<=\[)[^\[\]]*(?=\])"), 
      tc(utf, 0A) + "    " + #jarredit + tc(utf, 0A) + "  "
    ), 
    ("(?<!\[)(?< 
```

**V7** - WORKINGish (sh() problems)
```
  $lv(entity, tc(json, gv(tasker/fromtasker), ".entity_id"))$
  $lv(exists, if(tc(json, gv(json/device), "$.device_states[?(@.entity_id == '" + #entity + "')].entity_id") != "", 1, 0))$
  $lv(jsonwipe, sh("echo '" + gv(json/device) + "' | tr -d '\n' | sed 's/ //g'"))$
  $lv(jsonrem, (if(#exists = 1, sh("echo '" + gv(json/device) + "' | sed 's/{[^{]*" + tc(utf, 22) + "entity_id" + tc(utf, 22) + ": " + tc(utf, 22) + #entity + tc(utf, 22) + "[^}]*},//'"))))$
  $lv(jsonout, sh("echo '" + #jsonrem + "' | sed 's/},]}/}, " + gv(tasker/fromtasker) + "/'"))$
  $#jsonout$
```

**V6** - Current before JSON switch
```
  $lv(newstate, gv(tasker/fromtasker))$
  $lv(allstate, gv(tasker/statedat))$
  $lv(entityid, tc(split, #newstate, ":", 0))$
  $lv(exists, sh("echo '" + #allstate + "' | tr ';;' '\n' | awk -F':' '/^" + #entityid + ":/ {print $1}'"))$
  $lv(writesta, if(#exists != "",(tc(reg, #allstate, #entityid + ":.*", (#newstate + ";;")), #allstate + "
  " + #newstate + ";;")))$$#writesta$ 
```

**V5**
```
  $lv(newstate, gv(tasker/fromtasker))$
  $lv(allstate, gv(tasker/statedat))$
  $lv(entityid, tc(split, #newstate, ":", 0))$
  $lv(exists, sh("echo '" + #allstate + "' | tr ';;' '\n' | awk -F':' '/^" + #entityid + ":/ {print $1}'"))$
  $lv(writesta, if(#exists != "",(tc(reg, #allstate, #entityid + ":.*", (#newstate + ";;")), #allstate + "
  " + #newstate + ";;")))$$#writesta$
```

**V4**
```
  $lv(newstate, gv(tasker/fromtasker))$$lv(allstate, gv(tasker/statedat))$$lv(entityid, tc(split, #newstate, ":", 0))$
  $lv(exists, sh("echo '" + #allstate + "' | tr ';;' '\n' | awk -F':' '/^" + #entityid + ":/ {print $1}'"))$
  $lv(adddata, if(#exists != "",(tc(reg, #allstate, #entityid + ":.*", (#newstate + ";;")), #allstate + "
  " + #newstate + ";;")))$
  $lv(dedup, sh("echo '" + #adddata + "' | tr ';;' '\n' | awk -F':' '!seen[$1]++' | tr '\n' ';;'"))$
```

**V3**
```
  $lv(entityid, tc(split, gv(tasker/fromtasker), ":", 0))$
  $lv(state, tc(split, gv(tasker/fromtasker), ":", 1))$
  $if(tc(reg, gv(tasker/statedat), #entityid, #entityid) != "", tc(reg, gv(tasker/statedat), #entityid + ".*", gv(tasker/fromtasker) + ";;"), gv(tasker/statedat) + "
  " + gv(tasker/fromtasker) + ";;")$
```

**V2**
```
  $lv(entityid, tc(split, gv(tasker/fromtasker), ":", 0))$
  $lv(state, tc(split, gv(tasker/fromtasker), ":", 1))$
  $lv(count, tc(count, gv(tasker/statedat), #entityid))$
  $if(#count > 0, tc(reg, gv(tasker/statedat), #entityid + ".*", gv(tasker/fromtasker)), gv(tasker/statedat) + ";;
  " + gv(tasker/fromtasker))$
```

**V1.5**
```
  $lv(entityid, tc(split, gv(tasker/states), ":", 0))$
  $lv(state, tc(split, gv(tasker/states), ":", 1))$
  $lv(level, tc(split, gv(tasker/states), ":", 2))$
  $lv(count, tc(count, gv(haentnam), #entityid))$
  $if(#count > 0, 
      tc(reg, gv(haentnam), #entityid + " \S+", #entityid + " " + #state + " " + #level), 
      gv(haentnam) + "\n" + #entityid + " " + #state + " " + #level
  )$
```

**V1**
```
  $lv(entityid, tc(split, gv(tasker/fromtasker), ":", 0))$
  $lv(state, tc(split, gv(tasker/fromtasker), ":", 1))$
  $lv(level, tc(split, gv(tasker/fromtasker), ":", 2))$
  $lv(count, tc(count, gv(tasker/statedat), #entityid))$
  $if(#count > 0, tc(reg, gv(tasker/statedat), #entityid + " \S+", #entityid + " " + #state + " " + #level),
  gv(tasker/statedat) + "\n" + #entityid + " " + #state + " " + #level)$
```

## PREJSON STATEDATA GLOBAL FORMAT

TEST TASKER/STATEDAT CONTENT
light.bedroom_lights:on:255;;
light.study_lights:on:255;;
light.foyer_lights:on:125;;
light.lr_lights:on;;
sensor.s23_ultra_battery_level:92;;

light.bedroom_lights:on:255;;
light.study_lights:on:255;;
light.foyer_lights:on:125;;
light.lr_lights:on;;
sensor.s23_ultra_battery_level:92;;
