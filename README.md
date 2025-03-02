[![Docker](https://github.com/mrlt8/docker-wyze-bridge/actions/workflows/docker-image.yml/badge.svg)](https://github.com/mrlt8/docker-wyze-bridge/actions/workflows/docker-image.yml)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/mrlt8/docker-wyze-bridge?logo=github)](https://github.com/mrlt8/docker-wyze-bridge/releases/latest)
[![Docker Image Size (latest semver)](https://img.shields.io/docker/image-size/mrlt8/wyze-bridge?sort=semver&logo=docker&logoColor=white)](https://hub.docker.com/r/mrlt8/wyze-bridge)
[![Docker Pulls](https://img.shields.io/docker/pulls/mrlt8/wyze-bridge?logo=docker&logoColor=white)](https://hub.docker.com/r/mrlt8/wyze-bridge)

# WebRTC/RTSP/RTMP/HLS Bridge for Wyze Cam

![479shots_so](https://user-images.githubusercontent.com/67088095/224595527-05242f98-c4ab-4295-b9f5-07051ced1008.png)


Create a local WebRTC, RTSP, RTMP, or HLS/Low-Latency HLS stream for most of your Wyze cameras including the outdoor, doorbell, and 2K cams. 

No third-party or special firmware required.

It just works!

Streams direct from camera without additional bandwidth or subscriptions.

Based on [@noelhibbard's script](https://gist.github.com/noelhibbard/03703f551298c6460f2fd0bfdbc328bd#file-readme-md) with [kroo/wyzecam](https://github.com/kroo/wyzecam) and [bluenviron/mediamtx](https://github.com/bluenviron/mediamtx).


Please consider ⭐️ starring or [☕️ sponsoring](https://ko-fi.com/mrlt8) this project if you found it useful, or use the [affiliate link](https://amzn.to/3NLnbvt) when shopping on amazon!

## API Changes

As of July 2023, you will need to update your bridge to v2.3.x or newer for compatibility with the latest changes to the Wyze API.

## Quick Start

Install [docker](https://docs.docker.com/get-docker/) and run:

```bash
docker run -p 8554:8554 -p 8888:8888 -p 5000:5000 mrlt8/wyze-bridge
```

You can then use the web interface at `http://localhost:5000` where localhost is the hostname or ip of the machine running the bridge.

See [basic usage](#basic-usage) for additional information or visit the [wiki page](https://github.com/mrlt8/docker-wyze-bridge/wiki/Home-Assistant) for additional information on using the bridge as a Home Assistant Add-on.

## What's Changed in v2.5.1

* FIX `ON_DEMAND=False` option was broken in v2.5.0 #1036 #1037
* NEW API/MQTT commands Thanks @ralacher! #921:
  * GET: `/api/<cam-name>/accessories` | MQTT: `wyzebridge/<cam-name>/accessories/get`
  * SET: `/api/<cam-name>/spotlight` | MQTT: `wyzebridge/<cam-name>/spotlight/set`

## What's Changed in v2.5.0

* NEW camera support:
  * HL_DB2: Wyze Cam Doorbell v2 - thanks @hoveeman!
  * HL_CAM4: Wyze Cam V4
* NEW API Endpoint:
  * `/api/all/update_snapshot` - trigger interval snapshots via web API #1030

## What's Changed in v2.4.0

* Motion Events! 
  * Pulls events from the wyze API, so it doesn't require an active connection to the camera to detect motion - great for battery cams.
  * Motion status and timestamp available via MQTT and REST API:
    * MQTT topics: `wyzebridge/{cam-name}/motion` or `wyzebridge/{cam-name}/motion_ts`
    * REST endpoint: `/api/{cam-name}/motion` or `/api/{cam-name}/motion_ts`
  * Webhooks ready and works with ntfy.sh `triggers`. 
  * See [Camera Motion wiki](https://github.com/mrlt8/docker-wyze-bridge/wiki/Camera-Motion) for more information.
* Other fixes and changes:
  * Potential improvements for audio sync. Audio will still lag on frame drops. (#388)
    * Using **wallclock** seems to help in some situations: 
    `- FFMPEG_FLAGS=-use_wallclock_as_timestamps 1`
  * UPDATE FFmpeg to [v6.0](https://github.com/homebridge/ffmpeg-for-homebridge/releases/tag/v2.1.0) 
  * UPDATE MediaMTX version from v1.0.3 to v1.1.0 (#1002)
  * Store and reuse s3 thumbnail from events to reduce calls to the wyze api (#970)
  * Increase default `MTX_WRITEQUEUESIZE` (#984)
  * keep stream alive if livestream enabled (#985)
  * Catch RuntimeError if libseccomp2 is missing (#994)
  * Refactored API client to better handle exceptions and limit connections.
  * Check bitrate from videoParams for all 11.x or newer firmware (#975)
  * buffer mtx event data (#990)
  * Exclude battery cams from scheduled RTSP snapshots (#970)
* New ENV/Options:
  * `MOTION_API=True`  to enable motion events. (Default: False)
  * `MOTION_INT=<float>` number of seconds between motion checks. (Default: 1.5) 
  * `MOTION_START=True` to have the bridge initiate a connection to the camera on a motion event. (Default: False)
  * `MOTION_WEBHOOK=<str>` webhooks url. Can use `{cam_name}` in the url to make a request to a url with the camera name. Image url and title are available in the request header.
  * `MOTION_WEBHOOK_<CAM-NAME>=<str>` Same as `MOTION_WEBHOOK` but for a specific camera. 

[View previous changes](https://github.com/mrlt8/docker-wyze-bridge/releases)


## Supported Cameras

![Wyze Cam V1](https://img.shields.io/badge/wyze_v1-yes-success.svg)
![Wyze Cam V2](https://img.shields.io/badge/wyze_v2-yes-success.svg)
![Wyze Cam V3](https://img.shields.io/badge/wyze_v3-yes-success.svg)
![Wyze Cam V3 Pro](https://img.shields.io/badge/wyze_v3_pro-yes-success.svg)
![Wyze Cam Floodlight](https://img.shields.io/badge/wyze_floodlight-yes-success.svg)
![Wyze Cam Pan](https://img.shields.io/badge/wyze_pan-yes-success.svg)
![Wyze Cam Pan V2](https://img.shields.io/badge/wyze_pan_v2-yes-success.svg)
![Wyze Cam Pan V3](https://img.shields.io/badge/wyze_pan_v3-yes-success.svg)
![Wyze Cam Pan Pro](https://img.shields.io/badge/wyze_pan_pro-yes-success.svg)
![Wyze Cam Outdoor](https://img.shields.io/badge/wyze_outdoor-yes-success.svg)
![Wyze Cam Outdoor V2](https://img.shields.io/badge/wyze_outdoor_v2-yes-success.svg)
![Wyze Cam Doorbell](https://img.shields.io/badge/wyze_doorbell-yes-success.svg)
![Wyze Cam Doorbell V2](https://img.shields.io/badge/wyze_doorbell_v2-yes-success.svg)

Cameras from [Gwell Times](http://cloud.gwell.cc) are currently not supported:

![Wyze Cam Doorbell Pro](https://img.shields.io/badge/wyze_doorbell_pro-no-inactive.svg)
![Wyze Cam OG](https://img.shields.io/badge/wyze_og-no-inactive.svg)
![Wyze Cam OG 3x](https://img.shields.io/badge/wyze_og_3x-no-inactive.svg)

| Camera                        | Model          | Tutk Support                                                 | Latest FW |
| ----------------------------- | -------------- | ------------------------------------------------------------ | --------- |
| Wyze Cam v1 [HD only]         | WYZEC1         | ✅                                                            | 3.9.4.x   |
| Wyze Cam V2                   | WYZEC1-JZ      | ✅                                                            | 4.9.9.x   |
| Wyze Cam V3                   | WYZE_CAKP2JFUS | ✅                                                            | 4.36.11.x |
| Wyze Cam V4 [2K]              | HL_CAM4        | ❓                                                            | 4.52.?    |
| Wyze Cam Floodlight           | WYZE_CAKP2JFUS | ✅                                                            | 4.36.11.x |
| Wyze Cam Floodlight V2        | HL_CFL2        | ❓                                                            | -         |
| Wyze Cam V3 Pro [2K]          | HL_CAM3P       | ✅                                                            | 4.58.11.x |
| Wyze Cam Pan                  | WYZECP1_JEF    | ✅                                                            | 4.10.9.x  |
| Wyze Cam Pan v2               | HL_PAN2        | ✅                                                            | 4.49.11.x |
| Wyze Cam Pan v3               | HL_PAN3        | ✅                                                            | 4.50.4.x  |
| Wyze Cam Pan Pro [2K]         | HL_PANP        | ✅                                                            | -         |
| Wyze Cam Outdoor              | WVOD1          | ✅                                                            | 4.17.4.x  |
| Wyze Cam Outdoor v2           | HL_WCO2        | ✅                                                            | 4.48.4.x  |
| Wyze Cam Doorbell             | WYZEDB3        | ✅                                                            | 4.25.1.x  |
| Wyze Cam Doorbell v2 [2K]     | HL_DB2         | ✅                                                            | 4.51.1.x  |
| Wyze Cam Doorbell Pro 2       | AN_RDB1        | ❓                                                            | -         |
| Wyze Battery Cam Pro          | AN_RSCW        | [⚠️](https://github.com/mrlt8/docker-wyze-bridge/issues/1011) | -         |
| Wyze Cam Flood Light Pro [2K] | LD_CFP         | [⚠️](https://github.com/mrlt8/docker-wyze-bridge/issues/822)  | -         |
| Wyze Cam Doorbell Pro         | GW_BE1         | [⚠️](https://github.com/mrlt8/docker-wyze-bridge/issues/276)  | -         |
| Wyze Cam OG                   | GW_GC1         | [⚠️](https://github.com/mrlt8/docker-wyze-bridge/issues/677)  | -         |
| Wyze Cam OG Telephoto 3x      | GW_GC2         | [⚠️](https://github.com/mrlt8/docker-wyze-bridge/issues/677)  | -         |


## Compatibility

![Supports armv7 Architecture](https://img.shields.io/badge/armv7-yes-success.svg)
![Supports aarch64 Architecture](https://img.shields.io/badge/aarch64-yes-success.svg)
![Supports amd64 Architecture](https://img.shields.io/badge/amd64-yes-success.svg)

[![Home Assistant Add-on](https://img.shields.io/badge/home_assistant-add--on-blue.svg?logo=homeassistant&logoColor=white)](https://github.com/mrlt8/docker-wyze-bridge/wiki/Home-Assistant)
[![Homebridge](https://img.shields.io/badge/homebridge-camera--ffmpeg-blue.svg?logo=homebridge&logoColor=white)](https://sunoo.github.io/homebridge-camera-ffmpeg/configs/WyzeCam.html)
[![Portainer stack](https://img.shields.io/badge/portainer-stack-blue.svg?logo=portainer&logoColor=white)](https://github.com/mrlt8/docker-wyze-bridge/wiki/Portainer)
[![Unraid Community App](https://img.shields.io/badge/unraid-community--app-blue.svg?logo=unraid&logoColor=white)](https://github.com/mrlt8/docker-wyze-bridge/issues/236)

Should work on most x64 systems as well as on some arm-based systems like the Raspberry Pi.

The container can be run on its own, in [Portainer](https://github.com/mrlt8/docker-wyze-bridge/wiki/Portainer), [Unraid](https://github.com/mrlt8/docker-wyze-bridge/issues/236), as a [Home Assistant Add-on](https://github.com/mrlt8/docker-wyze-bridge/wiki/Home-Assistant), locally or remotely in the cloud.



### Ubiquiti Unifi 

Some network adjustments may be needed - see [this discussion](https://github.com/mrlt8/docker-wyze-bridge/discussions/891) for more information.

## Basic Usage

### docker-compose (recommended)

This is similar to the docker run command, but will save all your options in a yaml file.
(If your credentials have special characters, you must escape them)

1. Install [Docker Compose](https://docs.docker.com/compose/install/).
2. Use the [sample](https://raw.githubusercontent.com/mrlt8/docker-wyze-bridge/main/docker-compose.sample.yml) as a guide to create a `docker-compose.yml` file with your wyze credentials.
3. Run `docker-compose up`.

Once you're happy with your config you can use `docker-compose up -d` to run it in detached mode.

NOTE: You may need to [update the WebUI links](https://github.com/mrlt8/docker-wyze-bridge/wiki/WebUI#custom-ports) if you're changing the ports or using a reverse proxy.

#### Updating your container

To update your container, `cd` into the directory where your `docker-compose.yml` is located and run:

```bash
docker-compose pull # Pull new image
docker-compose up -d # Restart container in detached mode
docker image prune # Remove old images
```

### 🏠 Home Assistant

Visit the [wiki page](https://github.com/mrlt8/docker-wyze-bridge/wiki/Home-Assistant) for additional information on Home Assistant.

[![Open your Home Assistant instance and show the add add-on repository dialog with a specific repository URL pre-filled.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fmrlt8%2Fdocker-wyze-bridge)


## Additional Info

* [Camera Commands (MQTT/REST API)](https://github.com/mrlt8/docker-wyze-bridge/wiki/Camera-Commands)
* [Two-Factor Authentication (2FA/MFA)](https://github.com/mrlt8/docker-wyze-bridge/wiki/Two-Factor-Authentication)
* [ARM/Raspberry Pi](https://github.com/mrlt8/docker-wyze-bridge/wiki/Raspberry-Pi-(armv7-and-arm64))
* [Network Connection Modes](https://github.com/mrlt8/docker-wyze-bridge/wiki/Network-Connection-Modes)
* [Portainer](https://github.com/mrlt8/docker-wyze-bridge/wiki/Portainer)
* [Unraid](https://github.com/mrlt8/docker-wyze-bridge/issues/236)
* [Home Assistant](https://github.com/mrlt8/docker-wyze-bridge/wiki/Home-Assistant)
* [Homebridge Camera FFmpeg](https://sunoo.github.io/homebridge-camera-ffmpeg/configs/WyzeCam.html)
* [HomeKit Secure Video](https://github.com/mrlt8/docker-wyze-bridge/wiki/HomeKit-Secure-Video)
* [WebUI API](https://github.com/mrlt8/docker-wyze-bridge/wiki/WebUI-API)


## Web-UI

The bridge features a basic Web-UI which can display a preview of all your cameras as well as direct links to all the video streams.

The web-ui can be accessed on the default port `5000`:

```text
http://localhost:5000/
```

See also: 
* [WebUI page](https://github.com/mrlt8/docker-wyze-bridge/wiki/WebUI)
* [WebUI API page](https://github.com/mrlt8/docker-wyze-bridge/wiki/WebUI-API)


## WebRTC

WebRTC should work automatically in Home Assistant mode, however, some additional configuration is required to get WebRTC working in the standard docker mode.

- WebRTC requires two additional ports to be configured in docker:
  ```yaml
    ports:
      ...
      - 8889:8889 #WebRTC
      - 8189:8189/udp # WebRTC/ICE
    ```

- In addition, the `WB_IP` env needs to be set with the IP address of the server running the bridge.
  ```yaml
    environment:
      - WB_IP=192.168.1.116
  ```
- See [documentation](https://github.com/aler9/rtsp-simple-server#usage-inside-a-container-or-behind-a-nat) for additional information/options.

## Advanced Options

**WYZE_EMAIL** and **WYZE_PASSWORD** are the only two required environment variables.

* [Audio](https://github.com/mrlt8/docker-wyze-bridge/wiki/Camera-Audio)
* [Bitrate and Resolution](https://github.com/mrlt8/docker-wyze-bridge/wiki/Camera-Bitrate-and-Resolution)
* [Camera Substreams](https://github.com/mrlt8/docker-wyze-bridge/wiki/Camera-Substreams)
* [MQTT Configuration](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#mqtt-config)
* [Filtering Cameras](https://github.com/mrlt8/docker-wyze-bridge/wiki/Camera-Filtering)
* [Doorbell/Camera Rotation](https://github.com/mrlt8/docker-wyze-bridge/wiki/Doorbell-and-Camera-Rotation)
* [Custom FFmpeg Commands](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#custom-ffmpeg-commands)
* [Interval Snapshots](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#snapshotstill-images)
* [Stream Recording and Livestreaming](https://github.com/mrlt8/docker-wyze-bridge/wiki/Stream-Recording-and-Livestreaming)
* [rtsp-simple-server/MediaMTX Config](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#mediamtx)
* [Offline/IFTTT Webhook](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#offline-camera-ifttt-webhook)
* [Proxy Stream from RTSP Firmware](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#proxy-stream-from-rtsp-firmware)
* [BOA HTTP Server/Motion Alerts](https://github.com/mrlt8/docker-wyze-bridge/wiki/Boa-HTTP-Server)
* [Debugging Options](https://github.com/mrlt8/docker-wyze-bridge/wiki/Advanced-Option#debugging-options)
