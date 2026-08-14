---
layout: post
title: "Finding Initial Access on a real life Penetration Test"
date: 2023-03-23
updated: 17 Jan 2024
permalink: /post/finding-initial-access-on-a-real-life-penetration-test/
hero: /assets/img/posts/initial-hero.jpg
tags: [Active Directory, Internal, vCenter]
read_time: 3
excerpt_text: "When relaying and password cracking dry up, an exposed vCenter can become the route to domain dominance."
---
On a recent internal penetration test I was faced with a familiar problem: getting the initial foothold was harder than getting to Domain Admin once a foothold existed.

Putting red-team tactics such as phishing to one side, initial access usually comes down to one of two things:

- Something vulnerable
- Credentials

Responder, `mitm6`, relaying and other man-in-the-middle techniques are often useful for capturing credentials, but this client had been assessed repeatedly and had worked hard on its password policy. Even with serious cracking power the captured hashes were proving difficult to turn into useful plaintext credentials, and relaying/coercion didn't provide a path either.

That left **finding something vulnerable**.

#### Enter vCenter

A vulnerable vCenter instance was identified that was affected by **CVE-2021-22005**, an unauthenticated file-upload issue in VMware vCenter Server that could lead to remote command execution.

![vCenter exploitation output](/assets/img/posts/initial-vcenter.png)

With root access to vCenter, the next target was the `data.mdb` database. This can contain material relevant to vCenter's SSO and signing infrastructure.

![Retrieving vCenter data](/assets/img/posts/initial-data.png)

Using tooling released by Horizon3.ai, the extracted material could be used during the authorised assessment to create a session for the vSphere UI.

![vCenter session cookie creation](/assets/img/posts/initial-cookie.png)

Once authenticated to vSphere as an administrator I considered two practical routes:

- Download VMDKs, mount them locally and obtain `SYSTEM`, `SAM` and `SECURITY` hives for offline credential extraction.
- Inspect the available VMs for opportunities to gain an authenticated foothold.

I started with VMDK downloads. The files needed to come from stopped VMs or suitable backups and could be very large.

![Downloading a VMDK from vSphere](/assets/img/posts/initial-vmdk.png)

The workflow involved creating device mappings for the VMDK partitions with `kpartx`, mounting the appropriate filesystem, copying the registry hives and using `secretsdump` offline. That produced some RID 500 local-account hashes and cached domain credentials, although they did not immediately unlock the wider domain.

The more interesting route came from opening the VM consoles. One machine dropped straight to a desktop without requiring a password. Sophos was present, so I wanted to establish a controlled foothold quickly. I used the VM to trigger authentication back towards my assessment host, captured the resulting authentication and relayed it to permitted targets. The relayed privileged context allowed credentials to be recovered from two management servers.

Among the resulting secrets was a service account credential stored in an LSA secret. That account was a Domain Admin.

I still wanted to validate impact with a Cobalt Strike beacon. Before putting a payload on the Domain Controller I used a hook-detection utility to identify Windows APIs instrumented by the endpoint security product.

![Windows API hooks identified on the target](/assets/img/posts/initial-hooks.jpg)

With an appropriate execution technique selected for the authorised test, I established a high-integrity beacon on the Domain Controller and demonstrated domain dominance by obtaining the `krbtgt` credential material through DCSync.

The lesson from this engagement wasn't that any single technique was novel. It was that when the obvious routes dry up, infrastructure such as virtualisation management can provide an unexpectedly powerful bridge from a vulnerable appliance to the Windows estate behind it.
