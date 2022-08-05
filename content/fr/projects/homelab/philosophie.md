---
title: "Philosophie du homelab"
date: 2022-08-05T21:40:16+02:00
tags: ['philosophie', 'homelab']
---
Le homelab est un peu plus qu'un projet technique que l'on lance pour se faire plaisir, il s'agit en réalité de toute une philosophie qui reflète des visions plus globales sur l'industrie.

## Qu'est-ce que le "homelab" exactement ?

Le homelab, c'est l'art d'héberger des services alternatifs aux plus grandes plateformes de cloud que l'on peut trouver, souvent en réutilisant du matériel d'occasion ou de plus petites machines, comme des Raspberry Pi, des NAS intégrés et des NOC.

Il est donc fortement associé au self-hosted, dont on peut retrouver une liste de **beaucoup** d'excellents produits ici : [https://github.com/awesome-selfhosted/awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted).

Plus largement, le homelab comprend également l'administration de systèmes, souvent des questions d'orchestration, d'hypervision, de virtualisation ainsi que du réseau, parfois du développement et beaucoup de troubleshooting.

## Mon projet

Mon architecture de homelab est axée autour de l'amélioration de la productivité, de l'hébergement de projets personnels ainsi que de montée en compétences sur des sujets cloud, ainsi, j'y intégrerai principalement les technologies suivantes :

* Virtualisation avec [Proxmox VE](https://www.proxmox.com/en/)
* Routage, VLANs, pare-feux et VPN avec [pfSense](https://www.pfsense.org/)
* Orchestration avec [k3s](https://k3s.io/)
* Load Balancer TCP/IP avec [MetalLB](https://metallb.org/)
* Virtual IP avec [kube-vip](https://kube-vip.io/)
* NAS avec [TrueNAS](https://www.truenas.com/)
* Volumes distribués avec [Longhorn](https://longhorn.io)
* Services hébergés :
  * Écran d'accueil avec [Heimdall](https://github.com/fterh/heimdall)
  * Monitoring avec [Uptime Kuma](https://github.com/louislam/uptime-kuma)
  * Serveur DNS avec [PiHole](https://pi-hole.net/)
  * et plein d'autres à venir !

Pour héberger tout ce beau monde, nous disposons de 2 Dell PowerEdge R620 équipés, chacun, de 2x Intel Xeon E5-2640 (2.5GHz, 6c/12t), 64 GB de RAM, 4 x 600 GB HDD SAS, 2 PSU et 4x 1Gbps RJ45. Le coût unitaire de ces machines était de 319 EUR sans livraison. Vous pouvez retrouver cette offre ici : [https://www.ebay.fr/itm/255195436577](https://www.ebay.fr/itm/255195436577) (vendeur très sérieux au passage).

## Mon besoin

Le besoin de ce projet homelab vient d'une volonté de se détacher des services cloud pour le stockage de documents, la prise de notes, la rédaction de documents, l'hébergement de code source, et même l'hébergement de projets finaux. Étant un ingénieur DevOps certifié Google Cloud Platform, j'ai également ressenti la nécessité de garder la propriété incessible de ses projets, même personnels et sans grand intérêt. La donnée est ce qui vous définit, il devrait être la priorité de chacun de faire en sorte de la conserver.

Cela fait maintenant 5 ans que je loue un serveur dédié chez OVH, et bien qu'il m'ait vaillemment accompagné tout le long, en me trahissant très rarement, je sentais que je pouvais tirer un meilleur parti des serveurs dédiés en achetant moi-même du matériel, qui, malgré le prix initial d'achat, me permettrait de rentabiliser rapidement l'achat par rapport à la location.

Quand vous louez un serveur dédié chez un hébergeur, votre matériel ne sera pas mis à jour au fur et à mesure qu'ils mettent à jour leur infrastructure, vous devrez effectuer cette mise à jour manuellement. Compte tenu des prix de location, relativement élevés (à raison), il serait plus rentable d'acheter du matériel d'occasion "déprécié" plutôt que de louer un matériel qui deviendrait obsolète assez rapidement.

Bien sûr, l'hébergeur inclut dans le tarif final les charges de réseau (redondant et haut débit), d'électricité (redondante) et divers frais de service permettant de maintenir l'opérationnalité de votre serveur. Ces aspects-là seront de **votre** responsabilité dans un homelab.

## Les limites du homelab

Les premières limites du homelab ont en fait déjà été cités à la toute fin de la dernière section. Vous êtes responsable de toute l'infrastructure permettant l'hébergement de vos services (desquels vous êtes aussi responsable). De ce fait, vous devez calculer l'intérêt de redonder votre connexion Internet, votre réseau électrique ou même votre infrastructure en elle-même, s'il y en a.

Un des aspects qu'il faut aussi tenir en compte, vous devez administrer vos serveurs vous-mêmes. Vous n'aurez pas d'alternatives serverless pour vos services, sauf si vous en hébergez une. Vous n'aurez pas de CDN, sauf si vous en implémentez un. Vous devez **tout** gérer vous-mêmes.

Mais les deux derniers points sont le prix à payer pour la liberté : peu de gens ont réellement besoin d'une infrastructure résiliante, et la montée en compétences transverse est garantie à partir du moment où vous achetez votre matériel.

Et je vais couvrir tous ces aspects dans de prochains articles !