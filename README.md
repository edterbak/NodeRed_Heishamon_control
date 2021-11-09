# NodeRed <> Heishamon control options

Heishamon is created by 'Egyras'/'TheHog'. AWESOME job! 
Heishamon makes a lot of information available on the Panasonic heatpump through MQTT.
There are multiple options to use this information.
* []() 1: MQTT > Home Assistant
* []() 2: MQTT > OpenHab
* []() 3: MQTT > Domoticz
* []() 4: MQTT > NodeRed
* []() 5: ... any other 

All of the above options are ways to controle the HP.
I have choosen NodeRed (=NR). I already use a lot of NR functionality combined with HomeAssistant. In many of my cases NR is the controller and HomeAssistant (=HA) is just the graphical front. This CAN be different as HA is also able to control and automate using MQTT protocol. (Just to emphasize; because of the awesome design choice of Heishamon, it presents everything in the MQTT standard, and thats why it is able to interact with ANY software that can handle it.) 
BUT...I choose the NR option because if I update or restart HA, the controler and automations just continue. 

I have choosen option 4 in the list above. In this GIT you can find NR functions to control the panasonic. 

<!-- USAGE EXAMPLES -->
## Usage
* []() In NR, click on the hamburge icon (three horizontal stripes in the top right corner)
* []() Select Import
* []() Copy/past the content of the JSON file from this GIT 
* []() Click on Import

Once imported, you probably need to adjust the settings of the MQTT server in the function.  

## Changelog / Notes
[2021-11-09] Current versions of the function seem rough. I will polish it at a later time and update here. The Flows do contain comments to clarify. In future I will also publish my verion of a NR dashboard. Currently Im not fully happy with it.

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* []() original MQTT topic list from Egyras - https://github.com/Egyras/HeishaMon/blob/master/MQTT-Topics.md
* []() Github page of Egyras - https://github.com/Egyras/HeishaMon


<p align="right">(<a href="#top">back to top</a>)</p>
