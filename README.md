
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/top_banner.png" width="1000">
Current version: 22.17 Beta<br/> <br/>
Please please please... Make a backup of your current flow before updating.<br/>
<br/>

[RELEASE NOTES v22.00](#release_notes)
<br/>
## Introduction
Heishamon hardware is created by Egyras. AWESOME job! <br/>
https://www.tindie.com/stores/thehognl/ <br/>
It is able to communicate with a range of Panasonic Heatpumps. 
- Heishamon receives information from the Panasonic heat pump and sends it to a MQTT-broker.  
- Heishamon receives commands from the MQTT-broker and sends it to the Panasonic heatpump. 

There are multiple options to connect to the heishamon and control your heatpump.<br/>
* []() 1: MQTT > Home Assistant
* []() 2: MQTT > OpenHab
* []() 3: MQTT > Domoticz
* []() 4: MQTT > Node Red
* []() 5: MQTT > ... any other 

All of the above options are ways to interact with the HP. (And all are good if your happy with it)<br/>
I have chosen Node Red (=NR). I already use a lot of NR functionality/automations combined with HomeAssistant. In many cases NR is the controller and HomeAssistant (=HA) is just the graphical front. This CAN be different if you want, as HA is also able to send/receive MQTT messages.  

(Just to emphasize; because of the awesome design choice of Heishamon, it presents everything in the MQTT standard, and that’s why it is able to interact with ANY software that can handle it.)<br/>
BUT...I choose the NR option because if I update or restart HA, the controller and automations just continue. 
<br/>
I have chosen option 4 in the list above. In this GIT you can find NR functions to control the Panasonic.  
<br/>

********

## What can this Node Red flow do for you?
1 - The default room controller has a horrible temperature measurement. 1 degree accuracy and 1 degree hystresis. This is not suitable for controlling room temperature in a comfortable and stable manner.<br/>
This flow allows you to connect any custom temperature sensor to it. This temperature can be used to correct the water setpoint for ROOM heating. the RTC function was designed to do just this.
<br/><br/>
2 - The default outside temperature is sensative for direct sunlight. The temperature measured can be up to 15 degrees higher compared to reality. <br/>
This will result in unexpected behaviour, e.g. the pump stops functioning. The Panasonic native WAR function (compensation curve) is becuase of this sometimes not doing you would want. <br/>
This node offers a custom external temperature sensor to be connected. It can be your own sensor, or directly form a web sourse. <br/>
But be aware. This custom outside sensor is NOT able to feed the measurement value directly to the heishamon>panasonic for the pump to be used in compensation curve mode. This is not possible. However, when you set the pump in direct mode, the WAR function can take over this function based on your source and logic.
<br/>

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/requirements.png" width="500">

**What do I need to make use of this flow?**<br/>
1: The heishamon module working with your heatpump.<br/>
2: A functional MQTT broker. (Mosquitto/HiveMQ/etc)<br/>
3: A functional NodeRed instance.<br/>

 
<br/>
1: Obviously, you need the heishamon module connected to your heatpump and have it functional. Without this module, you can stop reading. 😄 See this site to get one: https://www.tindie.com/stores/thehognl/ <br/><br/>
2: You can use any broker that is 24/7 available. You can install it on any system, as long as the heishamon and the broker can communicate with eachother.<br/>
<br/>
3: You can install node red on a lot of devices. It can be directly on Linux or a device like Raspberry Pi. You can also run it in a container (self hosted) or within Home Assistant (add-on). For all options see: https://nodered.org/ <br/> All of these options are good, as long as the Node Red application can communicate with the broker and the Node Red instance has persistant storage enabled.<br/>
The NodeRed flow is stand-alone so: You do not require a database. You do not require HomeAssistant. If you have HomeAssistant also connected to heishamon >> Disable all related automations!! 2 captains on 1 ship does not go well....<br/><br/>
<br/><br/>

********

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/install.png" width="500">

<details>
## Requirements on the Node Red install:<br/>
A - Persistent data folder.

B - Adjust settings.js.

C - TimeZone setup

D - Required Node Red libraries/pallets

But first; Why?<br/>
I found that after reboot of node red, the logic did not have all variables populated yet. This caused the system not to start automatically where it left off before reboot. <br/>
To be honest, this does not present a major problem to me, but it will reduce the overall WAF significantly when I don’t notice something like this in time. So, I had to look for a solution to make the programmed setpoints, and all other variables, stick. This is how I achieved that. (Maybe there are better options, feel free to help me on this :) )

### A - Data folder
Make sure Node Red is installed with persistent local volume available. <br/>
If you have Node Red running directly inside Linux (not using docker) or on a RPI, this is already okay and you don't need to create a data folder.
If you have Node Red running in a docker container, make sure you install with this parameter:
```
-v FOLDER:/data
```
Here you can choose the name "FOLDER" yourself which will be the local storage folder of NR.<br/>

If you are node red withing Home Assistand, which is a HA controlled container, follow these instructions:
https://github.com/edterbak/NodeRed_Heishamon_control/tree/main/HomeAssistant
<br/>	
### B - Adjust settings.js
You need to change some settings in settings.js<br/>
Why? If you reboot node red, you will notice some of the variables are not populated yet. Setpoints we have programmed earlier are gone. To make them persistent after reboots, you have to enable the local storage option. Below I have set the flush interval (write to disk) to 300s. 

In a (proxmox) docker install, you can find this settings.js file in:
```
/var/lib/docker/volumes/FOLDER/_data
```
where 'FOLDER' is your own folder name chosen in part A. 

Search for contextStorage:
Put in the text as below:
```
contextStorage: {
	default: "memoryOnly",
	memoryOnly: { module: 'memory' },
	file: { module: 'localfilesystem', config: {flushInterval: '300'}, }
},
```
Reboot Node Red for the changes to load.<br/>

