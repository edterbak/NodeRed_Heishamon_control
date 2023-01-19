-------------------------------------------------</br>
[2023-01-19] v21.48 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Issue #67 Bug: in Node "Block".</br>
[Fix] Issue #74 Bug: Block_mode CUSTOM changing to BLOCK_ALL @power off (remote controller).</br>
[Fix] Issue #68 Bug: show logline only when 'Extend logging' is enabled.</br>
[Fix] Issue #69 Bug: Correct function node "set.war_ot_wt".</br>
-------------------------------------------------</br>
[2023-01-17] v21.47 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Fix SoftStart logging showed F_SS_old_correction with trailing zeros xx.0000000000003</br>
[Fix] Reduced SoftStart logging when SP_Final < HEAT_SP_lower_limit.</br>
[Fix] Label "Max MQTT-commands per day" Wasn't updating correctly, now fixed.</br>
[New] Improvements made for extra (more) debugging info. (trailing *** in log) and highlights.</br>
[Other] Code optimalisation in System Check, SS corr rounding etc.</br>
[Fix] Fixed a small difference between function Compare new SP and function SoftStart.</br>
[Optimalisation] Cleaned up code, removed unnecessary inject nodes (1/min).</br>
[New] When defrosting show status on Home (@Operation mode, actual).</br>
[Change] Increasing the max mqqt-messages/day now in bigger steps (10/2000).</br>
-------------------------------------------------</br>
[2023-01-15] v21.46 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[New] Function RTC automation, give name to OM mode when changing Operation mode.</br>
[Fix] RTC Turn power on/off & Change Operation mode was only executed after a room temp change.</br>
[Fix] SoftStart Quietmode was setting wrong Quietmode even when switch was disabled.</br>
[Fix] Status switch "Extend logging" was configured incorrectly.</br>
[Change] Changed the way label of "Block MQTT commands" updating.</br>
[Change] Better solution to update MQTT state and label by listening to command# topic.</br>
-------------------------------------------------</br>
[2023-01-13] v21.45 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Other] Removed some typos from changelog.</br>
[New] Added source (origin) for most MQTT-commands in log</br>
[New] Highlight mqqt-commands in log.</br>
[Fix] Fixed Bock mode "custom" changing to "block all" after 1 min.</br>
[New] Added actual block mode to label switch "Block MQTT commands".</br>
[Fix] Fixed some typos.</br>
[Other] Cleaned up code Function WAR2DIRECT (3rd pinout code obsolete).</br>

-------------------------------------------------</br>
[2023-01-12] v21.44 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Shutdown detection and response fixed.</br>
[Fix] RTC turn power off/on working again (broken in v21.43)</br>
[New] SoftStart timer: Not resetting at flow restart (when compressor was running).</br>
[Add] Added SoftStart timers to System Check.</br>
[Fix] Remember state of switch "Extend debugging".</br>
[Fix] Remember state of switch "Block MQTT commands" at boot.</br>
[Fix] Restore previous block_mode when disabling MQTT-Block.</br>
[Fix] Fixed status button MQTT block on "Manual OFF button press detected" page.</br>
[New] Added Home button to System navigation bar.</br>
-------------------------------------------------</br>
[2023-01-09] v21.43 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Changed] mqtt.block_mode defaulting to 1 after trigger mqtt.block_active.</br>
[Changed] when mqtt.block_mode IS defaulting to 1, push this state to GUI.</br>
-------------------------------------------------</br>
[2023-01-09] v21.42 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Shutdown response now working (I think)</br>
[NEW] added a button on the shutdown detection section</br>
-------------------------------------------------</br>
[2023-01-08] v21.41 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] small fix for shutdown response improvement.</br>
[Changed] Improved the System Check layout.</br>
[Add] Label "Max MQTT-commands per day" now shows actual messages from current day.</br>
-------------------------------------------------</br>
[2023-01-08] v21.40 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Shutdown response improvement.</br>
</br>
-------------------------------------------------</br>
[2023-01-07] v21.39 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Shutdown response and popups now working again.</br>
</br>

