---
layout: post
title:  A story about a camera.
description: A story about a simple camera request.
date:   2023-11-20 06:00:00 +0000
image:  '/images/posts/p1/p1.jpg'
tags:   [Technology, Homelab]
---

##### Today I want to tell you a story about a camera.

6 months ago my SO (significant other) asked for a camera so that we could keep an eye on our dog and our two cats. All technology aspects of our lives falls under my jurisdiction, and in the past, that has worked out well for us.

Once she asked, my gears started to turn. I knew nothing about the current state of cameras. All I knew was that I don’t want some camera of Alibaba that is filled with backdoors/data exfiltration capabilities. 

Friends and colleagues have asked me, “Why don’t you just block the camera from calling home in the firewall?”

Well, that is not enough to be safe from data exfiltration these days. 
Let’s take Amazon as an example ([*1](https://www.consumerreports.org/electronics/wireless-networking/pros-and-cons-of-amazon-sidewalk-network-plus-how-to-opt-out-a9048278100/)), the ring doorbell cameras have made their own mesh network, using other close devices networks, to call home with data.

I told her, “Fine. I’ll make one myself. It will take a bit of time though, it won’t be finished for a few weeks.”, thinking to myself, “**How hard can it be?**”.


Started to look around and found the ESP32-Cam board ([*2](https://www.amazon.com/DORHEA-Bluetooth-Development-4-75V-5-25V-Raspberry/dp/B09265G5Z4/ref=mp_s_a_1_4_maf_2?crid=25U3919D2RO9A&keywords=esp32+cam&qid=1698957287&sprefix=esp3%2Caps%2C137&sr=8-4)). Great, only 25$ for four cameras! Nothing suspicious on these boards, and root access to the chipset. Nice.

![](/images/posts/p1/ESP32-cam-mb_combined.png)
*ESP32-cam*

After having got the cameras and programmed them to serve a MJPEG stream through a web server for us to enjoy, I was happy! **Almost done**!

_However_, since the cameras will be placed inside, i decided our network must be hardened.

“Easy!” I thought for myself, I’ll just create an isolated IoT VLAN and set up a VPN tunnel in our Opnsense firewall to be able to reach them when we are not home. One _small_ issue though. Our current wired and wireless switches are dumb and cannot handle VLANs.  

I guess it’s time to get some new gear! A new managed switch ($60) and a Unifi AP ($170) were ordered.

Great, the network is up and running with an isolated IoT VLAN. Time to set up the cameras. After a lot of headaches with god damn c++ I get the cameras working. Unfortunately, the onboard WiFi antenna is **pure garbage**. To switch to an external antenna one needs to move a 1mm resistor in to another spot on the board. I tried this on three of my cameras. I managed to kill them all without getting the resistor into the correct place. Fuck sake. 

I guess ill have to befriend a chinese merchant on AliExpress and convince him/her to sell me boards with the resistor already moved. To my suprise, these vendors are really focused on customer experience. They help with anything you ask. _Sometimes_ i almost wish i lived in China. 

While I wait for my cameras to get through the Panama canal I can start working on the software side of things.

I am still a little worried about the network security. The plan is to implement the following:

1. Monitor the firewall logs for intruders using Splunk(Thank you employer).
2. Backend API to keep track of alerts from Splunk, keep track of online cameras, and serve recorded camera feeds.
3. A frontend website to display all the stuff listed in 2. This needs to be easy to use for my SO, and look good enough to show to others.

Uhh... now i kind of need a server. Oh well.. 

A NUC(650$) with 32GB ram and 1TB M.2 is now hosting two VMs: Splunk logging and the Backend/Frontend.

![](/images/posts/p1/nuc.jpg){:height="50%" width="50%"}
*my baby*

The backend was not that hard to put together. For some reason I’ve learned C# from earlier endeavors, and I god damn love it. A minified API with modularity, to be able to expand it in the future. Nice.

The frontend though… to be honest, ReactJS is very reactive and it can look **reeeaally good**. But is is fucking JavaScript. Fuck, I’ll guess I’ll just fucking learn that too then.

<div class="gallery-box">
    <div class="gallery">
        <img src="/images/posts/p1/frontend3.jpg">
        <img src="/images/posts/p1/frontend2.jpg">
    </div>
    <em>Frontend</em>
</div>

Ok, frontend and backend up. Still waiting for the modified ESP32-cam boards. Let’s start working on the camera recording! No good security camera if it does not record, right?

Right now the cameras stream using MJPEG, to make it easier to display on the frontend.

After _almost_ giving up on life trying to code my own solution for recording the feeds, I hit a wall. Image manipulation is fucking hard. Luckily, after a break from this madness I found something called MotionEye ([*3](https://github.com/motioneye-project/motioneye/tree/dev)). It can record MJPEG streams with ease! Great! I can now serve the directory where the recordings are stored from the backend so that the frontend can access them.

But as I said in the beginning, we have a dog and two cats. To have bare electronics laying around the flat is not ideal. One little lick from our friends on the wrong spot and something terrible might happen. Fuck. Oh well, guess I’ll have to buy a 3D printer($200) to make cases for the cameras.

These things are kind of cool! I got recommended a printer that was cheap and easy to use: ([*4](https://store.creality.com/eu/products/ender-3-v2-neo-3d-printer?spm=..collection_f039d777-5efa-459f-ae24-276dcb248e28.albums_1.1))

It just works! Alright, cases. Here is something I pasted together from a few models I found online. That will work for now.

![](/images/posts/p1/printedcam.jpg){:height="50%" width="50%"}
*3D printed ESP32-CAM*

Now, after about 6-7 months, my SO can finally see what our dog and cats are up to when we are not home. Did i go a bit overboard? Yes. But now we have a good base to build out a home automation system, with security elements to it!

Stay safe.