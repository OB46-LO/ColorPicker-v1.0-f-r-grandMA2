[README_1.md](https://github.com/user-attachments/files/35306000/README_1.md)
# ColorPicker v1.0 für grandMA2

Lua-Plugin für die **grandMA2** (Konsole und onPC). Es baut aus deinen vorhandenen **Groups** und **Color-Presets (4.x)** automatisch ein Color-Picker-Raster:

- pro Group und Farbe ein **Macro** mit `Group X At Preset 4.Y`
- Macros werden **benannt** (z. B. „Front Rot“) und **passend eingefärbt**. Die Farbe wird am Preset-Namen erkannt, deutsch oder englisch.
- alles wird in einem **Layout** als Raster angeordnet: Zeilen = Groups, Spalten = Farben, die Group selbst als Zeilenkopf
- optional eine Zeile **„Alle“**, die alle gewählten Groups gleichzeitig färbt

```
            Rot    Blau   Grün   Amber  …
Front       [■]    [■]    [■]    [■]
Back        [■]    [■]    [■]    [■]
Alle        [■]    [■]    [■]    [■]
```

## Installation
1. `ColorPicker_v1.0.lua` und `ColorPicker_v1.0.xml` zusammen in den Plugin-Ordner kopieren:
   - **USB-Stick:** Ordner `gma2/plugins`
   - **onPC (Windows):** üblicherweise `C:\ProgramData\MA Lighting Technologies\grandma\gma2_V_<Version>\plugins`
2. Auf der Konsole das Fenster **Plugins** öffnen, einen leeren Platz wählen, dann **Import** und `ColorPicker_v1.0` auswählen.
3. Plugin antippen oder per Command Line starten: `Plugin "ColorPicker_v1.0"`

## Benutzung
Beim Start fragt das Plugin nacheinander:

| Abfrage | Standard | Beispiel |
|---|---|---|
| Groups | `1-6` | `1-4,7` |
| Color-Presets 4.x | `1-10` | `1-8` |
| erstes Macro | `1001` | `501` |
| Layout-Nummer | `101` | `0` = kein Layout |
| Zeile „Alle“ | `ja` | `nein` |

- Groups und Presets, die es nicht gibt, werden übersprungen. Das steht im System Monitor.
- Ist der Macro-Bereich belegt, fragt das Plugin vorher, ob es überschreiben darf.
- Die Macros schreiben die Farbe in den **Programmer**. Danach wie gewohnt in Cues speichern.

### Erkannte Farbnamen
Rot/Red, Grün/Green, Blau/Blue, Hellblau, Dunkelblau, Congo, Weiß/White, Warmweiß, Kaltweiß, Gelb/Yellow, Amber, Orange, Magenta, Pink, Rosa, Lila/Violett/Purple, UV, Lavendel, Türkis, Cyan, Lime, CTO, CTB. Presets mit anderem Namen bekommen keine Macro-Farbe, funktionieren aber trotzdem.

## Wichtig
- Vor dem Einsatz in einer echten Show **auf einer Test-Show (onPC) ausprobieren.**
- Getestet wurde der Ablauf mit einer Simulation, noch nicht auf jeder Software-Version. Die Befehle `Appearance`, `Assign … At Layout … /x= /y=` und `Delete … /nc` sind die, bei denen es zwischen Versionen am ehesten Unterschiede geben kann. Falls einer davon auf deiner Version nicht geht, bleiben die Macros trotzdem nutzbar.
- Der XML-Kopf passt zu Software **3.9.60**. Bei einer anderen Version den Kopf aus einem eigenen exportierten Plugin übernehmen.

## Lizenz und Hinweis
Frei zur Nutzung und Anpassung. Inoffizielles Plugin, keine Verbindung zu MA Lighting. „grandMA2“ ist eine Marke der MA Lighting Technology GmbH.
