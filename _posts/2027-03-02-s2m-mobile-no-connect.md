---
published: false
layout: post
title:  "S2M — Mobile App - No Connection"
author: ishimoto
date:   2027-03-01
categories: Mobile
tags: [Mobile]
---

# Mobile App – No Connection

Let's talk about the plain version of the Mobile App, because first it is not connect to any servers.  
So what can it do and how does it look?

## The Icon

![The Icon](/assets/S2M/Mobile/NoConnection/Icon.jpeg)

## The Name

The name might change in the future, but for now the Project is called **MTBMobile**.

## Navigation

The Navigation is very simple; it has only one page, with several links.  

![Navigation](/assets/S2M/Mobile/NoConnection/Navigation.jpeg)

## App Section

### Authenticator

The empty screen for Two-Factor Authentication. (2FA)  
here we are able to add accounts. more to that in the 2FA post.

![Authenticater](/assets/S2M/Mobile/NoConnection/Authenticator.jpeg)

### About

The about Screen shows information about the app version, and when available, the current connected
Server information like Server name, Signed in userName, and current Domain.

![About](/assets/S2M/Mobile/NoConnection/About.jpeg)

### Settings

The Setting Screen allows you to configure the app's settings. 

![About](/assets/S2M/Mobile/NoConnection/Settings.jpeg)

* Connect – Scan pairing QR (Connect the Mobile App to a Server) [Check Connection Page]
* App – Show intro again
* App – Show what's new
* TreasureBoat - Developer Menu

#### Developer Menu

![Intro 1](/assets/S2M/Mobile/NoConnection/Developer.jpeg)

Adding the Developer TreasureBoat ID. Needed for connection to the TB Server. (only Developers)

Main Server:

Connection information for connection to the TB Main Server, special for development it is possible to adjust here.

##### Development

Here you can find some Developer Helper switches.

**Reusable pairing QR**: applies to every first-contact QR (adding a server and re-pairing)  
> a QR can be re-scanned and won't expire. Turn off to test production single-use.

**Invalidate server cache**: each request drops the server's cached navigation + .s2m config + action registry,  
so edited config shows without a restart (turn off when done).

> Both require a dev-mode server and are never sent in release builds.

![Development](/assets/S2M/Mobile/NoConnection/Development.jpeg)

#### Intro

The intro will be shown when the app is first installed or when the user chooses to show it again.  
This are the default screens and will be configurable.

![Intro 1](/assets/S2M/Mobile/NoConnection/Intro1.PNG)
![Intro 2](/assets/S2M/Mobile/NoConnection/Intro2.PNG)
![Intro 3](/assets/S2M/Mobile/NoConnection/Intro3.PNG)

#### What's new

The What's new sheet shows the latest changes and updates to the app.  
It shown when the app has a new version or when the user chooses to show it again.  
This will be configurable.

![What's new](/assets/S2M/Mobile/NoConnection/WhatsNew.PNG)

### Technical Information

* FaceID support
* Communication secure connection, different encryption for each device
