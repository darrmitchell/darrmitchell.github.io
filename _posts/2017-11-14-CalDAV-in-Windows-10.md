---
layout: post
title: "CalDAV in Windows 10"
date: 2017-11-14
---

I use [FastMail](https://www.fastmail.com/?STKI=13174429) for my personal e-mail, contacts, and calendar needs and have for several years now. They respect your privacy and provide open-source-backed technologies for sync: IMAP, CalDAV, and CardDAV.

For whatever reason, Microsoft does not support CalDAV natively in the otherwise decent Windows 10 Calendar app. I really wanted the native desktop notifications.

I noticed online that they supported CalDAV for iCloud, and noticed in the screenshot that the URL for the iCloud CalDAV server was editable. So I figured I'd try adding an "iCloud" account and using my Fastmail credentials/server settings. And what do you know, it worked. Here are the steps:

- Open calendar and add an account. Choose iCloud. 
- You can go ahead and put your CalDAV server username and password in here - they're not checking it against iCloud. 
- Once it's added, click Settings, go to "Manage accounts", and then "Change mailbox sync settings". 
- Click "Advanced mailbox settings" at the bottom
- Put your CalDAV and CardDAV URLs in
- Save and enjoy
