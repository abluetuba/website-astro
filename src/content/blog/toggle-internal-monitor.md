---
title: Toggle a monitor with xrandr
description: A script to toggle the built-in monitor of a laptop (on linux)
slug: toggle-monitor-xrandr
pubDate: 2025-01-25
---

Usually my laptop is connected to an external monitor and when I'm programming I normally use two screens.
But when I'm doing something else like watching a video or playing a game I just need one monitor, the external one.

So we will write a little script to toggle the built-in monitor on and off.

The command we will use is [xrandr](https://wiki.archlinux.org/title/Xrandr) which (quoting `man xrandr`) "is used to set the size, orientation and/or reflection of the outputs for a screen.
It can also set the screen size."

I found [this](https://wiki.archlinux.org/title/Xrandr#Toggle_external_monitor) guide in the ArchWiki that explains how to do a similar thing, toggling the external monitor. So I took that and slightly changhed it to my use case.

Here's the final script:

```bash
#!/bin/sh

if ! xrandr --listmonitors | grep HDMI; then
	return 0
fi

if xrandr --listmonitors | grep eDP; then
	xrandr --output eDP --off
else
	xrandr --output eDP --auto --left-of HDMI-A-0
fi
```

In the first part we use `xrandr --listmonitors` to get the list of connected monitors.
The output for me is like this:

```bash
❯ xrandr --listmonitors
Monitors: 2
 0: +*HDMI-A-0 1920/477x1080/268+1920+0  HDMI-A-0
 1: +eDP 1920/344x1080/193+0+0  eDP
```

So we can check if the external monitor is connected using `grep`.

If `xrandr --listmonitors | grep HDMI` is `false` we exit the script since there is nothing to do (the external monitor is not connected).

Then we use the same method to check if the built-in monitor (named `eDP` on my machine) is on. If it is we can turn it off with:

```bash
xrandr --output eDP --off
```

Instead if it's off we need to turn it on. We can also specify its position relative to the other monitor:

```bash
xrandr --output eDP --auto --left-of HDMI-A-0
```

So in my case I put it `--left-of` my external monitor.

Finally we can save the script to a file, let's call it `monitor`,
make it executable (`chmod u+x monitor`)
and put it to a location in our path (like `$HOME/bin`) so we can run it easily from the terminal.
