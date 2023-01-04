# Google Cast Edge Driver

This is an Edge port of the [cast-web SmartThings](https://vervallsweg.github.io/cast-web/) package and requires the [cast-web-api application](https://github.com/vervallsweg/cast-web-api) to be installed and running on a LAN computer.

This Edge driver will provide the capability to play media on Google Cast-compatible devices including SmartThings notifications and Google TTS messages.

Note that this driver should be considered BETA level, as limited testing has been done and there are some known issues.

## Driver Installation
The driver is currently available from my test channel here:  https://bestow-regional.api.smartthings.com/invite/Q1jP7BqnNNlL

Enroll your hub and select 'Google Cast v0.1' from the list of available drivers to install to your hub

Once the driver is available on your hub, use the SmartThings mobile app to perform an *Add device / Scan for nearby devices*.  A new device will then be created in your *No room assigned* room named 'Cast Manager'.  Go to device settings for thisnew device and configure the address of your cast-web-api application: e.g. 192.168.1.104:3000.  Return to the device Controls screen and observe the Status field, which will display the connection status with the cast-web-api app.

You can now do another *Add device / Scan for nearby devices* and this time devices will be created for each device reported by cast-web-api.

### Device Setting Options
#### Cast Manager
Up to 6 Presets can be configured.  Provide the title, URL, media-type, and stream-type for each.  URL must be in the form 'http://......'.  These presets can include live streaming URLs as well as single-play sound files.
#### Cast Device
Each cast device created can be configured with the following options:
* Switch ON, Switch OFF, Next Track, Previous Track actions
* Google TTS Language to use for voice synthesis commands

### Automations
#### Notifications
Cast speakers can play SmartThings notifications initiated through automation routines.  

For the THEN part of your automation routine, select 'Notify someone' and then 'Play message on speaker'.  For audio devices, select the desired cast speaker device.  Note that SmartThings is doing the TTS conversion, so speaker preferences are not available.

#### Speech Synthesis
An alternative way to play announcements is to use the Speech Synthesis capability of the cast device.  Here, you can configure the design language through the cast device settings options (Google TTS Language).

When creating an automation routine, for the THEN part of the routine, select the desired cast device, unselect 'Turn on', tap Speech Synthesis, turn on the toggle switch, and enter the text you want.

Now when your routine is triggered, the text will be sent to Google for converstion to audio, and played on your device.


## Speech Synthesis with Google TTS
The latest version of the cast-web-api no longer works with Google TTS and so need to be modified to have this work with this Edge driver.

This involves four steps:
1. Update the installed google-tts-api node module to the latest version
2. Download a replacement file that uses the new google-tts-api module
3. Copy the replacement file to a specific cast-web-api directory
4. Restart cast-web-api

Before getting started with the commands below, go ahead and download the google-tts.js file in this repository to your Downloads directory.

### Linux commands
Note: this assume npm is installed and current, and you have the cast-web-api-cli installed
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
* Disconnects are not uncommon after interrupting a group play with notifications; monitor the bottom Status field of a device.
* Media info such as current app or titles may be blank at times, but should eventually refresh

