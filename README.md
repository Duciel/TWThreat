# TWThreat v1.3.0 (octo-addons fork)

Threat meter for OctoWoW and other servers that implement the Turtle WoW threat
API. Fork of [MarcelineVQ/TWThreat](https://github.com/MarcelineVQ/TWThreat)
with a working **Healer Master Target** relay.

Requirements: in a `party` or `raid`, attacking `elite` creatures or `bosses`.
The numbers come from the server, not from the combat log, so the server must
answer the `TWT_UDTSv4` request. OctoWoW does. `/twtdebug` shows the incoming
`PACKET` lines if you want to check.

## Installation

In the launcher, choose **Add Addon from Git** and use:

```text
https://github.com/octo-addons/TWThreat.git
```

The addon should be installed as `Interface/AddOns/TWThreat`.

## Using it as a healer

The server only reports threat for the mob **you** have targeted. While you
target a party member to heal, the meter has nothing to show. Two ways around
that:

**1. Keep the mob targeted and heal without targeting.** Use click-casting
unit frames (Puppeteer, pfUI click casts) or mouseover macros, and `/assist`
the tank at the pull. The meter then works exactly as for everyone else and
needs nothing from anyone.

**2. Healer Master Target (relay).** Pick a group member, normally the tank.
Their client relays the threat table of *their* target to you, so your meter
stays filled no matter whom you target.

- Target the tank and type `/twthmt`, or `/twthmt Tankname`, or use the
  **From Target** button in the settings window.
- The tank must run this fork too. If they do, you get "Healer Master Target
  set to ..." and they see "Relaying threat to ...". If nobody answers within
  a few seconds you are told so.
- `/twthmt clear` removes it. It is also dropped when the tank leaves the
  group. Bars clear themselves when the tank stops fighting an elite.
- The relay adds one addon message per server packet on the tank's side, about
  two per second, only while at least one healer has asked for it.

Upstream shipped the button but not the code behind it: requests were sent and
never answered, and the relay sender would have errored on a missing field.
This fork implements the handshake, the relay and the clean-up.

## Sharing threat with the group

On top of the Healer Master Target relay, every client shares the threat table
of **its own target** with the group, using the same `TWTRelayV2` message and
without any request. This is on by default and toggled with `/twt share`.

- Healers are not affected: their meter still only reads the relay of their
  master target.
- Nameplate addons such as pfUI-ThreatPlates read every relay, so each player
  sees threat on all the mobs the group is targeting, not only on their own
  target.
- Traffic stays low: a client skips its relay when another group member
  relayed the same mob during the last update (one message per mob, about one
  per second), and nothing is sent outside of a party or raid.
- Relays always go to the right channel: `RAID` while in a raid, `PARTY`
  while in a party, picked at send time (a party converted to a raid no
  longer keeps sending on the old channel).

## Commands

- `/twt` - command list; `/twt show`, `/twt hide`, `/twt toggle`
- `/twt tankmode`, `/twt skeram`, `/twt who`
- `/twtshow` - force the main window visible
- `/twtdebug` - toggle packet debug output
- `/twthmt [name|clear]` - healer master target
- `/twt share` - share your target threat with the group (on by default)

If the window is lost off screen or tiny, this puts it back in the middle at
full size:

```text
/run TWTMain:ClearAllPoints() TWTMain:SetPoint("CENTER",UIParent,"CENTER",0,0) TWTMain:SetScale(1) TWTMain:SetAlpha(1) TWTMain:Show()
```

## Changes

### 1.3.0-octo2
- Share threat with the group: every client relays the threat table of its
  target with `TWTRelayV2`, without a `/twthmt` request, so that nameplate
  addons can show threat on all the mobs the group is fighting.
  Duplicates are skipped when another member already relayed the same mob.
  `/twt share` toggles it (on by default).
- Relays pick `RAID` or `PARTY` at send time instead of reusing the channel
  cached at the last target change.

### 1.3.0-octo1
- Healer Master Target works: request, confirmation, relay and removal are all
  implemented, with a timeout message when the chosen player has no compatible
  addon.
- Relay sends one compact message per packet instead of one per player, and no
  longer errors on a missing field.
- Relay recipients are tracked as a set, so leaving members no longer leave
  holes in the list.
- `/twthmt` command.

### 1.3.0
- Resistant to losing threat display if an addon is interfering with your targeting.
- Click to select TankMode targets directly (no need for target of target) if you have SuperWoW.
- Visually smoother threat bar updates.

### 1.2.3 unofficial balake version
- Queries threat when the target is in combat even if player is not in combat or did not do any threat on the target yet.
- Standarized update speed across specs.
- TANK MODE IS UNTESTED. If you like using that please volunteer to tell me if it's bug-free or not.

### v1.2.3
- Fixes for TW patch 16.0

### v1.2.2
- Bar animation fixes.
- TankName bug fix.
- Configurable aggro warning threshold.

### v1.2.1
- Scale TankMode window with main window.

### v1.2.0
- Threat api v4, older versions don't work anymore.
- [pfUI](https://github.com/shagu/pfUI) integration for target frame glow and threat percentage.
- tankMode is back !
- New threat warning sound.
- New icons for settings/lock/close buttons.


### v1.1.0
- Threat api v3, older versions don't work anymore.
- Summoned creatures show the right threat values.
- Settings for in and out of combat frame opacity.


### v1.0.2
- Revamped settings window with tabs.
- New setting: frame scale.
- Enabled bar animation.
- Disabled Tankmode (still buggy).

### v1.0.1
- Faster way to get guid
- Tankmode window is now movable and stickable to top/bottom/left/right of main window.
- Tankmode window has test data in config mode.
- Better colors for Tankmode targets and current target is highlighted.
- Window title is more stable for healers.
- Removed bar animation (maybe only temporary).
- Clear bars if creature dies, for healers.
- Fix potential bug where Pull Agro At bar was not shown.
- Fix tankthreat bug when tank is not top in threat list.

## Features
- Threat, threat to pull aggro, TPS, % Max, % to pull
- Target glow based on threat for default game target frame
- Target threat percent indicator for default game target frame
- Various options including bar height and font style
- Full Screen Glow on high threat percentage
- Aggro Warning Sound on high threat percentage
- Customizable labels for TPS, Threat, Percent Max
- Tankmode !

## Slashcommands
- `/twtshow`
- `/twt show`
- `/twt hide`
- `/twt toggle`
- `/twt tankmode`

## Settings
![settings](https://imgur.com/xBUdh2C.png)
![settings](https://imgur.com/MNdyp7w.png)
![settings](https://imgur.com/IT9wFXp.png)

### Glow
Shows a colored aura ranging from Green/Yellow/Red on the Default UI's unit frame.<br>
The color of the aura is based on your current threat threshold:<br>
- green - yellow for 0% - 49%<Br>
- yellow - red for 50% - 100%<br>

![glow](https://imgur.com/7rZt20N.png)
<br>

### Full Screen Glow
Red glowing screen edges when your threat is above 80%.<Br>
This feature is disabled in Tank Mode.<br>
<br>

### Aggro Warning Sound
Plays a warning sound when you reach 99% threat.<br>
This feature is disabled in Tank Mode.<br>
<br>

### Percentages
Shows threat percentages on the default target frame.<Br>
![percentages](https://imgur.com/jTlY6Gj.png)
<br>

### Combat Show
Automatically shows the window when combat begins.<br>
<br>

### Combat Hide
Automatically hides the window when combat begins.<br>
<br>

### Tank Mode
Shows an additional window with targets that you are currently tanking and the name and threat
percent of the next player in threat list for quick target swap (this mode disables
Full Screen Glow and Aggro Warning Sound).<br><br>
![tankmode](https://imgur.com/3eR28Zp.png)
<br>

### Show Name/TPS/Threat/% Max Label Row
Shows or hides the row above the threat bars.
<br>

### Show TPS Column
Shows or hides the TPS column.
<br>

### Show Threat Column
Shows or hides the Threat column.
<br>

### Show Percent Column
Shows or hides the Percent Max column.
<br>


## Contributors and testers
[Henry](https://armory.turtle-wow.org/#!/character/Henry), [Laughadin](https://armory.turtle-wow.org/#!/character/Laughadin), 
[Faralynn](https://armory.turtle-wow.org/#!/character/Faralynn), [Draxer](https://armory.turtle-wow.org/#!/character/Draxer) <BR><BR>

## Support
If you like my work consider buying me a coffee !<br>
https://ko-fi.com/xerron

[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://paypal.me/xerroner)
