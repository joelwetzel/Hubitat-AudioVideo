# Hubitat - Adventures in Audio/Video
This repo is a single place to document my experiments in controlling AV equipment from a Hubitat installation.

This is an area of functionality that is less developed (by Hubitat and the community) than things like lights, locks, motions sensors, etc.  Hubitat supports some smart receivers, but not a lot else.

It's also only barely supported by the hardware makers.  Some TVs can connect to HomeKit, but open APIs are scarce.  Some AVRs allow connection over telnet.  Soundbars generally don't.  Streaming devices are hit-and-miss for integrations.

My efforts certainly don't solve it all, but solved my own use cases.  Hopefully it can provide inspiration so that either Hubitat or the community can develop a larger set of tools.

## Context
I have an extensive Hubitat installation in my home.  I have it connected up to HomeKit and to Alexa.  Control of the devices generally happens through 3 mechanisms (from highest family usage to lowest):

1. Automations that happen on their own, based on time of day, motion sensors, or presence sensors.
2. Commands we give to Alexa.  ("Alexa, turn on the family room fan.")
3. Using Homekit dashboards on our iPhones and iPads.

## Goals
I wanted to integrate my home theatre with Hubitat/HomeKit/Alexa.  For example, I wanted to be able to say "Alexa, turn on Apple TV".  The following should happen:

- The tv screen should turn on
- The Apple TV should turn on
- The HDMI switch should change to the input for the Apple TV

Other similar commands:
- "Alexa, turn on cable"
- "Alexa, turn on Nintendo"
- "Alexa, tv off"

  ## Subtasks
  To make this happen, I broke the work down into a number of subtasks, that could be developed/integrated/configured/tested individually.

  1. TV power needs to be controllable
  2. AppleTV power needs to be controllable
  3. Ability to control an HDMI switch from Hubitat
  4. Expose the HDMI switch to HomeKit
  5. Configure HomeKit with scenes for the AV equipment
  6. Integrate with Alexa routines
 
### 1. TV power controllable

I have an LG C1 OLED. I haven't found any way to control it from Hubitat, but it does have a HomeKit integration.  It shows up in HomeKit as an "Input Controller".  It has a power button and an input selector.  In my case, I care only about tv power.  (I use an HDMI switch with a single output hdmi cable to the tv, so I always want the tv set to that input.)

Note: This puts a requirement on where "scenes" can be defined in my system.  They'll have to be defined in HomeKit, in order to control the tv.

TODO: Try out this hubitat integration:  https://community.hubitat.com/t/port-lg-smart-tv-discovery-2012/12761/271

### 2. AppleTV power needs to be controllable

Repo: https://github.com/joelwetzel/homebridge-appletv

Weirdly, Apple does not have this built into HomeKit.  Even weirder, AppleTV devices DO show up as accessories in HomeKit, but are only able to play/pause.  The native integration has no way to wake up the Apple TV or put it to sleep.

Different people have tried and published different api integrations over time.  Some of these have stopped working eventually, as Apple changes things in tvOS.  But, I found a HomeBridge plugin that currently works at: https://github.com/HuJohner/homebridge-appletv.

HuJohner's plugin can control power on AppleTV, as well as some other commands.  I made my own fork at https://github.com/joelwetzel/homebridge-appletv, and added some state-caching logic, to improve reliability. (I've sent it as a PR to HuJohner, but until the PR is accepted, I published my own npm package.)

HuJohner's code requires the HomeBridge system to have PyATV installed.  This can be tricky, but we figured out the process here:  https://github.com/HuJohner/homebridge-appletv/issues/1

Most of my HomeBridge is done from Raspberry Pis, but while I was iterating this code, I set up a dev HomeBridge on my dev iMac.

- HomeBridge on Mac install instructions were here:  https://github.com/homebridge/homebridge/wiki/Install-Homebridge-on-macOS
- I ran an "npm install" and "npm run build" and "npm link" in the local repo folder for homebridge-appletv.  This automatically made the plugin show up in my dev HomeBridge.  Those instructions came from: https://github.com/homebridge/homebridge-plugin-template
- After that, you have to follow the instructions to pair an AppleTV and update the Homebridge config for it: https://github.com/HuJohner/homebridge-appletv#retrieving-credentials

TODO: I'd like to control directly from Hubitat, instead of HomeBridge.
      I am working on an npm bridge here: https://github.com/joelwetzel/http-appletv-bridge
      I could extend this example to be my hubitat device driver:  https://github.com/hubitat/HubitatPublic/blob/master/examples/drivers/httpGetSwitch.groovy

### 3. Ability to control an HDMI switch from Hubitat

I made a small microcontroller device to piggyback on the HDMI switch.  Then, I made a Hubitat driver to talk to it.

Repo and documentation:  https://github.com/joelwetzel/Hubitat-4KMX42-H2A

### 4. Expose the HDMI switch to HomeKit

I ended up building a Homebridge plugin to expose the HDMI switch device to HomeKit.

Repo and documentation:  https://github.com/joelwetzel/homebridge-hubitat-mediainputsource

### 5. Configure HomeKit with scenes for the AV equipment

My scenes need to be defined in HomeKit, because the LG tv and the AppleTV streaming device do not integrate with Hubitat.  The LG integrates directly to HomeKit, and the AppleTV integrates to HomeBridge.

I've set up several scenes in the Home app, with settings like this:

**Apple TV**
- LG TV on
- Apple TV on
- HDMI Switch on and input set to Apple TV

**Cable**
- LG TV on
- Apple TV off
- HDMI Switch on and input set to Cable

**TV Off**
- LG TV off
- Apple TV off
- HDMI Switch off

### 6. Integrate with Alexa routines

Alexa can't set HomeKit scenes directly, so I created virtual switches (that auto-off after a couple seconds) in Hubitat that are exposed to both Alexa and HomeKit.  Then, for example:

- Create a routine in Alexa.  If I say something like "Alexa, turn on Apple TV", it will activate the vAppleTV virtual switch.
- This gets sent back to Hubitat, and then to HomeKit.
- Create an automation in HomeKit.  If the vAppleTV virtual switch turns on, activate the "Apple TV" scene.




