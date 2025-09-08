<img src="https://github.com/edterbak/NodeRed_Heishamon_control/blob/main/images/banners/top_banner.png" width="1000">

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/B0B5LZGCO)
********

**About v25.1 ALPHA 1**<br>
<h1>This is a highly experimental version!!</h1><br><br>

 **>>> upload flow.json this week<<<**
								 
<br> 
<br>
When I started this project, I was only building this for my own use. I was making baby steps in learning Node Red, JavaScript etc. Object based thinking and that kind of stuff.. All new. <br>
Then I got the idea to share my project. I found out there was a lot of interest for it and it was received well. <br>
<br>
It has been mentioned a lot and requested often that the flow is not well capable of working with other hardware setups, for example 2 zone operation, or using a buffer tank. <br>

In this alpha, I have tried to rework the dashboard so it is able to accomodate for 2 operational zones and control them. <br>
Just a short summary of what the impact of this is: <br>
- Stored variables had to be restructured and migrated<br> 
- RTC function has been duplicated. Two zones > Two Rooms to controle the temperature for. Code wise and dashboard wise this is built in now.<br> 
- CCC function has been duplicated. Two zones > Two compensation curves. Code wise and dashboard wise. (CCC is formally knwon as the WAR function, which was a horrible Dutch translation) <br> 
- SoftStart function needed to be broken out of the logic and moved. This function is not zone dependant.<br> 
- NightReduction function needed to be broken out of the logic and moved. This function is not zone dependant.<br> 
- The sensor input needs to be updated to also have a sensor for Room 2.<br> 
- Some of the Scheduler actions needed to be reworked to allow 2 zone specification.<br> 
- The logic of sending and checking the new setpoints for 2 separate zones.<br> 
<br> 
Well, short conclusion: it was a MAJOR journey to go through it step by step. Each step uncovering more work and more "Aiii... this is more complex than I thought..."<br> 

<br> 
<br> 
So, this is an Alpha !<br><br>

**What have I tested:**
- Direct operation mode<br>
- Compensation Curve operation mode<br>
- Zone control: Water<br>
- Zone control: Thermostat (internal)<br>
- 1 Zone<br>
  
**Things still ToDo:**
- RTC Automation: break out a toggle "Enable between X and Y" to the zone 1/2 pages. <br>
- RTC Automation: be able to select the specific zone where the automation should work with. Cannot be both independant, or they might work against eachother. <br>
- Cool function is currently 1 zone only. This needs to be expanded to 2 zones as well.<br>
- Buffer tank usage. I am a bit uncertian here. I feel it should alreayd work now with zone 1+2 operational. This needs to be dived into at a later stage.<br>
<br>

It should work as expected for people with just 1 zone. But because there is such a major amount of work done, this needs to be tested and verified. But I expect that with more people testing will deliver me bugs and issues to solve. <br><br>

**The test plan:**

Step 1: Test regular 1 zone operation.<br>
Step 2: Test zone 2 operation (after step 1 completed) <br>
Step 3: Test Buffer tank operation<br>
But for now, we are still at step 1.<br>


**I nead your help for step 2**

I need help from you all for step 2. I need a volunteer for this. I require access to the system of a person with 2 zones. <br>
To be able to do the work, I need to be able to see the dashboard + flow editor + heishamon web.<br>
This can be realised via TeamViewer, Remote Desktop, etc, and optionally in combination with voice call like discord. <br>
If you would like to help me as a volunteer, please send an e-mail to [ed_terbak@hotmail.com] with:
- your name
- Panasonic setup zones/buffer/...
- your availability
- what your preference or suggestion would be to give me the oppertunity to test/debug on your system
- if your able to make propper backups (and how).<br>


**IMHO, who is suited for testing this alpha:**
- People who are able to take a full backup of current running system and be able to restore it.<br>
  Note: Only making a backup of the current JSON and restoring this later during a roll-back, is NOT going to work well. A lot of stored variables have been migrated. So this means a lot of your settings will be EMPTY after the roll-back. You need to do some dashboard configurating again at the minimum. <br>
- People who can handle an error here or there and dont get nervous.<br>
- People who have the time to do the above and want to help this project mature :)<br>
<br>
Below some screenshot of the alpha.<br>
<br>

<img width="1628" height="1095" alt="image" src="https://github.com/user-attachments/assets/fe31922e-981f-450a-8bc5-7fa41de7f3b1" />
<br>
<img width="1301" height="978" alt="image" src="https://github.com/user-attachments/assets/c51809fe-ed91-4a9e-ba44-48a5a780e397" />
<br>
<img width="1305" height="875" alt="image" src="https://github.com/user-attachments/assets/a75f78d6-e70d-482f-b31d-0ca8a8f7e326" />
<br>
<img width="1303" height="1032" alt="image" src="https://github.com/user-attachments/assets/f87fe456-1484-4a3c-a0f5-6bee36ccfe1b" />
<br>
<img width="1300" height="872" alt="image" src="https://github.com/user-attachments/assets/7bc9e73e-94f2-4422-9a86-39024d77480a" />







  
  
