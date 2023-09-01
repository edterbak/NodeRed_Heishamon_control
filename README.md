
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/top_banner.png" width="1000">
Current version: 23.08 Stable release<br/> <br/>
<br/>
It has been some time since the last release, v22.00 Stable release. Now, after 33 beta versions, the flow has progressed nicely and I feel it is time for an update.</br>
Some things have improved a lot in my opinion. The layout, stability and usability. A lot of requested functionality has been added as well. I think this is a good result.</br></br>
These are the most significant improvements compared to v22.</br></br>

* []() The design of the menu is more logical now
* []() The menu items (tabs) can now be customized. You can hide items which you do not used. (System > Menu config)
* []() Solar function has been fixed now and is improved. You can now set a cool-down period after the dhw-run. The function has been renamed to “Solar²DHW” to avoid confusion with native solar function
* []() New feature: “Flexible pumpspeed function”. You can change the maximum pumpspeed depending on what the heatpump is doing, heat/dhw/nothing
* []() New feature: “Auto start DHW”. You can set a lower limit to temperature of the DHW tank. Once reached, a dhw run will be started
* []() New feature: “Boost DHW”. This function enables you to set a second (higher) temperature for the DHW tank. If you want to take a bath for example and require more and warmer water. When you activate this function the DHW run will start with “Forcefull DHW” enabled
* []() New feature: Scheduler now offers the ability to change the setpoint temperature of the water, room temperature or the shift temperature
* []() A lot of stability issues have been resolved
* []() Temperature sensors (readouts) during reboots have been improved. No sensor restart anymore
<br>
For the full changelog please visit the bottom part of the github page and unfold the button "changelog".
</br>
</br>
As always: Have fun with your heat pump!</br>
</br>
/Ed ter bak</br>
</br>

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

## Function 4: Solar²DHW
The aim of this function is to increase efficiency (and save cost) by utilizing solar energy as much as possible.
When there is solar energy in abundance, you can tell the heatpump to use that energy to heat up your DHW water tank. 
To determine if there is enough solar energy, you need any form of power measurement. This can be a P1 power meter, or a meter directly behind your panels.<br/>
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Solar.png?raw=true" width=80%>

## Scheduler
I have added an option to create 10x schedules (in dashboard) for:
- Heatpump power on/off
- Set water setpoint (heat shift / heat direct)
- Set room setpoint (RTC function)
- Force DHW runs
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
All changelog items from v22.00 are colapsed below. only the latest changelog will always be shown.
<details>
	
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

[2023-04-04] v22.18 beta (experimental). Changes:
- [Enhancement] GUI - [SETTINGS] Moved menu items to a more logical place
- [Enhancement] GUI - [Home] remaned "solar function" to "Solar energie to DHW"
- [Enhancement] GUI - [SYSTEM] MAJOR changes</br>
----------------------- Added "MENU CONFIG" section</br>
----------------------- Here you have the option to enable/disable sidebar tabs</br>
----------------------- Here you have the option to enable/disable menu sections on [Home] / [Settings] tabs</br>
----------------------- Combined HW info and HW config</br>

[2023-04-10] v22.19 beta (experimental). Changes:
- [Fix] - Changed Solar function logic. Hopefully less issues. (needs feedback)
- [New feature] Scheduler now allows you to set setpoint for: 
----------------------- Water (HEAT + pump Direct mode)
----------------------- Shift (HEAT + pump Compensation curve mode)
----------------------- Room temperature (for use in RTC function)
- [Enhancement] The COOL function has a modified function to calculate dew-point. Thanks to blb4!
- [rework] - Added manager for Operating mode + Heat SP
- [Fix] - Lot of changes and work done to allow pump in Compensation Curve operation. Needs testing and feedback from you all.

[2023-04-10] v22.20 beta (experimental). Changes:
- [Fix] - Solar function. case power off and allow power on toggie is off added with logging. Improved GUI name of toggle.

