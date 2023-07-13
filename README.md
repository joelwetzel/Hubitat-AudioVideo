# Hubitat-AudioVideo
This repo is a single place to document my experiments in controlling AV equipment from Hubitat and related systems.

## Context
I have an extensively developed Hubitat installation in my home.  I have it connected up to HomeKit and to Alexa.  Control of the devices generally happens through 3 mechanisms (from highest usage to lowest):

1. Automations that happen on their own, based on time of day, motion sensors, or presence sensors.
2. Commands we give to Alexa.  ("Alexa, turn on the family room fan.")
3. Using Homekit dashboards on our iPhones and iPads.

## Goals
I want to integrate my home theatre with Hubitat/HomeKit/Alexa.  For example, I want to be able to say "Alexa, turn on Apple TV".  The following should happen:

- The tv screen should turn on
- The Apple TV should turn on
- The HDMI switch should change to the input for the Apple TV

Other similar commands:
- "Alexa, turn on cable"
- "Alexa, turn on Nintendo"
- "Alexa, tv off"

  ## Subgoals
  To make this happen, I broke the work down into a number of subgoals, that could be developed/integrated/configured/tested individually.

  1. TV power needs to be controllable
  2. AppleTV power needs to be controllable
  3. Ability to control an HDMI switch from Hubitat
  4. Expose the HDMI switch to HomeKit
  5. Configure HomeKit with scenes for the AV equipment
  6. Integrate with Alexa routines
 
### 1. TV power controllable

I have an LG C1 OLED. I haven't found any way to control it from Hubitat, but it does have a HomeKit integration.  It shows up in HomeKit as an "Input Controller".  It has a power button and an input selector.  In my case, I care only about tv power.  I use an HDMI switch with a single output hdmi cable to the tv, so I always want the tv set to that input.

Note that this puts a requirement on where "scenes" can be defined in my system.  They'll have to be defined in HomeKit, in order to control the tv.

### 2. AppleTV power needs to be controllable

This is surprisingly difficult.  Shockingly, Apple doesn't have this built into HomeKit.  Even weirder, your AppleTV devices DO show up in HomeKit, but are only useful for play/pause.  You can't turn the device on and off.

Different people have tried different api integrations over time.  Some of these have stopped working eventually, as Apple changes things in tvOS.  But, I found a great starting point at https://github.com/HuJohner/homebridge-appletv.

HuJohner had built a homebridge plugin that could control power on AppleTV, as well as some other commands.  I did make my own fork at https://github.com/joelwetzel/homebridge-appletv, and added a little state-caching logic, to improve reliability.

HuJohner's code requires the HomeBridge to have PyATV installed.  This can be tricky, but we figured out the process here:  https://github.com/HuJohner/homebridge-appletv/issues/1

Most of my HomeBridge is done from Raspberry Pis, but since I was iterating this code, I set up a dev HomeBridge on my dev iMac.

- HomeBridge on Mac install instructions were here:  https://github.com/homebridge/homebridge/wiki/Install-Homebridge-on-macOS
- I ran an "npm install" and "npm run build" and "npm link" in the local repo folder for homebridge-appletv.  This automatically made the plugin show up in my dev HomeBridge.  Those instructions came from: https://github.com/homebridge/homebridge-plugin-template
- After that, you have to follow the instructions to pair an AppleTV and update the Homebridge config for it: https://github.com/HuJohner/homebridge-appletv#retrieving-credentials