-------------------------------------------------</br>
[2023-01-07] v21.38 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Crash/Freeze after turning on/off toggle  .</br>
</br>

-------------------------------------------------</br>
[2023-01-06] v21.37 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[-] continued with var renaming.</br>
[New] Linking RTC roomtemperature setpoint to nightreduction roomsetpoint</br>
</br>
-------------------------------------------------</br>
[2023-01-05] v21.36 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] fixed a small bug after renaming.</br>
[Changed] moved check of mqtt stuff of of system check, into load on boot MQTT.</br>
[Changed] Removed some of the automated node.warn() notifications</br>
</br>

-------------------------------------------------</br>
[2023-01-04] v21.35 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[FIX] SoftStart was using Night reduction water temp correction when switch was'nt enabled.</br>
[Other] started with new naming convention and code optimization.</br>
(old variables will be removed, please check all function switches)</br>
</br>

-------------------------------------------------</br>
[2023-01-04] v21.34 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Fix] Added to System Check: repair wrong settings MQTT caused by older versions.</br>
[Add] Added all night recduction variables to the System Check (debugging purposes).</br>
[Add] Added more SoftStart logging to main log when switch "extend logging is enabled".</br>
</br>

-------------------------------------------------</br>
[2022-12-25] v21.32 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[FIX] Typo in FILTER functions (2x) (Thanks sjampeter)</br>
</br>

-------------------------------------------------</br>
[2022-12-25] v21.32 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[FIX] mqtt signals being blocked too often. Power on/off, Operating Mode, Quiet Mode etc.</br>
</br>

-------------------------------------------------</br>
[2022-12-24] v21.31 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[Changed] Rework of block functionality. Rules around custom selection should be obayed now.</br>
[Changed] RTC function, help and layout. Select the action you want to allow, on/off or heatmode changing only.</br>
[Changed] RTC function, Booster renamed to incremental setpoints
[Changed] Scheduler function. Behaviour when other functions are activated.</br>
[Changed] SoftStart. Now a toggle is present if you want the function to restart every time after a defrost.</br>
[FIX] WAR function. TOP29 and TOP30 were flipped during import/export. </br>
[FIX] Heatpump information updated propperly after firmware change. (Thank you xtr3me)</br>
</br>

-------------------------------------------------</br>
[2022-12-15] v21.30 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[FIX] RTC on/off function. When no OperatingMode is stored, it would default back to Mode 0 (heat only)</br>
</br>

-------------------------------------------------</br>
[2022-12-13] v21.29 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[NEW] Bypass Softstart Quietmode during defrost.</br>
[Obsolete] RTC Booster fixed now. Use the switch on RTC dashboard to enable (default off).</br>
[Obsolete] Removed layout from dashboard Hardware configuration (was for testing only)</br>
[FIX] Nightreduction room temperature / Current status --> Manual on/off now working again.</br>
[FIX] Removed log message (@startup) "Function - SoftStart Quietmode: OFF (restored to level 0)". (the restored part)</br>
[FIX] Unneeded pumpspeed MQTT command (When enabled at a restart).</br>
[Moved] Moved Function reduce pump speed to Settings page. Layout now improved.</br>
[Changed] Replaced defrost toggle with button. Color status of button Red --> defrosting.</br>
[FIX] RTC Room actual temperature didn't update frequently.</br>
[Add] Added to System Check: RTC Booster, Function pump speed, MQTT-block settings and manual on/off settings.</br>
</br>

-------------------------------------------------</br>
[2022-12-12] v21.28 beta (experimental). Changes:</br>
-------------------------------------------------</br>
[ENHANCEMENT] Added toggle for NightReduction RoomSP in Scheduler screen.</br>
</br>
