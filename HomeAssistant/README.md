If you are running the HomeAssistant Node-Red add-on, you have the install details written here. They differ a little bit.

## Node-Red Addon

in the configuration section, state the required npm modules.
```
npm_packages:
  - node-red-dashboard
  - node-red-contrib-moment
```

Then SSH into your homeassistant instance through Putty or the the SSH Add-on.
Go to:
```
/config/node-red
```

Then type
```
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

