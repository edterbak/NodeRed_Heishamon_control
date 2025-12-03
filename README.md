<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/top_banner.png" width="1000">

Unfold to see the FULL changelog of the released alpha and Beta versions:
<details>

</br>
Version 25.06 (! Beta 5 !)</br>
Changes:</br>
- Fix Softstart: Buffertank enabled would have bad performance. Variable was not initialized propperly.</br>
- Fix Zone2: No change in setpoint being sent other than 0 or 1. There was a math error.</br>
- Fix Zone2: MQTT Comand spamming resolved.</br>
- New Feature: Created a generic control mechanism to verify sent commands have been processed. If not, a retry is initiated with a maximum retrycount of 3 for each topic. This is done for a couple of the most important commands.</br>
- Added an auto-tuning noice filter to the input sensors by default.</br>
- Fix Scheduler: Toggle functions to accomadate zone 1 and zone 2 were not adjusted yet. Now they should be working</br>
- Fix Scheduler: Removing line 2 did not work. Now it does. 
</br>

</br>
<h3>Version 25.06 (! BETA 4 !)</h3>
Changes:</br>
- Fix SP_Final: Initial calculation did not resolve. Resulting in no mqtt commands being sent to set heat. If you do not experience issues with Beta 3, you do not need to update. Thanks to Breadrun @ Tweakers.net for the good suggestion.</br>

  </br>
<h3>Version 25.06 (! BETA 3 !)</h3>
Changes:</br>
- Fix CCC: Incorrectly used TOP27 instead of TOP42/TOP43 for zone 1/2. Corrected this.</br>
- Improvement: HEAT commands are being sent in more situations now. Even when DHW-only is active.<br>
- Improvement: SoftStart function. Logic revised to have less limitations.<br>
- Improvement: SoftStart function. More information in the softstart tab <br>
- Improvement: The resending of Set Heat commands is abandoned. I now assume a sent message will be received. No active checking every 28seconds anymore.<br>
- Improvement: Dashboard generic. Layout changes, making it more uniform (half way through the process now)<br>
- Improvement: Scheduler conditions - Modified the lowerlimit of the outside temperature condition from 0 to -20</br>
<br>
**Important notice:** <br>
This version does NOT check if a command for HEAT has been received and set. It assumes after sending, it is set. This prevents the issue of needles resending commands fully. However, the QOS level of the MQTT command has been increased to level 2 to make sure the heishamon did receive it. <br>
</br>

</br>
<h3>Version 25.06 (! BETA 2 !)</h3>
Changes:</br>
- Fix CCC: In Direct mode, disabling CCC function set the target temperature to 20 instead of the given setpoint.</br>
- Fix SoftStart: When using buffertank and softstart together, the setpoint could be set too low, triggering the compressor-stop.</br>
- Enhancement: PumpSpeed function added the active mode in the graph (Mode 0=Function off / 1=Low / 2=HEAT / 3=DHW)</br>
- Enhancement: Added a LOT of additional external link-in nodes in WP Input tab. </br>

<h3>Version 25.06 (! BETA 1!)</h3>
Changes:</br>
- Fix SoftStart: Aligned the function to operate better with 2 zones</br>
- Fix SoftStart: Fixed an error in fetching RTC z2 result. It was fetching RTC z1 result</br>
- Fix CCC: Zone 2 logic was different from Zone 1 logic</br>
- Enhancement: Added a LOT of new external input options in the tab [WP Input] (WIP)</br>
</br>

<h3>Version 25.05 (! ALPHA 8!)</h3>
Changes:</br>
- Fixed zone 1 being set instead of zone 2</br>
- Fixed SoftStart graph not showing actual calculated setpoint</br>
- Improved SoftStart function: **Now it works with buffertank and Zone 1/2 usage**</br>
- Improved Temperature graph</br>
- Modified additional T outside, T sensor Z1+Z2 logic again. </br>
</br>

**If you experience issues with this version and you have a buffer, please try disabling SoftStart function !**

<h3>Version 25.05 (! ALPHA 7!)</h3>
Changes:</br>
- Fixed SoftStart function. It was dead...</br>
- Changed a little bit on logging for 2 zones. Should be quieter now in the log</br>
<br>

<h3>Version 25.05 (!ALPHA 6!)</h3>
Changes:</br>
- Fixed CCC zone 1 toggle refucing to play nice with us</br>
- Fixed Final water temperature for zone 2 (not updating)</br>
</br>

<h3>Version 25.05 (!ALPHA 5!)</h3>
Changes:</br>
- Fixed SetCurve NULL.</br>
- Added boot note to inform users</br>
- Improved cold boot startup logic in general. Not perfect yet, but way better.</br>
</br>

