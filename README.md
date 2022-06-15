# NodeRed <> Heishamon control option
Current version: 16<br/>
<br/>
Heishamon hardware and software is originally created by Egyras. AWESOME job! <br/>
https://www.tindie.com/stores/thehognl/ <br/>
It is able to communicate with a range of Panasonic Heat pumps. 
- Heishamon pushes a lot of readings and information from the Panasonic heat pump to a MQTT-broker in a 100+ topics.  
- Heishamon is also able to receive MQTT commands from the broker to control the heat pump.  

There are multiple options to use this information or control the heat pump.<br/>
* []() 1: MQTT > Home Assistant
* []() 2: MQTT > OpenHab
* []() 3: MQTT > Domoticz
* []() 4: MQTT > NodeRed
* []() 5: MQTT > ... any other 

All of the above options are ways to interact with the HP. (And all are good if your happy with it)<br/>
I have chosen Node Red (=NR). I already use a lot of NR functionality/automations combined with HomeAssistant. In many cases NR is the controller and HomeAssistant (=HA) is just the graphical front. This CAN be different if you want, as HA is also able to send/receive MQTT messages.  

(Just to emphasize; because of the awesome design choice of Heishamon, it presents everything in the MQTT standard, and that’s why it is able to interact with ANY software that can handle it.)<br/>
BUT...I choose the NR option because if I update or restart HA, the controller and automations just continue. 
<br/>
I have chosen option 4 in the list above. In this GIT you can find NR functions to control the Panasonic.  
<br/>
**My request to you:**<br/>
When you choose to use/try this (Feel free to do so), PLEASE give feedback on any issues you encounter. You can use the [Issues] section to report to me. <br/>
When making an issue, please give as much info as you think is required to solve this. Steps to reproduce. Maybe a screenshot etc. Reporting this will help me help you :) <br/>Thank you in advance. 

## installation 
<details>
	
## Required heat pump setting
This Node Red flow generates and controls the setpoint for the T outlet (Ta). This means you need to put your pump in DIRECT mode on the thermostat itself. 
From within Heishamon, TOP76, will inform you if you currently are in WAR mode or DIRECT mode. <br/>(0=WAR, 1=DIRECT)

## Prerequisite on the Node Red install:
A - Data folder.

B - Adjust settings.js.

C - Required Node Red libraries/pallets

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

### C - Required Node Red libraries/pallets
To make use of the dashboard functionality, you need to install the dashboard library.<br/>
https://flows.nodered.org/node/node-red-dashboard <br/>
The scheduler makes use of the moment lib.<br/>
https://flows.nodered.org/node/node-red-contrib-moment <br/>
node-red-contrib-noop is used as well <br/>
https://flows.nodered.org/node/node-red-contrib-noop <br/>
node-red-contrib-dashboard-bar-chart-data (from v13+)<br/>
https://flows.nodered.org/node/node-red-contrib-dashboard-bar-chart-data <br/>

## How to install the NR flows
Dashboard: http://IP:1880/ui
Flows: http://IP:1880/#flow

* []() In NR, click on the hamburger icon (three horizontal stripes) in the top right corner
* []() Select Import
* []() Copy/past the content of the .JSON file from this GIT. (or select a file to upload and select the flow.json file offered here)
* []() Click on Import

Once imported, you probably need to adjust the settings of the MQTT server. <br/>
Click on the hamburger icon and then configuration nodes. Find the MQTT broker part, double click it and change to your settings.<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/dashboard3.png?raw=true)<br/>

Important >> After import and correcting the MQTT settings, you still need to connect two sensors for the functions to work as designed.<br/>
1: a good room temperature sensor<br/>
2: the outside temperature<br/>
As explained by the image below, you CAN use the Panasonic sensors for this. To do this, in tab (1), WP Control, you only need to remove the LinkIN arrows at (2) and (3), Enable both MQTT sensors in the purple box (4). (double-click on them, on the bottom it says 'disabled', click it to enable)<br/>
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/External%20sources2.png?raw=true" width=75%> <br/>
** Note 1: The Panasonic Room Thermostat is not very accurate which might cause bad temperature control. <br/>
** Note 2: The Outside temperature sensor on the Panasonic might be subject to heating up due to direct sunlight. This can also have a negative impact on the functions.<br/>

## How to personalize or customize
It is advised to create a separate tab for your external sources. Any source available in Node Red can be conditioned and used as a sensor in the functions. If you do this in an 'personal tab', then it is likely easier to update later to newer versions. (no guarantees of course)<br/>
</details>

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
3, import new version<br/> 
4, correct setup, eg. MQTT server.<br/> 
5, re-connect your personalization’s.<br/> 
If there are better ideas about this, please inform me. <br/> <br/>

