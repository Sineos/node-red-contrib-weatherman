[
    {
        "id": "b016607a.570c28",
        "type": "tab",
        "label": "Weatherman",
        "disabled": false,
        "info": ""
    },
    {
        "id": "47595dc.ba94324",
        "type": "tcp in",
        "z": "b016607a.570c28",
        "name": "Listen Weatherman",
        "server": "server",
        "host": "",
        "port": "8186",
        "datamode": "single",
        "datatype": "utf8",
        "newline": "",
        "topic": "",
        "base64": false,
        "x": 130,
        "y": 680,
        "wires": [
            [
                "ae8d6dda.86f638"
            ]
        ]
    },
    {
        "id": "ae8d6dda.86f638",
        "type": "function",
        "z": "b016607a.570c28",
        "name": "Parse JSON",
        "func": "// customize basepath for the topic\n// the module type will be prepended\nconst basePath = '/status/';\n\n// customize basepath for info properties\n// the module type will be prepended\nconst basePathSys = '/info/';\n\nlet myTopic = '',\n    myValue = '',\n    myUnit = '',\n    myType = '',\n    myTime = '',\n    myObject = '';\n\nconst myResults = [],\n      myJSON = msg.payload;\n\n// Regex to match the unicode characters for EOT\nconst grepEOT = /(.*)(\\u0003|\\u0004)/;\nconst match = grepEOT.exec(myJSON);\n\n// String contains EOT, should be complete\nif (match !== null) {\n  // Try to parse JSON and validate object\n  myObject = JSON.parse(match[1]);\n  if (myObject && typeof myObject === 'object') {\n    const myObjectVars = myObject.vars;\n    const arrayLength = myObjectVars.length;\n    const moduleType = myObject.modultyp.toLowerCase();\n    myTime = (new Date()).toLocaleString('de-DE');\n\n    // loop through \"vars\" array\n    for (let i = 0; i < arrayLength; i += 1) {\n      myTopic = moduleType + basePath + myObjectVars[i].homematic_name;\n      myUnit = myObjectVars[i].unit;\n      myType = myObjectVars[i].type;\n\n      // ensure correct type\n      if (myType === 'number') {\n        myValue = Number(myObjectVars[i].value);\n      } else if (myType === 'string') {\n        myValue = String(myObjectVars[i].value);\n      } else if (myType === 'boolean') {\n        (myObjectVars[i].value === 'true' ? myValue = true : myValue = false);\n      }\n\n      // Push values as objects into an array\n      myResults.push({topic: myTopic, payload: myValue, unit: myUnit, timestamp: myTime});\n    }\n\n    // pass \"Systeminfo\" as object\n    myTopic = moduleType + basePathSys;\n    myValue = myObject.Systeminfo;\n\n    // Push values as objects into an array\n    myResults.push({topic: myTopic, payload: myValue, timestamp: myTime});\n\n    // return array with the results as messages\n    return [myResults];\n\n  // Possibly truncated / corrupt message\n  } else {\n    node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n  }\n// No EOT found or parse error. Possibly truncated / corrupt message\n} else {\n  node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n}\n",
        "outputs": 1,
        "noerr": 0,
        "x": 330,
        "y": 680,
        "wires": [
            [
                "a47aee8c.f71c88",
                "70bdc0ab.57bb68",
                "dd404d55.4d0298",
                "9bfbbc02.2286",
                "11d0add3.735b3a",
                "1fd174e9.955d3b",
                "4b0e207b.f9a3a8",
                "eec14340.79e85",
                "ef55953c.2a9d8",
                "6164d528.cf0d84",
                "118d8092.a8adaf",
                "fbe364b8.e7a858",
                "b2f46e2f.031ba8",
                "4315c17.dc0774",
                "3836dbab.93bbec",
                "85a350e9.cc59d",
                "54d5512e.0b394",
                "dfcb9053.296238",
                "6c22d9a6.186f28",
                "2617b6e9.75f202",
                "5a395e80.61ad28",
                "dbbee228.7b6678",
                "dfadea41.67e",
                "edd899e.dc0c0e8",
                "fbe3e58.776dc98",
                "466dd9a1.ec0828",
                "d52eec66.eecd2"
            ]
        ],
        "outputLabels": [
            "bla"
        ]
    },
    {
        "id": "76b596e8.b0a7a8",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "cda55cb9.d73fc",
        "name": "Temperatur",
        "order": 0,
        "width": "3",
        "height": "6",
        "format": "<script>\nvar linearThermo;\nlinearThermo = new steelseries.Linear('canvasLinearThermo', {\n                 width: 140,\n                 height: 320,\n                 gaugeType: steelseries.GaugeType.TYPE2,\n                 titleString: \"Außentemperatur\",\n                 unitString: \"°C\",\n                 ledVisible: false,\n                 lcdVisible: true,\n                 thresholdRising: false,\n                 thresholdVisible: false,\n                 niceScale: true,\n                 minValue: -25,\n                 maxValue: 40\n               });\n                    \nlinearThermo.setFrameDesign(steelseries.FrameDesign.BLACK_METAL);\nlinearThermo.setBackgroundColor(steelseries.BackgroundColor.BRUSHED_METAL);\nlinearThermo.setValueAnimated(0);\nlinearThermo.setMaxValue(40);\n\n(function(scope){ \n  scope.$watch('msg', function(msg) {\n    if (msg != null && msg.topic === 'weatherman/status/w_temperatur') {\n      linearThermo.setValueAnimated(msg.payload);\n      if (msg.payload > 0) {\n        linearThermo.setMaxValue(msg.payload + 2);\n        linearThermo.setMinValue(0);\n      } else if (msg.payload < 0) {\n        linearThermo.setMaxValue(0);\n        linearThermo.setMinValue(msg.payload - 2);\n      }\n    }\n  });\n})(scope);\n</script>\n\n<canvas id=\"canvasLinearThermo\" width=\"140\" height=\"320\"></canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 770,
        "y": 180,
        "wires": [
            []
        ]
    },
    {
        "id": "762ab434.62a9c4",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Weatherman",
        "info": "",
        "x": 110,
        "y": 60,
        "wires": []
    },
    {
        "id": "f2032b6f.a4ac7",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Weatherman - Values",
        "info": "",
        "x": 600,
        "y": 140,
        "wires": []
    },
    {
        "id": "9892c742.9c59d",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Weatherman - System Info",
        "info": "",
        "x": 610,
        "y": 1280,
        "wires": []
    },
    {
        "id": "4b9f5645.09b9",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "8132d18c.052bd",
        "name": "Load required scripts",
        "order": 3,
        "width": 0,
        "height": 0,
        "format": "<script type=\"text/javascript\" src=\"/addons/red/static/scripts/steelseries-min.js\"></script>\n<script type=\"text/javascript\" src=\"/addons/red/static/scripts/tween-min.js\"></script>\n<script type=\"text/javascript\" src=\"/addons/red/static/scripts/RGraph.common.core.js\"></script>\n<script type=\"text/javascript\" src=\"/addons/red/static/scripts/RGraph.radar.js\"></script>",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "global",
        "x": 140,
        "y": 100,
        "wires": [
            []
        ]
    },
    {
        "id": "a47aee8c.f71c88",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_temperatur",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_temperatur",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 180,
        "wires": [
            [
                "76b596e8.b0a7a8"
            ]
        ]
    },
    {
        "id": "4b0e207b.f9a3a8",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_windchill",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_windchill",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 570,
        "y": 220,
        "wires": [
            []
        ]
    },
    {
        "id": "eec14340.79e85",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_taupunkt",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_taupunkt",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 570,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "ef55953c.2a9d8",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_himmeltemperatur",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_himmeltemperatur",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 600,
        "y": 300,
        "wires": [
            []
        ]
    },
    {
        "id": "6164d528.cf0d84",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_feuchte_rel",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_feuchte_rel",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 340,
        "wires": [
            []
        ]
    },
    {
        "id": "118d8092.a8adaf",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_feuchte_abs",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_feuchte_abs",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "fbe364b8.e7a858",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_regensensor_wert",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_regensensor_wert",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 600,
        "y": 420,
        "wires": [
            []
        ]
    },
    {
        "id": "b2f46e2f.031ba8",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_regenmelder",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_regenmelder",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 460,
        "wires": [
            []
        ]
    },
    {
        "id": "4315c17.dc0774",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_regenstaerke",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_regenstaerke",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 500,
        "wires": [
            []
        ]
    },
    {
        "id": "3836dbab.93bbec",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_regen_letzte_h",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_regen_letzte_h",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 590,
        "y": 540,
        "wires": [
            []
        ]
    },
    {
        "id": "2617b6e9.75f202",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_regen_mm_heute",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_regen_mm_heute",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 600,
        "y": 580,
        "wires": [
            []
        ]
    },
    {
        "id": "5a395e80.61ad28",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_regenstunden_heute",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_regenstunden_heute",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 610,
        "y": 620,
        "wires": [
            []
        ]
    },
    {
        "id": "1fd174e9.955d3b",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_barometer",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_barometer",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 570,
        "y": 660,
        "wires": [
            [
                "2a6c8b35.51fe3c"
            ]
        ]
    },
    {
        "id": "85a350e9.cc59d",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_barotrend",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_barotrend",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 570,
        "y": 700,
        "wires": [
            [
                "2a6c8b35.51fe3c"
            ]
        ]
    },
    {
        "id": "dd404d55.4d0298",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_wind_mittel",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_wind_mittel",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 740,
        "wires": [
            [
                "f7f36707.4f1bb"
            ]
        ]
    },
    {
        "id": "dfcb9053.296238",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_wind_spitze",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_wind_spitze",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 860,
        "wires": [
            []
        ]
    },
    {
        "id": "54d5512e.0b394",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_windstaerke",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_windstaerke",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 820,
        "wires": [
            []
        ]
    },
    {
        "id": "9bfbbc02.2286",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_windrichtung",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_windrichtung",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 780,
        "wires": [
            [
                "f7f36707.4f1bb"
            ]
        ]
    },
    {
        "id": "6c22d9a6.186f28",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_lux",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_lux",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 550,
        "y": 940,
        "wires": [
            []
        ]
    },
    {
        "id": "dbbee228.7b6678",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_sonne_diff_temp",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_sonne_diff_temp",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 590,
        "y": 980,
        "wires": [
            []
        ]
    },
    {
        "id": "dfadea41.67e",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_sonne_scheint",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_sonne_scheint",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 590,
        "y": 1020,
        "wires": [
            []
        ]
    },
    {
        "id": "edd899e.dc0c0e8",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_elevation",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_elevation",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 570,
        "y": 1060,
        "wires": [
            []
        ]
    },
    {
        "id": "fbe3e58.776dc98",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_azimut",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_azimut",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 560,
        "y": 1100,
        "wires": [
            []
        ]
    },
    {
        "id": "466dd9a1.ec0828",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_minuten_vor_sa",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_minuten_vor_sa",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 590,
        "y": 1140,
        "wires": [
            []
        ]
    },
    {
        "id": "d52eec66.eecd2",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_minuten_vor_su",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_minuten_vor_su",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 590,
        "y": 1180,
        "wires": [
            []
        ]
    },
    {
        "id": "70bdc0ab.57bb68",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "weatherman/info",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/info/",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 580,
        "y": 1320,
        "wires": [
            [
                "9f2dc47c.4a883",
                "b285ab0f.38d18"
            ]
        ]
    },
    {
        "id": "9f2dc47c.4a883",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sec_seit_reset",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & 'sec_seit_reset'",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.sec_seit_reset",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 780,
        "y": 1320,
        "wires": [
            [
                "5da1840e.018e54"
            ]
        ]
    },
    {
        "id": "f7f36707.4f1bb",
        "type": "function",
        "z": "b016607a.570c28",
        "name": "WindData",
        "func": "'use strict';\n// Long term wind data in h\nconst limitLongTerm = 8;\n// Mid term wind data in h\nconst limitMediumTerm = 3;\n// Short term wind data in h\nconst limitShortTerm = 1;\n\n// Define context to store data across multiple messages\nvar windData = context.get('windData') || {};\nvar windVector = context.get('windVector') || [];\n\n// Collect both wind speed and wind direction\n// and store them in the context\nswitch (msg.topic) {\n  case 'weatherman/status/w_windrichtung':\n    windData.direction = msg.payload;\n    context.set('windData', windData);\n    msg = null;\n    break;\n  case 'weatherman/status/w_wind_mittel':\n    windData.speed = msg.payload;\n    context.set('windData', windData);\n    msg = null;\n    break;\n  default:\n    msg = null;\n    break;\n}\n\n// Start processing the data once both speed and \n// direction are available\nif (windData.direction != null && windData.speed != null) {\n  var LTdata = [],\n      MTdata = [],\n      STdata = [],\n      LTresult = [],\n      MTresult = [],\n      STresult = [],\n      windObj = {},\n      LTmsg = {},\n      MTmsg = {},\n      STmsg = {};\n  const LTlimit = Date.now() - (limitLongTerm * 3600 * 1000);\n  const MTlimit = Date.now() - (limitMediumTerm * 3600 * 1000);\n  const STlimit = Date.now() - (limitShortTerm * 3600 * 1000);\n\n  // Generate an object out of speed, direction and \n  // current time and store as array\n  windObj = {[windData.direction]: windData.speed, timestamp: Date.now()};\n  windVector.push(windObj);\n\n  // Null context to again collect a pair of speed \n  // and direction\n  windData = null;\n  context.set('windData', windData);\n\n  // Create arrays with data only for the above defined\n  // time ranges\n  LTdata = windVector.filter((arr) => arr.timestamp > LTlimit);\n  MTdata = LTdata.filter((arr) => arr.timestamp > MTlimit);\n  STdata = MTdata.filter((arr) => arr.timestamp > STlimit);\n  \n  // Make sure that the context does not grow infinitly \n  context.set('windVector', LTdata);\n  \n  // Get the sum of all wind speeds for the different\n  // cardinal points and for the above defined \n  // time ranges\n  LTresult[0] = LTdata.map((item) => (isNaN(item.N) ? 0 : item.N)).reduce((prev, next) => prev + next);\n  LTresult[1] = LTdata.map((item) => (isNaN(item.NE) ? 0 : item.NE)).reduce((prev, next) => prev + next);\n  LTresult[2] = LTdata.map((item) => (isNaN(item.E) ? 0 : item.E)).reduce((prev, next) => prev + next);\n  LTresult[3] = LTdata.map((item) => (isNaN(item.SE) ? 0 : item.SE)).reduce((prev, next) => prev + next);\n  LTresult[4] = LTdata.map((item) => (isNaN(item.S) ? 0 : item.S)).reduce((prev, next) => prev + next);\n  LTresult[5] = LTdata.map((item) => (isNaN(item.SW) ? 0 : item.SW)).reduce((prev, next) => prev + next);\n  LTresult[6] = LTdata.map((item) => (isNaN(item.W) ? 0 : item.W)).reduce((prev, next) => prev + next);\n  LTresult[7] = LTdata.map((item) => (isNaN(item.NW) ? 0 : item.NW)).reduce((prev, next) => prev + next);\n  LTmsg.payload = LTresult;\n  MTresult[0] = MTdata.map((item) => (isNaN(item.N) ? 0 : item.N)).reduce((prev, next) => prev + next);\n  MTresult[1] = MTdata.map((item) => (isNaN(item.NE) ? 0 : item.NE)).reduce((prev, next) => prev + next);\n  MTresult[2] = MTdata.map((item) => (isNaN(item.E) ? 0 : item.E)).reduce((prev, next) => prev + next);\n  MTresult[3] = MTdata.map((item) => (isNaN(item.SE) ? 0 : item.SE)).reduce((prev, next) => prev + next);\n  MTresult[4] = MTdata.map((item) => (isNaN(item.S) ? 0 : item.S)).reduce((prev, next) => prev + next);\n  MTresult[5] = MTdata.map((item) => (isNaN(item.SW) ? 0 : item.SW)).reduce((prev, next) => prev + next);\n  MTresult[6] = MTdata.map((item) => (isNaN(item.W) ? 0 : item.W)).reduce((prev, next) => prev + next);\n  MTresult[7] = MTdata.map((item) => (isNaN(item.NW) ? 0 : item.NW)).reduce((prev, next) => prev + next);\n  MTmsg.payload = MTresult;\n  STresult[0] = STdata.map((item) => (isNaN(item.N) ? 0 : item.N)).reduce((prev, next) => prev + next);\n  STresult[1] = STdata.map((item) => (isNaN(item.NE) ? 0 : item.NE)).reduce((prev, next) => prev + next);\n  STresult[2] = STdata.map((item) => (isNaN(item.E) ? 0 : item.E)).reduce((prev, next) => prev + next);\n  STresult[3] = STdata.map((item) => (isNaN(item.SE) ? 0 : item.SE)).reduce((prev, next) => prev + next);\n  STresult[4] = STdata.map((item) => (isNaN(item.S) ? 0 : item.S)).reduce((prev, next) => prev + next);\n  STresult[5] = STdata.map((item) => (isNaN(item.SW) ? 0 : item.SW)).reduce((prev, next) => prev + next);\n  STresult[6] = STdata.map((item) => (isNaN(item.W) ? 0 : item.W)).reduce((prev, next) => prev + next);\n  STresult[7] = STdata.map((item) => (isNaN(item.NW) ? 0 : item.NW)).reduce((prev, next) => prev + next);\n  STmsg.payload = STresult;\n\n  return [LTmsg, MTmsg, STmsg];\n}\n",
        "outputs": 3,
        "noerr": 0,
        "x": 780,
        "y": 760,
        "wires": [
            [
                "1c250c9.69932f3"
            ],
            [],
            []
        ],
        "outputLabels": [
            "LongTerm",
            "MidTerm",
            "ShortTerm"
        ]
    },
    {
        "id": "1c250c9.69932f3",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "a681ba6d.52c098",
        "name": "Wind Rose (LT)",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\n\n  /*\n   * Version 6 of the Windrose/Radar gauge\n   * Originally created by Mark Crossley\n   * Source: https://www.weather-watch.com/smf/index.php/topic,55071.0.html\n   *\n   */\n  \n  // Create some global variables to hold references to the buffers\n  var g_bufferRadar, g_bufferRadarFrame, g_bufferRadarBackground,\n      g_bufferRadarForeground, g_ctxRadarGauge;\n  var g_radarPlotSize;\n  \n  // Global variables that already exist in gauges-ss, if merging into gauge-ss, you\n  // DO NOT need to add these\n  var g_size = 245;\n  //var data = {};\n  var g_frameDesign = steelseries.FrameDesign.BLACK_METAL;\n  var g_background = steelseries.BackgroundColor.BRUSHED_METAL;\n  var g_foreground = steelseries.ForegroundType.TYPE1;\n  //var g_imgPathURL = 'images/';\n  var LANG = {};\n  LANG.compass = ['N', 'NE', 'E', 'SE', 'S', 'SW', 'W', 'NW'];\n  var wasinit = false;\n  \n  // Optional - background image - Already in gauges-ss\n  //var g_imgSmall = document.createElement(\"img\");                     //  background image\n  //g_imgSmall.setAttribute(\"src\", g_imgPathURL + \"logoLarge.png\");\n  \n  function init() {\n    // Calculate the size of the gauge background and so the size of radar plot required\n    g_radarPlotSize = g_size * 0.68;\n  \n    // Create a hidden div to host the Radar plot\n    var div = document.createElement('div');\n    div.style.display = 'none';\n    document.body.appendChild(div);\n  \n    // radar plot canvas buffer\n    g_bufferRadar = document.createElement('canvas');\n    g_bufferRadar.width = g_radarPlotSize;\n    g_bufferRadar.height = g_radarPlotSize;\n    g_bufferRadar.id = 'radarPlot';\n    div.appendChild(g_bufferRadar);\n  \n    // Create a steelseries gauge frame\n    g_bufferRadarFrame = document.createElement('canvas');\n    g_bufferRadarFrame.width = g_size;\n    g_bufferRadarFrame.height = g_size;\n    var ctxFrame = g_bufferRadarFrame.getContext('2d');\n    steelseries.drawFrame(ctxFrame, g_frameDesign, g_size/2, g_size/2, g_size, g_size);\n  \n    // Create a steelseries gauge background\n    g_bufferRadarBackground = document.createElement('canvas');\n    g_bufferRadarBackground.width = g_size;\n    g_bufferRadarBackground.height = g_size;\n    var ctxBackground = g_bufferRadarBackground.getContext('2d');\n    steelseries.drawBackground(ctxBackground, g_background, g_size/2, g_size/2, g_size, g_size);\n    // Optional - add a background image\n    //var drawSize = g_size * 0.831775;\n    //var x = (g_size - drawSize) / 2;\n    //ctxBackground.drawImage(g_imgSmall, x, x, drawSize, drawSize);\n  \n    // Add the compass points\n    drawCompassPoints(ctxBackground, g_size);\n  \n    // Create a steelseries gauge foreground\n    g_bufferRadarForeground = document.createElement('canvas');\n    g_bufferRadarForeground.width = g_size;\n    g_bufferRadarForeground.height = g_size;\n    var ctxForeground = g_bufferRadarForeground.getContext('2d');\n    steelseries.drawForeground(ctxForeground, g_foreground, g_size, g_size, false);\n  \n    // Get the context of the gauge canvas on the HTML page\n    g_ctxRadarGauge = document.getElementById('canvasRdGauge').getContext('2d');\n  \n    wasinit = true;\n  }\n  \n  // Just draw an empty gauge as a placeholder when the page loads\n  function drawWindRose() {\n    // Paint the gauge frame\n    g_ctxRadarGauge.drawImage(g_bufferRadarFrame, 0, 0);\n  \n    // Paint the gauge background\n    g_ctxRadarGauge.drawImage(g_bufferRadarBackground, 0, 0);\n  \n    // Paint the gauge foreground\n    g_ctxRadarGauge.drawImage(g_bufferRadarForeground, 0, 0);\n  }\n  \n  // Redraw the gauge with data\n  function doWindRose(windRoseData) {\n    //console.log(windRoseData);\n    // Clear the gauge\n    g_ctxRadarGauge.clearRect(0, 0, g_size, g_size);\n  \n    // Clear the existing radar plot\n    g_bufferRadar.width = g_bufferRadar.height = g_radarPlotSize;\n  \n    // Create a new radar plot\n    var radar = new RGraph.Radar('radarPlot', windRoseData);\n    radar.Set('chart.strokestyle', 'black');\n    radar.Set('chart.colors.alpha', 0.4);\n    radar.Set('chart.colors', ['red']);\n  \n    radar.Set('chart.title', 'Wind  Rose');\n    radar.Set('chart.title.size', Math.ceil(0.045 * g_radarPlotSize));\n    radar.Set('chart.title.bold', false);\n    radar.Set('chart.gutter.top', 0.2 * g_radarPlotSize);\n    radar.Set('chart.gutter.bottom', 0.2 * g_radarPlotSize);\n  \n    radar.Set('chart.tooltips.effect', 'snap');\n    radar.Set('chart.labels.axes', '');\n    radar.Set('chart.background.circles', true);\n    radar.Set('chart.radius', g_radarPlotSize/2);\n    radar.Draw();\n  \n    // Paint the gauge frame\n    g_ctxRadarGauge.drawImage(g_bufferRadarFrame, 0, 0);\n  \n    // Paint the gauge background\n    g_ctxRadarGauge.drawImage(g_bufferRadarBackground, 0, 0);\n  \n    // Paint the radar plot\n    var offset = (g_size - g_radarPlotSize) / 2;\n    g_ctxRadarGauge.drawImage(g_bufferRadar, offset, offset);\n  \n    // Paint the gauge foreground\n    g_ctxRadarGauge.drawImage(g_bufferRadarForeground, 0, 0);\n  \n  }\n  \n  // Helper function to put the compass points on the background\n  function drawCompassPoints(ctx, size) {\n    ctx.save();\n    // set the font\n    ctx.font = (0.06 * size) + 'px serif';\n    ctx.fillStyle = '#505050'; //'#000000';\n    ctx.textAlign = 'center';\n    ctx.textBaseline = 'middle';\n  \n    // Draw the compass points\n    for (var i=0; i<4; i++) {\n      ctx.translate(size/2, size*0.125);\n      ctx.fillText(LANG.compass[i*2], 0, 0, size);\n      ctx.translate(-size/2, -size*0.125);\n      // Move to center\n      ctx.translate(size/2, size/2);\n      ctx.rotate(Math.PI/2);\n      ctx.translate(-size/2, -size/2);\n    }\n    ctx.restore();\n  }\n  \n  angular.element(document).ready(function() {\n    init();\n    drawWindRose();\n  });\n  \n  (function(scope) {\n    scope.$watch('msg', function(msg) {\n      if (wasinit && msg != null && msg.payload != null) {\n        doWindRose(msg.payload);\n      }\n    });\n  }(scope));\n</script>\n\n<canvas id=\"canvasRdGauge\" width=\"245\" height=\"245\">No canvas in your browser...sorry...</canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 960,
        "y": 740,
        "wires": [
            []
        ]
    },
    {
        "id": "2a6c8b35.51fe3c",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "139638aa.aa1f7f",
        "name": "Barometer",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\n\nvar radial4;\nvar areas = [steelseries.Section(800, 850, 'rgba(220, 0, 0, 0.3)'),\n             steelseries.Section(1050, 1100, 'rgba(220, 0, 0, 0.3)')];\n\nradial4 = new steelseries.Radial('canvasRadial4', {\n            gaugeType: steelseries.GaugeType.TYPE4,\n            size: 269,\n            area: areas,\n            titleString: \"Luftdruck\",\n            unitString: \"mbar\",\n            thresholdRising: false,\n            minValue: 800,\n            maxValue: 1100,\n            userLedVisible: true,\n            useOdometer: false,\n            lcdVisible: true,\n            ledVisible: false,\n            trendVisible: true\n          });\n                    \nradial4.setFrameDesign(steelseries.FrameDesign.BLACK_METAL);\nradial4.setBackgroundColor(steelseries.BackgroundColor.BRUSHED_METAL);\nradial4.setForegroundType(steelseries.ForegroundType.TYPE1);\nradial4.setLabelNumberFormat(steelseries.LabelNumberFormat.STANDARD);\nradial4.setValueAnimated(0);\n\n(function(scope){ \n  scope.$watch('msg', function(msg) {\n    if (msg != null && msg.topic === 'weatherman/status/w_barometer') {\n      radial4.setValueAnimated(Math.round(msg.payload));\n    } else if (msg != null && msg.topic === 'weatherman/status/w_barotrend') {\n      if (msg.payload === 'steigend' || msg.payload === 'stark_steigend') {\n          radial4.setTrend(steelseries.TrendState.UP);\n      } else if (msg.payload === 'fallend' || msg.payload === 'stark_fallend') {\n          radial4.setTrend(steelseries.TrendState.DOWN);\n      } else if (msg.payload === 'stabil') {\n          radial4.setTrend(steelseries.TrendState.STEADY);\n      }\n    }\n  });\n})(scope);\n\n</script>\n\n<canvas id=\"canvasRadial4\" width=\"230\" height=\"252\"></canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 770,
        "y": 680,
        "wires": [
            []
        ]
    },
    {
        "id": "9dcb0ea8.a8b6c",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "cda55cb9.d73fc",
        "name": "Windrichtung",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\nvar radialWind;\nradialWind = new steelseries.Compass('canvasRadialWind', {\n        size: 269\n    });\n                    \nradialWind.setFrameDesign(steelseries.FrameDesign.BLACK_METAL);\nradialWind.setBackgroundColor(steelseries.BackgroundColor.BRUSHED_METAL);\nradialWind.setForegroundType(steelseries.ForegroundType.TYPE1);\nradialWind.setPointerType(steelseries.PointerType.TYPE1);\nradialWind.setPointerColor(steelseries.ColorDef.BLUE);\nradialWind.setValueAnimated(0);\n\n(function(scope){ \n  scope.$watch('msg', function(msg) {\n    if (msg != null && msg.topic === 'weatherman/status/w_wind_dir') {\n      radialWind.setValueAnimated(msg.payload);\n    }\n  });\n})(scope);\n</script>\n\n<canvas id=\"canvasRadialWind\" width=\"230\" height=\"252\"></canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 770,
        "y": 900,
        "wires": [
            []
        ]
    },
    {
        "id": "11d0add3.735b3a",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "w_wind_dir",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "weatherman/status/w_wind_dir",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 570,
        "y": 900,
        "wires": [
            [
                "9dcb0ea8.a8b6c"
            ]
        ]
    },
    {
        "id": "201b4985.43682e",
        "type": "catch",
        "z": "b016607a.570c28",
        "name": "Parse JSON Error",
        "scope": [
            "ae8d6dda.86f638",
            "6ff5d495.9ca94c",
            "b1c34c3a.cf0f18"
        ],
        "x": 1470,
        "y": 1380,
        "wires": [
            [
                "9244ec75.3c531"
            ]
        ]
    },
    {
        "id": "5da1840e.018e54",
        "type": "rbe",
        "z": "b016607a.570c28",
        "name": "WatchDog",
        "func": "rbe",
        "gap": "",
        "start": "",
        "inout": "out",
        "property": "payload",
        "x": 968,
        "y": 1320,
        "wires": [
            [
                "fac0c00d.98edc",
                "9c4bcfb1.a51cc8"
            ]
        ]
    },
    {
        "id": "fac0c00d.98edc",
        "type": "trigger",
        "z": "b016607a.570c28",
        "op1": "true",
        "op2": "false",
        "op1type": "bool",
        "op2type": "bool",
        "duration": "2",
        "extend": true,
        "units": "min",
        "reset": "1",
        "bytopic": "all",
        "name": "TimeOut",
        "x": 1120,
        "y": 1280,
        "wires": [
            [
                "5b96201c.34d428"
            ]
        ]
    },
    {
        "id": "9244ec75.3c531",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "5eeec4a7.36c72c",
        "name": "Weatherman Health",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\nvar TrafLight;\nTrafLight = new steelseries.TrafficLight('canvasTrafLight', {\n  width: 100,\n  height: 200\n});\n\n(function(scope){\n  scope.$watch('msg', function(msg) {\n    if (msg != null) {\n      if (msg.payload === 1) {\n        TrafLight.setGreenOn(true);\n      } else if (msg.payload === 0) {\n        TrafLight.setGreenOn(false);\n      } else if (msg.payload === 'con_ok') {\n        TrafLight.setRedOn(false);\n        scope.conStatus = 'Connected';\n      } else if (msg.payload === 'con_timeout') {\n        TrafLight.setRedOn(true);\n        scope.conStatus = 'Connection Timeout';\n      } else if (msg.error !== undefined) {\n        TrafLight.setYellowOn(true);\n        scope.jsonStatus = 'JSON Error';\n      }\n    }\n  });\n}(scope));\nscope.conStatus = 'Connected';\nscope.jsonStatus = 'JSON OK';\n</script>\n\n\n<table width=\"100%\" border=\"0\">\n  <tbody>\n    <tr>\n      <td width=\"105\" rowspan=\"3\"><canvas id=\"canvasTrafLight\" width=\"100\" height=\"200\"></canvas></td>\n      <td>{{conStatus}}</td>\n    </tr>\n    <tr>\n      <td>{{jsonStatus}}</td>\n    </tr>\n    <tr>\n      <td>Heart Beat {{msg.timestamp}}</td>\n    </tr>\n  </tbody>\n</table>",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 1740,
        "y": 1320,
        "wires": [
            []
        ]
    },
    {
        "id": "9c4bcfb1.a51cc8",
        "type": "trigger",
        "z": "b016607a.570c28",
        "op1": "0",
        "op2": "1",
        "op1type": "num",
        "op2type": "num",
        "duration": "250",
        "extend": false,
        "units": "ms",
        "reset": "",
        "bytopic": "all",
        "name": "Heart Beat",
        "x": 1490,
        "y": 1340,
        "wires": [
            [
                "9244ec75.3c531"
            ]
        ]
    },
    {
        "id": "4e2a98bb.c6a328",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "con_ok",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1480,
        "y": 1260,
        "wires": [
            [
                "9244ec75.3c531"
            ]
        ]
    },
    {
        "id": "5b96201c.34d428",
        "type": "switch",
        "z": "b016607a.570c28",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            },
            {
                "t": "false"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 1290,
        "y": 1280,
        "wires": [
            [
                "4e2a98bb.c6a328"
            ],
            [
                "2b603ee6.2329da"
            ]
        ]
    },
    {
        "id": "2b603ee6.2329da",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "con_timeout",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1480,
        "y": 1300,
        "wires": [
            [
                "9244ec75.3c531"
            ]
        ]
    },
    {
        "id": "37146cb.6e74914",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Einfaches Monitoring und Fehlerbehandlung",
        "info": "",
        "x": 1070,
        "y": 1240,
        "wires": []
    },
    {
        "id": "b285ab0f.38d18",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "firmware",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & 'firmware'",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.firmware",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 760,
        "y": 1360,
        "wires": [
            []
        ]
    },
    {
        "id": "cda55cb9.d73fc",
        "type": "ui_group",
        "z": "",
        "name": "Windrichtung",
        "tab": "3d6565b6.5d2912",
        "order": 3,
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "8132d18c.052bd",
        "type": "ui_group",
        "z": "",
        "name": "Taster",
        "tab": "fe205248.f04ed",
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "a681ba6d.52c098",
        "type": "ui_group",
        "z": "",
        "name": "WindRose",
        "tab": "3d6565b6.5d2912",
        "order": 4,
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "139638aa.aa1f7f",
        "type": "ui_group",
        "z": "",
        "name": "Barometer",
        "tab": "3d6565b6.5d2912",
        "order": 2,
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "5eeec4a7.36c72c",
        "type": "ui_group",
        "z": "",
        "name": "Weatherman Health",
        "tab": "3d6565b6.5d2912",
        "order": 1,
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "3d6565b6.5d2912",
        "type": "ui_tab",
        "z": "",
        "name": "Weatherman",
        "icon": "dashboard"
    },
    {
        "id": "fe205248.f04ed",
        "type": "ui_tab",
        "z": "",
        "name": "Geräte",
        "icon": "dashboard",
        "order": 1
    }
]