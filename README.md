# Hardware accellerated Sonarr SMA Docker container (e.g. for Synology Diskstation)

This version of [mdhiggins original Docker container](https://github.com/mdhiggins/sonarr-sma) uses ffmpeg with vaapi support enabled to enable hardware accelleration on devices with Intel QuickSync (and some others), for example most Synology Diskstations with Intel CPUs.

On a Synology Diskstation DS920+ (CPU: J4125, RAM: 4GB, GPU: Intel UHD 600) throughput is about 72fps at ~15% CPU usage with the sample command `ffmpeg -hwaccel vaapi -hwaccel_device /dev/dri/renderD128 -hwaccel_output_format vaapi -i input.mp4 -c:v h264_vaapi -b:v 2M -maxrate 2M output.mp4` on a typical 1080p movie.

Without hardware accelleration, it is just 20fps at 100% CPU usage.

## Version Tags

|Tag|Branch|Description|
|---|---|---|
|latest|master|Preview release from Sonarr (v3) with FFMPEG compiled from jrottenberg/ffmpeg:snapshot-vaapi|
|v2|v2|Stable release from Sonarr (v2) with FFMPEG compiled from jrottenberg/ffmpeg:snapshot-vaapi|

## Usage

For usage on Synology Diskstation:
- Download image
- Create container using the information given in [the official repository](https://github.com/mdhiggins/sonarr-sma).
- Do not start it yet
- Export container as json and then delete it
- Modify the devices section in the json file as follows
```
   "devices" : [
      {
         "CgroupPermissions" : "rwm",
         "PathInContainer" : "/dev/dri/renderD128",
         "PathOnHost" : "/dev/dri/renderD128"
      },
      {
         "CgroupPermissions" : "rwm",
         "PathInContainer" : "/dev/dri/card0",
         "PathOnHost" : "/dev/dri/card0"
      }
   ],
```
- Import the json container configuration into the diskstation
- Now you can run it

Don't forget to also set the vaapi codec as the first entry in the autoProcess.ini file, otherwise the hardware accelleration will not be used. Example:
```
[Video]
...
codec = h264vaapi, h265vaapi, h264, x264, hevc, x265
...
```

Please see [the official repository](https://github.com/mdhiggins/sonarr-sma) for everything else.

## Links

Docker Hub page: https://hub.docker.com/r/eikowagenknecht/sonarr-sma-vaapi

## Credits

All credits go to [mdhiggins](https://github.com/mdhiggins).
