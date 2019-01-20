---
title: 'Update #2 on Pocket-Sized Satellite Tracker'
date: Fri, 27 Oct 2017 23:11:52 +0000
draft: false
tags: [amateur radio, amsat, Satellites, Uncategorized]
---

Things are progressing nicely on the satellite tracker. The following is functional:

*   [libpredict](https://github.com/la1k/libpredict/) modified to work on [Adafruit Feather M0](https://www.adafruit.com/product/2772)
*   [Real-time clock](https://www.adafruit.com/product/3295) with battery backup complete
*   [Storage](https://www.adafruit.com/product/1895) for configs complete
*   [OLED display](https://www.amazon.com/Diymall-Serial-128x64-Display-Arduino/dp/B01HHOETIA) working and displaying data calculated by libpredict
*   USB Serial interface for configuring location, callsign, TLE's, and time (in case RTC loses battery power)

### Current tasks

*   Develop a dev pcb to replace bread-board. Just something that all the breakout boards above solder onto
*   Finish display graphics
*   code-cleanup

### Screenshot & Photos

_Serial interface_ ![Screenshot from 2017-10-28 11-10-24.png](/static/screenshot-from-2017-10-28-11-10-24.png) _All components_ ![IMG_20171028_105906851.jpg](/static/img_20171028_105906851.jpg) _Polar chart that will display relative satellite position_ ![IMG_20171028_105938349.jpg](/static/img_20171028_105938349.jpg) _Missing photo of the satellite position data due to being in middle of display code rewrite._
