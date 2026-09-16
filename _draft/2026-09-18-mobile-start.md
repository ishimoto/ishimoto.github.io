---
published: false
layout: post
title:  "S2M — Server Side Preparation for Mobile Authorization"
author: ishimoto
date:   2026-09-17
categories: Mobile
tags: [Mobile, Authentication]
---

# Server Side Preparation for Mobile Authorization

Before we start, please make sure you have the latest version of the **MTBMobile** application installed on your iOS device.  
If the application is not released yet, you can download the latest version from Testflight.

On the Web App, the entrance to all this features are in the **EditMyself** section.

### Mobile Authentication

`945 : EN = 'TBPerson' => tabS2M = ("[Mobile@policy=Policy.Sangria.mobile.login]", "(Mobile)", "mobileLogin", "pairedDevices") [Assignment]`

---

![Overview](/assets/S2M/Mobile/OverviewMobile.png)

### How to set up on your Server

Both are prebuilt and can be used with `tabInject` to inject them into the **EditMySelfTBPerson** page.

`1000 : (EN = 'TBPerson' and PC = 'EditMySelfTBPerson') => tabInject = ("tabTwoFactor", "tabS2M") [Assignment]`

### Policy

> To switch on Mobile Login add the Policy to a Role

* `Policy.Sangria.mobile.login`

