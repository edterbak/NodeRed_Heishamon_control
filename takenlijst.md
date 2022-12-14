-----------------------------------------------------------------------------------------------------------------------
Release 22 Stable (v21.xx) waar we nu aan werken
-----------------------------------------------------------------------------------------------------------------------

#### [1] Fix logging Quietmode restore in log</h2>
Geen idee. Is denk ik iets waar je alles van weet. Kun je beter beschrijven wat de bedoeling is? of het Probleem? Klinkt triviaal zonder impact op functionaliteit. dus gewoon doen :)

#### ~~[2] Heishamon_MQTT_Reconnects~~
Dit is niet oplosbaar door ons. Hebben we niks mee te maken.

#### ~~[3] last run kWh, averige kWh/run. @Maarten69 aangepast na je feedback~~

#### [6] fix unneeded punmpspeed mqtt sending
First priority. Voor mij nieuwe functionaliteit
Volgens mij heb je hier al een keer over gevraagd of ik het in een menu wilde zetten.
Ook hier voor geld, eerst een goede mockup maken. Hoe moet het er uit zien, waar wil je het. Daarna pas bouwen.
Probeer hier in vogel vlucht naar het dashboard te kijken waar het thuis hoort.
Is het een function met eigen tab?

#### [7] fix correct function of shutdown + custom scheduler (@sjampeter )
Probleem is dat de huidige implementatie van shutdown niet robust is.
scheduler zorgt namelijk voor een 'allow' op alles wat in scheduler gezet kan worden. Daarmee zijn andere functies dus in eens ook mogelijk. Dit is niet juist. De oplossing hiervoor heb ik al in gedachten, maar vereist een sloop/wederopbouw van deze functie.
Tijd: Hier heb ik meerdere dagen voor nodig.

#### [8] COP monthly not producing values.@Balans
Dit ligt aan het gegeven dat sommige geen DHW hebben. Dan komt er nooit een grafiek uit.
Kan ook liggen aan een NaN waarde waardoor de verdere opbouw ook niet meer loopt.

#### [9] Linking RTC roomtemperature setpoint to nightreduction roomsetpoint @sjampeter

-----------------------------------------------------------------------------------------------------------------------
release 23 (v22.xx)
-----------------------------------------------------------------------------------------------------------------------
#### [5] Selectable from Dashboard Hardware (if connected in flow).
Graag iets meer uitleg wat je exact bedoeld.
Ik had al een startje gemaakt met system tab.
Ik denk dat het plan hier eerst helemaal afgemaakt moet worden. Het ontwerp. hoe willen we het hebben, wat komt waar. Eerst een mockup.

#### [4] Sensor/devices on central place (flow).
Prima: to do zodra we stabiel 22 hebben. Niet nu nog.


-----------------------------------------------------------------------------------------------------------------------
Discussie

Volgens mij:
item 6 en 8 gedaan. 
Item 9 was mijn fiasco, diekan er af. Kun je dat bevestigen?