[2023-04-19] v22.21 beta (experimental). Changes:
- [Fix] - Solar function. Improved function. Should work robust now. Added cooling down period after completion of a solar-dhw run.
- [Enhancement] - Moved reduce pumpspeed to a separate tab. 
- [Enhancement] - Reduce pumpspeed rewritten and added option to set 3 speeds (low, dhw and heat) 

[2023-04-19] v22.22 beta (experimental). Changes:
- [Fix] - RTC function. When sterelization run active, do not allow rtc function to act. 
- [Enhancement] - Solar function status better visible on homepage with status behind it when it is running or done.
- [Enhancement] - Spumpspeed function. Added dynamic y-axis for the chart based on the highest setting.
- [Fix] - On home screen, the water "Outlet setpoint" was incorrect during dhw run. 
- [Fix] - empty popup after changing the DHW setpoint in some scenarios.


[2023-04-21] v22.23 beta (experimental). Changes:
- [Changed] - RTC function. Automations section. After installing this update, you need to set the correct activate/deactivate temperatures again. These will be removed intentionally. 
- [Enhancement] - Solar chart. The Power result graph now has automated Y axis. 
- [Improvement] - Changed some stored variable names (F_RTC.xxxx)
- [Enhancement] - System - MQTT settings now also shows IP adress of heishamon.


[2023-04-22] v22.24 beta (experimental). Changes:
- [Fix] - During sterilization run, the incorrect temperature of the setpoint was shown on dashboard. 
- [Fix] - small improvement related to pumpspeed function.

[2023-04-22] v22.25 beta (experimental). Changes:
- [Fix] - If you have installed v22.24, please update as soon as possible. 1 dumb error caused mqtt commands to be sent every 10 seconds.

[2023-05-02] v22.26 beta (experimental). Changes:
- [Fix] - Solar chart not being populated.
- [Enhancement] - Dashboard on multiple devices are now in sync. Menu configuration is instant over all devices. 
- [Enhancement] - Added a note to the Hardware Information section to clarify stuff when the model says 'unknown'. 

[2023-05-08] v22.27 beta (experimental). Changes:
- [Fix] - 95% fix memory leak (seems 100% after couple of days)
- [Fix] - Operating mode change not done in some cases. (incorrect filter usage) Needs to be checked further in other areas.
- [Enhancement] - Removed the repeated triggers and replaced them with a link in from UI-Control node. Only refresh when needed. 

[2023-05-10] v22.28 beta (experimental). Changes:
- [Fix] - RTC function, the automation for OperatingMode had a hard time staying active... fixed it.
- [Enhancement] - Start DHW-run below XX C (SETTINGS tab). This triggers Forcefull DHW run (by scheduler)
- [Fix] - Solar function did not revert operating mode/SP in some scenarios.  https://github.com/edterbak/NodeRed_Heishamon_control/issues/110
- [Fix] - Sterilization routine always reverts back to Operating Mode heat only. Now it is reverting the original OM befor the run. https://github.com/edterbak/NodeRed_Heishamon_control/issues/108

[2023-05-11] v22.29 beta (experimental). Changes:
- [Fix] - Attempt to fix Sterilization issue. https://github.com/edterbak/NodeRed_Heishamon_control/issues/108 

[2023-05-14] v22.30 beta (experimental). Changes:
- [Fix] - Night reduction not showing correct valuein dashboard https://github.com/edterbak/NodeRed_Heishamon_control/issues/111
- [New Feature] - Added DHW BOOST function. https://github.com/edterbak/NodeRed_Heishamon_control/issues/95
- [New Feature] - Added RESET when there is an active error on the device (eg. H62.. ) https://github.com/edterbak/NodeRed_Heishamon_control/issues/113
- [Rework] - Rewritten the logic a bit behind the force dhw toggles.
	
Fairly much has been changed. needs polishing.

[2023-05-17] v22.31 beta (experimental). Changes:
- [Fix] - RTC turn on/off function not working in all cases. (typo) https://github.com/edterbak/NodeRed_Heishamon_control/issues/114

	
[2023-06-01] v22.32 beta (experimental). Changes:
- [Enhancement] Added visual in RTC GUI to see the status of the automations
- Preparations for 23.00 Stable
	
