---
layout: post
title: "Diagnosing a DOA PC"
tags: blog computers
description: A prebuilt computer would not turn on when I received it. Here's what I found out.
---

## Diagnosing a Dead-On-Arrival PC
### or why to avoid buying from Lyte Gaming PCs
Before I begin, I would like to note that with the quarantines and everything due to the virus going around, some of the following may not reflect Lyte Technology's normal practice and may be a stroke of bad luck on our part. However, I feel the mistakes made with this computer are inexcusable for anyone wanting to be in the business of building PCs for customers, and write this with that in mind.

My wanted to get a gaming pc recently, and decided to get a prebuilt one. He bought a $1000 computer from Lyte Technology a.k.a. Lyte Gaming PCs, a company I had never heard of and was at least a little skeptical of because of this. The computer took a little over a month to be built and shipped out though thankfully FedEx delivered it in about 2 days. When it did arrive he quite literally ripped it out of the box, plugged it into the wall, pressed the power button, and...... nothing happened. I was called up to investigate the issue, but quick check of power switches and cables gave no results. The worst outcome had shown itself. Something in his brand new computer was dead. Time for an indepth investigation.

The first thing to investigate for me was the power supply. I pulled all the cables off the gpu and motherboard, and plugged the 24-pin connector into a test jumper block that came with the EVGA power supplies I had bought in the past. Flipping the switch brought the fans, rgb, and hard drives to life. Since the power supply seemed fine it was time to move farther down the line. My next guess was that perhaps something was wrong with the power switch in the case. I pulled the case leads off and put two single wire leads on, and touched them together to simulate pressing the power button. Still no results from the computer.

At this point it was decided to try calling Lyte's tech support. While we sat on hold for 15-20 minutes, I noticed my first worrying issue with the computer.
![IO shield](/images/lyte/io_shield.jpg)
There were IO shield straps in the HDMI port. Sure this should have no real effect on the usage of the computer since we would be using the discrete gpu, but this is something that should be checked as you're setting the motherboard in. I did notice as well that the cpu cooler seemed a bit crooked sitting in the case. I tried moving it with my hand but it didn't budge so I thought it was just by design, but more on that later. When we finally got connected to tech support I was asked two questions. "Did you flip the switch on the power supply?" and "Did you try plugging it into another outlet?" Great. After confirming I had already done both, he said he needed to connect us to an actual tech support representative. I thought that was what I was just on hold for... Since my brother needed to go to work and I didn't want to be on hold again for another pointless call, we scheduled a callback for Monday which was the next day Lyte was open as they were closed on weekends. I decided since the IO shield straps were bothering me, to pull the motherboard out and continue diagnosing on my own.

Taking a screwdriver to the motherboard, I noticed a lot of the screws were not on tightly. Only two of the six screws were at a proper tightness, one of the screws I could even turn with my finger. Taking the board out, I again felt like something was wrong with the cpu cooler and decided to take a closer look.
![Processor under cooler](/images/lyte/cooler.jpg)
I believe my reaction was something along the lines of "What the hell?" The cpu had been neither aligned nor inserted into the socket for having the cooler slammed on it. This was definitely my issue, and a massive screw up by whoever built this computer. I went to take the cooler off to see if the cpu could be salvaged at all. This was a difficult process because of how it had been jammed on there, requiring me to pull screws from the mounting brackets until I could flex stuff enough and slip the cooler's buckle system off. When I tilted the cooler away, the cpu came with it, and required me to actually pry it off with a plastic pry bar.
![Cooler removal](/images/lyte/cpu.jpg)
Unfortunately there were quite a number of bent pins which I have neither the time nor expertise to attempt fixing.
![Bent pins](/images/lyte/cpu_pins.jpg)

### Conclusion
There were quite a number of issues with this computer that would make me not want to do business with Lyte in the future. First being the computer was not as advertised. Looking at their site, the case listed in the build is completely different from what was received. The build also listed using a Gigabyte board and EVGA 500W power supply, however we ended up with an Asrock board and ThermalTake power supply. Not a major deal breaker, but definitely misleading and false advertising at best. The biggest issue and why I urge other to avoid them is their lack of attention and quality control with builds. When building a pc for a customer, every computer should go through at least one, if not two, boot tests to make sure the pc works. With the issue we had, it is obvious that this never happened as the pc could never have booted prior to being shipped in this state.
