Waiting for bugs to be fixed for v21.47 beta

-----------------------------------------------------------------------------------------------------------------------
Bugs to repair --> v22.00 stable:
-----------------------------------------------------------------------------------------------------------------------

- [ ] Oude vars meenemen in node Repair & delete old vars
global.set('MQTT_Block_Allow_function_rtc_onoff', undefined, "file");</br>
global.set('MQTT_Block_Allow_function_solar', undefined, "file");</br>
global.set('MQTT_Block_Allow_function_scheduled_tasks', undefined, "file");</br>
global.set('MQTT_Block_default_1', undefined, "file");</br>
global.set('MQTT_Block_default_2', undefined, "file");</br>
global.set('MQTT_Block_default_3', undefined, "file");</br>

- [ ] bug in Block?-node WP Control, voor SET5 SetZ1HeatRequest
Regel 1: if (global.get('MQTT.block_active', "file") == 1) {</br>

- [ ] Toon logregel alleen als "Extend logging" is enabled? (Function RTC: incremental setpoint correction at 19.3°C --> 3.4 °C)</br>
Regel 121 in function node "RTC Correction".</br>

opmerking: Houd even in gedachten dat we de var MQTT_Source Memory-Only houden, dus niet valt onder MQTT (file)

-----------------------------------------------------------------------------------------------------------------------
Taken meenemen naar v22.01 beta:
-----------------------------------------------------------------------------------------------------------------------

- [ ] Verwijder node "Repair & delete old vars" Vanaf deze versie niet meer nodig.


-----------------------------------------------------------------------------------------------------------------------
release 23 (v22.xx) wat gaan we plannen voor de volgende release
-----------------------------------------------------------------------------------------------------------------------

#### [ ] Check SoftStart Quietmode; use runtime from SoftStart to fix a restart at a flow restart.

#### [ ] Check RTC Automation why this message shows up in log while not relevant: "Function RTC: Operation mode to Heat only"

#### [ ] Continue with naming convention.

#### [ ] Add possibility to change setpoint through scheduler. (why?)

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

#### [4] Show MQTT-source in log (where does it come from?)
Het log toont alle MQTT-Commando's in log.
Het doel is om voordat een daadwerkelijk SET commando door de flow verzonden wordt deze bron/source mee te geven.
Dit zal het debugging bij anderen ook makkelijker maken.

```
12/01 13:34:37 - MQTT Command - SetMaxPumpDuty: 144 (Function Pump speed)
12/01 13:34:37 - Function SoftStart: Correction: 0 > -2 °C - (STARTUP) Freq: 20Hz, SP_Final: 28°C
12/01 13:34:37 - Compressor: running
12/01 13:34:37 - Function Pump speed: restored to: 144 (42%)
12/01 13:31:27 - MQTT Command - SetHeatpump: 1 (rtc on/off)
12/01 13:31:27 - Function RTC: on/off - Turn on power (Room temp: 20.1°C)
12/01 13:31:24 - RTC: Room setpoint changed to: 20.7 °C
```

#### [5] Variabelen opslaan onder de juiste naam (naming convention)
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