[2023-06-02] v22.32 beta (experimental). Changes:	
- [Fix] - improved the scheduler <> RTC on/off function working together. Should work better now.

</details>
	
[2023-06-05] v23.00 Stable.


[2023-06-06] v23.01 Stable. Changes:
- [FIX] Export error. A missing connection in WP Manager tab. For some reason this connection did not want to be exported, no matter what. Remove + new wire solved it. (??? weird...)

[2023-06-12] v23.02 Stable. Changes:
- [FIX] Cool mode in Home Dashboard. Kept showing incorrect HEAT setpoint. Now it is correct.
- [FIX] Cool menu area. You now see [setpoint lower limit] more clearly
- [FIX] SoftStart function + cool mode kept showing heat setpoint in the graph. This has now been fixed.

[2023-07-09] v23.03 Stable. Changes:
- [FIX] Cool function. Complete rewrite of the cool function. I expect no more NaN for sending cool setpoints.
- [Enhancement] Added manual lower limit for the cool function.
- [Enhancement] Setpoint changes from cool function will not be sent when heatpump is off, or cool is not in operating mode.
- [Enhancement] Updated HELP in cool section.

[2023-07-20] v23.04 Stable. Changes:
- [New feature] Add conditions to the scheduler. This is a nice addition to the existing scheduler.

[2023-07-22] v23.05 Stable. Changes:
- [New feature] Add to scheduler the option to set the DHW setpoint temperature. (https://github.com/edterbak/NodeRed_Heishamon_control/issues/120)

[2023-08-21] v23.06 Stable. Changes:
- [New feature] Scheduler - Conditions now allow multiselect.
- [FIX] Scheduler - after reboot the conditions were not shown in dashboard https://github.com/edterbak/NodeRed_Heishamon_control/issues/126
- [FIX] COOL function -  [error] [function:COOL function] is resolved https://github.com/edterbak/NodeRed_Heishamon_control/issues/127

[2023-08-21] v23.07 Stable. Changes:
- [FIX] Scheduler - Due to grouping the scheduler did not function anymore. This should now be improved a lot. https://github.com/edterbak/NodeRed_Heishamon_control/issues/128
- [FIX] Home dashboard did not show correct temperature behind 'Water temp'. Related to https://github.com/edterbak/NodeRed_Heishamon_control/issues/123.
- [FIX] Detection on persistent storage should now work propperly. 

[2023-09-01] v23.08 Stable. Changes:
- [FIX] Scheduler - There seems to be a NodeRed bug in the JUNCTION functionality during export/import. I have now removed it from the scheduler tab. I will start replacing the junction nodes with other nodes due to this crazy issue. For now, it seems that all is working. Sorry for the possible hassle this might have caused. :( https://github.com/edterbak/NodeRed_Heishamon_control/issues/128
  Update: v23.08 seems stable again with everything working as it should... I will keep this line updated.

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

### Donation
By multiple users of the flow, I have been asked to put up a donation on the site. I was never that fond of that idea, because it is just my hobby and I like doing it. In addition, I do not want to change the expectation of users, the open character of the communication here (moving from one enthusiast>enthusiast to a supplier>client relationship). This is just a hobby, and I do not expect I can quit my daytime job anytime soon.. Ghhehehe.. And of course it will stay like this.<br>
But the constant raising eyebrows of my social circle why I don't have such a button (because so many people do it), and the curiosity about how many users actually want to make a donation have won me over to try such a button. Let's see. <br>
<br>
But most important. In no way you are obliged to make any donation!! The flow remains completely free, free to use as you want, as always at your own risk. <br>

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/B0B5LZGCO)


********

**My request to you:**<br/>
When you choose to use/try this (Feel free to do so), PLEASE give feedback on any issues you encounter. You can use the [Issues] section to report to me. <br/>
When making an issue, please give as much info as you think is required to solve this. Steps to reproduce. Maybe a screenshot etc. Reporting this will help me help you :) <br/>Thank you in advance. 

<p align="right">(<a href="#top">back to top</a>)</p>
