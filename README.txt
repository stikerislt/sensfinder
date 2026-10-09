SensFinder server - version 0.5.14
==================================

A Counter-Strike 2 practice server that finds the mouse sensitivity you aim best with.
Players join, type !search in chat, play about 30 minutes of short aim tasks against
bots, and get a verdict (CHANGE / KEEP / INCONCLUSIVE) with a sensitivity range.
Up to 5 players at once, each in their own sealed room.

This zip contains only the SensFinder parts. You also need a CS2 dedicated server with
Metamod:Source and CounterStrikeSharp installed (step 1-2 below).


WHAT IS IN THIS ZIP
-------------------
  game/csgo/addons/counterstrikesharp/plugins/SensFinder/SensFinder.dll   the plugin
  game/csgo/cfg/sensfinder.cfg                                             server settings
  README.txt                                                               this file

The folders match the server's own layout: copy the "game" folder over the server's
"game" folder and both files land in the right place.


WHAT YOU NEED
-------------
  - A CS2 dedicated server, Windows or Linux: a rented one, or your own (SteamCMD app 730).
  - Metamod:Source 2.x for CS2 (dev build):   https://www.sourcemm.net/downloads.php?branch=dev
  - CounterStrikeSharp, latest release:        https://github.com/roflmuffin/CounterStrikeSharp/releases
      first install: the "with-runtime" file for your server's OS (windows or linux).
  - 15 player slots for 5 players (each player needs 1 slot + 2 bots). Fewer slots work,
    the plugin then lets fewer people in: 9 slots = 3 players, 12 = 4, 15 or more = 5.
  - For a server people can join over the internet: a Steam Game Server Login Token
    (app 730) from https://steamcommunity.com/dev/managegameservers and UDP port 27015 open.

Tested with: CS2 1.41.8.9, Metamod 2.0 dev, CounterStrikeSharp 1.0.375 (Windows) and
1.0.376 (Linux). After every CS2 update you may need newer Metamod/CounterStrikeSharp
builds - see TROUBLESHOOTING.


SETUP
-----
1. Metamod. Extract it into game/csgo/ so that game/csgo/addons/metamod/ exists. Then open
   game/csgo/gameinfo.gi, find the SearchPaths section and add this line directly ABOVE
   the existing "Game csgo" line (same indentation):

       Game    csgo/addons/metamod

   Many rented-server panels have a one-click Metamod / CounterStrikeSharp install that
   does steps 1 and 2 for you.

2. CounterStrikeSharp. Extract the with-runtime release into game/csgo/ so that
   game/csgo/addons/counterstrikesharp/ exists.

3. SensFinder. Copy the "game" folder from this zip over the server's "game" folder.

4. Launch options (in a rented panel: the "startup parameters" / "command line" field):

       -dedicated -console +game_type 0 +game_mode 1 +map de_dust2 +exec sensfinder.cfg -maxplayers 15

   add  +sv_setsteamaccount <your token>  for an internet server.
   game_type 0 / game_mode 1 (competitive) is the mode SensFinder is tested in.

5. The map. SensFinder needs its own arena map from the Steam Workshop:
       https://steamcommunity.com/sharedfiles/filedetails/?id=3810993736
   Once the server is running, type in the server console:

       host_workshop_map 3810993736

   The server downloads it (first time only) and switches to it; players' games download
   it from Steam automatically. If your panel has a "Workshop map ID" field, put
   3810993736 there instead. Do not put +host_workshop_map on the command line itself -
   it hung the server on start in testing.

6. Optional: change the server name or add a password in game/csgo/cfg/sensfinder.cfg
   (hostname "..." / add a line  sv_password "..." ).


CHECK IT WORKS (server console)
-------------------------------
  meta list            must list CounterStrikeSharp
  css_plugins list     must show  "SensFinder" (0.5.14)  as LOADED
  css_sf_lanes         second line: "player cap: 5 humans (15 slots, up to 10 bots)"
                       and 10 bots "parked"

  In addons/counterstrikesharp/logs/ the plugin writes one line per map:
    "Cvars: bot_stop, bot_dont_shoot, sv_infinite_ammo, weapon_accuracy_nospread all hold"
  If it says "did NOT take" instead, the server still works, but bullets have their normal
  random spread.

Do not let players in until css_plugins list shows SensFinder LOADED. Without the plugin
the bots walk around with guns and fight.


HOW PLAYERS USE IT
------------------
  1. Join:  connect <server address>   (no launch options, nothing to install)
  2. In chat, once:   !setup     two questions: mouse DPI and current sensitivity
  3. In chat:         !search    starts the run (about 30 minutes)
  4. The sign on the wall in front of you says what to do. When it asks for a new
     sensitivity, press ~ (console) and paste the line it shows, for example:
         sensitivity 0.85; css_sens_now 0.85
     The server cannot change your sensitivity itself - this line sets it AND tells
     the plugin. Trials start by themselves when it matches.
  5. At the end you get the verdict and a range in chat. Validate it in deathmatch.

  Other chat commands:  !help   !menu   !sens_stop (stop)   !solo mflick (practice one task,
  nothing recorded)   !sf_report (re-show your stored result)

  Each player's results are saved on the server in
  game/csgo/addons/counterstrikesharp/plugins/SensFinder/data/<SteamID64>/


SERVER CONSOLE COMMANDS
-----------------------
  css_sf_lanes            who plays in which room, bot positions, the player cap
  css_sf_lanes probe      checks the map: a bot must stand on the floor in every room
  css_sf_pinmovers 0|1    1 = freeze running bots too (older behaviour), 0 = default


TROUBLESHOOTING
---------------
  "Unknown command 'css_plugins'" or bots roaming and fighting
      The plugin is not loaded. Usually a CS2 update:
      - "meta list" unknown too: the update overwrote gameinfo.gi - redo setup step 1.
      - meta works but no CounterStrikeSharp: install a CounterStrikeSharp release made for
        the new CS2 version (these can take a few hours to a day after a CS2 patch).
      Meanwhile: bot_quota 0; bot_kick   removes the bots; a restart brings them back.

  "Your client is out of date" / "server is out of date"
      Update the side that is behind: restart Steam for your game, run the panel's game
      update (SteamCMD app_update 730 validate) for the server. Compare with "version"
      in each console.

  4th/5th/6th player gets "server is full"
      The plugin allows 1 player per 3 slots (max 5). Raise the slots. SourceTV/GOTV also
      takes a slot - turn it off or add one.

  Local server on the same PC will not start ("Access is denied" for cs2.exe)
      FACEIT Anti-Cheat (or similar) is running and blocks a second cs2.exe. Close CS2 and
      fully exit the anti-cheat from the tray, start the server, then start CS2.

  Running bots look slightly jittery
      Known, cosmetic, does not affect scoring.


SAFETY
------
  This is a practice server. Never use these files on matchmaking, Premier or any server
  you do not run yourself. The plugin turns sv_cheats OFF and handles the bot settings
  itself, so players cannot use noclip/god/give.


FILE CHECKSUMS (SHA-256)
------------------------
  SensFinder.dll    1CDBE4ADA4908CEC730987393E3D4D5FEE7E03111610C1F1E53B281C8A4892F1
  sensfinder.cfg    81C6885765B76F588788CC277D44F24073240C60870C11AA4483E05E220629F7
