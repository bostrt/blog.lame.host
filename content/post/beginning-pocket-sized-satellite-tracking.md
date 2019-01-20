---
title: 'Beginning Pocket-Sized Satellite Tracking'
date: Fri, 15 Sep 2017 23:07:30 +0000
draft: false
tags: [Uncategorized]
---

A couple months ago, I worked on putting a [satellite tracking application on a Raspberry Pi](https://robertbost.wordpress.com/2017/07/22/flyby-satellite-tracking-on-raspberry-pi/). This worked okay but I have to use a keyboard to select the satellite I want to track before going outside to find it. I also have to lug around an external battery outside which isn't fun. I could really use something more compact. Hard Requirements

*   Screen + Buttons
*   Tracking multiple satellites (not hundreds, just a few)
*   Display Azimuth
*   Display Elevation
*   Display transponder frequency with Doppler shift
*   Polar plot (sky plot)
*   Button to switch between satellites
*   Ability to set the time and timezone on device (RTC required)

Soft Requirements

*   Button to toggle between screens for a single satellite (if Polar plot and transponder data cannot fit in same screen)
*   Ability to update TLE's when plugging into computer

  I have one of these [pockeTETRIS](https://hackaday.io/project/26170-pocketetris) things and love the little OLED screen. I will probably end up using the same screen. I'd like to just re-purpose my already built pockeTETRIS, but, I still need the RTC and definitely a microcontroller with more program space for largest library I will need, [libpredict](https://github.com/la1k/libpredict), which comes out to around 40k when I drop features I don't need. The ATtiny85 in pocketTETRIS only has 8K. Pretty sure I am going with ATmega1284 a whopping 128k; plenty of room for libpredict, RTC library, OLED library, and my own code.