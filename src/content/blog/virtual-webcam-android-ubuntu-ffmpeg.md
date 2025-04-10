---
title: Faster Virtual Webcam with Android, Ubuntu and FFmpeg
description: Optimize resources used by a virtual webcam
slug: virtual-webcam-android-ubuntu-ffmpeg
pubDate: 2021-03-10
---

OBS made pretty easy to turn a phone into a virtual webcam but I wanted to see if I was able to find an alternative that uses less resources. I want to say that OBS isn't bad at all in this regard, but it comes with some overhead that can be avoided considering the particular use case of a simple webcam.

On the Android side I use [IP Webcam](https://play.google.com/store/apps/details?id=com.pas.webcam&hl=en_US&gl=US). The interface is pretty old but the functionalities that it offers are the most complete and reliable I could find.
It streams the video over http so you can receive it from any device on the same network. For the best performance you can use USB tethering and the latency will be basically zero.

The first thing to do on the pc is to create a virtual video device using [v4l2loopback](https://github.com/umlaeute/v4l2loopback).

```bash
> sudo modprobe v4l2loopback devices=1 max_buffers=2 exclusive_caps=1 card_label="Virtual Webcam"
```

Then run [v4l2-ctl](http://manpages.ubuntu.com/manpages/focal/man1/v4l2-ctl.1.html) and take note of the newly created device name (something like `/dev/video2`)

```bash
> v4l2-ctl --list-devices
```

The last thing is to read the video feed with [ffmpeg](https://ffmpeg.org/) and push it to the device we just created.

```bash
> ffmpeg -i http://192.168.42.129:8080/video -f v4l2 -pix_fmt yuv420p /dev/video2
```

And that's it!
