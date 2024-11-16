## HA_flow.json (v 1.0)</br>
HA climate control to adjust the Room setpoint for RTC (Serarate flow)</br>


## configuration.yaml </br>
example code for creating a virtual climate entity for use in NodeRED </br>
Carefull: Do NOT replace existing configuration.yaml with this one!</br>
</br>
</br>

If you are running the HomeAssistant Node-Red add-on, you have the install details written here. They differ a little bit compared to a bare Linux install of Node-Red. 
1: A persistent folder is automatically present for HomeAssistant.
2: The settings.js is located on a different location. 

## Node-Red Addon

in HomeAssistant, the Node Red Add-on > configuration section, under 'npm-packagess' enter the names of the required npm modules. See image below.

```
node-red-dashboard
node-red-contrib-moment
node-red-contrib-noop
node-red-contrib-dashboard-bar-chart-data
node-red-node-smooth
```

<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/HomeAssistant/2024-11-16%2023_52_34-Home%20Assistant%20-%20Brave.png?raw=true">

You must enter them one-by-one. After all of them have been entered, press [Save].

Then SSH into your homeassistant instance through Putty or the the SSH Add-on.

The settings file is in addons_config/xxxxx_nodered. 
You can find 'xxxxx' by using the 'ls' command in the terminal:

```
cd ..
cd addons_config
ls
cd xxxxx_nodered

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


Thanks to sjengfred for the HA update
