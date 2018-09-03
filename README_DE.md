
# node-red-contrib-weatherman

Dieser Flow erlaubt es die JSON Daten des [Weatherman's](https://www.stall.biz/project/weatherman-die-perfekte-wetterstation-fuer-die-hausautomation) zu verarbeiten und in einem Dashboard zu visualisieren. Dieser Flow geht von der Verwendung auf einer CCU3 / [RaspberryMatic](https://github.com/jens-maus/RaspberryMatic#raspberrymatic) und [RedMatic](https://github.com/hobbyquaker/RedMatic) aus. Beispielhaft wird dargestellt:

 - 3 Möglichkeiten JSON auszulesen
 - Einfache Möglichkeit den Weatherman und die Verbindung zu überwachen
 - Eine einfache UI unter Verwendung von [RGraph](https://www.rgraph.net/) und [Steelseries Gauges](https://github.com/HanSolo/SteelSeries-Canvas)
  -- [Demo Radial](http://www.wilmslowastro.com/steelseries/demoRadial.html) 
  -- [Demo Linear](http://www.wilmslowastro.com/steelseries/demoLinear.html)
  -- [Demo Extra](http://www.wilmslowastro.com/steelseries/demoExtras.html)
  -- [Demo Traffic Light](http://www.wilmslowastro.com/steelseries/demoTrafficLight.html)
  -- [Demo Radar](http://www.wilmslowastro.com/steelseries/radar/radar.html)
  -- [Demo Bulb](http://www.wilmslowastro.com/steelseries/demoLightBulb.html)

## Flow

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/flow.png" width="400"/>

### Import Flow
Den Inhalt von [raw flow.md](https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/flow.md) kopieren und in Node-Red importieren.

## Dashboard

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/dash.png" width="400"/>

## Vorbereitungen
### Weatherman
Der Weatherman sendet seine Daten standardmäßig zum lighthttp proxy der CCU auf Port 8181. Da hier die Integration über RedMatic erfolgen soll, muss der Port in Waetherman geändert werden, da der Port nicht doppelt von der CCU und RedMatic verwendet werden kann.
Die Änderung des Ports erfolgt über: `http://<ip weatherman>/param:12:<port>`
Der Flow hier verwendet z.B. den Port 8186
Für diese Anwendung ist eine minimale Firmware-Version v65 Voraussetzung

### RedMatic
In RedMatic bzw. Node Red muss ein `httpStatic` Verzeichnis eingerichtet werden, um die zusätzlichen Skript-Bibliotheken von RGraph und SteelSeries verwenden zu können.
#### SSH Zugang aktivieren
Um die entsprechenden Einstellungen vornehmen zu können muss der SSH Zugang aktiviert werden. Eine kurze Anleitung findet sich z.B. [hier](https://homematic.simdorn.net/ssh-zugang-auf-ccu2-einrichten/).
#### httpStatic Verzeichnis und Skript Dateien 
Mit dem SSH Zugang wird ein neues Verzeichnis für die entsprechenden Skripte angelegt. Der Flow hier verwendet:

    /usr/local/sdcard/www/addons/red/static/scripts
In dieses Verzeichnis müssen folgende Dateien hochgeladen werden:

- [steelseries-min.js](https://github.com/HanSolo/SteelSeries-Canvas/blob/master/steelseries-min.js)
- [tween-min.js](https://github.com/HanSolo/SteelSeries-Canvas/blob/master/tween-min.js)
- `RGraph.common.core.js` und `RGraph.radar.js` aus dem [minified RGraph zip](https://www.rgraph.net/download.html)

#### httpStatic aktivieren
Um das, im vorherigen Schritt angelegte, Verzeichnis in Node-Red bekannt zu machen, muss die Datei `settings.json` editiert werden. In RedMatic liegt diese unter:

    /usr/local/addons/redmatic/etc/settings.json
Diese Datei muss um den Eintrag `"httpStatic": "/usr/local/sdcard/www"` ergänzt werden und sieht dann beispielhaft so aus:
```
{
	"uiPort": 1880,
	"uiHost": "127.0.0.1",
	"flowFile": "flows.json",
	"userDir": "/usr/local/addons/redmatic/var",
	"httpRoot": "/addons/red",
	"httpStatic": "/usr/local/sdcard/www",
	"logging": {
		"ain": {
			"level": "info",
			"metrics": false,
			"audit": false
		}
	}
} 
```

Im Anschluss zu diesen Einstellungen muss RedMatic neu gestartet werden.

## Konfiguration der einzelnen Nodes
In der weiteren Erklärung wird auf einzelne Nodes näher eingegangen.
### 