ATTENTION: <br/>
After you have done this, and you seem to have it operational, be sure to test if all settings *stick* after a reboot of Node Red. Things that should stick are: Virtual SP (Ta), Function toggles on the main page, Setpoints in WAR function/graph. Setpoints for RTC function/graph, Heat Pump power toggle on/off
If this is not the case for you, you either need to correct this in settings (like given above), or choose not to use this NR flow, or even accept manual action needed after power loss situations.

### C - Timezone setup
If you use a linux version, make sure you set your correct timezone. You can do this by running this command from CLI root and go through the setup proces.
```
dpkg-reconfigure tzdata
```
You can check if it was succesful by executing this command
```
timedatectl 
```
If you are running Node Red from within HomeAssistant, follow the instructions from HomeAssistant on how to do that.

### D - Required Node Red libraries/pallets
To make use of the dashboard functionality, you need to install the dashboard library.<br/>
https://flows.nodered.org/node/node-red-dashboard <br/>
The scheduler makes use of the moment lib.<br/>
https://flows.nodered.org/node/node-red-contrib-moment <br/>
node-red-contrib-noop is used as well <br/>
https://flows.nodered.org/node/node-red-contrib-noop <br/>
node-red-contrib-dashboard-bar-chart-data (from v13+)<br/>
https://flows.nodered.org/node/node-red-contrib-dashboard-bar-chart-data <br/>
node-red-node-smooth (from 20+)<br/>
https://flows.nodered.org/node/node-red-node-smooth<br/>

## How to install the NR flows
Dashboard: http://IP:1880/ui	(For HomeAssistant: http://IP:1880/endpoint/ui)
Flows: http://IP:1880/#flow

* []() In NR, click on the hamburger icon (three horizontal stripes) in the top right corner
* []() Select Import
* []() Copy/past the content of the .JSON file from this GIT. (or select a file to upload and select the flow.json file offered here)
* []() Click on Import

Once imported, you probably need to adjust the settings of the MQTT server. <br/>
Click on the hamburger icon and then configuration nodes. Find the MQTT broker part, double click it and change to your settings.<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/dashboard3.png?raw=true)<br/>

Important >> After import and correcting the MQTT settings, you could want to connect two custom sensors for the functions to work as designed.<br/>
1: a good room temperature sensor<br/>
2: a custom outside temperature sensor<br/>
As explained by the image below, you CAN use the Panasonic sensors for this. To do this, in tab (1), WP Control, you only need to connect your custom sensor to the [CONNECT >> ..... ] nodes. The flows now automatically use the custom sensor when supplied. <br/>
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/External%20sources2.png?raw=true" width=75%> <br/>
** Note 1: The Panasonic Room Thermostat is not very accurate which might cause bad temperature control. <br/>
** Note 2: The Outside temperature sensor on the Panasonic might be subject to heating up due to direct sunlight. This can also have a negative impact on the functions. Personally I use DarkSky sensor for outside temperature, but anything is possible.<br/>

## How to personalize or customize
It is advised to create a separate tab for your external sources. Any source available in Node Red can be conditioned and used as a sensor in the functions. If you do this in an 'personal tab', then it is likely easier to update later to newer versions. (no guarantees of course)<br/>
</details>

********

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/Updating.png" width="500">

## How to create a backup of your current flow

<details>
There is no easy solution currently know by me to update only changed nodes or flows. <br/>
First: Create a backup of current version. Select all tabs by holding CTRL. Then in the right menu select Export > Download. <br/><br/>

![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Backup_animation1.gif) <br/>RightMouseClick on the GIF and select open in a new tab to see it full screen.<br/>
</details>


## How to update to a newer version
<details>	
Update to newer version:<br/>	
I found it is easiest to:<br/> 
1, remove the tabs, WP MQTT, WP Dash, WP Control, WP Solar, WP Scheduler completely<br/> 
2, remove all ui_base, ui_group and ui_tab references from the flows.<br/> 
	*Keep the MQTT (x.x.x.x) and Home Assistant references.<br/>
3, import new version<br/> 
4, correct setup, eg. MQTT server.<br/> 
5, re-connect your personalization’s.<br/> 
	*Not nessesary if you used a WP_Personal tab (see FAQ.md).<br/>
If there are better ideas about this, please inform me. <br/> <br/>

![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Update_animation_final.gif) <br/>RightMouseClick on the GIF and select open in a new tab to see it full screen.<br/>
	
</details>

********

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/dashboard.png" width="500">
<details>
This is currently the first page of the dashboard. From here, you can enable each function separate but you can ALSO use WAR + RTC or WAR + SS together. It does not matter. they add on top of each other. You can use multiple combinations. Virtual SP + RTC function. Or WAR + RTC functions or only set a Virtual SP. 

Set the 'Default water temp'. This is the starting temperature for Toutlet setpoint.<br/>
If you do not enable any other function, the 'Default water temp' will be the actual target temperature.<br/>
But, if you enable the Node-Red WAR function, this will override the setpoint. <br/>
If you enable the RTC function, the 'Default water temp' setpoint or WAR setpoint will be corrected by the RTC function.<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/home.png?raw=true)
<br/>
## Function 1: WAR (weather dependent temperature control)
The target water temperature is influenced by the outside temperature. When it gets colder, the temperature of the water should increase. I have built a GUI including a graph to create a temperature profile.
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/WAR.png?raw=true)

## Function 2: RTC (RoomTemperatureCorection)
This function adjusts the SP of the water depending on the room temperature. <br/>
When the temperature in a room gets too high, it will add a "-1" to the setpoint of the water temperature. <br/>
Through the GUI, you can set the room target temperature. And you can set the limits when you want to increase or decrease the SP by 1, 2 or 3 degrees.<br/>
A recent addition is the possibility to have the pump shut down when above the fourth limit. If you do not set a value, it will not turn off. If you have and dont want this function anymore. set the on/off values to extremes so they are never reached.<br/>
Note that when you let the HP shut down above a threshold, you need to look at your Scheduled actions. If you do want the scheduler to operate, use the override toggle here. <br/>
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/RTC.png?raw=true" width=60%>

