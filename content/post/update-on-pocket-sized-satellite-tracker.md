---
title: 'Update on Pocket-Sized Satellite Tracker'
date: Thu, 28 Sep 2017 00:35:06 +0000
draft: false
tags: [Uncategorized]
---

After some fighting with [libpredict](https://github.com/la1k/libpredict/), I found that I am going to need a microcontroller with better floating-point support. The Atmega series of microcontrollers do not have an [FPU](https://en.wikipedia.org/wiki/Floating-point_unit) which means any floating-point support is hard (either inaccurate, slow, or hacked). I found that some of the ARM Cortex processors do indeed have an FPU and their also come in  similar form as Arduinos or other development boards: ![Adafruit Feather M0 Basic Proto - ATSAMD21 Cortex M0](https://cdn-shop.adafruit.com/970x728/2772-01.jpg) [https://www.adafruit.com/product/2772](https://www.adafruit.com/product/2772) Or ![Teensy 3.2 + header](https://cdn-shop.adafruit.com/970x728/2756-00.jpg) [https://www.adafruit.com/product/2756](https://www.adafruit.com/product/2756) Above is Adafruit's Feather which I'm going to give a shot. It hosts a Cortex M0 which actually does not have an FPU but would handle floating point much better than Atmega due to 32-bit arch. The Teensy 3.2 hosts a Cortex M4 which actually does have an FPU which might be the winner. I'll be experimenting with both to see which one better suits my needs.