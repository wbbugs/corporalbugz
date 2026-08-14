---
layout: post
title: "Direct Send: The Phishing Vector You May Have Missed"
date: 2025-09-24
permalink: /post/direct-send-the-phishing-vector-you-may-have-missed/
hero: /assets/img/posts/direct-send.jpg
tags: [Microsoft 365, Red Teaming, Phishing]
read_time: 4
excerpt_text: "How unauthenticated Microsoft 365 mail flow can become a convincing phishing path during a red team."
---
During a recent red team engagement, we uncovered an unexpected gap in a client's Microsoft 365 mail flow. While testing external ingress routes, we found that Exchange Online Protection would accept unauthenticated SMTP traffic - behaviour commonly referred to as **Direct Send**.

Direct Send allows devices and applications such as printers, scanners or line-of-business systems to send mail through Microsoft 365 without authentication when the recipients are inside the same Microsoft 365 organisation.

Direct Send isn't a new phishing vector. What makes it relevant is its persistence: many organisations still overlook it, and it continues to appear during assessments as a route for mail that looks as though it originated inside the tenant.

#### The Basics

By walking through a simple SMTP handshake - `EHLO`, `MAIL FROM`, `RCPT TO` - we could demonstrate whether spoofed internal addresses would be accepted by the tenant's mail servers.

> Can I talk directly to your tenant's mail servers without logging in or being on your network?

We connected directly to the organisation's MX records on TCP/25 and worked through the initial SMTP conversation:

```text
EHLO attacker.example
MAIL FROM:<spoofed.internal@yourdomain.com>
RCPT TO:<target.user@yourdomain.com>
```

If the server accepts both sender and recipient without authentication, an attacker has learned that the path may be usable for internal-looking phishing. During validation we stopped before `DATA` until delivery was explicitly required by the engagement.

#### Some Caveats

There are practical issues to account for when testing Direct Send:

- Residential IP addresses are commonly blocked by reputation services such as Spamhaus.
- Regional mismatches can trigger an Office 365 `ATTR35` response indicating mail has reached the wrong region.
- Azure VMs, Azure Cloud Shell and many VPS providers block outbound TCP/25.

For one engagement I needed an egress IP in the same Office 365 region as the tenant. Message headers, the `EHLO` response and reverse DNS helped identify the region before switching the exit node via VPN.

A further observation was that tenants with a permissive DMARC policy were more likely to accept the spoofed messages, while stronger policies could push them into quarantine or reject them.

#### From POC to delivery

For controlled delivery I used PowerShell and .NET's `System.Net.Mail.MailMessage` classes so that the message body could be supplied as HTML. A delay between recipients was important; sending too quickly could result in responses such as:

```text
4.7.500 Server busy. Please try again later
```

Slowing the campaign down made delivery much more reliable. Non-delivery reports and out-of-office messages also returned to the sender mailbox that had been specified, which is worth considering during a larger red-team campaign because those replies can burn the pretext or attack path.

The messages could look very credible, but they remained subject to the tenant's normal anti-phishing controls including spam filtering, Safe Links, Safe Attachments and SPF/DKIM/DMARC evaluation.

#### Recommendations

- Apply an enforcement-level DMARC policy where appropriate, ideally `p=reject` once the domain is ready.
- Disable or restrict Direct Send in Exchange Online when it is not required.
- Review legitimate devices and applications that still depend on unauthenticated SMTP before changing the control.

#### Closing Thoughts

Direct Send isn't sophisticated, but that is exactly why it persists. Removing unnecessary relay paths and enforcing strong email-authentication controls makes it much harder for spoofed internal messages to reach users.
