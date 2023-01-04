# Google Cast Edge Driver

This is an Edge port of the [cast-web SmartThings](https://vervallsweg.github.io/cast-web/) package and requires the [cast-web-api application](https://github.com/vervallsweg/cast-web-api) to be installed and running on a LAN computer.

This Edge driver will provide the capability to play media on Google Cast-compatible devices including SmartThings notifications and Google TTS messages.

Note that this driver should be considered BETA level, as limited testing has been done and there are some known issues.

## Driver Installation
Before installing this Edge driver, be sure you have the cast-web-api application up and running on a LAN computer.  Follow the instructions [here](https://vervallsweg.github.io/cast-web/installation-cast-web-api/).  Note that this package requires node and npm to be installed on your computer.

The driver is currently available from my test channel here:  https://bestow-regional.api.smartthings.com/invite/Q1jP7BqnNNlL

Enroll your hub and select 'Google Cast v0.1' from the list of available drivers to install to your hub

Once the driver is available on your hub, use the SmartThings mobile app to perform an *Add device / Scan for nearby devices*.  A new device will then be created in your *No room assigned* room named 'Google Cast Manager'.  Go to device settings for this new device and configure the address of your cast-web-api application: e.g. 192.168.1.104:3000.  Return to the device Controls screen and observe the Status field, which will display the connection status with the cast-web-api app.

You can now do another *Add device / Scan for nearby devices* and this time devices will be created for each device reported by cast-web-api.

### Device Setting Options
#### Cast Manager
Up to 6 Presets can be configured.  Provide the title, URL, media-type, and stream-type for each.  URL must be in the form 'http://......'.  These presets can include live streaming URLs as well as single-play sound files.

Title and subtitle are provide in one field, separated by a '-' character.  So for example 'Local Radio - KLBJ'.  If no subtitle is desired then just provide the title.
#### Cast Device
Each cast device created can be configured with the following options:
* Switch ON, Switch OFF, Next Track, Previous Track actions
* Google TTS Language to use for voice synthesis commands

### Automations
#### Notifications
Cast speakers can play SmartThings notifications initiated through automation routines.  

For the THEN part of your automation routine, select 'Notify someone' and then 'Play message on speaker'.  For audio devices, select the desired cast speaker device.  Note that SmartThings is doing the TTS conversion, so voice/language preferences are not available.

#### Speech Synthesis
An alternative way to play announcements is to use the Speech Synthesis capability of the cast device.  Here, you can configure the desired language through the cast device Settings options (Google TTS Language).

When creating an automation routine, for the THEN part of the routine, select the desired cast device, unselect 'Turn on', tap Speech Synthesis, turn on the toggle switch, and enter the text you want.

Now when your routine is triggered, the text will first be sent to Google for conversion to audio, and then played on your device.

#### Other
An 'invisible' switch is included in each cast device which can be used by external SmartThings integrations for simple media control.  Configure the desired actions in device Settings

## Device Controls
### Manager Device
The Info field should show a list of known device names and their IP addresses
The Refresh button can be used if you suspect the connection to the cast-web-api has been lost.  This button will force a reconnection attempt and callback re-initialization with cast-web-api.  Monitor the Status field for results.
### Cast Device
#### Dashboard
The dashboard control can be used to pause and play the current media for that device.
#### Controls screen
The *Media playback* GUI controls may not work correctly - particularly on iOS devices.  This is a known SmartThings problem and has been reported.  Because of this, there is a second button just below it labeled "Alternate media controls" that can be used for manual selection of play/pause/stop.

Note that the *rewind* and *fastforward* buttons do nothing, as this is not available for cast devices.

The *Media track* control is useful primarily for going to the next or previous Preset.  However, alternative actions can be configured in device Settings.

The *Play a favorite* button will present a list of Presets you have configured (in the Manager device).  Select one to play the media configured for that Preset.

*Volume/Mute* works as expected, except this cannot control the volume of Chromecast devices.

The Status section will show information if anything is currently playing.  *Media Player Status* may show 'no status' if nothing is playing or information has not yet been received from the cast-web-api (it may eventually update if media is playing).

The bottom field in the Status section, *Status*, shows whether or not the device is connected.  Note that this can occasionally show a disconnected state, but within a minute or so, it should automatically be reconnected.  Keep an eye on this field if you are having trouble controlling the device.  Don't attempt to issue any media controls unless the device is in a 'Connected' state.


## Speech Synthesis with Google TTS
The latest version of the cast-web-api no longer works with Google TTS, but it can be modified to get it to work with this Edge driver.

This involves four steps:
1. Update the installed google-tts-api node module to the latest version
2. Download a replacement file that uses the new google-tts-api module
3. Copy the replacement file to a specific cast-web-api directory
4. Restart cast-web-api

Before getting started with the commands below, go ahead and download the google-tts.js file in this repository to your Downloads directory.

Also, be sure you have Version 2023-01-04T01:23:15.134697978 or later of the Google Cast v0.1 Edge driver.

### Linux commands
Note: this assumes npm is installed and current, and you already have the cast-web-api-cli installed
```
cd ~/.npm-global/lib/node_modules/cast-web-api-cli/node_modules/cast-web-api
npm i google-tts-api@latest

cd lib/device/id
cp google-tts.js google-tts.js_original
cp ~/Downloads/google-tts.js google-tts.js

cast-web-api-cli stop
cast-web-api-cli start
```

## Limitations
* Google Assistant functionality is not currently working due to a change in the supported Google oath flows.
* Control of Chromecast devices is very limited due to the above
* When playing notifications, any currently playing **Preset** will be resumed afterwards.  If the device is playing other media that had been initiated directly on the device or through the Google app, then that media cannot be resumed
* Disconnects are not uncommon after interrupting a group play with notifications; monitor the device's Status field at the bottom of its Controls screen.
* Media info such as current app or titles may be blank at times, but should eventually refresh

