# NodeRed <> Heishamon control option

Heishamon is originally created by Egyras. AWESOME job! 
- Heishamon pushes a lot of readings and information from the Panasonic heatpump to MQTT topics. 
- Heishamon can also receive MQTT commands and control the heatpump. 

There are multiple options to use this information or control the heatpump.
* []() 1: MQTT > Home Assistant
* []() 2: MQTT > OpenHab
* []() 3: MQTT > Domoticz
* []() 4: MQTT > NodeRed
* []() 5: MQTT > ... any other 

All of the above options are ways to interact with the HP.
I have choosen NodeRed (=NR). I already use a lot of NR functionality/automations combined with HomeAssistant. In many cases NR is the controller and HomeAssistant (=HA) is just the graphical front. This CAN be different as HA is also able to control and automate using MQTT protocol. 

(Just to emphasize; because of the awesome design choice of Heishamon, it presents everything in the MQTT standard, and thats why it is able to interact with ANY software that can handle it.) 
BUT...I choose the NR option because if I update or restart HA, the controler and automations just continue. 

I have choosen option 4 in the list above. In this GIT you can find NR functions to control the panasonic. 

## Usage
* []() In NR, click on the hamburge icon (three horizontal stripes in the top right corner)
* []() Select Import
* []() Copy/past the content of the JSON file from this GIT 
* []() Click on Import

Once imported, you probably need to adjust the settings of the MQTT server in the function.  

## Changelog / Notes
[2021-11-09] Current versions of the function seem rough. I will polish it at a later time and update here. The Flows do contain comments to clarify. In future I will also publish my verion of a NR dashboard. Currently Im not fully happy with it.

## Acknowledgments

* []() Egyras: The original creator of Heishamon. https://github.com/Egyras
* []() Egyras: For the MQTT topic list - https://github.com/Egyras/HeishaMon/blob/master/MQTT-Topics.md
* []() CurlyMo: The original automagical 'Stooklijn' correction from CurlyMo: https://gathering.tweakers.net/forum/list_messages/2039982#Automagische_stooklijncorrectie

<p align="right">(<a href="#top">back to top</a>)</p>
