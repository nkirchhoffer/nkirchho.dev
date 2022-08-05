---
title: "Homelab's Philosophy"
date: 2022-08-05T21:40:16+02:00
tags: ["philosophy", "homelab"]
---
The homelab is a little more than just a technical project "just for fun", it is also a whole philosophy that reflects many global issues of the IT industry.

## What exactly is a "homelab" ?

The Homelab is the art of hosting alternative services to major cloud providers we can find, often by reusing enterprise-grade hardware or smaller platforms such as integrated NAS, Raspberry Pi's and NOC.

It is associated with the "self-hosted" philosophy, whose services we can find on this GitHub repository : [https://github.com/awesome-selfhosted/awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted).

Maintaining a homelab also includes system administration (sysadmin), orchestration, hypervision, virtualization, networking, programming and troubleshooting. It requires many skills to have its own homelab !

## My project

My homelab's architecture is based around improving my productivity, hosting my personal projects and improving my own skills on cloud computing subjects. This way, I will mainly host these different technologies :

* Virtualization with [Proxmox VE](https://www.proxmox.com/en/)
* Firewalls, VPNs, routing and VLANs (SDN) with [pfSense](https://www.pfsense.org/)
* Orchestration with [k3s](https://k3s.io/)
* TCP/IP Load Balancing with [MetalLB](https://metallb.org/)
* Virtual IP with [kube-vip](https://kube-vip.io/)
* NAS platform with [TrueNAS](https://www.truenas.com/)
* Distributed volumes with [Longhorn](https://longhorn.io)
* Hosted services :
  * Home page with[Heimdall](https://github.com/fterh/heimdall)
  * Monitoring and alerting with [Uptime Kuma](https://github.com/louislam/uptime-kuma)
  * DNS servers with [PiHole](https://pi-hole.net/)
  * and many others to come !

To host everything, we own 2 Dell PowerEdge R620 equipped with 2x Intel Xeon E5-2640 (2.5 GHz, 6c/12t), 64 GB of RAM, 4 x 600 GB SAS Hard drivers, 2 PS and 4x 1 Gbps NICs. The unit price of these servers was 319 EUR without shipping. You can find this offer right here : [https://www.ebay.fr/itm/255195436577](https://www.ebay.fr/itm/255195436577) (very serious seller, by the way).

## My needs

This project comes from a will to detach myself from cloud services for documents storage, notes taking, documents writing, source code and final projects hosting. Being a Google Cloud certified DevOps Engineer, I felt the need to keep my projects' whole property, even personal ones without much commercial interest. Data is what defines you on the Internet, it should be your number one priority to try to keep it for yourselves.

I have been renting a dedicated server for 5 years at OVH, and, even though it was very useful along my way and never once failed, I felt that I could make a better profit purchasing one myself, which I did, even if it costs much more initially.

When you rent a dedicated server at a provider, your hardware won't be upgraded when their whole infrastructure is upgraded. You will keep the same hardware for the whole rent duration. So, in order to upgrade your machines, you would have to migrate every service you own manually. If you would have to do this every now and then, I feel like you'd be better by having your own hardware.

Of course, renting a server has a lot of benefits. First of all, the provider takes care of the networking and electricity for you, it is included in the price, which is the better part of renting a server. You also pay for maintenance fees, including that if you have major issues, their staff will be there to help you resolve them (at most providers, though). With your own infrastructure, these aspects will be at **your** responsibility.

## Homelabs' limitations

The first limitations of homelabs have been listed at the end of the previous section. You are responsible of the whole infrastructure behind your services, even electricity and networking. Without forgetting that you will also be responsible for your services themselves. That said, you will need to determine whether or not a homelab is your fit. 

Lots of people don't need resilient infrastructures with high availability and redundancy, but, be conscious that you rely on and **only** on yourself to host your data. So, I recommend having at least 1 copy on an another server and 1 copy off-site to prevent from physical incidents where your servers are established.

Hosting services by yourself, you won't have a serverless platform on which **rapidly** host your source code, you would have to make one. You also won't have a CDN to propagate static assets on the world, neither would you have a dedicated worldwide resilient network with many POPs (Points of Presence), you would have to rely on another infrastructure (which may question the principle of having a homelab). Every managed service you've used with a Cloud Provider, you would have to host yourself.

People mainly build homelabs to have fun and make things by themselves, it is the best way to improve your system administrations, virtualization and networking skills, it is also a great way to understand the responsibilities you can have when providing services to customers. 

Besides all the previous limitations, Homelabs are great, you should definitively build one ! 