![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Update_animation_final.gif) <br/>RightMouseClick on the GIF and select open in a new tab to see it full screen.<br/>
	
</details>

## The Dashboard
<details>
This is currently the first page of the dashboard. From here, you can enable each function separate but you can ALSO use WAR + RTC or WAR + SS together. It does not matter. they add on top of each other. You can use multiple combinations. Virtual SP + RTC function. Or WAR + RTC functions or only set a Virtual SP. 

Set the 'Virtual SP'. This resembles the Ta in direct mode. <br/>
If you do not enable any other function, the Virtual SP will be the actual target temperature.<br/>
But, if you enable the WAR function, this will override the virtual SP. <br/>
If you enable the RTC function, the virtual SP or WAR result will be corrected by the RTC function. <br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/Home1.png?raw=true)
<br/>
## Function 1: WAR (weather dependent temperature control)
The target water temperature is influenced by the outside temperature. When it gets colder, the temperature of the water should increase. I have built a GUI including a graph to create a temperature profile.
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/WAR2.png?raw=true)

## Function 2: RTC (RoomTemperatureControl)
This function adjusts the SP of the water depending on the room temperature. <br/>
When the temperature in a room gets too high, it will add a "-1" to the setpoint of the water temperature. <br/>
Through the GUI, you can set the room target temperature. And you can set the limits when you want to increase or decrease the SP by 1, 2 or 3 degrees.<br/>
A recent addition is the possibility to have the pump shut down when above the fourth limit. If you do not set a value, it will not turn off. If you have and dont want this function anymore. set the on/off values to extremes so they are never reached.<br/>
Note that when you let the HP shut down above a threshold, you need to look at your Scheduled actions. If you do want the scheduler to operate, use the override toggle here. <br/>
<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/rtc2.png?raw=true" width=60%>

## Function 3: SoftStart (EXPERIMENTAL!!)
This function is present but not advised to be used yet. I have not tested this well enough. Last time I tested it worked, but not stable resulting in early stops. This function still needs work.<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/SS.png?raw=true)

## Function 4: Solar
The aim of this function is to increase efficiency (and save cost) by utilizing solar energy as much as possible.
When there is solar energy in abundance, you can tell the heatpump to use that energy to heat up your DHW water tank. 
To determine if there is enough solar energy, you need any form of power measurement. This can be a P1 power meter, or a meter directly behind your panels.<br/>
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/solar3.png?raw=true)

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

## Changelog / Notes
All older changelog items are colapsed below. only the latest changelog will always be shown.
<details>
[2021-11-09] (v1) First release. Current versions of the function seem rough. I will polish it at a later time and update here. The Flows do contain comments to clarify. In future I will also publish my verion of a NR dashboard. Currently Im not fully happy with it.

[2021-12-02] (v2) First major upgrade. Things done are:
- Rewritten the functions itself. Better readable, more useful comments to help all users.
- Implemented 3 special functions in total now. **1**: WAR (weather dependent control) **2**: RTC (RoomTemperatureControled) and **3**: SoftStart function.
- Added good useful charts to each of the above three charts. But this can still use some more love.
- Improved on the stability of the control system. After reboot, things should continue now as they should. Making use of local storage and variables. 

[2021-12-04] (v3) Bug fixing
- Toggles on main page were not respected on other pages. 
- Setpoint sending to SET5 was needlessly being 'flooded' (every 10 seconds the same SP).

[2021-12-16] (v4) Things done are:
- Added scheduler. This is currently only for a DHW run or Sterilization run. Will expand this
- Added NR lib "moment" which is used in the scheduler.
- Added Energy charts (including COP calculation)
- General improvements

[2021-12-17] (v5) Things done are:
- Added the option to have a DHW threshold temperature above which the planned DHW run does not start
- Fixed import issues with non-heishamon sources. (Thank you @lampy25)
- Fixed hover-tip error in Function RTC tab (Thank you @lampy25)
- Improved visuals in Function RTC tab. (Thank you @lampy25 for your suggestion!)
- Fixed multiple graph errors, e.g. legend / names. (Thank you @lampy25)
- removed [smooth] references in the flow. (it did not help much)

[2021-12-19] (v6) Things done are:
- Expanded scheduler to include operation mode and quiet mode
- Expanded scheduler from 6 lines to 10 lines. 
- Added an option (in GUI) to prevent DHW run to start above a chosen temperature. 
- Improved user experience with import process so it does not import non-heishamon items. No rogue nodes. 

