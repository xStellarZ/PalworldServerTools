# Overview
PalworldServer Tools is a PowerShell script used for administering dedicated Palworld servers.
Currently this is a tool without a GUI designed to run when called by task scheduler, sensors or other scripts.
A GUI will hopefully be developed down the track, I need to learn how to create these first!.

**Note:**
This is an early version of this script and will likely have some bugs.<br>
If you notice anything funky, would like additional functionality or have any questions, please raise an [issue](https://github.com/shupershuff/PalworldServerTools/issues).<br>
Please also see the [Planned Changes](#planned-changes-features--fixes) section below.<br>

## Use Case:
Script is designed to be used with Task Scheduler (for updating/launching server)<br>
Can also be used with HASS.Agent (or other tools) to issue and capture the results of RCON commands.<br>
To use, call the script with launch parameters eg "& '.\PalworldServerTools.ps1' -save"<br>
<br>
Note: It's early days, I'll look at developing a front end, auto refreshing CLI so you can server status (players online, version, update status etc).<br>
You can also review the log file for what's happening when you're running commands that don't have a lot of CLI output.<br>

## Features:
**RCON Calls**<br>
You can run RCON commands to retrieve data or initiate server actions.<br>
Retrieve Server Info (or specifically Servername or server Version provided by info), Players online (including Player Count).<br>
<br>
Issue Server Commands:<br>
Schedule a shutdown (with a message), shutdown immediately, force a save, broadcast a message.<br>
Note: Kick and Ban not implemented yet, Game bug not allowing messages with spaces to be sent.<br>

**Shutdown Reminders**<br>
Want your server to restart daily but want to give users some warning?<br>
When using the script to launch the server again it will schedule a shutdown (time to shutdown is configured by you in config.xml)<br>
NOTE: An improved version of this feature will be in release in v1.1.0 which will enable multiple reminders in an increasing frequency when server is about to restart.<br>

**Daily Settings Switcher**<br>
Run daily events on your server! That's right you can have different settings for each day of the week.<br>
Simply create the custom config (.ini) files and specify which day should load them. See [Setup Information](#setup-information) for more details.<br>
Example Idea below:<br>
![image](https://github.com/shupershuff/PalworldServerTools/assets/63577525/6adabee0-8a00-422d-a873-7ff469bc871f)<br>

**Server Admin**<br>
You can check for updates (manually and at server launch), initiate updates, start and stop the server.<br>

Note: Kick and Ban not implemented yet, will be in v1.1.0<br>
Note: Auto Update feature not implemented yet but plan to have a feature to regularly check for new versions and if so force a restart and update.<br>

**Backups**<br>
Not available yet, coming soon in v1.1.0. This will sort backups into hourlies, dailies and monthlies.<br>
Will also supply a guide on how you can have your backup folder sync to the cloud, for free, essentially the same instructions I wrote for Valheim: https://www.reddit.com/r/valheim/comments/lxjdu7/guide_auto_cloud_backup_your_valheim_worldplayer/?rdt=33007

**Useful for those with Home Assistant**<br>
For Home Assistant users, Using HASS.Agent on your windows PC you can setup PowerShell based sensors/commands to retrieve/send data to the server.<br>
You can setup a card like this and share it with your players/mods for basic remote control functions without needing to give them the RCON password.<br>
![image](https://github.com/shupershuff/PalworldServerTools/assets/63577525/6c331e8c-e8a2-4fbb-a685-b7bff4a3b313)<br>

# Setup Information #
**Download Script Release, ARRCON.exe and SteamCMD**<br>
1. Download ARRCON from https://github.com/radj307/ARRCON and place the .exe somewhere on your PC (anywhere is fine).
2. Download Latest PalworldServerTools Release from my Github [here](https://github.com/shupershuff/PalworldServerTools/releases/latest).
3. Extract PalworldServerTools to any folder you want, I would recommend putting the files in the same folder as palserver.exe
4. TBC SteamCMD steps

**Optional - Configure your .ini files**<br>
If you want the script to load different server settings depending on what day it is, you'll need to create the .ini files. Otherwise you can skip this section.<br>
Please note that the way that this feature works if the script is run with the -startthemed parameter, it will overwrite PalworldSettings.ini with the contents of your custom .ini files.<br>
1. Copy the PalWorldSettings.ini from "<SERVERPATH>\Pal\Saved\Config\WindowsServer\" to "<SERVERPATH>\Pal\Saved\Config\WindowsServer\CustomSettings".<br>
   a. Note that if you want to store the custom settings in a different location (eg a folder path that's sync'd to the cloud), this is fine but you must specify the location in the config.xml file (covered in the next section).<br>
2. Rename the file you just copied to the custom folder to the name of the theme eg "Monday Funday.ini"
3. Edit this file to have the configuration options you want. See [Palworld Documentation](https://tech.palworldgame.com/optimize-game-balance) on the options available.
4. Ensure you mention the names of the .ini files you want to use in config.xml (covered in the next section). If you don't want to use this feature then set each day in config.xml to the same value as NormalSettingsName.

**Configure the XML file**
Section TBC but the config.xml file should mostly be self explanatory with the descriptions above each config option.<br>
- HostIP - IP of the Server.
- GamePort - Port that the server uses for connections. Default 8211.
- CommunityServer - Set to True if you want the server to be visibile in the community servers list
- RCONPort - Connection Port for RCON
- RCONPass - Administrator Password for RCON
- SteamCMDPath - Path to the folder steamcmd.exe sits in.
- ARRCONPath -  Path to the folder ARRCON.exe sits in.
- ServerPath - Path to the folder that palserver.exe sits in.
- ThemeSettingsPath - Path to where your custom.ini files live. Leave blank for the default path to be used (<SERVERPATH>\Pal\Saved\Config\WindowsServer\CustomSettings\")
- NormalSettingsName - Name of the file that you want your standard server settings (.ini file) to load from. Default "Normal Settings"
- AutoShutdownTimer - Used for restarting server. Time that server should wait until shutdown to give players a bit of notice.
- AutoShutdownMessage - This XML config will be removed in v1.1.0
- Monday..Sunday - Specify what settings (.ini) file you want to load from for each day of the week. Default "Normal Settings"
Currently there is some basic validation to check if *some* of the info in the config file is valid.<br>

**Testing Script**<br>
You'll want to test the functions first and ensure there aren't any errors due to misconfiguration.<br>
1. In Windows Explorer, browse to the folder where the script is sitting. In the Folder Path/Address bar type in powershell and press enter. This will open a Powershell terminal set to your scripts directory.<br>
![image](https://github.com/shupershuff/PalworldServerTools/assets/63577525/ef656764-1a45-4f6f-83ae-03a861d118a3)<br>
2. Enter in "& '.\PalworldServerTools.ps1' -info" to test (info as an example parameter). Obviously change the parameter for any other things you want to test.
3. If there are issues, you can review the log file in the folder where the script lives for further information.

**Configure Task Scheduler**<br>
If you need help or want to see examples, see [PalworldTools Task Scheduler Documentation](docs/TASKSCHEDULER.md).
## Usage ##
**Launch Parameters available**<br>
At this stage this script is mostly a backend tool so these Parameters are the main means of performing tasks.<br>
Parameters utilizing RCON:<br>
- -Info - RCON request to return server Info.
- -Version - Used to specifically return server version text provided by -info
- -ServerName - Used to specifically return server name text provided by -info
- -ShowPlayers - Show the names of all logged in players. TBC FROM v1.1.0 this will instead show a comma separated response, the exact response you'd get from RCON.
- -ShowPlayerNamess - TBC FROM v1.1.0 ONWARDS Show the names of all logged in players.
- -ShowPlayerCount - Number of players online
- -Shutdown - Used to initiate a planned shutdown. Can be used in conjunction with -shutdowntimer and -shutdownmessage. The script will also initiate a broadcast message notifying of shutdown when it's imminent.
- -ShutdownTimer - Optional parameter. Used to specify how many seconds until shutdown should occur. If not specified it will use the value from the config file.
- -ShutdownMessage - To use this use -broadcast "message". Used to customise the shutdown message.
- -Broadcast - To use this use -broadcast "message". Sends a message to all players. Note that as of the time of writing, it's not possible to send messages with spaces in them (game limitation).
- -DoExit - Schedule an immediate shutdown of the server.
- -kick (or -kickplayer) - TBC FROM v1.1.0 ONWARDS Used to kick a player. Script allows you to specify either steam ID or playername to kick.
- -ban (or -banplayer) - TBC FROM v1.1.0 ONWARDS Used to ban a player. Script allows you to specify either steam ID or playername to ban. If specified player isn't online, script will warn you (in case you made a typo).
- -Save - Force server to save data.

Server Launch and misc Parameters:
- -StartNoTheme - Will check for updates and start the server using your "Normal" settings
- -StartThemed - Will check for updates and start the server using the 'themed' settings for whatever day it is. NOTE that this will overwrite PalworldSettings.ini with the contents of the .ini file you've specified in config.xml
- -NoUpdate - To be used in conjuntion with -StartNoTheme or -StartThemed so that server starts immediately without checking for updates.
- -UpdateOnly - Will launch the script to update the Palworld server.
- -UpdateCheck - Used to check if there's updates available (but not update) and return either "Version Up to date" or "Update Available"
- -TodaysTheme - This will display the name of the settings (.ini) that has been loaded
- -NoLogging - This disables entries being written to the log file. Useful if you want to prevent sensors from writing to the log file every minute.

Parameters you'll likely not need, but they're here if you need them:
- -ServerPath - Used to specify server path. Parameter only useful if you're hosting more than one server.
- -ThemeSettingsPath - Used to specify where custom ini files live. Parameter only useful if you're hosting more than one server and can be left blank to assume the default location.
- -LaunchParameters - Used to specify custom launch parameters if they differ from the default. Can be left blank.
- -HostIP - Used to specify server address/domain. Parameter only useful if you're hosting more than one server.
- -RCONPort - Used to specify RCON Port. Parameter only useful if you're hosting more than one server.
- -RCONPass - Used to specify RCON Password. Parameter only useful if you're hosting more than one server.

**Home Assistant Nerds**<br>
See [PalworldTools Home Assistant Documentation](docs/HOMEASSISTANT.md).

# FAQ #
- Nothing Yet!
- Not seeing something here? Go to [GitHub issues](https://github.com/shupershuff/PalworldServerTools/issues) and log a request, issue or question.

# Planned Changes, Features & Fixes #
- Automate Local Backups (hourlies for 1 day, dailies, weekly) for both level data and characters.
- Automated Server update checks
- Add a means of checking whether there's a script update available from GitHub.
- Front End CLI or better yet a proper GUI (would love help if you're any good with this sort of thing).
- Fix stuff that doesn't work :)
- Not seeing something here? Go to [GitHub issues](https://github.com/shupershuff/PalworldServerTools/issues) and log a request, issue or question.

**Known Issues**<br>
- Unable to broadcast any messages with spaces in them for broadcast or shutdownmessage. Appears to be a game bug.

# Usage and Limitations #
Happy for you to make any modifications this script for your own needs providing:<br>
 - Any variants of this script are never sold.
 - Any variants of this script published online should always be open source.
 - Any variants of this script are never modifed to enable or assist in any malicious behaviour including (but not limited to): Bannable Mods, Cheats, Exploits, Phishing, Botting.

# Thanks and Credit for things I stole #
- ARRCON - This tool makes use of another tool someone else on the internet made: https://github.com/radj307/ARRCON
- ChatGPT for helping with regex patterns.
- Google.com
- Thanks to Nintendo for releasing this game.
  <br>
  <br>
Tags for Google SEO (maybe): PalworldServerTools, Shuper, whyareyoureadingthesetags, Pal World, Server Administration, admin, RCON, stillreadingthesetags, palworld dedicated server, palworld server, didyouspotthespellingerrorinoneofthetags, powershell, therearenospellingerrorsorarethere, pocketable-monsters
