---
published: false
layout: post
title:  "S2M — Mobile App - Connecting to a Server"
author: ishimoto
date:   2026-10-22
categories: Mobile
tags: [Mobile, Authentication]
---

# Mobile App – Connecting to a Server

In the last post the app was alone, without any server. Now we connect it to one.  
Once connected, the server decides what the app shows: the menu comes from the server, not from the app.

## What you need

* **TreasureBoat Mobile** from the App Store, on an iPhone or iPad.
* A login on a TreasureBoat server that supports the Mobile App.
* That server's **pairing QR**. You find it after logging in on the web, in your own profile
  <!-- TODO Ken: exact place in Lake / NipponYa (tab name) + screenshot -->

## Pairing

On the phone, open **Settings**. Under **Connect** tap **Scan pairing QR** and point the camera at the QR on the web page.

![Add Server](/assets/S2M/Mobile/Add/AddServer.png)

That's it. The app and the server now share a key that belongs to this one device.  
Every request is signed with it, so nobody else can talk to the server pretending to be your phone.

> The pairing QR can be used only once and expires. If the scan fails, reload the web page for a fresh QR.

Can't use the camera? Copy the QR text and paste it into **Or paste the QR text…**, then tap **Pair**.

## The Server Card

After pairing, Settings shows a **Servers** section with a card for each server.  
The server you are working with is marked **ACTIVE**.

![Server List](/assets/S2M/Mobile/Add/ServerList.png)

Tap the card to see the details:

![Server Details](/assets/S2M/Mobile/Add/ServerDetails.png)

* **Server** – the name and address of the server
* **Signed in** – who you are on that server, and your current **Domain**
* **Nickname** – a label just for this device, if the server's name is not enough
* **Use this server** – makes it the active server
* **Re-pair / refresh credentials** – scan a fresh QR to renew this device's key; your domain and cached screens are kept
* **Unpair this device** – removes the pairing; you need to scan a QR again to reconnect

You can pair with more than one server. Each gets its own card, and you switch with **Use this server**.

![Multiple Servers](/assets/S2M/Mobile/Add/MultipleList.png)

## The Server's Menu

Now the navigation comes from the server. The first release keeps it simple:

| Entry | What it does |
|---|---|
| **2FA** | Your Two-Factor Authentication codes |
| **Server Connection** | The Settings screen from above: your servers, pairing, switching |
| **Quick Login** | Log in on the web by scanning a QR – no password typing |
| **Barcode** *(NipponYa)* | Scan a product barcode and see the product information |

<!-- TODO Ken: screenshot of the Lake menu (iPad, store build) -->

### 2FA

The same Authenticator from the first post. Add an account by scanning the QR from your profile on the web.  
After that, the login form asks for the 6-digit code, and the app shows it.

### Quick Login

On a computer, open the server's login page. When Quick Login is switched on, the page shows a QR.  
In the app tap **Quick Login → Scan login QR** and scan it.

The app asks **Log in to "…"?** and shows which server it is. Check it, then tap **Approve**.  
The computer signs you in. Nothing happens without that tap, so a QR on a fake page can't log you in by itself.

<!-- TODO Ken: screenshot of the login page with the QR + the Approve sheet -->

### Barcode (NipponYa)

Tap **Barcode** and point the camera at a product barcode (EAN/JAN).  
The app looks the product up on the server and shows its information.

## Your Phone and your iPad

If you use the app on more than one device, two things behave differently:

* **2FA accounts sync.** They are stored in the iCloud Keychain, so an account you add on the iPhone
  shows up on the iPad by itself. You don't lose your codes when you change phones.
* **Server pairings don't sync.** Each pairing is a key for one device and never leaves it.
  On a new device, scan the pairing QR again – that is on purpose, so a copied backup can't act as your phone.

## Technical Information

* Every device has its own key; requests are signed per device
* The pairing QR is single-use and expires
* 2FA secrets: iCloud Keychain (synced) – pairing keys: device only
* The menu is defined on the server, so it can change without an app update