## Function 3: SoftStart
Default behaviour of the heatpump is when it starts up the compressor will go to high Hz for a period. Only when the returning water temperature approaches the setpoint, it ramps down the Hz and get more economic.<br/><br/>

If the SoftStart function is enabled and the compressor starts, the water setpoint will be lowered. This should cause the compressor to ramp the compressor down within minutes. When ramp down has occured, the HEAT SP restrictions will be lifted.<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/SoftStart.png?raw=true)

There's an add-on to the SoftStart called SoftStart-Quietmode

## SoftStart Quietmode
This is an extra (add-on) function to reduce the compressor frequency at startup even more.<br/>
Some heatpump models will start a run and always go up to 45 Hz in de first minute(s) regardless of the target temperature.<br/>
This Quietmode (when switched on) will put the heatpump in the Quietmode (level 3) at rest and waits till the run starts.<br/>
When the compressor turns on, the Quietmode will remain active for an amount of time and after this time switches back to the previous Quietmode (if set).<br/>
You can specify this fallback time in the Setup - Quietmode time (default 5 min).<br/>
<br/>

## Function 4: Solar
The aim of this function is to increase efficiency (and save cost) by utilizing solar energy as much as possible.
When there is solar energy in abundance, you can tell the heatpump to use that energy to heat up your DHW water tank. 
To determine if there is enough solar energy, you need any form of power measurement. This can be a P1 power meter, or a meter directly behind your panels.<br/>
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Solar.png?raw=true" width=80%>

## Scheduler
I have added an option to create 10x schedules (in dashboard) for:
- DHW runs
- Sterilization runs
- Quiet modes
- Operation modes
<br/>You can enable/disable a schedule, multiselect a day of the week, specify the time (hour + minute) and give the schedule a name.<br/>
In addition, the override toggle will allow you to start a planned action, even when the HeatPump is turned off at that time. If this toggle is disabled, a switched off heatpump will not do the scheduled task.<br/>

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/scheduler5.png?raw=true" width=70%><img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Scheduler3_multiselect_day.png?raw=true" width=15%><img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/scheduler3_actions.png?raw=true" width=14%>
Image: [Scheduler] [Multiselect the day] [possible actions to select]

### Dashboard
You can find the link to the dashboard like this:<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/dashboard2.png?raw=true) ![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/dashboard1.png?raw=true)
</details>

********

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/faq.png" width="500">

Q - Can I forward the information coming mqtt messages from the heatpump towards my InfluxDB database? 
<details>
    Yes. There is a separate flow available to download. You can find it in the folder "heishamon2influxdb". It requires an additional contrib (node-red-contrib-influxdb). Install the contrib, download the flow, import it, configure the influxdb and your set. You do not need Domoticz or HomeAssistnat fot that now.
</details>

Q - I use the Node Red Addon in Home Assistant, how to go to the Node Red Gui for editing?
<details>
    Most users should visit via a browser: 'http://Your_NodeRed_IP:1880/ui'<br/>
    In my case this wasn't working, perhabs due to the use of the DuckDNS addon. <br/>
    Use something like 'https://your.duckdns.org:1880/endpoint/ui/' instead.<br/>
</details>
    
Q - Can I use both WAR and RTC at the same time?
<details>
    Yes. If you have both enabled, the WAR function first calculates the SP (Setpoint) depending on outside temperature. Next the RTC function will correct that SP(war) depending on the roomtemperature. The only SP going to the heatpump will be SP at the end of all the calculations/corrections.
</details>

Q - If I switch off the Heatpomp via the Remote Controller will it disable the automation from the Node Red flow also?
<details>
    No, both will work seperatly. If you turn the Heatpump on via the Remote Controller, the pump will switch on.<br/>
    Keep in mind however that if the Node Red flow decides it time to adjust some settings it will act accordingly and overwrite settings.<br/>
</details>

Q - I know the function SOFTSTART is experimental, but can you explain what this function does?
<details>
    The idea behind this function is that when the compressor is just started, the frequency of the compressor goes up to 45+Hz. Well out of the efficient range. This is caused by the inner controller of the heatpump. When turned on, it 'wants' to see the impact of the compressor. It looks at the returning watertemperature for this. Only when the water temperature returning towards the heatpump is nearing the target temperature, it will start to lower the compressor frequency and get in a stable/efficient mode. Throttling... <br/>
This late throttling behaviour can be quiet energy consuming, it can cause the heat pump to generate too much heat at the start and turn off again. <br/><br/>
This SOFTSTART function, when enabled, looks at the moment when the compressor is on, and lower the setpoint to just above the temperature of the returning water. This will in theory cause the heatpump to start throttling down a lot quicker. 
<br/>This function can be usefull directly after defrost cycles when the pump starts again, or when the temperature difference between outside/inside is getting smaller. 
</details>

Q - Is there an overview and explanation of all settings from the dashboards?
<details>
    I will try to help Edterbak adding more HELP/INFO pages for all seprate dashboards, like at the moment you'll find on the "Function (Solar)"-dashboard.
</details>

