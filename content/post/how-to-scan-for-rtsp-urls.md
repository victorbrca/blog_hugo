---
title: "How to Scan for RTSP Urls"
date: 2018-02-09T23:04:40-05:00
draft: false
author: "Victor Mendonça"
description: "How to find the RTSP URL for a network camera"
tags: ["Linux", "Shell", "Networking"]
---

So you have a camera but can't figure out what the RTSP URL is? Here's the solution.

Head over to [nmap.org](https://nmap.org/nsedoc/scripts/rtsp-url-brute.html) and download their [rtsp-url-brute](https://svn.nmap.org/nmap/scripts/rtsp-url-brute.nse) script to your computer. Then run it with nmap as:

```
nmap --script rtsp-url-brute -p 554 [IP]
```

You should see results similar to the ones below:

```
nmap --script rtsp-url-brute -p 554 192.168.1.15

Starting Nmap 7.60 ( https://nmap.org ) at 2018-02-09 23:00 EST
Nmap scan report for 192.168.1.15
Host is up (0.0033s latency).

PORT    STATE SERVICE
554/tcp open  rtsp
| rtsp-url-brute:
|   errors:
|     rtsp://192.168.1.15/media.amp
|     rtsp://192.168.1.15/media/media.amp
|     rtsp://192.168.1.15/media/video1
|     rtsp://192.168.1.15/media/video2
|     rtsp://192.168.1.15/media/video3
|     rtsp://192.168.1.15/medias1
|     rtsp://192.168.1.15/mjpeg.cgi
|     rtsp://192.168.1.15/mjpeg/media.smp
|     rtsp://192.168.1.15/media
|     rtsp://192.168.1.15/mp4
|   discovered:
|     rtsp://192.168.1.15/
|     rtsp://192.168.1.15/1
|     rtsp://192.168.1.15/1.AMP
|     rtsp://192.168.1.15/1/1:1/main
|     rtsp://192.168.1.15/1/cif
|     rtsp://192.168.1.15/1/stream1
|     rtsp://192.168.1.15/11
|     rtsp://192.168.1.15/12
|   other responses:
|     400:
|       rtsp://192.168.1.15/0
|       rtsp://192.168.1.15/0/video1
|       rtsp://192.168.1.15/4
|       rtsp://192.168.1.15/CAM_ID.password.mp2
|       rtsp://192.168.1.15/CH001.sdp
|       rtsp://192.168.1.15/GetData.cgi
|       rtsp://192.168.1.15/H264
|       rtsp://192.168.1.15/HighResolutionVideo
|       rtsp://192.168.1.15/HighResolutionvideo
|       rtsp://192.168.1.15/Image.jpg
|       rtsp://192.168.1.15/LowResolutionVideo
|       rtsp://192.168.1.15/MJPEG.cgi
|       rtsp://192.168.1.15/MediaInput/h264
|       rtsp://192.168.1.15/MediaInput/h264/stream_1
|       rtsp://192.168.1.15/MediaInput/mpeg4
|       rtsp://192.168.1.15/ONVIF/MediaInput
|       rtsp://192.168.1.15/ONVIF/channel1
|       rtsp://192.168.1.15/PSIA/Streaming/channels/0?videoCodecType=H.264
|       rtsp://192.168.1.15/PSIA/Streaming/channels/1
|       rtsp://192.168.1.15/PSIA/Streaming/channels/1?videoCodecType=MPEG4
|       rtsp://192.168.1.15/PSIA/Streaming/channels/h264
|       rtsp://192.168.1.15/Possible
|       rtsp://192.168.1.15/ROH/channel/11
|       rtsp://192.168.1.15/Streaming/Channels/1
|       rtsp://192.168.1.15/Streaming/Channels/101
|       rtsp://192.168.1.15/Streaming/Channels/102
|       rtsp://192.168.1.15/Streaming/Channels/103
|       rtsp://192.168.1.15/Streaming/Channels/2
|       rtsp://192.168.1.15/Streaming/Unicast/channels/101
|       rtsp://192.168.1.15/Streaming/channels/101
|       rtsp://192.168.1.15/Video?Codec=MPEG4&Width=720&Height=576&Fps=30
|       rtsp://192.168.1.15/VideoInput/1/h264/1
|       rtsp://192.168.1.15/access_code
|       rtsp://192.168.1.15/access_name_for_stream_1_to_5
|       rtsp://192.168.1.15/av0_0
|       rtsp://192.168.1.15/av0_1
|       rtsp://192.168.1.15/av2
|       rtsp://192.168.1.15/avn=2
|       rtsp://192.168.1.15/axis-media/media.amp
|       rtsp://192.168.1.15/axis-media/media.amp?videocodec=h264&resolution=640x480
|       rtsp://192.168.1.15/cam
|       rtsp://192.168.1.15/cam/realmonitor
|       rtsp://192.168.1.15/cam/realmonitor?channel=1&subtype=00
|       rtsp://192.168.1.15/cam/realmonitor?channel=1&subtype=01
|       rtsp://192.168.1.15/cam/realmonitor?channel=1&subtype=1
|       rtsp://192.168.1.15/cam0_0
|       rtsp://192.168.1.15/cam0_1
|       rtsp://192.168.1.15/cam1/h264
|       rtsp://192.168.1.15/cam1/h264/multicast
|       rtsp://192.168.1.15/cam1/mjpeg
|       rtsp://192.168.1.15/cam1/mpeg4
|       rtsp://192.168.1.15/cam1/onvif-h264
|       rtsp://192.168.1.15/cam4/mpeg4
|       rtsp://192.168.1.15/camera.stm
|       rtsp://192.168.1.15/cgi-bin/viewer/video.jpg?resolution=640x480
|       rtsp://192.168.1.15/ch0
|       rtsp://192.168.1.15/ch0.h264
|       rtsp://192.168.1.15/ch001.sdp
|       rtsp://192.168.1.15/ch01.264
|       rtsp://192.168.1.15/ch0_0.h264
|       rtsp://192.168.1.15/ch0_unicast_firststream
|       rtsp://192.168.1.15/ch0_unicast_secondstream
|       rtsp://192.168.1.15/channel1
|       rtsp://192.168.1.15/dms.jpg
|       rtsp://192.168.1.15/dms?nowprofileid=2
|       rtsp://192.168.1.15/h264
|       rtsp://192.168.1.15/h264.sdp
|       rtsp://192.168.1.15/h264/ch1/sub/
|       rtsp://192.168.1.15/h264/media.amp
|       rtsp://192.168.1.15/h264Preview_01_main
|       rtsp://192.168.1.15/h264Preview_01_sub
|       rtsp://192.168.1.15/h264_vga.sdp
|       rtsp://192.168.1.15/image.jpg
|       rtsp://192.168.1.15/image.mpg
|       rtsp://192.168.1.15/image/jpeg.cgi
|       rtsp://192.168.1.15/img/media.sav
|       rtsp://192.168.1.15/img/video.asf
|       rtsp://192.168.1.15/img/video.sav
|       rtsp://192.168.1.15/ioImage/1
|       rtsp://192.168.1.15/ipcam.sdp
|       rtsp://192.168.1.15/ipcam/stream.cgi?nowprofileid=2
|       rtsp://192.168.1.15/ipcam_h264.sdp
|       rtsp://192.168.1.15/jpg/image.jpg?size=3
|       rtsp://192.168.1.15/live
|       rtsp://192.168.1.15/live.sdp
|       rtsp://192.168.1.15/live/av0
|       rtsp://192.168.1.15/live/ch0
|       rtsp://192.168.1.15/live/ch00_0
|       rtsp://192.168.1.15/live/ch00_1
|       rtsp://192.168.1.15/live/ch1
|       rtsp://192.168.1.15/live/ch2
|       rtsp://192.168.1.15/live/h264
|       rtsp://192.168.1.15/live/mpeg4
|       rtsp://192.168.1.15/live0.264
|       rtsp://192.168.1.15/live1.264
|       rtsp://192.168.1.15/live1.sdp
|       rtsp://192.168.1.15/live2.sdp
|       rtsp://192.168.1.15/live3.sdp
|       rtsp://192.168.1.15/live_h264.sdp
|       rtsp://192.168.1.15/live_mpeg4.sdp
|       rtsp://192.168.1.15/livestream
|_      rtsp://192.168.1.15/livestream/
```
