---
published: true
layout: post
title:  "S2M — TreasureBoat Mobile - Developer Menu"
author: ishimoto
date:   2026-09-16
categories: Mobile
tags: [Mobile]
---

# Developer Menu

![Intro 1](/assets/S2M/Mobile/NoConnection/Developer.jpeg)

Adding the Developer TreasureBoat ID. Needed for connection to the TB Server. 
(only Developers and for need of a special Video Channel)

Main Server:

Connection information for connection to the TB Main Server, special for development, it is possible to adjust here.

### Development

Here you can find some Developer Helper switches.

**Reusable pairing QR**: applies to every first-contact QR (adding a server and re-pairing)  
> A QR can be re-scanned and won't expire. Turn off to test production single-use.

**Invalidate server cache**: each request drops the server's cached navigation + .s2m config + action registry,  
so edited config shows without a restart (turn off when done).

> Both require a dev-mode server and are never sent in release builds.

![Development](/assets/S2M/Mobile/NoConnection/Development.jpeg)

## Technical Information

* FaceID support
* Communication secure connection, different encryption for each device
