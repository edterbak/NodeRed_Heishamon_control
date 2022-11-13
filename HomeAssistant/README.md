## HA_flow.json </br>
HA climate control to adjust the Room setpoint for RTC (Serarate flow)</br>


## configuration.yaml </br>
example code to put in configuration.yaml to add a climate entity for use in NodeRED </br>
Carefull: Do NOT replace existing configuration.yaml with this one!</br>
</br>
</br>
</br>
</br>



If you are running the HomeAssistant Node-Red add-on, you have the install details written here. They differ a little bit compared to a bare Linux install of Node-Red. 
1: A persistent folder is automatically present for HomeAssistant.
2: The settings.js is located on a different location. 

## Node-Red Addon

in HomeAssistant, the configuration section, state the required npm modules.
```
npm_packages:
  - node-red-dashboard
  - node-red-contrib-moment
  - node-red-contrib-noop
  - node-red-contrib-dashboard-bar-chart-data
```

Then SSH into your homeassistant instance through Putty or the the SSH Add-on.
Type:
```
cd ..
cd config/node-red
nano settings.js
```

scroll down to the bottom somewhere and add the following lines. 
```
contextStorage: {
	default: "memoryOnly",
	memoryOnly: { module: 'memory' },
	file: { module: 'localfilesystem', config: {flushInterval: '300'}, }
},
```
Make sure the spacing is correct.

After save>exit and reboot Node-Red addon, you should see the following which confirms the values stick after reboot.
![](https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/HomeAssistant/ha2.png?raw=true)
