-----------------------------------------------------------------------------------------------------------------------
Release 22 Stable (v21.xx) waar we nu aan werken
-----------------------------------------------------------------------------------------------------------------------

#### ~~[1] Fix logging Quietmode restore in log~~ (Status: fixed)
```
Geen idee. Is denk ik iets waar je alles van weet. 
Kun je beter beschrijven wat de bedoeling is? of het Probleem? Klinkt triviaal zonder impact op functionaliteit. dus gewoon doen :)
Update: 
```

#### ~~[2] Heishamon_MQTT_Reconnects~~
```
Dit is niet oplosbaar door ons. Hebben we niks mee te maken.
update: Je bedoelde ook het aantal mqtt berichten hieromtrend. toch?
Antwoord: Nee, alleen het tonen van het aantal reconnects, voor de System Check. (uitlezen van panasonic_heat_pump/stats).
```

#### ~~[3] last run kWh, averige kWh/run. @Maarten69 aangepast na je feedback~~

#### ~~[4] fix unneeded punmpspeed mqtt sending (Status: fixed)~~
```
Details: First priority. Voor mij nieuwe functionaliteit
Volgens mij heb je hier al een keer over gevraagd of ik het in een menu wilde zetten.
Ook hier voor geld, eerst een goede mockup maken. Hoe moet het er uit zien, waar wil je het. Daarna pas bouwen.
Probeer hier in vogel vlucht naar het dashboard te kijken waar het thuis hoort.
Is het een function met eigen tab?
```

#### ~~[5] fix correct function of shutdown + custom scheduler (Status: wip)~~
```
Details: Probleem is dat de huidige implementatie van shutdown niet robust is.
scheduler zorgt namelijk voor een 'allow' op alles wat in scheduler gezet kan worden. 
Daarmee zijn andere functies dus in eens ook mogelijk. Dit is niet juist. 
De oplossing hiervoor heb ik al in gedachten, maar vereist een sloop/wederopbouw van deze functie.
Tijd: Hier heb ik meerdere dagen voor nodig.
```

#### ~~[6] COP monthly not producing values.~~ (Status: fixed)
```
Details: Dit ligt aan het gegeven dat sommige geen DHW hebben. Dan komt er nooit een grafiek uit.
Kan ook liggen aan een NaN waarde waardoor de verdere opbouw ook niet meer loopt.
```

#### ~~[7] Linking RTC roomtemperature setpoint to nightreduction roomsetpoint (Status: obsolete?)~~
```
Details:
```

#### ~~[8] TOP29 en TOP30 verwisseld tijdens export/import.~~ (Status: Fixed)
```
Doorgegeven door blb4. Tijdens import is de grafiek in WAR chart onjuist. TOP29 en TOP30 zijn per abuis verkeerd gemaped naar de war_wtx variabelen. Dit door niet goed lezen/begrijpen van de tekst en onvoldoende testen.
```

-----------------------------------------------------------------------------------------------------------------------
release 23 (v22.xx) wat gaan we plannen voor de volgende release
-----------------------------------------------------------------------------------------------------------------------
#### [1] Selectable from Dashboard Hardware (if connected in flow).
Graag iets meer uitleg wat je exact bedoeld.
Ik had al een startje gemaakt met system tab.
Ik denk dat het plan hier eerst helemaal afgemaakt moet worden. Het ontwerp. hoe willen we het hebben, wat komt waar. Eerst een mockup.

#### [2] Sensor/devices on central place (flow).
Prima: to do zodra we stabiel 22 hebben. Niet nu nog.

#### [3] T room custom & T outside custom improvement
Huidige detectie van custom sensoren gebeurt pas na dat de eerste meetwaarde binnen komt. 
Verbetering is om T_outside_custom en T_room_custom als object op te slaan met .payload de temperatuur en .time de tijd van de laatste meting.
Dan kan bij de eerst volgende trigger gelijk de juiste temperatuur worden gepakt. 

#### [4] Variabelen opslaan onder de juiste naam (naming convention)
![image](https://user-images.githubusercontent.com/3155621/210887553-2f58c9a3-a5d9-44e1-a343-75019a14db8f.png)

![image](https://user-images.githubusercontent.com/3155621/210887657-1b649e7a-603c-485b-bec2-07a828eabd9f.png)

```
MQTT	
	.active								MQTT block enabled/disabled 1,0
	.allow_rtc_onoff			
	.allow_scheduler			
	.allow_solar				
	.block_mode							BLOCK ALL (1) , CUSTOM (2), UNRESTRICTED (3)
	.block_mode_previous						Restore to this block_mode when disabling switch
	.counter				    			Total MQTT messages counter
	.messages_today			    				No. MQTT messages today
	.messages_yesterday		    				No. MQTT messages yesterday
	.message_limit			    				Maximum allowed messages in 24h allow before block will activate

NightReductionWaterTemp
	.current_value							renamed from NightReduction_current_value
	.correction							renamed from NightReduction_correction
	.state								renamed from nightreduction_on_off

NightReductionRoomSetpoint
	.state 								Night reduction room setpoint status (1,0)
	.LOW								renamed from RoomSetpointLOW
	.NORMAL								renamed from RoomSetpointNORMAL

F_ReducePumpSpeed
	.state								Reduce pump speed active 1,0
	.PumpMaxFlowSetting						
	.SavedPumpspeed							Value to restore to when disabled
	.LowSpeed							Value used as low pump speed when active

F_RTC
	.state								RTC main function on/off (1,0)
	.incremental_setpoints_state					incremental_setpoints status of switch. (Renamed from Booster), value 1,0
	.correction_value (Memory Only)					RTC correction (renamed from F_RTC_correction)
	.F1_power_state							function turn on/off heatpump (1,0)
	.F2_OM_state							function change OM heatpump
	.on_off_active							0= waiting to turn heat on. 1= waiting to turn heat off
	.on_off_status							value: 0,1,2

Defrost
	.Counter							Renamed from Defrost_Counter
	.Counter_Today							Renamed from Defrost_Counter_Today
	.Counter_Yesterday						Renamed from Defrost_Counter_Yesterday

```

-----------------------------------------------------------------------------------------------------------------------
release 24 (v23.xx) later..
-----------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------



