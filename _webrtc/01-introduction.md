---
title: "Introduction to WebRTC Libraries"
permalink: /webrtc/introduction/
excerpt: "WebRTC Library Comparision"
header:
  overlay_image: /assets/images/webrtc/01-introduction/introduction.png
  teaser: /assets/images/webrtc/01-introduction/introduction.png
  overlay_filter: 0.5
last_modified_at: 2018-05-05T15:58:49-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Jay
---

If you are visiting this blog, hope that you are already familiar with [WebRTC](https://webrtc.org/). This guide will not cover the basics of WebRTC

## What this guide is about

Well, this would be more in to the use cases of webrtc. Specially our experience with opensource WebRTC libraries and tools. We'll discuss about settingup, bringing them to production, pros and cons.
This guide would be useful if you are,
1. Not willing to use proprietary libraries or third party APIs for your WebRTC application
2. Want to build your own WebRTC infrastructure
3. Heavily using AWS infrastructure

**Note:** This might not discuss about all the available WebRTC libraries and tools, but the ones we have experienced.
{: .notice--info}

## What this is Not About

Definitely this is not an introduction to WebRTC. And this might not be useful in following cases.

1. If you dont want to manage your own WebRTC infrastructure
2. If you just need a single peer to peer connection, and not looking for scale it up. 

## Use Cases

These are some use cases that we have experienced with and this guide is mainly written with that experience.

1. Online Tutoring
2. Online Interviewing
3. Video Conferencing with SIP integration
4. Tele-Medicine
5. Remote Expert Support
6. Real-Time Object Recognition
		

## Comparision

Here is the list of the tools and libraries that we are going to discuss and a comparision about their features.

|                   |   P2P         | SFU Mode  | MCU Mode  | Chat  | Screen Sharing    | File Sharing  | Recording | Mobile SDK|
|:---:              |---:           |---:       |---:       |---:   |---:               |---:           |---:       |---:       |
| SimpleWebRTC      | yes           | x         | x         | yes   | yes               | yes           | x         | x         |
| Jitsi             | yes           | yes       | x         | yes   | yes               | x             | yes       | yes       |
| Kurento           | x             | yes       | yes       | yes   | no                | x             | yes       | yes       | 
{: rules="groups"}
---

That's it! Lets begin.
