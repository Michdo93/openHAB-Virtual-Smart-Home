# openHAB Virtual Smart Home

Ein komplettes Smart Home für openHAB 5 – ohne ein einziges echtes Gerät.
Rund 90 virtuelle Items in acht Räumen bilden Lampen, Steckdosen, Haushaltsgeräte,
Heizung, Energie, Garten und Roboter nach. Regeln lassen sie wie echte Geräte wirken:
Die Raumtemperatur schwankt und die Fußbodenheizung regelt mit Hysterese nach,
Rollläden fahren langsam und lassen sich anhalten, die PV-Anlage folgt dem Sonnenstand,
die Waschmaschine zählt ihre Restzeit herunter, der Mähroboter zieht seine Bahnen.

Das Repository ist als gemeinsame Grundlage für mehrere Bücher und Projekte gedacht –
zum Beispiel für die Bücher *Smart Home & ROS2* und *Smart-Home-Dashboards für
Webentwickler und Designer*. Jeder installiert es auf seinem eigenen openHAB.

## Alle Item-Typen

| Item-Typ | Beispiele |
|---|---|
| `Switch` | Deckenlampe, Steckdose, Backofen, Klimaanlage, Wallbox |
| `Dimmer` | Helligkeit, Farbtemperatur, Lautstärke, Lamellenwinkel |
| `Color` | Farblampe (HSB) |
| `Contact` | Fensterkontakt, Haustür, Kühlschranktür |
| `Number` ganzzahlig | Programmplatz des Fernsehers, Kochstufe, belegte Zonen |
| `Number` Fließkomma | Leistungszahl (COP) der Wärmepumpe |
| `Number:<Dimension>` | Temperature, Power, Energy, Dimensionless (%, ppm), Time, Length, Speed, Volume, Density, ElectricCurrent |
| `Rollershutter` | Rollladen, Jalousie, Garagentor |
| `Player` | Soundsystem |
| `String` | Sender, Betriebsarten, Status von Waschmaschine und Robotern |
| `DateTime` | Uhrzeit, Backofen fertig um, zuletzt geklingelt |
| `Location` | Position von E-Auto und Mähroboter |
| `Call` | Türruf der Klingel |
| `Image` | Karte des Saugroboters |
| `Group` | Räume und Geräte (semantisches Modell) sowie `Group:Switch:OR`, `Group:Contact:OR`, `Group:Number:Temperature:AVG`, `Group:Number:Power:SUM` |

## Räume und Geräte

| Raum | Geräte |
|---|---|
| Wohnzimmer | Farblampe, Deckenlampe, Steckdose mit Verbrauch, Rollladen, Jalousie mit Lamellen, Fenster mit Motor, Thermostat der Fußbodenheizung, Raumluftsensor, Soundsystem, Fernseher |
| Küche | Backofen, Kochfeld, Kühlschrank, Spülmaschine, Rauchmelder |
| Schlafzimmer | Luftreiniger, Klimaanlage, Luftbefeuchter, Temperatur |
| Bad | Waschmaschine, Trockner, Luftentfeuchter, Wassermelder |
| Diele | Präsenzmelder mit Zonen, Haustür mit Schloss, Klingel, Alarmanlage |
| Technikraum | Wärmepumpe, Smart Meter, Wasserzähler, PV-Anlage |
| Garage | Garagentor, Wallbox, E-Auto |
| Garten | Außentemperatur, Wetterstation (Wind, Regen), Mähroboter, Sprinkler |
| ganzes Haus | Uhrzeit, Saug- und Wischroboter mit Karte |

Jedes Gerät mit mehreren Items ist eine eigene Gruppe mit semantischen Tags
(Location → Equipment → Point/Property), sodass die Main UI das Modell automatisch anzeigt.
Alle Gruppen beginnen mit `gVH_`, damit sie nicht mit anderen Konfigurationen kollidieren.

## Was die Regeln simulieren

| Regel | Verhalten |
|---|---|
| Startwerte | setzt beim Start alle Items, die noch `NULL` sind |
| Uhrzeit | jede Minute |
| Dimmer | `INCREASE`/`DECREASE` in 10er-Schritten (das macht sonst das Gerät) |
| Rollläden | fahren 10 % pro Sekunde, `STOP` hält an; morgens um 7 Uhr hoch, abends um 20 Uhr runter |
| Jalousie | fährt bei starker Mittagssonne herunter und stellt die Lamellen schräg |
| Raumklima | alle 2 Minuten: Temperatur mit Heizungs-Hysterese, CO₂ steigt bei geschlossenem Fenster |
| Wetter und Energie | alle 5 Minuten: Außentemperatur und PV nach Sonnenstand, Smart Meter, Wasserzähler |
| Geräte | Waschmaschine, Spülmaschine und Trockner laufen ab (1 Minute = 10 Minuten) |
| Diele | Präsenz zufällig (etwa jede vierte Minute jemand da), Klingel setzt Zeitpunkt und Türruf, Alarmanlage löst bei „extern“ aus |
| Garage | Wallbox lädt das Auto, Reichweite folgt dem Ladestand |
| Roboter | Mähroboter fährt Bahnen (Position), beide Roboter entladen und laden ihre Akkus |

## Installation

Voraussetzung: openHAB 5 mit der MAP-Transformation (`transformation-map`).

```bash
git clone https://github.com/Michdo93/openHAB-Virtual-Smart-Home.git
cd openHAB-Virtual-Smart-Home
sudo cp items/*.items         /etc/openhab/items/
sudo cp rules/*.rules         /etc/openhab/rules/
sudo cp sitemaps/*.sitemap    /etc/openhab/sitemaps/
sudo cp transform/*.map       /etc/openhab/transform/
```

Bei einer manuellen Installation liegen die Ordner unter `openhab/conf/` statt
`/etc/openhab/`. Die Sitemap heißt `virtuelles_haus`
(`http://openhab:8080/basicui/app?sitemap=virtuelles_haus`).

## Lizenz

MIT – siehe [LICENSE](LICENSE).
