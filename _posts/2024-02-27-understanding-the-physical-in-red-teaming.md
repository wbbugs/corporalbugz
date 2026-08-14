---
layout: post
title: "Understanding the 'Physical' in Red Teaming"
date: 2024-02-27
permalink: /post/understanding-the-physical-in-red-teaming/
hero: /assets/img/posts/physical-hero.jpg
tags: [Physical, Red Teaming, Social Engineering]
read_time: 6
excerpt_text: "Threat profiling, social engineering, props and the tradecraft behind physical security assessments."
---
A simple search for red-team training returns a huge amount of material covering cyber intrusion, penetration testing and adversary emulation. One area that receives much less attention is the **physical** dimension of red teaming.

Addressing physical vulnerabilities matters because a well-rounded assessment may need to simulate real-world access to premises, assets or personnel — not just attacks against networks and applications.

#### What is Physical Penetration Testing?

Physical penetration testing focuses on evaluating the controls protecting an organisation's premises, equipment and people. Depending on the engagement, that might include access control, tailgating resistance, social engineering, security processes, removable media, unattended systems or other physical attack paths.

#### Considerations

Physical testing needs a thorough pre-engagement process. In my opinion this is the most important phase, because the consequences of getting the scope wrong are much greater than with many purely technical tests.

Key areas include:

1. Scoping
2. Rules of engagement
3. Cost
4. Duration
5. Threat profile and planning
6. Explicit authorisation

The goals and objectives need to be unambiguous and aligned with the organisation's real security concerns. Any physical testing should be discussed in depth and explicitly authorised. A red team can run for a prolonged period and may involve multiple testers, travel and additional operational costs.

#### Threat Profiling

A threat profile identifies the adversaries and attack paths that are relevant to the organisation. It helps the team decide which scenarios are realistic enough to emulate.

Useful areas to model include:

- **Adversaries** — who might realistically target the organisation?
- **Attack vectors** — phishing, social engineering, malware, physical access and other paths.
- **Tactics, techniques and procedures** — the behaviours the chosen adversary would actually use.

Once a threat profile is established, the red team can emulate it against the in-scope targets. Physical testing may be relevant to scenarios involving tailgating, theft, malicious insiders, vandalism or covert access.

#### Tradecraft

Each threat has its own tradecraft: the tactics, techniques and procedures that need to be simulated. Red teamers develop their own approaches over time while borrowing heavily from disciplines such as social engineering, intelligence gathering and covert operations.

![Lockpicking practice during a security event](/assets/img/posts/physical-lockpicking.jpg)

#### What skills do I need to learn?

These aren't in any particular order, but I believe a competent physical tester needs skills in several areas:

1. **Social engineering** — confidently navigating conversations, persuading people and eliciting information while maintaining a believable pretext.
2. **OSINT** — using online research, observation, public records and other lawful sources to learn about buildings, people, suppliers, access controls and working patterns.
3. **Tooling** — depending on the scope this may include badge-related tooling, lock picks, drop boxes, USB devices, covert cameras, communications equipment or wireless kit.
4. **Props** — uniforms, fake passes and other items that support an authorised pretext. A believable appearance can sometimes bypass surprisingly strong controls.
5. **Creativity** — a good cover story needs to survive basic questioning: who are you, why are you there, who sent you and who can confirm the story?

A pretext has to be coherent. If you are posing as a service engineer, for example, the clothing, equipment, company knowledge and contact details all need to match the story.

#### How do I learn Physical Penetration Testing?

Unless you come from law enforcement or the military, specialist physical-engagement training can be expensive and is often bespoke. Working alongside experienced red teamers on properly authorised engagements can therefore be an invaluable way to develop the skill set.

Security conferences can also help. Events such as DEF CON include villages and workshops dedicated to areas such as lockpicking and social engineering.

![Team members at DEF CON](/assets/img/posts/physical-defcon.jpg)

![Social Engineering Village at DEF CON](/assets/img/posts/physical-se-village.png)

#### Closing Notes

Physical testing isn't for everyone. It can involve walking into unfamiliar situations alone, maintaining a false persona and dealing with people who may actively challenge you.

If you're new to it, build confidence gradually and legally. Learn to talk comfortably to strangers, study persuasion and human behaviour, practise locks only where you have permission, and seek supervised training before attempting real engagements.

I've been fortunate to attend bespoke physical training delivered by former military and law-enforcement professionals covering surveillance, counter-surveillance, communications, dress and mannerisms, and cover stories. Even after successful engagements, there is always more tradecraft to learn.

**Do not practise physical intrusion techniques against any organisation or individual without explicit authority.** The legal and personal consequences of getting that wrong can be serious.