<h3>Version 25.05 (!ALPHA 4!)</h3>
Changes:</br>
- Added logic about the selected p1 meter-type so it does not forget the selected value in the dashboard.</br>
- Added delay funtionality to "Auto start DHW" option. Popup and all. </br>
- Improved on Solar²DHW dashboard. Tried to reduce too large datasets to prevent loading issues.</br>
</br>

<h3>Version 25.04 (!ALPHA 2!)</h3>
Changes:</br>
- Fixed Scheduler which did not work at all. Disabled trigger > Enable.</br>
- Changed the button actions for zone 1 and 2 to not remove a chart</br>
- Changed the storege of RTC chart to make it more robust</br>
- Added Zone 1 and Zone 2 above the RTC charts to make it more clear what is selected.</br>
- Removed some entries for Zone 2 in Settings tab.</br>
- Moved Zone 2 Cool from Settings tab to Home tab. </br>
</br>
</details>


---

## About v25.06 BETA 5  
<h1>⚠️ Getting close to a stable version with the beta's. ** ⚠️</h1>

**>>> But, I still really need your help for testing !!!**

---

### Background 
Question: Why do I need your help so much?
Answer: Well, when I started this project, it was purely for my own use. I was still learning Node-RED, JavaScript, object-based programming—everything was new to me.  

Later, I decided to share the project. To my surprise, there was a lot of interest and it was very well received.  
And even more later, I became aware of the incompatibility with other hardware setups, like people with 2 zones active, or with a buffertank, etc. 
There were frequent requests were to improve compatibility with the different hardware setups.   
Building this is not that easy as it sounds, because 
- I myself do not own the hardware to be able to test and build it.
- I needed to restructure and rewrite the already existing functions
- I needed to restructure the already existing variables to handle multizone
- I lack the knowledge on how those other hardware setups behave and react

---

### What’s New in This Beta  
In this release, I’ve reworked the dashboard to support **two operational zones**. This required a lot of changes under the hood.  

Here’s a short overview of the main impacts:  

- **Stored variables**: restructured and migrated  
- **RTC function**: duplicated → now supports 2 rooms (temperature control in both code and dashboard)  
- **CCC function**: duplicated → now supports 2 compensation curves (formerly WAR function, badly translated from Dutch)  
- **SoftStart function**: separated from zone logic (not zone-dependent)  
- **NightReduction function**: separated from zone logic (not zone-dependent)  
- **Sensors**: added support for a second room sensor  
- **Scheduler**: updated to allow 2-zone specification  
- **Setpoint logic**: rewritten for handling 2 separate zones
- **Extra Input's**: Added a lot of new external input connections to the dashboard (See tab: WP Input)

**In short:** this was a **major effort**—every step uncovered more complexity.  

---

### Current Status  

✅ **Tested so far:**  
- Direct operation mode  
- Compensation Curve operation mode  
- Zone control (Water)  
- Zone control (Internal Thermostat)  
- 1-zone setups  
- 2-zone setups
- Buffer tank setups. I *think* it should work.

🔧 **Still to do:**  
- **RTC Automation**:  
  - Add toggle “Enable between X and Y” for zones 1/2  
  - Ensure automation is bound to a specific zone (independent modes could conflict)  
- **Cooling function**: currently only works for 1 zone → needs expansion to 2 zones  


---

### What This Means for You  
If you have just 1 zone, the flow should still work as expected.  
However, because so much has changed, I need wider testing to uncover bugs and issues.  

---

### Test Plan  
1. Test regular 1-zone operation ✅  
2. Test zone 2 operation (next step)  
3. Test buffer tank operation  

Currently, we’re still at **Step 1**.  

---

### Who Should Test This Alpha?  
This alpha is best suited for people who:  
- Can take **full system backups** and restore them if needed  
  - ⚠️ Note: just backing up the current JSON is **not enough**. Many stored variables have been migrated, so restoring only the JSON will leave lots of settings empty. At minimum, you’ll need to reconfigure parts of the dashboard.  
- Can handle occasional errors without stress  
- Have the time and interest to help this project mature  

---

### Screenshots  

Below are some screenshots of the alpha version in action:  

<img width="1628" height="1095" alt="image" src="https://github.com/user-attachments/assets/fe31922e-981f-450a-8bc5-7fa41de7f3b1" />  
<img width="1301" height="978" alt="image" src="https://github.com/user-attachments/assets/c51809fe-ed91-4a9e-ba44-48a5a780e397" />  
<img width="1305" height="875" alt="image" src="https://github.com/user-attachments/assets/a75f78d6-e70d-482f-b31d-0ca8a8f7e326" />  
<img width="1303" height="1032" alt="image" src="https://github.com/user-attachments/assets/f87fe456-1484-4a3c-a0f5-6bee36ccfe1b" />  
<img width="1300" height="872" alt="image" src="https://github.com/user-attachments/assets/7bc9e73e-94f2-4422-9a86-39024d77480a" />  