[2021-12-28] (v7) Things done are:
- BUG fix: after import the layout of Home was messy. (Node Red limitation causes spacers not to be imported/exported. Fixed this with blanks instead of spacers.)
- BUG fix: Scheduler, Actions were not correctly stored / recalled.
- BUG fix: Setting Quiet Mode was not functioning properly. Fixed the issue.
- ENHANCEMENT: Error handling added in some functions. Not fully done yet, but already good enough to be published.
- ENHANCEMENT: Inserted logic to catch situations where variables are not available yet. Functions and Dashboard now keep running if there is one variable unknown.
- ENHANCEMENT: Created a new GUI tab (SETTINGS) with nearly all settings you can change through Heishamon. 
- Improved import process further.

[2021-12-28] (v7.1) Things done are:
- BUG fix: Quiet Mode now really fixed
- Typo in threshold (thank you timovd!)

[2022-01-01] (v8) Things done are:
- BUG fix: Finally fixed the issue where the scheduled quiet mode or operation mode were not being processed correctly.
- BUG fix: After import, the layout of scheduler was off. This has now been fixed.
- REQUEST: added the option for upper / lower limit on the Setpoint

[2022-01-02] (v9) Thing done is:
- BUG fix: Fixed scheduled operation mode not working 

[2022-01-03] (v10) Thing done is:
- REQUEST: Increased upper limit of WAR setpoints (https://github.com/edterbak/NodeRed_Heishamon_control/issues/8)
- BUG fix: Enable Softstart threw an error in line 179 of function. Fixed

[2022-01-03] (v11) Things done are:
- Major rework on scheduler in the flows section.
- REQUEST: Scheduler: added option to plan start/shutdown
- REQUEST: Scheduler: added option to plan night reduction
- REQUEST: Scheduler: add option to override current powerstate with scheduled tasks.

[2022-03-01] (v12) Things done are:
- ENHANCEMENT: issue #19. "settings - DHW/HEAT. show current value" implemented (https://github.com/edterbak/NodeRed_Heishamon_control/issues/19)
- ENHANCEMENT: issue #20 Night reduction - accuracy to 0,1 instead of 1 - (https://github.com/edterbak/NodeRed_Heishamon_control/issues/20)
- ENHANCEMENT: issue #21. Night reduction - add toggle in the Home screen to controle it manually (https://github.com/edterbak/NodeRed_Heishamon_control/issues/21)
- ENHANCEMENT: First attempt to include degree days (graaddagen) in the dashboard. It is NOT finished yet, and on my personal tab, but might affect basic dashboard / 


[2022-04-23] (v13) Things done are:
- ENHANCEMENT: issue #17. "Add graaddagen to dashboard" implemented (https://github.com/edterbak/NodeRed_Heishamon_control/issues/17) The result should be a good step in the richt direction. If this needs more work or you have other suggestions, please add to the issue in githug.
- BUG fix: I noticed the colors of the chart of RTC function was not correct. timing issue. fixed it.
- Removed the need for NR contrib https://flows.nodered.org/node/node-red-contrib-dashboard-average-bars You can safely remove it if you dont use it elsewhere.
- Add contrib [node-red-contrib-dashboard-bar-chart-data] (https://flows.nodered.org/node/node-red-contrib-dashboard-bar-chart-data) 
- Made some items in the dashboard collapsable. 

[2022-04-23] (v13.1) Things done are:
- BUG fix: COP was not working
- ENHANCEMENT: Made the Energy chart look a little better with titels etc. 

[2022-05-05] (v14 beta1) Things done are:
- ENHANCEMENT: Feature request; first attempt to turn heatpump off with RTC-function, when room temperature is above threshold. 
- Fixes in Energy Charts

[2022-05-08] (v14 beta2) Things done are:
- ENHANCEMENT: Feature request; second attempt to turn heatpump off with RTC-function, when room temperature is above threshold. Added DHW to the mix. it should be safe to use now. I have tested it and it does seem to work. Could use some more love to make sure it works propperly.

[2022-05-12] (v14 beta3) Things done are:
- ENHANCEMENT: Feature request; third attempt to turn heatpump off with RTC-function. Bug fixed that the stored rtc_on / rtc_off temperature ranges are not persistent.
- BUG: Fixed scheduler bug where the 'override' toggles were not persistent over reboot.

[2022-05-18] (v14 beta4) Things done are:
- ENHANCEMENT: Feature request; fourth attempt to turn heatpump off with RTC-function. https://github.com/edterbak/NodeRed_Heishamon_control/issues/24
- BUG: fixed incorrect calculated dhw/heat kwh values. (https://github.com/edterbak/NodeRed_Heishamon_control/issues/27) Needs to be confirmed working.
- ENHANCEMENT: Feature reuqest; Solar function. (https://github.com/edterbak/NodeRed_Heishamon_control/issues/26) This is the first attempt. 

[2022-05-19] (v14 beta5) Things done are:
- BUG: Solar function. not producing kWh values. fixed

[2022-05-20] (v14 beta6) Things done are:
- ENHANCEMENT: General polishing of the Solar functionality
- Fixed other issues.
- ENHANCEMENT: Changed the gauge by a multicolor chart. Hope this is nicer. Added threshold to the graph.

[2022-05-22] (v14 beta7) Things done are:
- ENHANCEMENT: General polishing of the Solar functionality.
- Removed the need for the contrib "Media". Doesnt work after you imported. 
- improved logic on COP calculation. store/restore.
- Solar function: Rounded power readings to 0 decimals.

[2022-05-27] (v14 beta8) Things done are:
- Reduced excess SET commands to nearly zero (Thanks for the tip!)

[2022-05-29] (v14 beta9) Things done are:
- BUG: Fixed issue HeatPump being turned off when using the on/off temperature from RTC function
- BUG: Fixed issue where OperatingMode would not stick, also RTC function on/off related.
- removed bar-chart for COP. It keeps throwing errors or NaN value for DHW. Need to dive in at a later time.
- Solar function: for the time being use JavaBoon's code. Thank you !!!

[2022-05-29] (v14 beta10) Things done are:
- BUG: I forgot to enable the SET9 command again after some debugging. Re-enabled it.
- Feature request: Added a button on the request of klerk (and added some lines to make it look better)
- re-added bar-chart for COP. It seems to work. It should start building a chart once DHW + HEAT info is available. untill then it will be empty. Need time to test if this is solid or not.

[2022-05-31] (v14 beta11) Things done are:
- Improved reduction of SET messages even further. 
- FEATURE: Added a LOG page on the dashboard. 
- Added smoothening filter (moving average over last 15 minutes) to reduce frequent SP changes.

[2022-06-01] (v14 beta12) Things done are:
- BUG (critical): Oscillation behaviour (turn on/off heatpump) due to the RTC + Solar function not playing nice. https://github.com/edterbak/NodeRed_Heishamon_control/issues/29 

[2022-06-04] (v14 beta13) Things done are:
- Improved reduction of SET messages. Now also no messages after NR reboot. 
- BUG: Logs text color not being correct.
- Improved logs: Added toggle status changes to log 
- Removed smoothening filter again on room temperature (add it in personal area if you needed it.)

[2022-06-06] (v14 final) Things done are:
- Compared to 14beta13, Improved visuals in log page, increased amount of lines to 40.
- Compared to 14beta13, Improved chart storage.
- A lot: see details in above changelogs.

[2022-06-07] (v15) Things done are:
- Feature request: Compressor runtime added to home dashboard (https://github.com/edterbak/NodeRed_Heishamon_control/issues/28)
- Feature request: remove Fan 2 from home dashboard (https://github.com/edterbak/NodeRed_Heishamon_control/issues/28)
- Improvement: Logging - SOLAR function triggers and actions now loggged.
</details><br/>

[2022-06-07] (v16) Things done are:
- Improvement: Logging - SOLAR function triggers and actions now loggged even better. In other section the logging has improved as well.
- Improvement: Reduce SET commands even further. Example, when due to RTC function the HP is off, but WAR function calculates a change in SetPoint. This setpoint is then NOT communicated to HP now. 
- Enhancement: Layout of the [Function Solar] has been improved with buttons which hide/show the chart/help section.
- BREAKING CHANGE !! : The connection node for P1 power meter has changed (simplified). This needs to be corrected manually after import of this version (16) or later.
	
	
## TODO list
- [ ] Fix SoftStart routine. (it might be working for some, but be cautious)

## Acknowledgments
* []() Egyras: The original creator of Heishamon. https://github.com/Egyras
* []() Egyras: For the MQTT topic list - https://github.com/Egyras/HeishaMon/blob/master/MQTT-Topics.md
* []() CurlyMo: The original calculation for automagical 'Stooklijn' correction - https://gathering.tweakers.net/forum/list_messages/2039982#Automagische_stooklijncorrectie
* []() AUijtdehaag: The COP calculation - https://github.com/Dylantje/WP-Heishamon-sripts/blob/master/WP%20LUA%20COP%20berekening
* []() lampy25. For unselfishly giving supportive feedback and good idea's. Thanks!
* []() timovd for his contributions. ! Thanks.
* []() E1cid from Node Red forum, for the help with the bar-chart. 
* []() Javaboon for his assistance in the Solar functionality
* []() klerk for his feature requests and helping with testing. MEGAAA THANKS :) .

<p align="right">(<a href="#top">back to top</a>)</p>
