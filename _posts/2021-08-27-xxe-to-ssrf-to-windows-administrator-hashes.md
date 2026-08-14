---
layout: post
title: "XXE to SSRF to Windows Administrator Hashes"
date: 2021-08-27
updated: 18 Dec 2025
permalink: /post/xxe-to-ssrf-to-windows-administrator-hashes/
hero: /assets/img/posts/xxe-hero.webp
tags: [XXE, SSRF, WebApp]
read_time: 3
excerpt_text: "A blind XXE finding that grew from a collaborator hit into local file disclosure and recovered deployment credentials."
---
**Disclaimer:** details are generic and no client information is present in this post.

XXE occurs when unsafe XML parsing allows external entities to influence how the application reads local or remote resources. SSRF is the broader class of issue where a server-side application can be induced to make requests to another location chosen by the attacker.

On a penetration test I was assessing a bespoke Windows application that communicated with a REST API. The application was used by sales staff to create quotes and manage customers. Because users were sometimes offline for long periods, data was cached locally and synchronised with the cloud when connectivity returned.

After spending some time using the application and capturing its requests in Burp Suite, I noticed that updating customer details generated a POST request containing XML. I sent it to Repeater and tried a blind XXE payload. The first test generated an interaction with Burp Collaborator.

![Blind XXE interaction received](/assets/img/posts/xxe-collaborator.webp)

That confirmed external entity processing, so the next question was whether it could be used to read anything interesting.

I hosted a controlled DTD on my assessment infrastructure and used it to request files from the Windows server. The first harmless target was `C:\Windows\win.ini`.

![Hosted DTD used for the XXE test](/assets/img/posts/xxe-dtd.png)

![win.ini returned through the XXE channel](/assets/img/posts/xxe-winini.webp)

For the next hour I worked through candidate files. Some paths did not exist, some returned access-denied errors and others contained characters that interfered with the HTTP-based exfiltration channel.

I also tested whether alternative URL schemes were reachable. FTP connections worked, which could be observed on the controlled responder host.

![Outbound FTP connection triggered by the XML parser](/assets/img/posts/xxe-ftp.png)

SMB egress was blocked, so capturing NetNTLM authentication that way wasn't available. The application could, however, still reach some internal resources and disclose local files. I wanted to turn that into a finding with clearer impact than simply retrieving `win.ini`.

The next targets were files commonly associated with unattended Windows deployment, including `Unattend.xml`, `sysprep.inf` and `sysprep.xml`.

![Attempt to retrieve unattended installation data](/assets/img/posts/xxe-unattended.webp)

One of the accessible deployment files contained an encrypted Administrator credential. Continuing through common deployment-file locations eventually produced a `sysprep.xml` containing base64-encoded account data.

![Payload used while searching for deployment files](/assets/img/posts/xxe-payload.png)

After decoding the value, I had a plaintext Administrator username and password.

![Administrator credential recovered from deployment configuration](/assets/img/posts/xxe-admin.png)

At that point the impact was clear: what initially looked like blind XXE could be used as an SSRF/file-read primitive and ultimately expose privileged Windows deployment credentials.

This was the first post I published on Corporal Bugz. The main lesson still stands: once you confirm an interaction, keep exploring the reachable protocols and the data the server-side parser can access. The interesting impact is often several steps beyond the initial collaborator hit.