Q - How do I add my Entities from Home Assistant like Livingroom temperature?
<details>
    In the Nope Red Gui (http://Your_NodeRed_IP:1880/ui) you will have to go to the [WP Control] tab;<br/>
    In the square "REQUIRED INPUTS FOR FUNCTIONS" you will have to add a node (left pane under Home Assistant) named "Events: state".<br/>
    You can drag and drop this in the square mentioned first.<br/>
    It will replace the "TOP33 - panasonic_heat_pump/main/Room_Thermostat_Temp"-node eventually, which you should disable if not using.<br/>
    Select the newly created node, then double click to open up the properties window;<br/>
    Give is a name of your choosings like 'LivingRoom_temperature'<br/>
    In the Entitie box you can try to find your sensor entitie (assuming you have already setup the MQTT settings earlier).<br/>
    Make sure that in the 'Output properties' you have a MSG.PAYLOAD = EVENT STATE<br/>
    Check the box 'output on connect' and make sure at the bottom this node is enabled.<br/>
    Finally press DONE to close the properties dialog and connect your node to the function node 'set.T_woonkamer_BT'<br/>
    <img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/External%20sources2.png?raw=true" width=75%> <br/>
 </details>
 
Q - How to use a dark theme in the dashboard?
 <details>
    In the Node Red Gui (http://Your_NodeRed_IP:1880/ui) press the top most right arrow-down sign and select dashboard;<br/>
    Now press the Theme-tab and pick your style.<br/>
    <img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/ce92e6c7e48323f48742035acb441fb9d3d76aba/images/ChangeTheme.png?raw=true" width=50%> <br/>
</details>

Q - How to update flow to latest version and keep my inputs, MQTT and Home Assistant settings?
 <details>
    Create a personal tab (WP Personal) and place your inputs here (P1, Temperature sensors)<br/>
    Give those inputs.sensors each an own [Link Out] node<br/>
    Connect those [Link Out] nodes to the already excisting [Link In] nodes by double clicking the [Link Out] node and select the corresponding one from the list.<br/>
     Some images to explain this:<br/>
    <img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/8f62fbce4816944de337439dd60ab32d9d4a384f/images/WP_Personal_tab1.png?raw=true" width=50%> <br/>
    <img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/49247191465139a9ee67749accd87a6f65600fcc/images/WP_Personal_tab2.png?raw=true" width=50%> <br/>
    <img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/5095a6e0640ca666a06a1487f71c55de10c06946/images/WP_Personal_tab3.png?raw=true" width=50%> <br/>
    Follow the update procedure from the Readme as desciped here: https://github.com/edterbak/NodeRed_Heishamon_control#how-to-update-to-a-newer-version
</details>

Q - How should I configure the Remote Controller for use with this Node Red Flow?
 <details>
    Make sure the Panasonic Heatpump is off <br/>
    in Dutch:<br/>
    -Intellingen installateur->Systeem instellingen->Zone&Sensor: Watertemperatuur <br/>
    -Intellingen installateur->Bedrijfsinstellingen->Verw.->Watertemperatuurverwarmen: Direct <br/>
    -Intellingen installateur->Bedrijfsinstellingen->Buitentemp voor verw. UIT: 20 (*) <br/>
    *voorbeeld: Bij buitentemp boven 20 graden stopt de WP en gaat waterpomp uit <br/>
	(zet deze op 20 of hoger om onverwacht gedrag te voorkomen)<br/>
	<br/>
    in English:<br/>
    -Installer Setup->System Setup->Zone&Sensor: Water temperature <br/>
    -Installer Setup->Operation Setup->HEAT.->Water temp for heating ON: Direct <br/>
    -Installer Setup->Operation Setup->Outdooor temp for heating OFF: 20 (*) <br/>
    *example: at an outdour temp of 20 degrees the Heatpump will stop <br/>  
	(Set this at 20 degrees to avoid unexpected behavour)<br/>
</details>

********

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/changelog.png" width="500">
All changelog items from v21.00 are colapsed below. only the latest changelog will always be shown.
<details>
[2022-11-12] (v21.00 - Stable) Things done are:
Changelog compared to v18 is:... <br/>
I would like to give a big thank Aikon for his input on the grammar corrections. Still some bits to improve, but its getting a lot better. Thanks to Aikon!!<br/>
Also a huge thank you to Maarten69 and MikeyMan. Their help with testing, their improvments and suggestions are golden. There will be more things to come from them after this stable release. So keep hold on tight...<br/>
- Enhancement: Clean install / first boot optimizations.
- Enhancement: Softstart function rewritten. Logic is now more stable, but still not 100% flawless. This is still work in progress.
- Enhancement: Experimental - this version allows "Compensation curve" mode as well as Direct mode.
- Home all Functions: when function is disabled, the result is grayed out.
- Enhancement: WAR function. With a fresh install, by default Panasonic WAR temperature profole is automatically imported.
- Enhancement: WAR function. Added information of the curve in panasonic controller (read only)
- Enhancement: WAR function. Added Import / Export buttons for the curves
- Enhancement: Solar function. Corrections for P1 can now be configured though dashboard
- Enhancement: Log page now shows 300 lines. More logging now shown which helps with possible debugging.
- Enhancement: Added timezone awareness to node red. (nice to have with the scheduler and logs)


[2022-11-13] v21.1 - Changes compared to Main 21.00:
- [Optional] HA climate control to adjust the Room setpoint for RTC (Separate flow, now in folder Home Assistant)<br/>
https://github.com/edterbak/NodeRed_Heishamon_control/tree/main/HomeAssistant
- BUG: Fixed problem with falling back to built-in temperature sensor from remote controller when using a custom one. (@Restart of flow)
- Enhancement: [New] Added nightreduction room setpoint to scheduler and update the HELP text.
- BUG: Fixed temp sensor log messages: SETUP - Sensors: Custom room temperature sensor FOUND.
- Enhancement: [New] Softstart addon to reduce compressor frequency during startup even more (Using Quiet mode 3) Limits freq to 35 Hz
- Enhancement: [Request] Moved all settings from scheduler tab --> settings tab
- Enhancement: [New] Write panasonic_heat_pump last Error to log.
- Enhancement: [New] Show current status of heater on HOME dashboard.
- Enhancement: [New] (RTC) Send Changed Room setpoint to log
- Enhancement: [New] Enable debug temperatures (switch) for more temperature logging.


[2022-11-13] v21.11 - Changes compared to Main 21.10:
- BUG: Log - fixed extra debug switch. From start it was auto-enabled.
- Enhancement: Re-implemented some of Aikons textual corrections.
- Enhancement: Fixed location of HELP button for settings, improved text overal in help buttons, size of the box etc.
- Enhancement: Moved 'Scheduler related settings' to Scheduler.

[2022-11-15] v21.12 - Changes:
- BUG: Home sceen operatoin mode actual not updating fixed.
- Enhancement: Layout Home
- Enhancement: typos in various places.

[2022-11-18] v21.13 beta - Changes:
- Added 4 new Panasonic models to the list
- Removed debugging options from LOG dashboard
- Fix: Store Quietmode to restore if Addon-Quietmode for SoftStart routine is used.
- Updated SoftStart help for the new function SoftStart-Quietmode.

[2022-11-18] v21.14 beta - Changes:
- BUG: Energie graph was incorrect. Fixed
- Added reset Panasonic Heatpump information (in FLOW)
- Changed some timing and amount of triggers in Control / function nodes. should reduce machine load.

[2022-11-20] v21.15 beta - Changes:
- BUG: Fixed RTC function. +1 was the highest it went. now +3 again.
- Enhancement: CLEAR button in the log tab now automatically refreshes the page. 
- BUG: Fixed showing wrong Operation Mode on Home dashboard.
- BUG: Fixed log spamming bug caused by SoftStart Quietmode.
- Added some extra's to System Check (Defrost counter etc).

[2022-11-21] v21.16 beta - Changes:
- BUG: Fixed incorrect line (25) in [debug] tab, mqtt counter
- BUG: first attempt to fix on/off/on/off/on/off issue reported. 
- Enhancement: Added delete button for scheduled tasks.

[2022-11-23] v21.17 beta - Changes:
- Layout improvement SYTEM.
- Use change of Room_Setpoint to trigger RTC heat on/off instead of timer.
- SYSTEM Health can detect custom devices connected to Heishamon (1-wire, kWh).
- Added MQTT-Blocker to System incl. warning through Notification.
- Check F_rtc_on_off_active on all false conditions.
- Added System "Repair settings" button to fix only this RTC on/off problem.
- Added: System Check includes Error counter, show last error message.
- Changed labels on settings page (status TOP90 / 91) Backup heater hours (HEAT), Backup heater hours (DHW).

[2022-11-27] v21.17 beta - Fixes:
- MQTT-Blocker; user can set the daily limit. (default 200 messages/day)
- Small fixes regarding saving of today/yesterday counter values.

[2022-11-28] v21.17 beta - Changes:
- Auto increase MQTT-Blocker limit when disabling after a block. (+100)
- Write status MQTT-Block active to output-node (request)

[2022-11-30] v21.18 beta (experimental). Changes:
- NEW: Introduce a popup when the pump is turned OFF on the controller. This is now detected. In the popup you can choose what the pump should be doing and what the default behaviour should be. You can find this popup under: Settings > Heat Pump > Shutdown response SETUP. 
- NEW: MQTT-Blocker; adjusted so it allows the settings entered in the popup mentioned 1 line up.
- FIX: Small attempt of a fix to get rid of the incorrect 'RoomThermostat found or using Compensation curve. Using internal Panasonic WAR function. NodeRed WAR function is now disabled' This might do it.
- CHANGED: RTC on/off function. This will now always trigger, more than once, as long as the rtc_off temp is below the actual temp. 
- CHANGED: RTC on/off function. When it is active, toggling off the function will result in reverting the taken actions.
- CHANGED: RTC function does NOT need to be on for the RTC on/off function to be active. Two separate functions they are now actually.
- CHANGED: Solar function. When it is active, toggling off the function will result in reverting the taken actions.

[2022-12-02] v21.19 beta (experimental). Changes:
- FIX: Defrost_Counter_Today not reset anymore at restart of flow.
- NEW: Removed fixed scale for RTC-Chart, now chart will auto-set it's range.
- CHANGED: Re-Added F_rtc_on_off_active to System Check.
- NEW: Added frequency gauge to HOME instead of number in Hz
- CHANGED: messages from RTC on/off (looked like error message) and displays OM in text instead of number.
- FIX: Update log version at start

[2022-12-03] v21.20 beta (experimental). Changes:
- CHANGED: Added to Manual OFF button press setup: notification when user selects/click on action.
- NEW: Added a "force defrost" button to page System Health. (+confirmation dialog)
- NEW: Added a 4th RTC correction to RTC for use as a booster.
- NEW: Added Actual room temp to log message RTC result.

[2022-12-05] v21.21 beta (experimental). Changes:
- FIX: Shutdown pup-up. It was not functioning correctly. Now it works a lot better. 
- FIX: Shutdown pop-up. You should get only onece a popup with a question to lift block.
- FIX: Improved layout RTC tab and added new T Custom to the help section.
- FIX: Heatpump model detection now working as it should. 
- FIX: attempt at a fix for DHW NaN value. NOt sure if this will work. Need to wait and see.

[2022-12-06] v21.22 beta (experimental). Changes:
- [FIX] 4th RTC threshold was not calculated correctly after previous update (always +10)
- [NEW] New function Reduce pump speed added to System Health (temporary location)
- [NEW] Flow can now handle quarter water temperatures from inlet and outlet.

[2022-12-07] v21.23 beta (experimental). Changes:
- [FIX] DHW COP graph and HEAT COP graph are now more robust. prevent 'infinite' result. Got rid of incorrect old code.

[2022-12-07] v21.24 beta (experimental). Changes:
- [FIX] Small round problem in System Check showed 20.200000000000003°C
- [NEW] Reduce pump speed not reducing pump speed during defrost cycle.

[2022-12-08] v21.25 beta (experimental). Changes:
- [NEW] Added fan(s) speed and COP to Gather HP data.
- [Improvement] Polling room setpoint more efficiently to log.
- [TEST] Test RTC booster temp leveling between +3 and +custom levels (currently showing only in log).

[2022-12-08] v21.26 beta (experimental). Changes:
- [NEW] Reduced SoftStart messages (skip unnecessary corrections).
- [NEW] Disabling SoftStart during defrost.<br/>
- [Fix] Changed compressor status to log (includes defrost state).
- [Change] Renamed switch Gather HP data to Extend logging. (Function off by default/restart).
- [Change] Extend logging shows all SoftStart corrections in log (incl. those not affecting setpoint).
- [Change] Extend logging shows compressor start/stops during defrost cycle.
- [Change] Increased default max MQTT messages per day to 300 (new users).

[2022-12-12] v21.27 beta (experimental). Changes:
- [FIX] COP 12month chart now working robustly. Does not matter if only DHW, only HEAT or both are installed now. It will produce a graph anyway.
- [FIX] Connect toggle Dashboard: [RTC - room temperature PV] to toggle Dashboard: [Scheduler > Setpoint normal]
- [FIX] When rebooting NodeRed, sending mqtt commands (enable pump / dhw temperature) now removed.
- [FIX] When rebooting NodeRed, sending mqtt weird commands to SET5 SetZ1HeatRequest, like NaN, 0 or negative values.

[2022-12-12] v21.28 beta (experimental). Changes:
- [ENHANCEMENT] Added toggle for NightReduction RoomSP in Scheduler screen.

[2022-12-13] v21.29 beta (experimental). Changes:
- [NEW] Bypass Softstart Quietmode during defrost.
- [Obsolete] RTC Booster fixed now. Use the switch on RTC dashboard to enable (default off).
- [Obsolete] Removed layout from dashboard Hardware configuration (was for testing only)
- [FIX] Nightreduction room temperature / Current status --> Manual on/off now working again.
- [FIX] Removed log message (@startup) "Function - SoftStart Quietmode: OFF (restored to level 0)". (the restored part)
- [FIX] Unneeded pumpspeed MQTT command (When enabled at a restart).
- [Moved] Moved Function reduce pump speed to Settings page. Layout now improved.
- [Changed] Replaced defrost toggle with button. Color status of button Red --> defrosting.
- [FIX] RTC Room actual temperature didn't update frequently.
- [Add] Added to System Check: RTC Booster, Function pump speed, MQTT-block settings and manual on/off settings.

[2022-12-15] v21.30 beta (experimental). Changes:
- [FIX] RTC on/off function. When no OperatingMode is stored, it would default back to Mode 0 (heat only). Now HEAT is added if not there/possible.

[2022-12-25] v21.31 beta (experimental). Changes:
- [Changed] Rework of block functionality. Rules around custom selection should be obayed now.
- [Changed] RTC function, help and layout. Select the action you want to allow, on/off or heatmode changing only.
- [Changed] RTC function, Booster renamed to incremental setpoints
- [Changed] Scheduler function. Behaviour when other functions are activated.
- [Changed] SoftStart. Now a toggle is present if you want the function to restart every time after a defrost.
- [FIX] WAR function. TOP29 and TOP30 were flipped during import/export.
- [FIX] Heatpump information updated propperly after firmware change. (Thank you xtr3me)

[2022-12-25] v21.32 beta (experimental). Changes:
- [FIX] mqtt signals being blocked too often. Power on/off, Operating Mode, Quiet Mode etc.

[2022-12-25] v21.33 beta (experimental). Changes:
- [FIX] Typo in FILTER functions (2x) (Thanks sjampeter)

[2023-01-04] v21.34 beta (experimental). Changes:
- [Fix] Added to System Check: repair wrong settings MQTT_Block caused by older versions.
- [Add] Added all night recduction variables to the System Check (debugging purposes).
- [Add] Added more SoftStart logging to main log when switch "extend logging is enabled".

[2023-01-04] v21.35 beta (experimental). Changes:
- [FIX] SoftStart was using Night reduction water temp correction when switch was'nt enabled.
- [Other] started with new naming convention and code optimization.
>> (old variables will be removed, please check all function switches). <<

[2023-01-05] v21.36 beta (experimental). Changes:
- [Fix] fixed a small bug after renaming. More fixes still needed.
- [Changed] moved check of mqtt stuff of of system check, into load on boot MQTT.
- [Changed] Removed some of the automated node.warn() notifications.

[2023-01-06] v21.37 beta (experimental). Changes:
- [Other] Continued with var renaming.
- [Other] Renamed label from graph Today's result (kWh) to Result (instead of  "(+) Import (-) Export")
- [New] Linking RTC roomtemperature setpoint to nightreduction roomsetpoint (Normal)

[2023-01-07] v21.38 beta (experimental). Changes:
- [Fix] Crash/Freeze after turning on/off toggle

[2023-01-07] v21.39 beta (experimental). Changes:
- [Fix] Shutdown response and popups now working again.

[2023-01-08] v21.40 beta (experimental). Changes:
- [Fix] Shutdown response improvement.

[2023-01-08] v21.41 beta (experimental). Changes:
- [Fix] small fix for shutdown response improvement.
- [Changed] Improved the System Check layout.
- [Add] Label "Max MQTT-commands per day" now shows actual messages from current day.

[2023-01-09] v21.42 beta (experimental). Changes:
- [Fix] Shutdown response now working (I think).
- [NEW] added a button on the shutdown detection section.

[2023-01-09] v21.43 beta (experimental). Changes:
- [Changed] mqtt.block_mode defaulting to 1 after trigger mqtt.block_active
- [Changed] when mqtt.block_mode IS defaulting to 1, push this state to GUI.


[2023-01-12] v21.44 beta (experimental). Changes:
- [Fix] Shutdown detection and response fixed.
- [Fix] RTC turn power off/on working again (broken in v21.43)
- [New] SoftStart timer: Not resetting at flow restart (when compressor was running).
- [Add] Added SoftStart timers to System Check.
- [Fix] Remember state of switch "Extend debugging".
- [Fix] Remember state of switch "Block MQTT commands" at boot.
- [Fix] Restore previous block_mode when disabling MQTT-Block.
- [Fix] Fixed status button MQTT block on "Manual OFF button press detected" page.
- [New] Added Home button op System navigation bar.

[2023-01-..] v21.45 beta (experimental). Changes:
- [Other] Removed some typos from changelog.
- [New] Added source (origin) for most MQTT-commands in log
- [New] Highlight mqqt-commands in log.
- [Fix] Fixed Bock mode "custom" changing to "block all" after 1 min.
- [New] Added actual block mode to label switch "Block MQTT commands".
- [Fix] Fixed some typos.
- [Other] Cleaned up code Function WAR2DIRECT (3rd pinout code obsolete).


[2023-01-15] v21.46 beta (experimental). Changes:
- [New] Function RTC automation, give name to OM mode when changing Operation mode.
- [Fix] RTC Turn power on/off & Change Operation mode was only executed after a room temp change.
- [Fix] SoftStart Quietmode was setting wrong Quietmode even when switch was disabled.
- [Fix] Status switch "Extend logging" was configured incorrectly.
- [Change] Changed the way label of "Block MQTT commands" updating.
- [Change] Better solution to update MQTT state and label by listening to command# topic.


[2023-01-17] v21.47 beta (experimental). Changes:
- [Fix] Fix SoftStart logging showed F_SS_old_correction with trailing zeros xx.0000000000003
- [Fix] Reduced SoftStart logging when SP_Final < HEAT_SP_lower_limit.
- [Fix] Label "Max MQTT-commands per day" Wasn't updating correctly, now fixed.
- [New] Improvements made for extra (more) debugging info. (trailing *** in log) and highlights.
- [Other] Code optimalisation in System Check, SS corr rounding etc.
- [Fix] Fixed a small difference between function Compare new SP and function SoftStart.
- [Optimalisation] Cleaned up code, removed unnecessary inject nodes (1/min).
- [New] When defrosting show status on Home (@Operation mode, actual).
- [Change] Increasing the max mqqt-messages/day now in bigger steps (10/2000).

[2023-01-19] v21.48 beta (experimental). Changes:
- [Fix] Issue #67 Bug: in Node "Block".
- [Fix] Issue #74 Bug: Block_mode CUSTOM changing to BLOCK_ALL @power off (remote controller).
- [Fix] Issue #68 Bug: show logline only when 'Extend logging' is enabled.
- [Fix] Issue #69 Bug: Correct function node "set.war_ot_wt".
- [New] Added a link to changelog.md on dashboard System/About.

[2023-01-21] v21.49 beta (experimental). Changes:</br>
- [Fix] Function Reduce Pump Speed fixed</br>

</details>
<a name="release_notes"></a>

# Release notes</br>

Where to start.. </br>
First off, I would like to welcome Maarten69 to the team. He has joined the project and gave a major boost with new ideas, new energy and new functions. So a big "THANK YOU" from me (Ed ter bak) for all the effort and free time you have invested. I appreciate it a lot, and I am sure a lot of other users do as well.</br>
</br>
That said... For a long time, there has not been a stable update. A lot of bigger things were done together making it harder to do this. 
But, this process is going to be managed better in future. We aim to have shorter release cycles, with less major changes per update to make it manageable.</br>
</br>
</br>
This release has a lot of changes compared to previous stable build, v21.00.</br>
Here is a brief overview of the most noticeable items.</br>
</br>
<ins>Operation mode:</ins></br>
Previously it was only possible to use this Node Red flow when your heat pump is in Direct mode, and not using the Compensation Curve. This has now been changed. You can operate the heat pump in Compensation Curve mode. This means the heat pump itself is managing the water temperature depending on the outside temperature. The NodeRed WAR function will then automatically be disabled. Current implementation is not yet allowing you to manually shift the curve up/down. (This will be added soon in next builds) But, it IS possible to have this done for you by using the RTC-function. </br>
</br>
<ins>SoftStart:</ins></br>
This function has had a lot of attention. You now have the options to use quietmode during the start-up phase and you can choose to ignore restarts after a defrost cycle. In addition, you can use this function to increase the length of the compressor runs. The HELP text describes more about this. </br>
</br>
<ins>WAR function:</ins></br>
This tab has been improved so it can import the current settings from the heat pump and show it in the graph. Also, you can change the temperatures and upload it again to the heat pump. </br>
</br>
<ins>Reduce pump speed:</ins></br>
In the [Settings] tab, you will now find a bit about "Reduce pump speed". This new function allows you to reduce the pump speed when the compressor is not on.</br>
</br>
<ins>Scheduler:</ins>
In the scheduler there are now a couple of extra options; Night reduction. Remove a schedule by pressing the bin.</br>
</br>

<ins>Shutdown response:</ins></br>
We found that the node red flow is not aware of certain situations, like maintenance. When you manually turn the unit off through the wall mount, it can happen that a scheduled task is planned and turns the heat pump back on. To handle this situation, there is now an option to predefine the reaction of the flow when a manual OFF is pressed on the wall mounted controller. You can find this section under Settings > Shutdown response > [setup]</br>
</br>
<ins>Safeguard:</ins></br>
In addition to the shutdown response, the node red flow is keeping track of te amount of mqtt messages sent per day. If this exceeds a limit then the flow will block all future messages. The default limit is 500. Normal operation should result in 20-100 messages per day. If you are unlucky with a lot of defrosts, this can go up to 200. So 500 indicates a problem for sure.</br>
</br>
<ins>Debugging:</ins></br>
It has been made a lot easier by Maarten69 to debug if there are issues. Under the SYSTEM tab, System Health, you can now list a lot of the hidden variables used by the node red flow. When posting issues, it is advised to supply a copy of that text. :) </br>
</br>
Finally, a short note. The layout of v22.00 is a bit of a mess. The structure in the dashboard is now a little hard to detect. It is in need of some polishing here and there. This will be on the agenda soon to tidy up to give a better user experience.</br>
</br>
Have fun with your heat pump!</br>
</br>
/Ed ter bak</br>
</br>
</br>

[2023-02-06] v22.01 beta 1 (experimental). Changes:
- [Fix] Softstart. Rewritten the timing logic, and reduced resource footprint at the same time. 
- [Fix] Softstart. Added gradual reduction of the correction after function end. Now it is going to correction 0 with 1C/5minutes.
- [Fix] Max MQTT-commands per day not shown correctly.

[2023-02-12] v22.02 beta 1 (experimental). Changes:
- [Fix] Softstart. Rewritten the complete softstart function (copy parts) but now it works solidly. 
- [Fix] Quietmode addon (softstart). End signal of quietmode never came. 
- [changed] Variables of softstart functin are now under 1 main F_SS. variable. More logical names, and better performance.

[2023-02-12] v22.03 beta 1 (experimental). Changes:
- [Fix] Softstart-Quietmode addon. missing declared msg in the function. 

[2023-02-12] v22.04 beta 1 (experimental). Changes:
- [Fix] Softstart stored variables flipped by default, now correct again. 

[2023-02-14] v22.05 beta 1 (experimental). Changes:
- [Fix] Some variables, when undeclared, threw errors preventing correct functionality. (will check further)

[2023-02-14] v22.06 beta 1 (experimental). Changes:
- [Fix] F_RTC memoryOnly <> file storage location error caused incorrect results in GUI and 2 result of 2 functions.

[2023-02-19] v22.07 beta 1 (experimental). Changes:
- [Fix] Softstart function now on a different level. Added more advanced settings. (be carefull). The softstart function seems a lot more effective now.
- [Enhancement]	#89 setpoint arrows on the dashboard to shift when in compensation curve mode. In case of compensation curve mode, the functions RTC, SoftStart and Night reduction should now work. (requires testing !)
- [Fix] #81. setting the upper/lower limits for heat should now be set correctly.

[2023-02-20] v22.08 beta 1 (experimental). Changes:
- [Fix] GUI item not set correctly of the Softstart function.
- [Fix] upper/lower limit values not being bound to the min/max values. 

[2023-03-19] v22.09 beta 1 (experimental). Changes:
- [Fix] Rewrite quietmode add-on. Now using quiet mode manager. 
- [Enhancement] Changed GUI around softstart.

[2023-03-26] v22.10 beta 1 (experimental). Changes:
- [Enhancement] SoftStart Chart now shows quietmode (https://github.com/edterbak/NodeRed_Heishamon_control/issues/104)
- [Fix] Solar Function. Half rewritten. Added DHW setpoint manager and introduced popups when changing stuff. Need testing.
- [Enhancement] Added HP models no 29-32 to the list.

[2023-03-26] v22.11 beta 1 (experimental). Changes:
- [Fix] Fix room temperature readout during NR Deploy (https://github.com/edterbak/NodeRed_Heishamon_control/issues/93)

[2023-03-26] v22.12 beta 1 (experimental). Changes:
- [Fix] On Temperature page the Ta is plotted on the living room graph (https://github.com/edterbak/NodeRed_Heishamon_control/issues/75)
- [Fix] stopped always MQTT quietmode sending during deploy / reboot 

[2023-03-26] v22.13 beta (experimental). Changes:
- [Fix] Room temperature readoud now rounded to 2 decimals.

[2023-03-26] v22.14 beta (experimental). Changes:
- [Fix] Fixed unneeded sending of temperature from new node [T_Room Input Selector]

[2023-03-27] v22.15 beta (experimental). Changes:
- [Fix] Fixed quietmode switching back within 5 minutes after quietmode time passed.
- [Enhancement] Renamed some extra variables. 


[2023-04-01] v22.16 beta (experimental). Changes:
- [Fix] Fixed two small issues with logs being flooded by SOLAR function messages. 
- [Enhancement] Renamed a lot of variables. 
- [Fix] cleaned up some remnant variables


[2023-04-02] v22.17 beta (experimental). Changes:
- [Enhancement] GUI - [SYSTEM] - MQTT tab made
- [Enhancement] GUI - [SYSTEM] - moved shutdown response to MQTT tab.
- [Enhancement] GUI - [SYSTEM] - HW information tab made (moved from [settings] tab
- [Enhancement] GUI - if mqtt block is enabled, the node red dashboard title turns red.
- [Enhancement] GUI - if defrost is enabled, the node red dashboard title turns blue.
- [fix] minor tweaks to solar function. I need to know if there are still issues with this function. please give feedback
- [fix] minor tweaks to mqtt block function. variable usage.



********

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/acknowledgments.png" width="500">

* []() Egyras: The original creator of Heishamon. https://github.com/Egyras
* []() Egyras: For the MQTT topic list - https://github.com/Egyras/HeishaMon/blob/master/MQTT-Topics.md
* []() CurlyMo: The original calculation for automagical 'Stooklijn' correction - https://gathering.tweakers.net/forum/list_messages/2039982#Automagische_stooklijncorrectie
* []() AUijtdehaag: The COP calculation - https://github.com/Dylantje/WP-Heishamon-sripts/blob/master/WP%20LUA%20COP%20berekening
* []() lampy25. For unselfishly giving supportive feedback and good idea's. Thanks!
* []() timovd for his contributions. ! Thanks.
* []() E1cid from Node Red forum, for the help with the bar-chart. 
* []() Javaboon for his assistance in the Solar functionality.
* []() Maarten69 for his feature requests and helping with testing. MEGAAA THANKS :).
* []() Maarten69 (yes, again) for contributing to this project and helping with a FAQ !!!!! :).
* []() Aikon for contributing to this project and helping with layout/textual improvements.
* []() Maarten69 for contributing to this project. New additions can be expected from him on this project. Mega sweet!!
* []() MikeyMan for contributing with testing and discussing a lot about this. Good testing is very valuable!


********

**My request to you:**<br/>
When you choose to use/try this (Feel free to do so), PLEASE give feedback on any issues you encounter. You can use the [Issues] section to report to me. <br/>
When making an issue, please give as much info as you think is required to solve this. Steps to reproduce. Maybe a screenshot etc. Reporting this will help me help you :) <br/>Thank you in advance. 

<p align="right">(<a href="#top">back to top</a>)</p>
