---
title: "Video Encoding Cheatsheet"
permalink: /docs/encoding_cheatsheet/
excerpt: "Useful hints and reminders for video encoding"
toc: false
classes: wide
---
## General thoughts

### UHD with HDR:  Use ffmpeg to extract appropriate video and audio track (or encode the audio track)
```
ffmpeg -i <input filename> -c:v copy -tag:v hvc1 -c:a eac3 -b:a 224k <output filename>.mp4
```
The hvc1 tag is important for quicktime to recognise the hevc file.  In the example above we're also re-encoding the audio file as eac3

If we want to encode it is possible but HDR makes a little bit of a mess of things.  The following seems to work (only for HDR input!) In the following example we're also rescaling the video to 1080 across (preserving the aspect ratio).  We also needed to download the latest build of ffmpeg as the brew installed version didn't support the zscale filters (hadn't been compiled with them)

```
ffmpeg/bin/ffmpeg -i <input filename> -c:v hevc_videotoolbox -b:v 24M -c:a eac3 -b:a  224k -t 200 -tag:v hvc1 -vf zscale=tin=smpte2084:min=bt2020nc:pin=bt2020:rin=tv:t=smpte2084:m=bt2020nc:p=bt2020:r=tv,zscale=t=linear:npl=100,format=gbrpf32le,zscale=p=bt709,tonemap=tonemap=hable:desat=0,zscale=t=bt709:m=bt709:r=tv,colormatrix=bt709:bt601,zscale=w-1:h=1080:-1 <Output filename>
```

### HD:  Use ffmpeg to transcode the video using the videotoolbox (probably possible using gstreamer, undocumented)

Add -t <seconds> if you want to test a snippet.  In this example we're also resizing to 1080

```
ffmpeg/bin/ffmpeg -i <input filename> -c:v hevc_videotoolbox -b:v 24M -c:a eac3 -b:a  224k -vf zscale=w-1:h=1080:-1 -tag:v hvc1 <output filename>.mp4
-- Possibly for gstreamer on the jetson
gst-launch-1.0 -e filesrc location=<input filename>.mkv ! matroskademux name=demux demux. ! queue ! h265parse ! omxh265dec enable-low-outbuffer=1 ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12' ! nvv4l2h265enc bitrate=12000000 ! h265parse ! qtmux name=mux ! filesink location=<output>.mp4 demux. ! queue ! dtsdec ! avenc_aac ! mux.
```

We can use nv3dsink as an end sink if on the screen

## Jetson Nano: filesystem mounting (eg, linux filesystem mounting)
```
sudo mount -t cifs //<ip address>/<share name> <local_mount> -o user=<username>
```
