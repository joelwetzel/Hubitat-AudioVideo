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
