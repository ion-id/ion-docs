# Beginners Guide to Beckn for ION participants

## Introduction
ION (Indonesia Open Network) is envisoned as a open, interoperable digital public infrastructure for commerce. To achieve a this, ION relies on an open commerce protocol called [Beckn](https://beckn.io). ION builds on this protocol with additions relevant to the Indonesian regulation and ecosystem. This note is an informal list of annotated links to better understand Beckn (the base protocol), for ION participants. Other documents in this folder will have more focus on ION specific details and guidance. 

## Beckn Protocol
Beckn Protocol has evolved over the past six years (Since early 2020). Currently it is being stewarded by [Networks For Humanity(NFH)](https://networksforhumanity.org/). 

1. In order to start learning about the latest version of Beckn (v2.0), start with the [introduction to protocol here.](https://docs.nfh.global/beckn/introduction-to-beckn/beckn-protocol). It covers all the technical details succinctly.
2. To understand the motivation behind the origin of Beckn and how it is different from other approaches for commerce, view some [introductory videos from Ravi, one of the founders of the protocol](https://www.youtube.com/playlist?list=PLBC6c8MLy9uVUIb1BOgdOa8tP4rX6c4aK). While together with later videos, this playlist provides a comprehensive overview of v1 of Beckn, it refers to many actors (e.g. Gateway), actions (e.g. search,on_search), and objects (e.g. Order), which have been removed or renamed in V2. 
3. Try out Beckn on your own machine before diving into the details. The details [to setup an entire working Beckn network locally are here](https://docs.nfh.global/beckn/experience-beckn/getting-started-with-beckn)
4. To make it easy to build your own applications on the Beckn protocol, NFH has released an adapter called "Beckn ONIX" which facilitates communication on any Beckn enabled network. You can read an [introduction to ONIX here](https://docs.nfh.global/beckn/introduction-to-beckn/fabric-the-value-exchange-infrastructure/connect-to-everything-using-beckn-onix) or a [deep dive here](https://github.com/beckn/beckn-onix)
5. To get an idea of how Beckn can be adapted to various domains, see examples from [Retail](https://github.com/beckn/local-retail) and [Electric Vehicle Charging network](https://github.com/beckn/DEG/blob/main/docs/implementation-guides/v2/EV_Charging/EV_Charging.md). 
6. In addition to these, coming soon (May 2026) will be a developer labs website for ION participants. With this, you can sign up for ION and get access to a portal that allows developers to try Beckn protocol, understand, buid around it and test their own apps on staging for ION protocol compliance. 

## Links to other Beckn resources
- [The basic document hub of latest version of protocol](https://docs.nfh.global/beckn)
- [The organization root on github which lists all Beckn related repositories](https://github.com/beckn/)
- [The latest protocol specification](https://github.com/beckn/protocol-specifications-v2)
- [ONIX - The adapter that helps you get onto Beckn networks easily](https://github.com/beckn/beckn-onix)
- Beckn protocol ensures that communications between parties are signed and can be verified, thereby providing cryptographic proof of identity of sender and ensuring packet is not altered. The [nitty gritty details are here](https://github.com/beckn/protocol-specifications/blob/master/docs/BECKN-006-Signing-Beckn-APIs-In-HTTP-Draft-01.md)
- Beckn has published various Schema objects covering multiple domains. They can be browsed at the [Beckn Schema repository](https://schema.beckn.io/)
- Apart from Beckn protocol and ONIX, ION uses other NFH infrastructure including Registr and Catalg. [Details of these can be found here](https://docs.nfh.global/beckn/introduction-to-beckn/fabric-the-value-exchange-infrastructure)