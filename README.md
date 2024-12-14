# Playing games with SteamCMD (on linux)
You do not need Steam (with Chromium bloat) to play games. This really does not work too well, but it's a start.
This guide _may_ work on Windows as well, however I don't have the motivation to figure it out.
Pull requests for clarification accepted.

## What doesn't work
- VAC
  - Does not work due to unknown reasons. Probably something to do with signatures mismatching.
- Overlay
  - Since the switch to a web-based overlay, this will also not work.
  - This could have worked previously with the VGUI overlay, however I only found out about this too late, there is now no VGUI overlay to return to.
- Shortcuts (non-steam apps)
  - Cannot be launched, even with their internal AppID or GameID.
  - Cannot be created or deleted.
  - Dev note: A message does appear when logging in: `LoadShortcuts: loading shortcuts from: /home/onni/.local/share/Steam/userdata/304570273/config/shortcuts.vdf`
- Proton
  - Proton does not work at all.
  - Games will not attempt to launch with Proton, and will instead directly use system WINE.
  - It might be possible to launch proton games outside of SteamCMD, and specify the correct environment so they will work with SteamCMD.
    - Launch one of these games from Steam and dump its environment and full launch command. Execute this command outside of Steam, with SteamCMD running instead of Steam.
- Cloud?
  - It's unknown if this syncs cloud saves before launch and after exit.
  - You may need to do this manually (instructions TODO)
- Game Streaming (to/from)
  - Not implemented.
- Game clipping
  - Not implemented.
- Achievements?
  - Unknown if they work.
- Remote Client
  - Can't see remote clients or act as a remote client.

## What does work
- Remote Download
  - Instead of having to use commands to manage downloads, you can login to the Steam website and look at your own profile's game list to download and install games
- Friends List
  - You can use the [friends list](https://steamcommunity.com/chat/) from your browser, upon which you will appear online and can invite (and join?).
- Launching native games
- Downloading games
- An extremely minimal footprint, 62MB of RAM, 0% CPU, and does not reserve GPU resources. 
 
## Usage
You will need SteamCMD and Steam. Start both at least once, so we have all the necessary files.
The process is not very user friendly.

### Step 1: Start Steam
We need some files from a regular Steam install, so we need to wait for Steam to download and install client updates.

### Step 2: Start SteamCMD
SteamCMD needs to update at least once to create its directory tree. Run it once to download any client updates.

### Step 3: Copy necessary files
You absolutely need to copy (or symlink):
- `.steam/steam/ubuntu12_32/reaper` -> `.steam/steamcmd/linux32/reaper`
- `.steam/steam/ubuntu12_32/steam-launch-wrapper` -> `.steam/steamcmd/linux32/steam-launch-wrapper`

These files are necessary for launching any games.
Optionally, you may copy:
- `.steam/steam/ubuntu12_32/steamservice.so` -> `.steam/steamcmd/linux32/steamservice.so`

This _should_ make VAC games work, however currently it does nothing.
### Step 4: Starting SteamCMD the correct way
Now that we've configured the files correctly, we now only need to start SteamCMD properly.
Run `steamcmd -globaluser`. Ensure Steam is not running prior to running this command.
After this, you may `login <username> <password> <steam_guard_code>`.
With future logins, you can just run `login <username>` and Steam will use cached credentials.

### Step 5: Gaming.
We're now ready to start gaming.
You'll need the AppID of the game(s) you want to play, and the respective launch option.
Go to SteamDB, find your game and copy down its AppID.
Then, go to the Configuration section, and look at the launch options.
You'll need to use logic here to figure out your launch option, so look at the option's OS, Arch, Description and other info.
Once you've found it out, the title row of the launch option contains a number. Note that number.

Now let's craft the command:
`app_launch <appid> <launch option>`

Now you should be happily playing, but if you get an "update required" error, you must update the game, either from your profile or with `app_update <appid>`

### Step 6: Done gaming
Once you're done, close your game and type in `quit`.
Note that cloud sync does not work (instructions TODO)

### Single command (with caveats)
You may also craft a command that combines all this together to launch a game: `steamcmd -globaluser +login <username> <password> +app_launch <appid> <launchopt>`
Note that the `<password>` bit is not necessary if you use cached credentials.
Additionally, the entire `+login <username> <password>` is invalid if using steam guard. 
SteamCMD also does not cache credentials properly, and will lose them often. SteamCMD will also, unfortunately stay running after the game exits so this can't be used for anything useful (to automate stuff or make shortcuts that launch SteamCMD in the background). Adding `+quit` will terminate SteamCMD while the game is still running, which will NOT work, since the IPC server will get terminated, and thus the game will panic. 
