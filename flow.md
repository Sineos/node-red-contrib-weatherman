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
        "x": 110,
        "y": 800,
        "wires": [
            [
                "b1c34c3a.cf0f18",
                "c258c89f.9bcea8"
            ]
        ]
    },
    {
        "id": "b1c34c3a.cf0f18",
        "type": "function",
        "z": "b016607a.570c28",
        "name": "Parse JSON",
        "func": "// customize baspath for the topic\nvar basePath = '/weatherman/status/';\n\nvar myJSON = msg.payload;\n\n// Regex to match the unicode characters for EOT\nvar grepEOT = /(.*)(\\u0003|\\u0004)/;\nvar match = grepEOT.exec(myJSON);\n\n// String contains EOT, should be complete\nif (match !== null) {\n  // Try to parse JSON and validate object\n  msg.payload = JSON.parse(match[1]);\n  if (msg.payload && typeof msg.payload === 'object') {\n    myTime = new Date();\n    myTime = myTime.toLocaleString();\n    msg.timestamp = myTime;\n    msg.topic = basePath;\n    return msg;\n  } else {\n    node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n  }\n// No EOT found or parse error. Possibly trunctuated / corrupt message\n} else {\n  node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n}\n",
        "outputs": 1,
        "noerr": 0,
        "x": 290,
        "y": 800,
        "wires": [
            [
                "ca6507f0.a7691",
                "eaac634f.459d48",
                "8d99e4ad.a11e28",
                "5ac140db.dc0e2",
                "5151e269.9a1b34",
                "2b0c84d0.8fdb7c",
                "90784725.19d498",
                "70210607.d37878",
                "e1c6dc33.047fe",
                "ee5a2745.49bda",
                "aea3e3f4.b1d32",
                "b99c6b72.dcc7d",
                "ee8e368e.1e51",
                "c786b197.4b4988",
                "4b0c4832.fdea68",
                "25a9b07a.d59438",
                "bd26fa7c.ff4398",
                "fb1a74c9.6900f",
                "1d8fdcd.75ece23",
                "9e77bf25.7b11b",
                "b4f0dc80.1f6158",
                "adbd98bc.cbf4b",
                "c1e7464a.c7381",
                "121cfaa0.d36c0d",
                "36773959.d9ea3e",
                "9ffa6bc2.5d2ed",
                "7a71ad5e.2f2edc",
                "f512d611.2601a8",
                "4dfe5f77.3c7d2",
                "c06f0220.dcbd",
                "a3c943b1.6d31d8",
                "d1a538f9.81dde",
                "180e2619.de0ee2",
                "cb9d9021.2c44f",
                "e9a1d0fd.80ef58",
                "a68a6e.40eab59"
            ]
        ]
    },
    {
        "id": "ca6507f0.a7691",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "weatherman_ip",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[0].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.0.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.0.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "eaac634f.459d48",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "aussentemperatur",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[1].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.1.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.1.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 550,
        "y": 240,
        "wires": [
            [
                "76b596e8.b0a7a8"
            ]
        ]
    },
    {
        "id": "8d99e4ad.a11e28",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "gefuehlte_temperatur",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[2].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.2.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.2.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 280,
        "wires": [
            []
        ]
    },
    {
        "id": "646f59b7.9b7b6",
        "type": "catch",
        "z": "b016607a.570c28",
        "name": "Parse JSON Error",
        "scope": [
            "ae8d6dda.86f638",
            "6ff5d495.9ca94c",
            "b1c34c3a.cf0f18"
        ],
        "x": 1256,
        "y": 1640,
        "wires": [
            [
                "e93bf32f.ba111"
            ]
        ]
    },
    {
        "id": "90784725.19d498",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "taupunkt_temperatur",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[3].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.3.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.3.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "70210607.d37878",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "temp_m_gestern",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[4].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.4.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.4.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 550,
        "y": 360,
        "wires": [
            []
        ]
    },
    {
        "id": "e1c6dc33.047fe",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "himmel_temperatur",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[5].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.5.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.5.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 550,
        "y": 400,
        "wires": [
            []
        ]
    },
    {
        "id": "ee5a2745.49bda",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "rel_feuchte",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[6].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.6.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.6.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 440,
        "wires": [
            []
        ]
    },
    {
        "id": "aea3e3f4.b1d32",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "abs_feuchte",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[7].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.7.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.7.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 480,
        "wires": [
            []
        ]
    },
    {
        "id": "5ac140db.dc0e2",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "regenmelderwert",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[8].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.8.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.8.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 550,
        "y": 520,
        "wires": [
            []
        ]
    },
    {
        "id": "b99c6b72.dcc7d",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "regenstatus",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[9].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.9.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.9.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 560,
        "wires": [
            []
        ]
    },
    {
        "id": "ee8e368e.1e51",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "regenstaerke",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[10].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.10.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.10.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 600,
        "wires": [
            []
        ]
    },
    {
        "id": "c786b197.4b4988",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "regen_pro_h",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[11].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.11.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.11.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 640,
        "wires": [
            []
        ]
    },
    {
        "id": "4b0c4832.fdea68",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "regen_pro_24h",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[12].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.12.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.12.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 680,
        "wires": [
            []
        ]
    },
    {
        "id": "25a9b07a.d59438",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "regen_gestern",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[13].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.13.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.13.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 720,
        "wires": [
            []
        ]
    },
    {
        "id": "e9a1d0fd.80ef58",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "nn_luftdruck",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[14].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.14.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.14.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 760,
        "wires": [
            [
                "728267a4.260f18"
            ]
        ]
    },
    {
        "id": "5151e269.9a1b34",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "luftdrucktrend",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[15].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.15.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.15.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 800,
        "wires": [
            [
                "728267a4.260f18"
            ]
        ]
    },
    {
        "id": "bd26fa7c.ff4398",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "avg_windgeschwindigkeit",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[16].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.16.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.16.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 570,
        "y": 840,
        "wires": [
            [
                "e86ffdf9.f674d8"
            ]
        ]
    },
    {
        "id": "fb1a74c9.6900f",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "peak_windgeschwindigkeit",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[17].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.17.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.17.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 580,
        "y": 960,
        "wires": [
            []
        ]
    },
    {
        "id": "1d8fdcd.75ece23",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "bft_windgeschwindigkeit",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[18].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.18.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.18.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 570,
        "y": 920,
        "wires": [
            []
        ]
    },
    {
        "id": "9e77bf25.7b11b",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "windrichtung",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[19].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.19.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.19.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 880,
        "wires": [
            [
                "e86ffdf9.f674d8"
            ]
        ]
    },
    {
        "id": "b4f0dc80.1f6158",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "windwinkel",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[20].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.20.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.20.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 1000,
        "wires": [
            [
                "c84048af.3ee108"
            ]
        ]
    },
    {
        "id": "adbd98bc.cbf4b",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "helligkeit",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[21].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.21.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.21.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 520,
        "y": 1040,
        "wires": [
            []
        ]
    },
    {
        "id": "c1e7464a.c7381",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonnentemperatur",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[22].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.22.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.22.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 550,
        "y": 1080,
        "wires": [
            []
        ]
    },
    {
        "id": "121cfaa0.d36c0d",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonnen_difftemperatur",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[23].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.23.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.23.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 1120,
        "wires": [
            []
        ]
    },
    {
        "id": "2b0c84d0.8fdb7c",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonne_scheint",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[24].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.24.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.24.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 1160,
        "wires": [
            []
        ]
    },
    {
        "id": "36773959.d9ea3e",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonne_heute",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[25].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.25.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.25.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 1200,
        "wires": [
            []
        ]
    },
    {
        "id": "9ffa6bc2.5d2ed",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonne_gestern",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[26].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.26.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.26.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 1240,
        "wires": [
            []
        ]
    },
    {
        "id": "7a71ad5e.2f2edc",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonne_elevation",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[27].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.27.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.27.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 1280,
        "wires": [
            []
        ]
    },
    {
        "id": "f512d611.2601a8",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sonne_azimut",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "$$.topic & $$.payload.vars[28].homematic_name",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "unit",
                "pt": "msg",
                "to": "payload.vars.28.unit",
                "tot": "msg"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.vars.28.value",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 1320,
        "wires": [
            []
        ]
    },
    {
        "id": "4dfe5f77.3c7d2",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "MAC-Adresse",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "MAC-Adresse",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.MAC-Adresse",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 1420,
        "wires": [
            []
        ]
    },
    {
        "id": "c06f0220.dcbd",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "Homematic_CCU_ip",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Homematic_CCU_ip",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.Homematic_CCU_ip",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 1460,
        "wires": [
            []
        ]
    },
    {
        "id": "a3c943b1.6d31d8",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "WLAN_ssid",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "WLAN_ssid",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.WLAN_ssid",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 1500,
        "wires": [
            []
        ]
    },
    {
        "id": "d1a538f9.81dde",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "WLAN_Signal_dBm",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "WLAN_Signal_dBm",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.WLAN_Signal_dBm",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 560,
        "y": 1540,
        "wires": [
            []
        ]
    },
    {
        "id": "180e2619.de0ee2",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "sec_seit_reset",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "sec_seit_reset",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.sec_seit_reset",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 540,
        "y": 1580,
        "wires": [
            [
                "30adad7a.1c38aa"
            ]
        ]
    },
    {
        "id": "cb9d9021.2c44f",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "firmware",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "firmware",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.firmware",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 520,
        "y": 1660,
        "wires": [
            []
        ]
    },
    {
        "id": "30adad7a.1c38aa",
        "type": "rbe",
        "z": "b016607a.570c28",
        "name": "WatchDog",
        "func": "rbe",
        "gap": "",
        "start": "",
        "inout": "out",
        "property": "payload",
        "x": 754,
        "y": 1580,
        "wires": [
            [
                "9e5b35e2.43549",
                "aeceb6b5.141948"
            ]
        ]
    },
    {
        "id": "9e5b35e2.43549",
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
        "x": 906,
        "y": 1540,
        "wires": [
            [
                "8cb9463d.dc93a8"
            ]
        ]
    },
    {
        "id": "ae8d6dda.86f638",
        "type": "function",
        "z": "b016607a.570c28",
        "name": "Einzelne Messages",
        "func": "'use strict';\n// customize baspath for the topic\nvar basePath = '/weatherman/status/';\n// customize basepath for info properties\nvar basePathSys = '/weatherman/info/';\n\nvar myTopic = '',\n    myValue = '',\n    myUnit = '',\n    myType = '',\n    myTime = '',\n    myResults = [],\n    myJSON = msg.payload,\n    myObject = '';\n\n\n// Regex to match the unicode characters for EOT\nvar grepEOT = /(.*)(\\u0003|\\u0004)/;\nvar match = grepEOT.exec(myJSON);\n\n// String contains EOT, should be complete\nif (match !== null) {\n  // Try to parse JSON and validate object\n  myObject = JSON.parse(match[1]);\n  if (myObject && typeof myObject === 'object') {\n    // loop through vars array\n    var myObjectVars = myObject.vars;\n    var arrayLength = myObjectVars.length;\n    for (var i = 0; i < arrayLength; i += 1) {\n      myTopic = basePath + myObjectVars[i].homematic_name;\n      myUnit = myObjectVars[i].unit;\n      myType = myObjectVars[i].type;\n      myTime = new Date();\n      myTime = myTime.toLocaleString();\n      // ensure correct type\n      if (myType === 'number') {\n        myValue = Number(myObjectVars[i].value);\n      } else if (myType === 'string') {\n        myValue = String(myObjectVars[i].value);\n      } else if (myType === 'boolean') {\n        (myObjectVars[i].value === 'true' ? myValue = true : myValue = false);\n      }\n      // Push values as objects into an array\n      myResults.push({topic: myTopic, payload: myValue, unit: myUnit, timestamp: myTime});\n    }\n    // loop through Systeminfo\n    var myObjectSys = myObject.Systeminfo;\n    for (var k of Object.keys(myObjectSys)) {\n      myTopic = basePathSys + k;\n      myValue = myObjectSys[k];\n      myTime = Math.floor(Date.now() / 1000);\n      // Push values as objects into an array\n      myResults.push({topic: myTopic, payload: myValue, timestamp: myTime});\n    }\n    // return array with the results as messages\n    return [myResults];\n  // Possibly truncated / corrupt message\n  } else {\n    node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n  }\n// No EOT found or parse error. Possibly truncated / corrupt message\n} else {\n  node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n}\n",
        "outputs": 1,
        "noerr": 0,
        "x": 1430,
        "y": 320,
        "wires": [
            []
        ],
        "outputLabels": [
            "bla"
        ]
    },
    {
        "id": "6ff5d495.9ca94c",
        "type": "function",
        "z": "b016607a.570c28",
        "name": "Einzelne Outputs",
        "func": "'use strict';\n// customize baspath for the topic\nvar basePath = '/weatherman/status/';\n// customize basepath for info properties\nvar basePathSys = '/weatherman/info/';\n\nvar myTopic = '',\n    myValue = '',\n    myUnit = '',\n    myType = '',\n    myTime = '',\n    myResults = [],\n    myJSON = msg.payload,\n    myObject = '';\n\n\n// Regex to match the unicode characters for EOT\nvar grepEOT = /(.*)(\\u0003|\\u0004)/;\nvar match = grepEOT.exec(myJSON);\n\n// String contains EOT, should be complete\nif (match !== null) {\n  // Try to parse JSON and validate object\n  myObject = JSON.parse(match[1]);\n  if (myObject && typeof myObject === 'object') {\n    // loop through vars array\n    var myObjectVars = myObject.vars;\n    var arrayLength = myObjectVars.length;\n    for (var i = 0; i < arrayLength; i += 1) {\n      myTopic = basePath + myObjectVars[i].homematic_name;\n      myUnit = myObjectVars[i].unit;\n      myType = myObjectVars[i].type;\n      myTime = new Date();\n      myTime = myTime.toLocaleString();\n      // ensure correct type\n      if (myType === 'number') {\n        myValue = Number(myObjectVars[i].value);\n      } else if (myType === 'string') {\n        myValue = String(myObjectVars[i].value);\n      } else if (myType === 'boolean') {\n        (myObjectVars[i].value === 'true' ? myValue = true : myValue = false);\n      }\n      // Push values as objects into an array\n      myResults.push({topic: myTopic, payload: myValue, unit: myUnit, timestamp: myTime});\n    }\n    // loop through Systeminfo\n    var myObjectSys = myObject.Systeminfo;\n    for (var k of Object.keys(myObjectSys)) {\n      myTopic = basePathSys + k;\n      myValue = myObjectSys[k];\n      myTime = Math.floor(Date.now() / 1000);\n      // Push values as objects into an array\n      myResults.push({topic: myTopic, payload: myValue, timestamp: myTime});\n    }\n    // map results to the node's outputs\n    var singleOutputs = myResults.map(function (reformated) {\n        return reformated;\n    });\n    return singleOutputs;\n  // Possibly truncated / corrupt message\n  } else {\n    node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n  }\n// No EOT found or parse error. Possibly truncated / corrupt message\n} else {\n  node.error('Weatherman: Incomplete or corrupt JSON received', msg);\n}\n",
        "outputs": 36,
        "noerr": 0,
        "x": 1430,
        "y": 680,
        "wires": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            []
        ],
        "outputLabels": [
            "weatherman_ip",
            "aussentemperatur",
            "gefuehlte_temperatur",
            "gefuehlte_temperatur",
            "temp_m_gestern",
            "himmel_temperatur",
            "rel_feuchte",
            "abs_feuchte",
            "regenmelderwert",
            "regenstatus",
            "regenstaerke",
            "regen_pro_h",
            "regen_pro_24h",
            "regen_gestern",
            "nn_luftdruck",
            "luftdrucktrend",
            "avg_windgeschwindigkeit",
            "peak_windgeschwindigkeit",
            "bft_windgeschwindigkeit",
            "windrichtung",
            "windwinkel",
            "helligkeit",
            "sonnentemperatur",
            "sonnen_difftemperatur",
            "sonne_scheint",
            "sonne_heute",
            "sonne_gestern",
            "sonne_elevation",
            "sonne_azimut",
            "MAC-Adresse",
            "Homematic_CCU_ip",
            "WLAN_ssid",
            "WLAN_Signal_dBm",
            "sec_seit_reset",
            "zeitpunkt",
            "firmware"
        ]
    },
    {
        "id": "e93bf32f.ba111",
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
        "x": 1546,
        "y": 1600,
        "wires": [
            []
        ]
    },
    {
        "id": "aeceb6b5.141948",
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
        "x": 1276,
        "y": 1600,
        "wires": [
            [
                "e93bf32f.ba111"
            ]
        ]
    },
    {
        "id": "4bc25c55.8a0a54",
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
        "x": 1266,
        "y": 1520,
        "wires": [
            [
                "e93bf32f.ba111"
            ]
        ]
    },
    {
        "id": "8cb9463d.dc93a8",
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
        "x": 1076,
        "y": 1540,
        "wires": [
            [
                "4bc25c55.8a0a54"
            ],
            [
                "69a4848c.c1c5bc"
            ]
        ]
    },
    {
        "id": "69a4848c.c1c5bc",
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
        "x": 1266,
        "y": 1560,
        "wires": [
            [
                "e93bf32f.ba111"
            ]
        ]
    },
    {
        "id": "728267a4.260f18",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "139638aa.aa1f7f",
        "name": "Barometer",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\n\nvar radial4;\nvar areas = [steelseries.Section(800, 850, 'rgba(220, 0, 0, 0.3)'),\n             steelseries.Section(1050, 1100, 'rgba(220, 0, 0, 0.3)')];\n\nradial4 = new steelseries.Radial('canvasRadial4', {\n            gaugeType: steelseries.GaugeType.TYPE4,\n            size: 269,\n            area: areas,\n            titleString: \"Luftdruck\",\n            unitString: \"mbar\",\n            thresholdRising: false,\n            minValue: 800,\n            maxValue: 1100,\n            userLedVisible: true,\n            useOdometer: false,\n            lcdVisible: true,\n            ledVisible: false,\n            trendVisible: true\n          });\n                    \nradial4.setFrameDesign(steelseries.FrameDesign.BLACK_METAL);\nradial4.setBackgroundColor(steelseries.BackgroundColor.BRUSHED_METAL);\nradial4.setForegroundType(steelseries.ForegroundType.TYPE1);\nradial4.setLabelNumberFormat(steelseries.LabelNumberFormat.STANDARD);\nradial4.setValueAnimated(0);\n\n(function(scope){ \n  scope.$watch('msg', function(msg) {\n    if (msg != null && msg.topic === '/weatherman/status/w_barometer') {\n      radial4.setValueAnimated(Math.round(msg.payload));\n    } else if (msg != null && msg.topic === '/weatherman/status/w_barotrend') {\n      if (msg.payload === 'steigend' || msg.payload === 'stark_steigend') {\n          radial4.setTrend(steelseries.TrendState.UP);\n      } else if (msg.payload === 'fallend' || msg.payload === 'stark_fallend') {\n          radial4.setTrend(steelseries.TrendState.DOWN);\n      } else if (msg.payload === 'stabil') {\n          radial4.setTrend(steelseries.TrendState.STEADY);\n      }\n    }\n  });\n})(scope);\n\n</script>\n\n<canvas id=\"canvasRadial4\" width=\"230\" height=\"252\"></canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 770,
        "y": 780,
        "wires": [
            []
        ]
    },
    {
        "id": "c84048af.3ee108",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "cda55cb9.d73fc",
        "name": "Windrichtung",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\nvar radialWind;\nradialWind = new steelseries.Compass('canvasRadialWind', {\n        size: 269\n    });\n                    \nradialWind.setFrameDesign(steelseries.FrameDesign.BLACK_METAL);\nradialWind.setBackgroundColor(steelseries.BackgroundColor.BRUSHED_METAL);\nradialWind.setForegroundType(steelseries.ForegroundType.TYPE1);\nradialWind.setPointerType(steelseries.PointerType.TYPE1);\nradialWind.setPointerColor(steelseries.ColorDef.BLUE);\nradialWind.setValueAnimated(0);\n\n(function(scope){ \n  scope.$watch('msg', function(msg) {\n    if (msg != null && msg.topic === '/weatherman/status/w_wind_dir') {\n      radialWind.setValueAnimated(msg.payload);\n    }\n  });\n})(scope);\n</script>\n\n<canvas id=\"canvasRadialWind\" width=\"230\" height=\"252\"></canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 770,
        "y": 1000,
        "wires": [
            []
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
        "format": "<script>\nvar linearThermo;\nlinearThermo = new steelseries.Linear('canvasLinearThermo', {\n                 width: 140,\n                 height: 320,\n                 gaugeType: steelseries.GaugeType.TYPE2,\n                 titleString: \"Außentemperatur\",\n                 unitString: \"°C\",\n                 ledVisible: false,\n                 lcdVisible: true,\n                 thresholdRising: false,\n                 thresholdVisible: false,\n                 niceScale: true,\n                 minValue: -25,\n                 maxValue: 40\n               });\n                    \nlinearThermo.setFrameDesign(steelseries.FrameDesign.BLACK_METAL);\nlinearThermo.setBackgroundColor(steelseries.BackgroundColor.BRUSHED_METAL);\nlinearThermo.setValueAnimated(0);\nlinearThermo.setMaxValue(40);\n\n(function(scope){ \n  scope.$watch('msg', function(msg) {\n    if (msg != null && msg.topic === '/weatherman/status/w_temperature') {\n      linearThermo.setValueAnimated(msg.payload);\n      if (msg.payload > 0) {\n        linearThermo.setMaxValue(msg.payload + 2);\n        linearThermo.setMinValue(0);\n      } else if (msg.payload < 0) {\n        linearThermo.setMaxValue(0);\n        linearThermo.setMinValue(msg.payload - 2);\n      }\n    }\n  });\n})(scope);\n</script>\n\n<canvas id=\"canvasLinearThermo\" width=\"140\" height=\"320\"></canvas>\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 830,
        "y": 240,
        "wires": [
            []
        ]
    },
    {
        "id": "33a2bb8f.474a2c",
        "type": "link in",
        "z": "b016607a.570c28",
        "name": "",
        "links": [
            "c258c89f.9bcea8"
        ],
        "x": 1260,
        "y": 320,
        "wires": [
            [
                "ae8d6dda.86f638",
                "6ff5d495.9ca94c"
            ]
        ]
    },
    {
        "id": "c258c89f.9bcea8",
        "type": "link out",
        "z": "b016607a.570c28",
        "name": "",
        "links": [
            "33a2bb8f.474a2c"
        ],
        "x": 235,
        "y": 840,
        "wires": []
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
        "x": 560,
        "y": 160,
        "wires": []
    },
    {
        "id": "9892c742.9c59d",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Weatherman - System Info",
        "info": "",
        "x": 570,
        "y": 1380,
        "wires": []
    },
    {
        "id": "67ddec63.0b16c4",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Parse JSON - Möglichkeit 1",
        "info": "",
        "x": 230,
        "y": 720,
        "wires": []
    },
    {
        "id": "1614a0d7.2ac517",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Parse JSON - Möglichkeit 2",
        "info": "",
        "x": 1450,
        "y": 283,
        "wires": []
    },
    {
        "id": "8bc9450e.f6133",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Parse JSON - Möglichkeit 3",
        "info": "",
        "x": 1450,
        "y": 388,
        "wires": []
    },
    {
        "id": "a335dfa8.15e8f8",
        "type": "comment",
        "z": "b016607a.570c28",
        "name": "Einfaches Monitoring und Fehlerbehandlung",
        "info": "",
        "x": 1106,
        "y": 1460,
        "wires": []
    },
    {
        "id": "a68a6e.40eab59",
        "type": "change",
        "z": "b016607a.570c28",
        "name": "zeitpunkt",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "zeitpunkt",
                "tot": "str"
            },
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Systeminfo.zeitpunkt",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 520,
        "y": 1620,
        "wires": [
            []
        ]
    },
    {
        "id": "8500acf4.93092",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "a681ba6d.52c098",
        "name": "Wind Rose (LT)",
        "order": 0,
        "width": "5",
        "height": "5",
        "format": "<script>\n\n/*\n * Version 6 of the Windrose/Radar gauge\n * Originally created by Mark Crossley\n * Source: https://www.weather-watch.com/smf/index.php/topic,55071.0.html\n *\n */\n\n\n// Create some global variables to hold references to the buffers\nvar g_bufferRadar, g_bufferRadarFrame, g_bufferRadarBackground,\n    g_bufferRadarForeground, g_ctxRadarGauge;\nvar g_radarPlotSize;\n\n// Global variables that already exist in gauges-ss, if merging into gauge-ss, you\n// DO NOT need to add these\nvar g_size = 281;\nvar data = {};\nvar g_frameDesign = steelseries.FrameDesign.BLACK_METAL;\nvar g_background = steelseries.BackgroundColor.BRUSHED_METAL;\nvar g_foreground = steelseries.ForegroundType.TYPE1;\nvar LANG = {};\nLANG.compass = ['N', 'NE', 'E', 'SE', 'S', 'SW', 'W', 'NW'];\n\n// Optional - background image - Already in gauges-ss\n//var g_imgPathURL = 'images/';\n//var g_imgSmall = document.createElement(\"img\");                     //  background image\n//g_imgSmall.setAttribute(\"src\", g_imgPathURL + \"logoLarge.png\");\n\nfunction init() {\n\n  // Calcuate the size of the gauge background and so the size of radar plot required\n  g_radarPlotSize = g_size * 0.68;\n\n  // Create a hidden div to host the Radar plot\n  var div = document.createElement('div');\n  div.style.display = 'none';\n  document.body.appendChild(div);\n\n  // radar plot canvas buffer\n  g_bufferRadar = document.createElement('canvas');\n  g_bufferRadar.width = g_radarPlotSize;\n  g_bufferRadar.height = g_radarPlotSize;\n  g_bufferRadar.id = 'radarPlot';\n  div.appendChild(g_bufferRadar);\n\n  // Create a steelseries gauge frame\n  g_bufferRadarFrame = document.createElement('canvas');\n  g_bufferRadarFrame.width = g_size;\n  g_bufferRadarFrame.height = g_size;\n  var ctxFrame = g_bufferRadarFrame.getContext('2d');\n  steelseries.drawFrame(ctxFrame, g_frameDesign, g_size/2, g_size/2, g_size, g_size);\n\n  // Create a steelseries gauge background\n  g_bufferRadarBackground = document.createElement('canvas');\n  g_bufferRadarBackground.width = g_size;\n  g_bufferRadarBackground.height = g_size;\n  var ctxBackground = g_bufferRadarBackground.getContext('2d');\n  steelseries.drawBackground(ctxBackground, g_background, g_size/2, g_size/2, g_size, g_size);\n  // Optional - add a background image\n  //var drawSize = g_size * 0.831775;\n  //var x = (g_size - drawSize) / 2;\n  //ctxBackground.drawImage(g_imgSmall, x, x, drawSize, drawSize);\n\n  // Add the compass points\n  drawCompassPoints(ctxBackground, g_size);\n\n  // Create a steelseries gauge forground\n  g_bufferRadarForeground = document.createElement('canvas');\n  g_bufferRadarForeground.width = g_size;\n  g_bufferRadarForeground.height = g_size;\n  var ctxForegound = g_bufferRadarForeground.getContext('2d');\n  steelseries.drawForeground(ctxForegound, g_foreground, g_size, g_size, false);\n\n  // Get the context of the gauge canavs on the HTML page\n  g_ctxRadarGauge = document.getElementById('canvasGauge').getContext('2d');\n}\n\n\n// Just draw an empty gauge as a placeholder when the page loads\nfunction drawWindRose() {\n  // Paint the gauge frame\n  g_ctxRadarGauge.drawImage(g_bufferRadarFrame, 0, 0);\n\n  // Paint the gauge background\n  g_ctxRadarGauge.drawImage(g_bufferRadarBackground, 0, 0);\n\n  // Paint the gauge foreground\n  g_ctxRadarGauge.drawImage(g_bufferRadarForeground, 0, 0);\n}\n\n// Redraw the gauge with data\nfunction doWindRose() {\n  //console.log(data.WindRoseData);\n  // Clear the gauge\n  g_ctxRadarGauge.clearRect(0, 0, g_size, g_size);\n\n  // Clear the existing radar plot\n  g_bufferRadar.width = g_bufferRadar.height = g_radarPlotSize;\n\n  // Create a new radar plot\n  var radar = new RGraph.Radar('radarPlot', data.WindRoseData);\n  radar.Set('chart.strokestyle', 'black');\n  radar.Set('chart.colors.alpha', 0.4);\n  radar.Set('chart.colors', ['red']);\n\n  radar.Set('chart.title', 'Wind  Rose');\n  radar.Set('chart.title.size', Math.ceil(0.045 * g_radarPlotSize));\n  radar.Set('chart.title.bold', false);\n  radar.Set('chart.gutter.top', 0.2 * g_radarPlotSize);\n  radar.Set('chart.gutter.bottom', 0.2 * g_radarPlotSize);\n\n  radar.Set('chart.tooltips.effect', 'snap');\n  radar.Set('chart.labels.axes', '');\n  radar.Set('chart.background.circles', true);\n  radar.Set('chart.radius', g_radarPlotSize/2);\n  radar.Draw();\n\n  // Paint the gauge frame\n  g_ctxRadarGauge.drawImage(g_bufferRadarFrame, 0, 0);\n\n  // Paint the gauge background\n  g_ctxRadarGauge.drawImage(g_bufferRadarBackground, 0, 0);\n\n  // Paint the radar plot\n  var offset = (g_size - g_radarPlotSize) / 2;\n  g_ctxRadarGauge.drawImage(g_bufferRadar, offset, offset);\n\n  // Paint the gauge foreground\n  g_ctxRadarGauge.drawImage(g_bufferRadarForeground, 0, 0);\n\n}\n\n\n// Helper function to put the compass points on the background\nfunction drawCompassPoints(ctx, size) {\n  ctx.save();\n  // set the font\n  ctx.font = (0.06 * size) + 'px serif';\n  ctx.fillStyle = '#000000';\n  ctx.textAlign = 'center';\n  ctx.textBaseline = 'middle';\n\n  // Draw the compass points\n  for (var i=0; i<4; i++) {\n    ctx.translate(size/2, size*0.125);\n    ctx.fillText(LANG.compass[i*2], 0, 0, size);\n    ctx.translate(-size/2, -size*0.125);\n    // Move to center\n    ctx.translate(size/2, size/2);\n    ctx.rotate(Math.PI/2);\n    ctx.translate(-size/2, -size/2);\n  }\n  ctx.restore();\n}\n\n(function(scope) {\n    scope.$watch('msg', function(msg) {\n    if (msg != null) {\n      init();\n      drawWindRose();\n      data.WindRoseData = msg.payload;\n      doWindRose();\n    }\n  });\n}(scope));\n\nangular.element(document).ready(function () {\n  init();\n  drawWindRose();\n});\n\n</script>\n\n<canvas id=\"canvasGauge\" width=\"301\" height=\"301\">No canvas in your browser...sorry...</canvas>\n\n\n\n\n\n",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "local",
        "x": 980,
        "y": 847,
        "wires": [
            []
        ]
    },
    {
        "id": "e86ffdf9.f674d8",
        "type": "function",
        "z": "b016607a.570c28",
        "name": "WindData",
        "func": "'use strict';\n// Long term wind data in h\nconst limitLongTerm = 8;\n// Mid term wind data in h\nconst limitMediumTerm = 3;\n// Short term wind data in h\nconst limitShortTerm = 1;\n\n// Define context to store data across multiple messages\nvar windData = context.get('windData') || {};\nvar windVector = context.get('windVector') || [];\n\n// Collect both wind speed and wind direction\n// and store them in the context\nswitch (msg.topic) {\n  case '/weatherman/status/w_wind_direction':\n    windData.direction = msg.payload;\n    context.set('windData', windData);\n    msg = null;\n    break;\n  case '/weatherman/status/w_wind_avg':\n    windData.speed = msg.payload;\n    context.set('windData', windData);\n    msg = null;\n    break;\n  default:\n    msg = null;\n    break;\n}\n\n// Start processing the data once both speed and \n// direction are available\nif (windData.direction != null && windData.speed != null) {\n  var LTdata = [],\n      MTdata = [],\n      STdata = [],\n      LTresult = [],\n      MTresult = [],\n      STresult = [],\n      windObj = {},\n      LTmsg = {},\n      MTmsg = {},\n      STmsg = {};\n  const LTlimit = Date.now() - (limitLongTerm * 3600 * 1000);\n  const MTlimit = Date.now() - (limitMediumTerm * 3600 * 1000);\n  const STlimit = Date.now() - (limitShortTerm * 3600 * 1000);\n\n  // Generate an object out of speed, direction and \n  // current time and store as array\n  windObj = {[windData.direction]: windData.speed, timestamp: Date.now()};\n  windVector.push(windObj);\n\n  // Null context to again collect a pair of speed \n  // and direction\n  windData = null;\n  context.set('windData', windData);\n\n  // Create arrays with data only for the above defined\n  // time ranges\n  LTdata = windVector.filter((arr) => arr.timestamp > LTlimit);\n  MTdata = LTdata.filter((arr) => arr.timestamp > MTlimit);\n  STdata = MTdata.filter((arr) => arr.timestamp > STlimit);\n  \n  // Make sure that the context does not grow infinitly \n  context.set('windVector', LTdata);\n  \n  // Get the sum of all wind speeds for the different\n  // cardinal points and for the above defined \n  // time ranges\n  LTresult[0] = LTdata.map((item) => (isNaN(item.N) ? 0 : item.N)).reduce((prev, next) => prev + next);\n  LTresult[1] = LTdata.map((item) => (isNaN(item.NE) ? 0 : item.NE)).reduce((prev, next) => prev + next);\n  LTresult[2] = LTdata.map((item) => (isNaN(item.E) ? 0 : item.E)).reduce((prev, next) => prev + next);\n  LTresult[3] = LTdata.map((item) => (isNaN(item.SE) ? 0 : item.SE)).reduce((prev, next) => prev + next);\n  LTresult[4] = LTdata.map((item) => (isNaN(item.S) ? 0 : item.S)).reduce((prev, next) => prev + next);\n  LTresult[5] = LTdata.map((item) => (isNaN(item.SW) ? 0 : item.SW)).reduce((prev, next) => prev + next);\n  LTresult[6] = LTdata.map((item) => (isNaN(item.W) ? 0 : item.W)).reduce((prev, next) => prev + next);\n  LTresult[7] = LTdata.map((item) => (isNaN(item.NW) ? 0 : item.NW)).reduce((prev, next) => prev + next);\n  LTmsg.payload = LTresult;\n  MTresult[0] = MTdata.map((item) => (isNaN(item.N) ? 0 : item.N)).reduce((prev, next) => prev + next);\n  MTresult[1] = MTdata.map((item) => (isNaN(item.NE) ? 0 : item.NE)).reduce((prev, next) => prev + next);\n  MTresult[2] = MTdata.map((item) => (isNaN(item.E) ? 0 : item.E)).reduce((prev, next) => prev + next);\n  MTresult[3] = MTdata.map((item) => (isNaN(item.SE) ? 0 : item.SE)).reduce((prev, next) => prev + next);\n  MTresult[4] = MTdata.map((item) => (isNaN(item.S) ? 0 : item.S)).reduce((prev, next) => prev + next);\n  MTresult[5] = MTdata.map((item) => (isNaN(item.SW) ? 0 : item.SW)).reduce((prev, next) => prev + next);\n  MTresult[6] = MTdata.map((item) => (isNaN(item.W) ? 0 : item.W)).reduce((prev, next) => prev + next);\n  MTresult[7] = MTdata.map((item) => (isNaN(item.NW) ? 0 : item.NW)).reduce((prev, next) => prev + next);\n  MTmsg.payload = MTresult;\n  STresult[0] = STdata.map((item) => (isNaN(item.N) ? 0 : item.N)).reduce((prev, next) => prev + next);\n  STresult[1] = STdata.map((item) => (isNaN(item.NE) ? 0 : item.NE)).reduce((prev, next) => prev + next);\n  STresult[2] = STdata.map((item) => (isNaN(item.E) ? 0 : item.E)).reduce((prev, next) => prev + next);\n  STresult[3] = STdata.map((item) => (isNaN(item.SE) ? 0 : item.SE)).reduce((prev, next) => prev + next);\n  STresult[4] = STdata.map((item) => (isNaN(item.S) ? 0 : item.S)).reduce((prev, next) => prev + next);\n  STresult[5] = STdata.map((item) => (isNaN(item.SW) ? 0 : item.SW)).reduce((prev, next) => prev + next);\n  STresult[6] = STdata.map((item) => (isNaN(item.W) ? 0 : item.W)).reduce((prev, next) => prev + next);\n  STresult[7] = STdata.map((item) => (isNaN(item.NW) ? 0 : item.NW)).reduce((prev, next) => prev + next);\n  STmsg.payload = STresult;\n\n  return [LTmsg, MTmsg, STmsg];\n}\n",
        "outputs": 3,
        "noerr": 0,
        "x": 780,
        "y": 860,
        "wires": [
            [
                "8500acf4.93092",
                "d336ca05.e19a1"
            ],
            [],
            [
                "f92fff0f.ef8a68"
            ]
        ],
        "outputLabels": [
            "LongTerm",
            "MidTerm",
            "ShortTerm"
        ]
    },
    {
        "id": "4b9f5645.09b9",
        "type": "ui_template",
        "z": "b016607a.570c28",
        "group": "8132d18c.052bd",
        "name": "Load required scripts",
        "order": 0,
        "width": 0,
        "height": 0,
        "format": "<script type=\"text/javascript\" src=\"/addons/red/static/scripts/steelseries-min.js\"></script>\n<script type=\"text/javascript\" src=\"/addons/red/static/scripts/tween-min.js\"></script>\n<script type=\"text/javascript\" src=\"/addons/red/static/scripts/RGraph.common.core.js\"></script>\n<script type=\"text/javascript\" src=\"/addons/red/static/scripts/RGraph.radar.js\"></script>",
        "storeOutMessages": true,
        "fwdInMessages": true,
        "templateScope": "global",
        "x": 820,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "d336ca05.e19a1",
        "type": "debug",
        "z": "b016607a.570c28",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "x": 960,
        "y": 800,
        "wires": []
    },
    {
        "id": "f92fff0f.ef8a68",
        "type": "debug",
        "z": "b016607a.570c28",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "x": 980,
        "y": 920,
        "wires": []
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