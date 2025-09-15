<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/top_banner.png" width="1000">

Changelog of the Alpha's:
<details>
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

## About v25.1 ALPHA 2  
<h1>⚠️ This is a **highly experimental version** ⚠️</h1>

**>>> I really need your help for testing !!!  Read below <<<**

---

## I Need Your Help for Step 2  

To move forward, I need **a volunteer with a 2-zone/buffertank**.  

What’s required:  
- Access to your **dashboard + flow editor + Heishamon web**  
- Remote access (TeamViewer, Remote Desktop, etc.)  
- Optionally, voice call support (Discord, etc.)  

📧 If you’re interested, please email me at **ed_terbak@hotmail.com** with:  
- Your name  
- Your Panasonic setup (zones, buffer, etc.)  
- Your availability  
- Preferred method for testing/debugging access  
- Info on how you can make proper backups
  
---

### Background  
When I started this project, it was purely for my own use. I was still learning Node-RED, JavaScript, object-based programming—everything was new to me.  

Later, I decided to share the project. To my surprise, there was a lot of interest and it was very well received.  

One of the most frequent requests has been better compatibility with different hardware setups—for example, **2-zone operation** or **using a buffer tank**.  
Building this is not that easy because I myself do not own the hardware to be able to test and build it around. 

---

### What’s New in This Alpha  
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

**In short:** this was a **major effort**—every step uncovered more complexity.  

---

### Current Status  

✅ **Tested so far:**  
- Direct operation mode  
- Compensation Curve operation mode  
- Zone control (Water)  
- Zone control (Internal Thermostat)  
- 1-zone setups  

🔧 **Still to do:**  
- **RTC Automation**:  
  - Add toggle “Enable between X and Y” for zones 1/2  
  - Ensure automation is bound to a specific zone (independent modes could conflict)  
- **Cooling function**: currently only works for 1 zone → needs expansion to 2 zones  
- **Buffer tank**: uncertain. I *think* it should already work with 2-zone operation, but needs deeper testing later  

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
