Frequently Asked Questions
==========================

Q - I use the Node Red Addon in Home Assistant, how to go to the Node Red Gui for editing?
<details>
    Most users should visit via a browser: 'http://Your_NodeRed_IP:1880/ui'<br/>
    In my case this wasn't working, perhabs due to the use of the DuckDNS addon. <br/>
    Use something like 'https://your.duckdns.org:1880/endpoint/ui/' instead.<br/>
</details>
    
Q - Can I use both WAR and RTC at the same time?
<details>
    Yes, the SP (Setpoint) will be adjusted depending on outside temperature and controlled by the settings in RTC
</details>

Q - If I switch off the Heatpomp via the Remote Controller will it disable the automation from the Node Red flow also?
<details>
    No, both will work seperatly. If you turn the Heatpump on via the Remote Controller, the pump will switch on.<br/>
    Keep in mind however that if the Node Red flow decides it time to adjust some settings it will act accordingly and overwrite settings.<br/>
</details>

Q - I know the function SOFTSTART is experimental, but can you explain what this function does?
<details>
    ...
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
    -Intellingen installateur->Systeem instellingen->Ruimtethermostaat: Intern <br/>
    -Intellingen installateur->Bedrijfsinstellingen->Verw.->Watertemperatuurverwarmen: Direct <br/>
    -Intellingen installateur->Bedrijfsinstellingen->Buitentemp voor verw. UIT: 20 (*) <br/>
    *voorbeeld: Bij buitentemp boven 20 graden stopt de WP en gaat waterpomp uit <br/>
    in English:<br/>
    -Installer Setup->Systeem Setup->Zone&Sensor: Water temperature <br/>
    -Installer Setup->Systeem Setup->Room thermostat: Intern <br/>
    -Installer Setup->Operation Setup->HEAT.->Water temp for heating ON: Direct <br/>
    -Installer Setup->Operation Setup->Outdooor temp for heating OFF: 20 (*) <br/>
    *example: at an outdourt temp of 20 degrees the Heatpump will stop <br/>    
</details>
