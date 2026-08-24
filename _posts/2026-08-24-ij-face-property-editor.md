---
published: true
layout: post
title:  "The Face Property Editor"
author: ishimoto
date:   2026-08-24
categories: IntelliJ
tags: IntelliJ
---

# The Face Property Editor

A **Face** is a skin / variant / tenant of a single TreasureBoat application. One
deployed app can serve several faces — each with its own branding, configuration
and, often, its own slice of data. (EdisonSystem, for instance, runs faces like
`acotro`, `cscw`, `lake`, `metlakatla`… all from one build.)

You've already met FaceIDs twice in this series — the [Navigation Editor]({% post_url 2026-08-21-ij-navigation-editor %})
gives each FaceID its own menu, and an [EO Migration]({% post_url 2026-08-17-ij-eo-migration-editor %})
can target specific faces. This editor is the third piece: each face's
**configuration**.

## Face property files

Each face has a `face_{FaceID}.properties` file in the `Property/` folder:

```
src/main/resources/
    Property/
        face_default.properties     ← the base / fallback
        face_acotro.properties
        face_lake.properties
```

It's plain key–value configuration for that face — base URLs, SMTP addresses,
logos, feature flags, text overrides:

```
org.treasureboat.core.app.baseUrl             = https://…
org.treasureboat.security.login.logoResource  = face://LogoLogin.png
mu.app.mail.sending.address.from              = office@treasureboat.org
```

> `face://` is a per-face resource reference — `face://LogoLogin.png` resolves to
> *this* face's logo, so each tenant can ship its own without any code change.

---

## The editor

![Overview](/assets/FacePropertyEditor/Overview.png)

* **Faces panel (left)** — every FaceID found in `Property/`. **+** creates a new `face_{name}.properties`, **−** removes one.
* **Properties table (centre)** — the selected face's keys and values, edited in place; add and remove properties here.
* **Comparison panel (right)** — pick a property and see its value **across every other face** at once. This is the useful part: it makes it obvious when a key is missing from one face, or intentionally different between them.

**Toolbar:** Save All · Reload · Help.

---

## Good to know

* **`default` is the fallback.** A face inherits from `face_default.properties`;
  a `face_{X}.properties` only needs to list what differs for that face.
* The **Comparison panel** is your consistency check — line up SMTP addresses,
  URLs and feature flags across faces and spot the gaps.
* Each file usually starts with a **load-check** key
  (`org.treasureboat.loadedFace.Properties = acotro`) so at runtime you can
  confirm the right face's properties actually loaded.
* Faces are TreasureBoat's lightweight multi-tenancy: **one app, one deploy, many
  branded configurations** — driven by these files plus per-face navigation and
  data.

---

## Related

* [The Navigation Editor]({% post_url 2026-08-21-ij-navigation-editor %}) — per-FaceID menus
* [The EO Migration Editor]({% post_url 2026-08-17-ij-eo-migration-editor %}) — the `Face` targeting field
* [Project Layout]({% post_url 2026-08-15-ij-project-layout %}) — where `Property/` lives

---
