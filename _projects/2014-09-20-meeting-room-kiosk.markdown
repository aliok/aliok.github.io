---
layout: post
title:  "Meeting room kiosk"
date:   2014-09-20 00:38:00
thumbnail: "/post-assets/layout/meeting-room-kiosk-thumb.png"
banner: "/post-assets/layout/meeting-room-kiosk-banner.png"
alternate_banner: True
banner_attribution: "*Sketch of sensor box during ergo-design*"
banner_source: "#"

---

I created a meeting room kiosk for use in Innflow. Project is hosted on [Github](https://github.com/aliok/meetingRoomKiosk).
<!--more-->

### Motivation
I hate going to meeting rooms and not knowing who is inside. Or, when the current meeting ends, or when the next meeting starts...

We had paper-based schedule posted on the doors and one can check it out. That is done by someone in the company every monday. 

I thought of a cool system to be mounted on meeting room doors.
Proposed solution is:

* interactive : You can touch the screen and see more information
* always up-to-date : Data is fetched from meeting room server (Outlook in this case)
* modern and impressive : Visitors are impressed
* reducing the costs : That person doing the paper posting every monday doesn't need to do it again

Anyway, my main motivation was to learn more about Raspberry Pi development and native Android development.

### Parts
Here is the basic overview:

![parts](/post-assets/meeting-room-kiosk-parts.png)

Sensor box is a Raspberry Pi with a couple of electronic sensor components. The most important component is the ultrasonic distance sensor.
These electronic devices and Raspberry Pi are all covered by a custom designed 3D printed case
([Sketch 1](/post-assets/meeting-room-kiosk-sensor-case-layers.jpg)), ([Sketch 2](/post-assets/meeting-room-kiosk-sensor-case-outside.jpg)).  

Display is an Android tablet.


#### Sensor
To make it really interactive and less power consuming, we need to turn on the display when someone walks in.

It is smart enough to act different in cases of person approaching being in different distances.
That is the wow effect since when the person is 10 meters away, we can turn the display on and play a welcome sound.
When s/he approaches further we can display the actual schedule.
This is the wow effect.

Also, sensor needs to be smart enough to not trigger the display unnecessarily.

All of these requirements have led me to an ultrasonic distance sensor. Alternative would be a movement sensor, but that would trigger the screen too often.

Here is the tutorial I used for this purpose : <http://www.raspberrypi-spy.co.uk/2012/12/ultrasonic-distance-measurement-using-python-part-1/>

#### Display
Display is just an Android tablet where it sends requests to server (to be discussed) and displays the schedule and meeting information.

Any type of tablet would be fine in this project. A 10" one is probably the best.

### Architecture
Here is the basic overview:

![parts](/post-assets/meeting-room-kiosk-architecture.png)

1. Sensor notifies server
1. Server sends a push to display (tickle)
1. Display receives the push
1. Display turns on the screen and fetches the schedule from the server 

Since Raspberry Pi and Android tablet cannot communicate directly, I needed a server that controls the communication.
Also, in server, we can do things like caching the schedule information, etc. Server is a plain old REST backend.

Cloud is also an essential part as the standard way of sending push notifications is using Google Cloud Messaging.

### Conclusion 
This project is currently on hold at Innflow. However, I accomplished 3 things personally:

1. It was my first big native Android project. I had already some experience with it, but not like this one where I needed to receive push notifications,
   play with OS scheduling and other things as well.
1. I learnt more about doing things with Raspberry Pi. It is unbelievably easy to use any kind of sensor and write production quality code.
1. I had a lot of fun playing around with the Raspberry Pi.

Project is hosted on [Github](https://github.com/aliok/meetingRoomKiosk).

### Alternative ideas
An alterntive idea would be using just one display and having it open always. An e-ink Android would suffice but current ones on market are terrible.
Also, a non-interactive black&white screen is far away than giving the wow effect. 