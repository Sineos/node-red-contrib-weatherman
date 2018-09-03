
# node-red-contrib-weatherman

Dieser Flow erlaubt es die JSON Daten des [Weatherman's](https://www.stall.biz/project/weatherman-die-perfekte-wetterstation-fuer-die-hausautomation) zu verarbeiten und in einem Dashboard zu visualisieren. Dieser Flow geht von der Verwendung auf einer CCU3 / [RaspberryMatic](https://github.com/jens-maus/RaspberryMatic#raspberrymatic) und [RedMatic](https://github.com/hobbyquaker/RedMatic) aus. Beispielhaft wird dargestellt:

 - 3 Möglichkeiten JSON auszulesen
 - Einfache Möglichkeit den Weatherman und die Verbindung zu überwachen
 - Eine einfache UI unter Verwendung von [RGraph](https://www.rgraph.net/) und [Steelseries Gauges](https://github.com/HanSolo/SteelSeries-Canvas)
   - [Demo Radial](http://www.wilmslowastro.com/steelseries/demoRadial.html) 
   - [Demo Linear](http://www.wilmslowastro.com/steelseries/demoLinear.html)
   - [Demo Extra](http://www.wilmslowastro.com/steelseries/demoExtras.html)
   - [Demo Traffic Light](http://www.wilmslowastro.com/steelseries/demoTrafficLight.html)
   - [Demo Radar](http://www.wilmslowastro.com/steelseries/radar/radar.html)
   - [Demo Bulb](http://www.wilmslowastro.com/steelseries/demoLightBulb.html)

Die hier dargestellte Visualisierung soll nur als Anregung dienen und die obigen Beispiele zeigen, welche Möglichkeiten die Steelseries Gauges noch bieten.

Sollte hier jemand schicke Ergänzungen vornehmen, würde ich mich über ein Pull-Request oder ein Teilen des Flows freuen.

## Flow

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/flow.png" width="400"/>

### Import Flow

Den Inhalt von [raw flow.md](https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/flow.md) kopieren und in Node-Red importieren.

## Dashboard

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/dash.png" width="400"/>

## Vorbereitungen

### Weatherman

Der Weatherman sendet seine Daten standardmäßig zum lighthttp proxy der CCU auf Port 8181. Da hier die Integration über RedMatic erfolgen soll, muss der Port in Weatherman geändert werden, da der Port nicht doppelt von der CCU und RedMatic verwendet werden kann.

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

Dieses Verzeichnis muss angelegt werden und folgende Dateien müssen hochgeladen werden:

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

### TCP in Node: Listen Weatherman

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/tcp_in.png" width="400"/>

#### Beschreibung

Verbindungspunkt zum Weatherman.

#### Funktion

 - Öffnet einen Server Port (hier 8186) zu dem sich der Weatherman periodisch verbindet
 - Das eingehende JSON Paket wird erstmal als einfache Zeichenkette weitergegeben 

### Function Node: Parse JSON

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/function_parse1.png" width="400"/>

#### Beschreibung

Beispielhaft stellt der Flow 3 verschiedene Möglichkeiten dar, wie das Weatherman JSON verarbeitet werden kann. Das JSON hat hier eine Besonderheit, da es mit einem `End Of Transmission (EOT)` Zeichen endet, welches nicht zu den Daten gehört, aber praktisch ist, da man einen kompletten Empfang damit validieren kann.

Die 3 Möglichkeiten sind:

 1. JSON auslesen und in folgenden Nodes auf die einzelnen Daten zugreifen
 2. JSON auslesen und über einen Output die Daten als einzelne Nachrichten verschicken
 3. JSON auslesen und über n Outputs die Daten zur Weiterverarbeitung zur Verfügung stellen

#### Funktion

 - Mit der Variable `BasePath` wird der erste Teil der späteren `msg.topic` gesetzt. Dieser BasePath wird jeweils um den Namen des entsprechenden Datenpunkts vom Weatherman ergänzt
 - Über `msg.timestamp` wird den Daten jeweils noch die aktuelle Zeit mitgegeben  
 - Mit `node.error` wird ein Fehler-Event erzeugt, falls es beim Parsen der Daten Probleme gibt, z.B. kein EOT gefunden oder bei einem fehlerhaften JSON Datensatz 
 - Bei Möglichkeit #2 und #3 wird der jeweiligen Nachricht noch über `msg.unit` die entsprechende Messgröße mitgegeben

### Change Node: weatherman_ip

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/change_ip.png" width="400"/>

#### Beschreibung

Die gesamte Reihe an Change Nodes zerlegt das geparste JSON Paket in seine Bestandteile und stellt damit die gemessenen Werte einzeln zur Verfügung.

Mit `payload.vars.0` wird der erste Werteblock im JSON angesprochen, mit `payload.vars.1` der zweite usw.

Sollte sich mit späteren Weatherman Firmware Versionen die Reihenfolge der Daten ändern, ist hier entsprechend anzupassen, da sonst die Temperatur ggf. beim Wind landet etc.

#### Funktion

 - Mit dem JSONata Ausdruck `$$.topic & $$.payload.vars[0].homematic_name` wird das vorhandene `msg.topic` (siehe `BasePath` aus Parse JSON) genommen und um den Wert des `homematic_name` Schlüssels ergänzt.
 - Der Wert aus `payload.vars.0.unit` wird `msg.unit` zugewiesen
 - Der Wert aus `payload.vars.0.value` wird `msg.payload` zugewiesen

### Template Node: Load required scripts

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/template_scripts.png" width="400"/>

#### Beschreibung

Stellt die, zur Visualisierung benötigten, Skript Bibliotheken global zur Verfügung.

#### Funktion

 - Lädt die Skripte welche unter [httpStatic Verzeichnis und Skript Dateien](#httpStatic-Verzeichnis-und-Skript-Dateien) auf die CCU kopiert wurden

### Template Node: Windrichtung

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/template_winddir.png" width="400"/>

#### Beschreibung

An Hand dieses Beispiels wird kurz auf die Visualisierung eingegangen.

#### Funktion

    (function(scope){ 
      scope.$watch('msg', function(msg) {
        if (msg.payload !== undefined && msg.topic === '/weatherman/status/w_wind_dir') {
          radialWind.setValueAnimated(msg.payload);
        }
      });
    })(scope);

 - Kern der Visualisierung ist jeweils diese function(scope): Diese wird benötigt um Nachrichten innerhalb eines Templates auch in einem Skript nutzen zu können, denn ansonsten stehen diese Informationen nur den (Angular) html Elementen zur Verfügung
 - Im Rest der Visualisierungs-Templates wird dann jeweils über die SteelSeries Funktionalitäten eine Anzeige generiert und diese dann mit den entsprechenden Daten gefüttert

### Function Node: WindData

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/function_winddata.png" width="400"/>

#### Beschreibung

Diese Funktion summiert, über angegebene Zeitintervalle, die aufgetretenen Windgeschwindigkeiten in den jeweiligen Windrichtungen, um damit im Folgenden ein Diagramm zu erzeugen aus dem sich die hauptsächliche Windrichtung(en) in diesem Intervall ablesen lassen.

#### Funktion

 - Mit den Konstanten `limitLongTerm`, `limitMediumTerm` und `limitShortTerm` können die Zeitintervalle (in Stunden) bestimmt werden in der der Wind beobachtet werden soll
 - Für diese Zeitintervalle sammelt das Skript die aufgetretenen Windgeschwindigkeiten in den 8 Himmelsrichtungen (N, NE, E, SE, S, SW, W, NW) und summiert diese
 - Die Ergebnisse werden in einem Array als `msg.payload` zur Verfügung gestellt, wobei die Node für jedes Zeitintervall einen eigenen Output nutzt

### Nodes: Einfaches Monitoring und Fehlerbehandlung

<img src="https://raw.githubusercontent.com/Sineos/node-red-contrib-weatherman/master/src_readme/error_handling.png" width="400"/>

#### Beschreibung

Dieser Block von Nodes überwacht den Weatherman und stellt im den Fehlerfall über eine Ampelvisualisierung dar.

#### Funktion

 - Die RBE Node hängt an dem `sec_seit_reset` Wert des Weatherman und lässt nur Werte durch, welche sich im Vergleich zum letzten gändert hat
 - Der Trigger `TimeOut` wird mit jedem Wert zurückgesetzt. Sollte für länger als 2 Minuten kein Wert ankommen läuft der Timer ab und der Trigger sendet ein `false`. Dieses wird dann als Verbindungsabbruch gewertet und schaltet die Ampel auf rot
 - Der Trigger `Heart Beat` lässt mit jeder Aktualisierung der Daten die grüne Ampel kurz blinken 
 - `Parse JSON Error` reagiert auf `node.error`, welcher ggf. von den JSON Parse Funktionen erzeugt wird und schaltet in diesem Fall die Ampel auf gelb
