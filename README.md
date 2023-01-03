# Google Cast Edge Driver

This is an Edge port of the cast-web-api SmartThings package and requires the [cast-web-api application](https://github.com/vervallsweg/cast-web-api) to be installed and running on a LAN computer.

This Edge driver will provide the capability to play media on Google Cast-compatible devices including SmartThings notifications and Google TTS messages.

## Google TTS
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
