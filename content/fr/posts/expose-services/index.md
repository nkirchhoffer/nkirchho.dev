---
title: "Exposer des services Kubernetes sur Internet depuis chez soi"
date: 2023-05-06T00:00:00+02:00
draft: false
showTableOfContents: true
cover: "/posts/expose-services/cover.png"
coverAlt: "Couverture générée par Midjourney affichant une île remplie d'immeubles à l'esthétique proche de serveurs informatiques, avec un bateau accosté à ses côtes"
tags: ['homelab', 'cloudflare', 'kubernetes', 'metallb']
description: "Vous n'avez pas envie d'ouvrir les ports de votre connexion Internet personnelle ? Lisez cet article!"
showTaxonomies: true
---
Le but ultime pour un-e ingénieur-e Cloud et un-e administrateur-trice système qui s'intéresse au DevOps est de déployer un cluster Kubernetes sur ses machines pour automatiser la gestion de cycle des applications. 

Seulement, en bare metal, cette tâche peut s'avérer laborieuse et on ne dispose pas de tous les outils du cloud public pour nous aider...

Notamment, utiliser des Load Balancers au sein de son cluster Kubernetes pour exposer des services paraît tout bonnement impossible sans architecture réseau propice ! On ne dispose pas de routeurs BGP, on ne dispose pas non plus de Load Balancer physique qui permet de faire du failover (détecter un serveur en panne et diriger le trafic vers d'autres serveurs fonctionnels)...

Dans cet article, je vais tenter de vous donner une solution correcte pour exposer votre cluster Kubernetes à Internet en toute sécurité et gratuitement ! 

## Le problème

Celleux qui ont déjà tenté de déployer un cluster Kubernetes sur une infrastructure bare-metal ont sûrement connu le statut "\<pending\>" interminable sur les services LoadBalancer... et les migraines qui ont suivi.

{{< figure
    src="/posts/expose-services/svc-pending.png"
    alt="External-IP \<spending\> sur un service LoadBalancer"
    caption="La propriété External-IP reste bloquée sur \<pending\> pour les services LoadBalancer"
    >}}

En fait, sur les cloud publics, les produits Load Balancer (tels que Cloud Load Balancing sur Google Cloud) fournissent une adresse IP aux services LoadBalancer des clusters Kubernetes. Sur notre infrastructure bare metal, nous ne disposons pas de ce service...

## La solution

Heureusement, l'écosystème Kubernetes est très riche, et une solution a été proposée à ce problème spécifique, il s'agit de [MetalLB](https://metallb.universe.tf).

Ce logiciel s'inspire du principe de Virtual IP pour fournir des adresses IP aux LoadBalancers Kubernetes. Il utilise ARP en mode L2 et BGP pour diffuser les adresses fournies.

Si vous déployez un cluster Kubernetes chez vous, vous n'aurez en général pas accès à des routeurs BGP, ni à une pool d'adresses IPv4. On va donc se concentrer sur le mode L2 de MetalLB.

{{< alert >}}
Notez que le mode L2 de MetalLB ne permet pas un failover avec zero downtime, à cause du fonctionnement d'ARP. Le temps que la panne d'un noeud soit détectée et que le cache ARP des autres noeuds soit actualisé, il peut s'écouler quelques précieuses minutes.
{{</ alert >}}

Pour l'installation, référez-vous à la page suivante : [MetalLB, bare metal load-balancer for Kubernetes](https://metallb.universe.tf/installation/)

MetalLB via L2 utilise ARP (Address Resolution Protocol) pour associer un [Service](https://kubernetes.io/docs/concepts/services-networking/service/) à une adresse IP spécifique. Ceci permet de balancer la charge grâce aux mécanismes natifs des Services Kubernetes (notamment grâce à `kube-proxy`).

En pratique, imaginons que l'on dise à MetalLB d'utiliser la plage d'adresses 10.0.0.100-10.0.0.200 pour exposer les services, et que le service `nginx` se voit attribué l'adresse `10.0.0.100` : 

*Si vous utilisez le mode Sombre, vous aurez peut-être des difficultés à lire le diagramme suivant. Il s'agit d'un bug d'affichage lié au thème Hugo que j'utilise. Je suis en train de réfléchir à une solution.*

{{< mermaid >}}
sequenceDiagram
    participant Utilisateur
    participant MetalLB Operator
    participant Service nginx

    Utilisateur->>MetalLB Operator: Qui a 10.0.0.100 ?
    MetalLB Operator->>Utilisateur: 10.0.0.100 est à f9:a7:bc:fe:00:3a
    Utilisateur->>Service nginx: Requête HTTP GET /
    Service nginx->>Utilisateur: Réponse HTTP
{{</ mermaid >}}

{{< badge >}}
Précision
{{</ badge>}}

Une requête ARP se fait en broadcast (vu qu'on ne connaît pas qui est à l'adresse IP concernée), mais le diagramme ci-dessus a été simplifié au maximum. L'opérateur MetalLB concerné (et pas tous les opérateurs de tous les noeuds) répond avec l'adresse MAC associée au Service et au Pod de l'application déployée, que l'utilisateur peut donc contacter. Il faut que l'utilisateur et le cluster Kubernetes soient situés sur le même domaine de broadcast pour que MetalLB fonctionne.

Lire plus au sujet de MetalLB L2 (notamment ses limites) [ici](https://metallb.universe.tf/concepts/layer2/).

Pour indiquer une pool d'adresses IP que MetalLB peut utiliser, il faut la déclarer via un manifest : 

{{< highlight yaml "linenos=table" >}}
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: home-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.100-192.168.2.200
{{</ highlight >}}

Il faut aussi lui dire d'annoncer cette plage d'adresses IP via L2 :

{{< highlight yaml "linenos=table" >}}
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: home
  namespace: metallb-system
spec:
  ipAddressPools:
  - home-pool
{{</ highlight >}}

{{< alert >}}
Il ne faut pas que ces adresses IP soient utilisées pour éviter les conflits d'adresses et ainsi des problèmes de connectivité. Si vous utilisez DHCP sur le réseau en question, excluez la plage d'adresses MetalLB de votre plage DHCP pour pas qu'elles ne soient attribuées à vos appareils domestiques.
{{</ alert >}}

Désormais, MetalLB va donc assigner une adresse IP à votre service, que vous pourrez obtenir via `kubectl get svc` :

{{< figure
    src="/posts/expose-services/assigned-address.png"
    alt="Le service blog se voit attribuer l'adresse 10.0.0.104 par MetalLB"
    caption="MetalLB a assigné l'adresse `10.0.0.104` au service `blog`."
    >}}

Votre service est donc désormais exposé sur votre réseau local, vous accédez bien à l'application déployée, mais vous désirez l'héberger sur votre nom de domaine.

Vous pouvez désormais installer un [Ingress Controller](https://kubernetes.io/fr/docs/concepts/services-networking/ingress/) pour router vos sites Internet !

## Exposer votre service à Internet

Historiquement, une solution simple consistait à effectuer un PAT (ou "ouvrir les ports") sur votre box Internet. Cela correspond à autoriser les connexions à des ports spécifiques liés à votre adresse IP publique.

Plusieurs inconvénients :
- Vous exposez votre réseau domestique
- Votre adresse IP publique peut changer (en fonction des baux DHCP de votre FAI)
- Vous ne pourrez pas toujours pointer vers une adresse IP virtuelle en fonction de votre FAI

Une solution possible pour contourner ces limitations est [CloudFlare Tunnels](https://www.cloudflare.com/fr-fr/products/tunnel/). Il s'agit d'un agent, que vous installez sur une machine de votre réseau domestique, qui va établir une connexion entre Cloudflare et votre réseau.

Cette méthode présente plusieurs avantages :
- Cloudflare se comporte comme un reverse proxy, n'exposant pas directement votre réseau
- Cloudflare gère vos certificats TLS publics
- Aucune configuration n'est requise, l'installation de l'agent étant triviale

{{< alert >}}
Ce service est gratuit jusqu'à 50 utilisateurs, mais il faut quand même renseigner une carte bancaire ou associer son compte PayPal. Je vous conseille d'utiliser une carte bancaire virtuelle pour éviter de divulguer vos informations bancaires.
{{</ alert >}}

Pour mettre en place ce service, il faut déjà configurer les champs NS de votre nom de domaine pour pointer vers les serveurs de Cloudflare. Suivez la marche à suivre sur [CloudFlare Dash](https://dash.cloudflare.com). Il faut ensuite se diriger vers la catégorie "Zero Trust", choisir un plan (gratuit), puis se diriger vers la page `Tunnels` dans la catégorie `Access`.

Cliquez sur le bouton `Create a tunnel`, donnez un nom à votre tunnel (par exemple: home), puis choisissez une méthode d'installation pour l'agent. Je recommande d'utiliser Docker et d'héberger l'agent en-dehors de votre cluster Kubernetes pour ne pas se situer dans le même périmètre de panne. Notez bien que si la machine hébergeant votre agent est down, votre site Internet le sera également.

Si le statut du tunnel est `HEALTHY`, vous pouvez ensuite associer un sous-domaine (ou votre domaine principal) à une adresse IP privée (et même un port si nécessaire). En réalité, Cloudflare va héberger un proxy vers votre adresse locale via un sous-domaine et y pointer un CNAME pour l'exposer sur votre (sous-)domaine.

{{< figure
    src="/posts/expose-services/tunnel.png"
    alt="Mise en place du tunnel Cloudflare"
    caption="Mise en place du tunnel Cloudflare. Ici, `blog.nkirchho.dev` va pointer vers l'adresse IP privée `10.0.0.104` fournie par MetalLB et désignant le service `blog`."
    >}}

Si vous utilisez un Ingress Controller, vous devrez peut-être préciser la valeur du header HTTP `Host` manuellement sous les paramètres `HTTP Settings` du tunnel, pour que votre controller détermine à quel service vous voulez accéder.

## Solution alternative

Habituellement, j'aurais présenté une solution libre et open-source pour répondre à ce besoin spécifique. Cependant, étant donné le fonctionnement actuel d'Internet, notamment les difficultés d'accès à des adresses IPv4, le plus simple est de dépendre d'un service-tiers, ici, Cloudflare. Je ne dispose d'aucune relation commerciale, autre qu'une relation client, avec eux.

Si vous ne souhaitez pas dépendre d'une société américaine, vous pouvez toujours utiliser le principe de DNS dynamique pour assigner continuellement (même quand elle change) votre adresse IP publique à votre (sous-)domaine. Ceci ne retire cependant pas les autres inconvénients de la méthode PAT.

À noter que dans tous les cas, vous dépendez d'un hébergeur pour votre nom de domaine. Ce n'est alors qu'une question de point de vue personnel. Cloudflare étant une entreprise omniprésente sur Internet et qui défend (notamment) la liberté d'expression, je ne m'y oppose pas formellement et je ne me refuse pas d'utiliser leurs services.

À vous de voir !

Merci de m'avoir lu jusqu'ici. Si vous voulez faire un commentaire ou me poser une question, vous pouvez le faire à expose-services[at]nkirchho[dot]dev, ou ouvrir une PR sur le [GitHub de mon blog](https://github.com/nkirchhoffer/nkirchho.dev).