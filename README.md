OpenDTU-OnBattery-Controller
============================

Variable energy price control for OpenDTU-OnBattery 

# About

This project contains Node Red flows that will act as contoller for OpenDTU-OnBattery and TeslaSolarCharger. The flow will steer these two systems based on the variable energy price (EPEX Spot market). 
The solution is somewhat specific to the German market so the rest of the description will be in german. Please use Google translate if you like to understand this. In essence the Node Red flow will:

* Read the EPEX Spot prices
* En-/disable the OpenDTU Dynamic Power Limiter depending on the EPEX spot price
* En-/disable the OpenDTU Dynamic Power Limiter Solar Passthrough depending on the EPEX spot price
* En-/disable the OpenDTU Huawei PSU depending on the EPEX spot price. Control the charging voltage / current from the UI
* Control TeslaSolarCharger and trigger maximum charging power when the EPEX spot price is low

# Details

Dieses Projekt enthält mehrere Node Red flows die grösstenteils unabhängig voneinander verwendet werden können. Im Einzelnen sind das:

* Übersicht
  * Dieser Flow empfängt Informationen per MQTT aus OpenDTU-OnBattery und stellt diese in der Übersicht in der UI dar.
* Durchschnittspreisberechnung: 
  * Dieser Flow holt die EPEX Strompreisdaten des aktuellen Tages über die Awattar API und speichert diese Informationen. 
  Er ermittelt ausserdem den aktuellen Strompreis und stellt die Preise in einem Diagramm dar.
* Aktueller Ertrag:
  * Dieser einfache Flow wird verwendet um den Ertrag mehrerer Inverter zu kombinieren. Die Daten werden über MQTT empfangen und publiziert.
* Dynamic Power Limiter:
  * Dieser Flow enthält die Logik um den OpenDTU Dynamic Power Limiter entsprechend den Strompreises zu steuern. 
  Liegt der Strompreis oberhalb der Schwelle für "DPL aktiv ab:" wird der Dynamic Power Limiter eingeschaltet. 
  Liegt der Strompreis unter dem Wert ist der Dynamic Power Limter typischerweise ausgeschaltet. Dann ist es günstiger Strom einfach zu kaufen.
  Liegt der Strompreis zwischen dem "DPL aktiv ab:" und "Solar PT zum Auto ab:" liefert der Dynamic Power Limiter genau die aktuelle solare Leistung damit der Strom in die Autobatterie gespeichert werden kann.
* Ertragsprognose:
  * Dieser Flow ermittelt eine Ertragsprognose für den aktuellen und den folgenden Tag. 
  Es wird eine Solaranlage mit drei Ausrichtungen angenommen. Die Ergebnisse werden im UI dargestellt.
* Huawei Netzteil:
  * Dieser Flow steuert das Huawei Netzteil. 
  Liegt der Strompreis unter der im UI eingestellten Grenze wird das Netzteil mit den gegebenen Strom und Spannungsgrenzen gestartet.
  Darüber hinaus wird permament über den Tag ermittelt ob der erwartete Ertrag des Solarstandorts "Garage" ausreicht um die Batterie im Laufe des restlichen Tages komplett voll aufzuladen.
  Sollte dies nicht der Fall sein wird das Huawei Netzeil in den Automatikmodus geschaltet um anderweitigen Überschuss im Haus zum laden der Batterie zu nutzen.
* Auto:
  * Dieser Flow steuert den TeslaSolarCharger. Liegt der Strompreis niedrig wird das Auto mit maximaler Leistung geladen.
* Historie:
  * Dieser Flow stellt verschiedene Daten über die Zeit dar.

Die einzelnen Flows die den Power Limiter, das Netzteil und den TeslaSolarCharger steuern stellen den Betriebzustand und den Grund für diesen Zustand in dem Statusfeld dar.
