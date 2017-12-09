---
layout: post
title: "DMARC Forwarding Madness"
date: 2017-12-08
---

I'm working with a customer currently to deploy Mimecast as both an inbound and outbound mail gateway for GSuite. Mimecast advises cutting over outbound first because there's no major concerns around mail flow, and it allows the system to "learn" addresses. If it doesn't work, you just cut it off. That's not the case for inbound when global DNS propagation is involved.

I configured everything for outbound as outlined in Mimecast's documentation: created a host in GSuite to relay to, then created two routing rules: for external bound mail, route via Mimecast. For internal bound mail, route via GSuite. Everything appeared to be working fine until I checked the "Bounces" log in Mimecast and started seeing legitimate e-mails bouncing due to DMARC failures.

The failures all had one thing in common: they were showing a "From" address of `[gsuiteusername]+caf_=[externalusername]=[externaldomain]@gsuitedomain.com`, or `user+caf_=user=gmail.com@domain.com` It didn't take me long to realize that what I was seeing was auto-forwarded messages out of GSuite to personal mailboxes. It also didn't take me long to figure out that DMARC reject policy breaks this.

There was no fix, but the testing outcomes were interesting enough to share. It seems that:

- some providers, i.e. iCloud, do strict DMARC checking and adhere to the senders request (p=reject;) to outright reject messages that violate their policy
- some providers, i.e. Office 365, do not adhere to reject policy and instead quarantine. [Microsoft documents this:](https://technet.microsoft.com/en-us/library/mt734386(v=exchg.150).aspx)

>If the DMARC policy of the sending server is p=reject, EOP marks the message as spam instead of rejecting it.

- GSuite/Google supposedly does respect hard fails for DMARC, but for some reason this does not appear to extend to forwarded messages from GSuite to GMail. I'm not sure why this is the case. In fact, I've seen some pretty crazy stuff that Google does to try to "dummy proof" e-mail flow: DKIM when there is no DKIM configured for the domain, or passing hard SPF fails to the user's mailbox anyway.

In this case the important thing was that we had several users who wanted to keep forwarding out to GMail intact. Since Google doesn't reject DMARC fails for forwards that stay "internal" (GSuite to GMail), I was able to fulfill this request by creating a compliance rule that looks for "X-Forwarded-For" in outbound (external) messages and routes out via "Normal" GSuite routes instead of the Mimecast gateway.