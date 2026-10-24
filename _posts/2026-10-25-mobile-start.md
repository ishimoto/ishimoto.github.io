---
published: true
layout: post
title:  "S2M — Server Side Preparation for Mobile Connection"
author: ishimoto
date:   2026-10-25
categories: Mobile
tags: [Mobile, Authentication]
---

# Server Side Preparation for Mobile Authorization (For Developer)

Before we start, please make sure you have the latest version of the **TreasureBoat Mobile** application installed on your iOS device.  
This Connection setup works with build 1.

On the Web App, the entrance to all these features is in the **EditMyself** section.

### Mobile Authentication

`945 : EN = 'TBPerson' => tabS2M = ("[Mobile@policy=Policy.Sangria.mobile.login]", "(Mobile)", "mobileLogin", "pairedDevices") [Assignment]`

### Policy

> To switch on Mobile Login, add the Policy to a Role

* `Policy.Sangria.mobile.login`

---

![Overview](/assets/S2M/Mobile/OverviewMobile.png)

### How to set up on your Server

Both are prebuilt and can be used with `tabInject` to inject them into the **EditMySelfTBPerson** page.

`1000 : (EN = 'TBPerson' and PC = 'EditMySelfTBPerson') => tabInject = ("tabTwoFactor", "tabS2M") [Assignment]`
