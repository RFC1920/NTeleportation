# RTeleportation
Local version of NTeleportation, renamed to avoid confusion with the one on umod.

Multiple teleportation systems for admins and players

Latest version: 1.0.88

[Download](https://code.remod.org/RTeleportation.cs)

Works with: Clans, Economics, ServerRewards, Friends, RustIO;

See note below about rteleportation.home permission, which is required for basic function.  This finally allows control over who may use the basic commands.

## WARNING!!!!

If you switch to the new name, you will have to setup your permissions again and copy your old config and data files to the new names, e.g.

  cp NTeleportationAdmin.json RTeleportationAdmin.json
  ...

You may be better off making the switch at the next wipe!

## Configuration

- UseFriends  - Check via Friends API that owner and player are friends
- UseClans - Check via Clans API that owner and player are in the same clan *(support only for the Oxide Clans.cs and perhaps RustIO::Clans - other clan plugins status is unknown)*
- UseTeams - Check via Rust teams that owner and player are friends
- UseEconomics - Use the Economics plugin to pay for teleports and/or pay to bypass cooldowns
- UseServerRewards - Use the ServerRewards plugin to pay for teleports and/or pay to bypass cooldowns
	- WipeOnUpgradeOrChange - If true, wipe homes in the event of the server detecting a new save
	- UsableOutOfBuildingBlocked  - Allows a player to teleport out of building blocked area (not into)  
- ForceOnTopOfFoundation - If true the player must set home on a foundation or floor.  If false, player can set a home anywhere not limited by another config (e.g. nterruptTPOnMonument)
	- AllowAboveFoundation  - Allows to set homes on Nth floor of a building when above owned/shared foundation (floor/ceiling).  If false, player must use home/sethome on a foundation (1st floor).
	- VIPCooldowns/VIPDailyLimits/VIPHomesLimits/VIPCountdowns:  

```json
{
	"Settings": {
		"ChatName": "<color=red>Teleportation</color>: ",
			"HomesEnabled": true,
			"TPREnabled": true,
			"TownEnabled": true,
			"InterruptTPOnHurt": true,
			"InterruptTPOnSafe": true,
			"InterruptTPOnCold": false,
			"InterruptTPOnHot": false,
			"InterruptTPOnHostile": true,
			"InterruptTPOnBalloon": true,
			"InterruptTPOnCargo": true,
			"InterruptTPOnRig": false,
			"InterruptTPOnExcavator": false,
			"InterruptTPOnLift": true,
			"InterruptTPOnMonument": false,
			"InterruptTPOnMounted": true,
			"InterruptTPOnSwimming": true,
			"InterruptAboveWater": false,
			"StrictFoundationCheck": false,
			"MinimumTemp": 0,
			"MaximumTemp": 40,
			"DefaultMonumentSize": 50.0,
			"CaveDistanceSmall": 40.0,
			"CaveDistanceMedium": 60.0,
			"CaveDistanceLarge": 100.0,
			"BlockedItems": {},
			"BypassCMD": "pay",
			"UseEconomics": false,
			"UseServerRewards": false,
			"WipeOnUpgradeOrChange": false,
			"AutoGenOutpost": false,
			"AutoGenBandit": false,
			"OutpostEnabled": false,
			"BanditEnabled": false
	},
		"GameVersion": {
			"Network": 2180,
			"Save": 180,
			"Level": "Procedural Map",
			"LevelURL": "",
			"WorldSize": 4000,
			"Seed": 7777
		},
```

```json
		"VIPCooldowns": {
			"<permission name1>": <limit1>,
			"<permission name2>": <limit2>,
			"<permission nameN>": <limitN>
		}
```

```json
		"Home": {
			"HomesLimit": 10,
			"VIPHomesLimits": {
				"rteleportation.vip": 20
			},
			"Cooldown": 120,
			"Countdown": 5,
			"DailyLimit": 100,
			"AllowCave": true,
```

```json
			"Pay": 0,
			"Bypass": 0
		},
		"TPR": {
			"Cooldown": 600,
			"Countdown": 5,
			"DailyLimit": 5,
			"VIPDailyLimits": {
				"rteleportation.vip": 5
			},
			"VIPCooldowns": {
				"rteleportation.vip": 5
			},
			"VIPCountdowns": {
				"rteleportation.vip": 5
			},
			"RequestDuration": 30,
			"OffsetTPRTarget": true,
			"BlockTPAOnCeiling": true,
			"CupOwnerAllowOnBuildingBlocked": true,
``` 

Multiple entries for different levels of vip can be created here.  The default and included entry is for rteleportation.vip.  Others added here will cause the plugin to register oxide permissions for them upon plugin reload.  After the permissions have been created, they can be assigned to oxide users or groups as desired.

If Pay is set for /home, /tpr, or /town, and Economics or ServerRewards is available, using these commands will withdraw the configured amount from their balance.

If Bypass is set for /home, /tpr, or /town|outpost|bandit, and Economics or ServerRewards is available, using these commands during a cooldown period will ask if the player wants to pay to bypass the cooldown.  Note that if you elect to bypass cooldown by paying for a /tpr, you will pay the bypass cost even if the target does not accept via /tpa.  Only after a successful /tpa and teleport will you pay the Pay cost, if configured.

This also requires the global setting BypassCMD.  This is a keyword to use for the bypass, e.g.:

- /town pay
- /home 1 pay

You must also set UseEconomics to true to enable this usage of the Economics plugin.  You may also set UseServerRewards to true to enable usage of the ServerRewards plugin.  If both are set to true, Economics will be checked first.

For the InterruptTPOnCold/Hot settings, be careful adjusting the default values for MinimumTemp and MaximumTemp.  The user will only display Cold/Hot between 0 and 40C.  Otherwise they will likely be confused.  However, negative values for MinimumTemp should be possible.  **NOTE**: If you want to actually change the defaults you need to set InterruptTPOnCold/Hot to true.  Then set the min/max temps.  This is true even if you want to NOT interrupt on cold/hot - in that case set the temps to some extremes that would not likely be met such as -30 and 100.

If InterruptTPOnHostile is true, and the player is considered hostile, /outpost and /bandit will be blocked, and the user will get a notice including how long until they will no longer be considered hostile per Rust.

If InterruptTpOnHurt is true, the teleport may still be interrupted when hot/cold even if InterruptTPOnCold/Hot are false.  This is because the player is taking damage when hot or cold.

The CaveDistance{Small/Medium/Large} settings are available to tweak distance from caves required when AllowCave == false inside of the Home/Town/TPR config sections.  Anything within those distances (from the player) should be blocked.

StrictFoundationCheck: Default false.  If set to true, perform an additional check to ensure that the player is at least near the center of a foundation or floor.  This is not run on tpa/tpr but is on sethome/home.

DefaultMonumentSize: This is required if InterruptTPOnMonument is set to true.  Many monuments do not present their size when queried - at least how we are currently doing it.  For those that do not, this will be the default distance required for using /home, etc.
## Permissions

	- rteleportation.home - /home, /sethome, /removehome
	- rteleportation.deletehome - /home delete & /deletehome  
	- rteleportation.homehomes - /home homes & /homehomes  
	- rteleportation.importhomes - teleport.importhomes  
	- rteleportation.radiushome - /home radius & /radiushome  
	- rteleportation.tp - /tp  
	- rteleportation.tpb - /tpb  
	- rteleportation.tpr - /tpr
	- rteleportation.tpconsole - teleport.topos & teleport.toplayer  
	- rteleportation.tphome - /home tp & /tphome  
	- rteleportation.tptown - /town  
	- rteleportation.tpoutpost - /outpost  
	- rteleportation.tpbandit - /bandit
	- rteleportation.tpn - /tpn  
	- rteleportation.tpl - /tpl  
	- rteleportation.tpremove - /tpremove  
	- rteleportation.tpsave - /tpsave  
	- rteleportation.wipehomes - /wipehomes  
	- rteleportation.crafthome - allow craft during home tp  
	- rteleportation.crafttown - allow craft during town tp  
	- rteleportation.craftoutpost - allow craft during outpost tp  
	- rteleportation.craftbandit - allow craft during bandit tp  
	- rteleportation.crafttpr - allow craft during tpr tp  

## Commands

### Chat

	- home add  NAME - Saves your current position as the location NAME. (alias sethome)  
	- home list - Shows you a list of all the locations you have saved. (alias listhomes)  
	- home remove NAME - Removes the location NAME from your saved homes. (alias removehome)  
	- home NAME - Teleports you to the home location.  
	- home NAME pay - Teleports you to the home location NAME, bypassing cooldown by paying from your Economics balance.  
	- tpr  - Sends a teleport request to the player.  
	- tpa - Accepts an incoming teleport request.  
	- tpc - Cancel teleport or request.  
	- town - Teleports yourself to town (if set).
	- outpost - Teleports yourself to Outpost (if set).
	- bandit - Teleports yourself to Bandit Town (if set).
	- town/outpost/bandit pay - Teleports you to town/outpost/bandit, bypassing cooldown by paying from your Economics balance.  
	- e.g. /town pay
	- tpinfo - Shows limits and cooldowns.  
	- tphelp - Shows help.

	Admin:  
	- tp  - Teleports yourself to the target player.  
	- tp   - Teleports the player to the target player.  
	- tp    - Teleports you to the set of coordinates.  
	- tpl - Shows a list of saved locations.  
	- tpl {name} - Teleports you to a saved location.  
	- tpsave  - Saves your current position as the location name.  
	- tpremove  - Removes the location from your saved list.  
	- tpb - Teleports you back to the place where you were before teleporting.  
	- home radius  - Find all homes in radius.  
	- home delete   - Remove a home from a player.  
	- home tp   - Teleports you to the home location with the name 'name' from the player.  
	- home homes  - Shows you a list of all homes from the player.  
	- home wipe - Removes all homes.  
	- town set - Saves the current location as town.  
	- outpost set - Saves the current location as Outpost. 
	- bandit set - Saves the current location as Bandit Town. 
	- wipehomes - Removes all homes.  

### Console

	- teleport.topos     - teleports player to position  
	- teleport.toplayer   - teleports player to targetplayer  
	- teleport.importhomes - imports homes from m-Teleportation  

## For Developers

```csharp
	Dictionary GetHomes(object playerObj) // param playerObj string/ulong userId  
	int GetLimitRemaining(BasePlayer player, string type) // param type: home, tpr, town  
	int GetCooldownRemaining(BasePlayer player, string type) // param type: home, tpr, town
```

## Work in progress
	- Nothing at this time

## Tutorials
	[SRT Bull RTeleportation Tutorial](https://www.youtube.com/watch?v=yXId_jCnup8)
## Credits

	- **Nogrod**, the original author of this plugin
	- **Loup-des-Neiges of [FR] Bestiaire.eu**, for helping and fundng this plugin
	- **CMEPTb**, Russian translation
