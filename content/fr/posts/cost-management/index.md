---
title: "L'enjeu de la maîtrise des coûts dans le cloud public"
date: 2023-05-10T00:00:00+02:00
draft: false
showTableOfContents: true
cover: "/posts/expose-services/cover.png"
coverAlt: "Couverture générée par Midjourney affichant une île remplie d'immeubles à l'esthétique proche de serveurs informatiques, avec un bateau accosté à ses côtes"
tags: ['serverless', 'architecture', 'microservices', 'overengineering']
description: "En 2023, nombreuses sont les entreprises à effectuer des changements d'infrastructure suite aux coûts du cloud public"
showTaxonomies: true
---
Les coûts des services du cloud public ont toujours fait partie du débat dans l'industrie. Jugés plus ou moins transparents en fonction des plateformes, les ingénieur.e.s Cloud ont souvent redoublé d'effort pour réduire la facture à chaque fin de mois.

L'actualité s'est encore saisie du dossier ces dernières semaines lorsque Prime Video a [annoncé transformer son architecture micro-services en monolithe](https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90) pour réduire leur facture cloud de 90% (leur plateforme cloud étant d'ailleurs une autre filiale d'Amazon, AWS).

> Moving our service to a monolith reduced our infrastructure cost by over 90%. It also increased our scaling capabilities. Today, we’re able to handle thousands of streams and we still have capacity to scale the service even further.<br>
> — Marcin Kolny, Senior SDE @ Prime Video

Dans la sphère technique, on a également observé avec stupéfaction le blog de Stack Overflow (plus largement Stack Exchange) démontrant qu'il était encore possible, en 2023, [d'encaisser un trafic conséquent](https://stackexchange.com/performance) (1.3 milliard de pages vues par mois) en demeurant complètement On-Premise.

{{< figure
    src="/posts/cost-management/stackexchange-performance.png"
    alt="La page de StackExchange présentant leur infrastructure. Ils indiquent 1.3 milliards de pages vues par mois, 55 TB de données transférés"
    caption="Infographie de StackExchange présentant leur volumétrie"
    >}}


Sans être dans une démarche radicale de refus du cloud public, quels peuvent être les leviers d'action pour en réduire sa facture (parfois monstrueuse) ? 

## La qualité logicielle comme levier d'action sur le coût des services

Avec 9 serveurs web IIS et 4 serveurs de bases de données SQL Server (ainsi que quelques couches de cache), Stack Exchange Network démontre qu'il est encore possible de gérer un site web recensant plus de 20 millions d'utilisateurs simplement. Leur stack logicielle n'a pas beaucoup changé depuis leur ouverture en 2008, mettant en oeuvre une architecture MVC avec C# et ASP.NET, ainsi qu'un ORM maison avec [Dapper](https://github.com/DapperLib/Dapper).

Un seul serveur de proxy live HAProxy leur est requis pour diriger toutes les requêtes vers un serveur web, et un deuxième est positionné en failover.

La métrique qu'ils mettent le plus en avant est le temps de rendu des pages de leurs sites web. Ils avaient [mis à jour leur infrastructure matérielle en 2015](https://blog.serverfault.com/2015/03/05/how-we-upgrade-a-live-data-center/) en gardant cet objectif de réduction du temps de rendu en tête.

Cette métrique de temps de rendu est intéressante, car, au-delà du simple fait d'augmenter la capacité des serveurs (mise à l'échelle verticale), il faut absolument penser le logiciel de manière efficace. Il paraît compliqué de garantir de bonnes performances lorsque, même sans la contrainte inévitable de trafic et de capacité, une requête obtient une réponse lente. Souvent, le choix d'un langage et d'un framework réputés rapides permet déjà de se simplifier la tâche, même si le design du code est un facteur majeur à prendre en compte lorsque l'on développe un site web.

Pour maintenir cette métrique et respecter leurs objectifs, ils appliquent une démarche importante et protocolaire quant à [l'observabilité de leurs systèmes](https://blog.serverfault.com/2013/11/26/the-importance-of-observability/). De la même façon, ils expriment l'importance des outils DevOps et insistent surtout sur le besoin qu'ils s'adaptent à leur infrastructure et à leur organisation. 

Stack Exchange ne semble pas avoir adopté la mode des frameworks réactifs, probablement parce qu'une requête HTTP traitée par un framework MVC permet de rendre une page complète, ce qui n'est pas le cas des architectures découplées, nécessitant un ou plusieurs appels d'API supplémentaires pour peupler les pages. Les temps de traitement et de rendu restant courts, la navigation sur le site web n'en est pas forcément impactée et reste agréable.

La logique paraît pourtant indéniable : plus l'architecture est simple, moins elle va consommer dans son ensemble. On se limite à l'essentiel, que ce soit au niveau des fonctionnalités comme des appels réseau. Encore plus étrange, ça semble (sans le vouloir à l'origine) se rapprocher de pas mal de [bonnes pratiques d'éco-conception](https://github.com/cnumr/best-practices). Bizarre, non ? Qui aurait pu croire que consommer moins de ressources était également bon pour l'environnement ? :face_with_rolling_eyes:

## Les micro-services, un "silver bullet" parfois inefficace

Pendant longtemps et sans réel discours contradictoire, les micro-services ont été enseignés comme une référence à beaucoup d'ingénieurs logiciels. Moi y compris. Aujourd'hui, on se rend compte que l'application constante et automatique de ce pattern peut être contre-productive, voire néfaste au bon fonctionnement d'une application.

Pourtant, l'architecture micro-services est pleine de bonne volonté et de promesses : isoler des fonctionnalités sans rapport d'une application pour en réduire le couplage. On les appelle alors des services. Chaque service dispose de sa propre base de données, et de sa propre politique de mise à l'échelle. Plus simple de dupliquer des petites applications qu'un gros monolithe, non ? Fini le couplage fort, si on a besoin de récupérer du contenu d'un autre service, on envoie un message sur un canal. Aussi simple que ça.

Sur le papier, c'est la solution ultime pour encaisser de gros trafics. Il suffit d'augmenter le nombre d'instances du service qui subit le plus. Tout étant élastique, pas besoin de s'occuper du reste des services, qui suivront avec la charge si besoin.

Vous avez une super architecture élastique capable d'encaisser 15 trillions de requêtes par seconde si besoin, mais voilà ce avec quoi vous devez composer en tant qu'ingénieur.e dans le logiciel.

{{< figure
    src="/posts/cost-management/microservices-headache.png"
    alt="Une représentation des micro-services de Netflix : il y en a tellement qu'il est incapable d'en distinguer un seul"
    caption="Représentation des micro-services de Neftlix"
    >}}

Un nombre non-négligeable de requêtes sont en erreur sur un service précis : sauriez-vous le repérer dans ce sac de noeuds ?

L'observabilité tant chérie de Stack Exchange dont on discutait avant en prend un coup. À tel point que de nouvelles [méthodes de monitoring ont été mises au point](https://www.tfir.io/how-to-avoid-microservice-observability-traps/) pour pouvoir retrouver un semblant de repères dans un tel système.

On le voit : la complexité est croissante au fur et à mesure qu'une application évolue, que ce soit au niveau de ses fonctionnalités comme au niveau de son trafic.

Cette complexité, on l'accepte plus facilement quand une application brasse des milliards d'utilisateurs et autant de requêtes à la seconde. Elle semble normale pour des sites tels que Netflix et Amazon qui implémentent un modèle baptisé [Death Star](https://dzone.com/articles/navigating-the-microservice-deathstar-with-deployh).

Quand on déploie un blog ou une petite boutique en ligne, il paraît compliqué de justifier les budgets, tant de conception que de maintenance et d'hébergement, d'une telle architecture.

En parallèle, les plateformes cloud ont longtemps mis en avant la méthode micro-services en proposant des solutions simples et attirantes pour les développeur.se.s : le serverless. Le premier service de Google Cloud était d'ailleurs serverless, il s'agit d'App Engine. Sur AWS, beaucoup connaissent et utilisent Lambda.

Ces services sont très performants à toute échelle, le terme "serverless" impliquant qu'on n'a pas besoin de s'occuper de l'infrastructure sous-jacente de nos applications, la plate-forme cloud le faisant pour nous. Cependant, il faut bien comprendre que le coût de cette opération (appelée "overhead") est répercuté sur notre facture en fin de mois.

Facture qui, logiquement, en devient beaucoup moins lisible, étant donné qu'on multiplie le nombre de services entrant en jeu pour le maintien en vie de notre application. Chaque brique dispose de son propre coût et de son budget : l'hébergement de l'application sur les services serverless en question, le trafic réseau à travers les différents Load Balancers, le stockage en base de données, le throughput de nos bases de données (même managées), le stockage à plat de fichiers, etc...

Dans ce contexte, les technologies FinOps se développent, pour permettre aux équipes de clarifier les dépenses et d'identifier des postes d'améliorations.

Cependant, il convient de noter que pour de petites applications simples, ne disposant que d'un service ou deux, l'hébergement serverless est imbattable. Les faibles volumes de trafic et de données sont rarement facturés. Par exemple, ce blog est hébergé par [Cloudflare Pages](https://pages.cloudflare.com/), un service permettant d'héberger les applications JAMstack sans limites de requêtes ni de bande passante. J'ai également développé de petites applications pour le fun, ne stockant pas beaucoup de données et n'ayant pas une grande base d"utilisateurs, que j'ai hébergées sur App Engine avec BigQuery et Cloud Datastore, et qui me coûtent quelques centimes par mois tout au plus.

## Hybrider pour mieux régner

Si toutefois vous disposez de plusieurs applications qui font votre business, ou que vous êtes contraint.e.s à maintenir une complexité élevée de conception, il existe des solutions pour réduire le coût de vos infrastructures.

L'une d'entre elles est de faire recours au cloud hybride. 

Lorsque vous aurez repéré une base de consommation de vos services, établi un trafic type en fonction des jours, des différentes heures de la journée et de votre activité également, vous êtes capable de mettre en point une architecture plus précise que vous pouvez potentiellement héberger chez vous. 

Si votre application est bien conçue et ne demande pas trop de ressources, vous ne nécessiterez pas plus d'infrastructure que Stack Exchange. Il ne sera donc pas trop compliqué pour vous d'investir dans du matériel et de l'amortir sur 10 ans par exemple. 

Le cloud public, même si vous ne payez que l'usage concret de vos services, reste plutôt coûteux. Une VM de 2 vCPUs et 8 GB de RAM vous [coûtera 49 USD par mois](https://cloud.google.com/products/calculator#id=b2d34177-bc84-45dd-b951-00f30ccb76f1) avec Compute Engine sur Google Cloud, par exemple.

Annualisée, et rationnalisée par rapport aux ressources habituelles des serveurs d'entreprise, cette seule dépense peut être concurrencée par un bon dimensionnement d'un rack informatique, mais la charge de la dépréciation, de l'administration et du remplacement du matériel vous revient à vous seul.e.

Vous vous retrouvez alors à devoir embaucher du personnel qualifié dans l'infrastructure IT, à louer des baies ou des salles blanches (~ 500€/mois le demi-rack) et à entretenir l'infrastructure en elle-même. C'est donc une solution plus facile à aborder si vous disposez déjà de quelques serveurs On-Premise avec quelques personnels qualifiés.

Ensuite, le cloud hybride repose sur l'interconnexion entre votre matériel IT et les réseaux des Cloud Providers. Vous pouvez alors utiliser leurs solutions VPN, mais si votre trafic grandit, vous serez amenés à véritablement vous connecter avec la plateforme, comme le propose la solution [Cloud Interconnect](https://cloud.google.com/network-connectivity/docs/interconnect?hl=fr) chez Google Cloud. On parle alors de liens 4x 10 Gbps (ou 100 Gbps, rien que ça...), de manipulation de VLAN, de maîtrise du protocole eBGP et des notions de peerings. Il faut aussi que le datacenter dans lequel vous êtes en colocation soit déjà relié à votre provider. La liste des inconvénients est longue.

Ainsi, on peut imaginer que la maîtrise des coûts seule ne soit pas un argument suffisant à se diriger vers l'hybride, mais qu'on peut l'accompagner de notions de gouvernance de données (échapper aux GAFAM) et d'harmoniser les infrastructures d'outils internes avec celles des services publics.

Une pratique de l'hybridation cloud privé/cloud public qui me plaît est celle du Cloud Bursting. Le principe est simple, vous hébergez votre application à l'accoûtumée (par exemple sur un cluster Kubernetes On-Premise), maintenant le control-plane et les worker nodes. Si vous manquez de ressources, vous faites appel aux API de votre cloud provider pour réaliser votre mise à l'échelle horizontale, en ajoutant des workers. 

Des services permettant bien évidemment de réduire cette tâche parfois fastidieuse, sur [AWS](https://aws.amazon.com/fr/hybrid/) comme sur Google Cloud avec [Anthos](https://cloud.google.com/anthos?hl=fr)

La facture est alors beaucoup plus simple : on ne paie que les instances workers des moments de pics. 

Cette pratique est bien adaptée au e-commerce, où les pics de trafics ont souvent lieu et sont imprévisibles : périodes de soldes, Black Friday... historiquement, les administrateurs.trices systèmes dimensionnaient leur infrastructure par rapport aux pics historiques de leur application.

Encore une fois, les sujets de cloud hybride ou de Edge Computing sont relativement complexes à mettre en oeuvre et sont plus simples à aborder si vous disposez déjà d'un semblant de cloud privé.

## Conclusions

Difficile d'émettre une conclusion simple, efficace et définitive : la solution la plus adaptée à votre usage dépendra évidemment de vos besoins.

S'il y a bien une chose que cet article tend à prouver est qu'il n'existe pas de "silver bullet". Aucune solution n'est adaptée à tous les usages. L'ingénierie logicielle permet principalement à mieux comprendre les systèmes que l'on cherche à développer puis à déployer, à réfléchir à l'art et la manière de répondre, le plus simplement possible, au besoin global d'une application.

Cependant, on peut retenir les quelques notions suivantes : 
- Il ne faut pas avoir peur du cloud public. En réalité, la facturation se faisant à l'usage, vous ne paierez que ce que vous y déployez. La charge est alors à vous de la réduire avec les différents principes explicités dans cet article. Finalement, la facturation n'est que le reflet de la conception de vos applications.
- Il faut, au maximum, viser la simplicité. Une architecture simple permet d'en réduire sa consommation de ressources et ainsi sa facturation.
- Sur le principe du point précédent, appliquer le pattern des micro-services par défaut n'est pas forcément une bonne idée. Il vaut mieux construire un monolithe de manière intelligente, en suivant des principes tels que le [Single-Responsibility Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle), permettant ensuite de le découper en micro-services plus facilement si nécessaire.
- Globalement, les services managés (ou "serverless") peuvent être une bonne idée pour de petits applicatifs, mais le passage à l'échelle peut aussi augmenter les besoins de l'overhead (= la répercution du coût du management par le cloud provider), mais également votre dépendance à un service de messaging (disposant aussi de sa propre facturation, parfois élevée)

La question de la facturation sur le cloud public est complexe et peut être abordée de plein de façons différentes. J'espère avoir donné quelques pistes pour l'aborder avec moins de difficultés et ainsi la maîtriser plus sereinement !

Merci d'avoir lu cet article (bien monolithique 😊) jusqu'au bout. Si vous avez des remarques ou des questions, vous pouvez le faire à l'adresse mail suivante cost-management[at]nkirchho[dot]dev ou en [faisant une Issue/PR sur GitHub](https://github.com/nkirchhoffer/nkirchho.dev/issues/new).

## Références

- Scaling up the Prime Video audio/video monitoring service and reducing cost by 90%, Marcin Kolny, Mars 2023, https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90 (en)
- Performance - Stack Exchange, https://stackexchange.com/performance (en)
- Dapper - A simple object mapper for .Net, https://github.com/DapperLib/Dapper
- How we upgrade a live data center - Server Fault Blog, Mars 2015, https://blog.serverfault.com/2015/03/05/how-we-upgrade-a-live-data-center/ (en)
- The importance of Observability, Server Fault Blog, Novembre 2013, https://blog.serverfault.com/2013/11/26/the-importance-of-observability/ (en)
- Web Ecodesign Best Practices, Collectif Conception Numérique Responsable, https://github.com/cnumr/best-practices
- How to avoid the microservice observability traps, Eric D. Schabell, Mars 2023, https://www.tfir.io/how-to-avoid-microservice-observability-traps/ (en)
- Navigating the microservice Death Star with DeployH, Tracy Ragan, Novembre 2019, https://dzone.com/articles/navigating-the-microservice-deathstar-with-deployh (en)
- Cloudflare Pages, Site commercial, https://pages.cloudflare.com/ (en)
- Simulateur de coût Google Cloud, https://cloud.google.com/products/calculator#id=b2d34177-bc84-45dd-b951-00f30ccb76f1 (en)
- Documentation Cloud Interconnect - Google Cloud, https://cloud.google.com/network-connectivity/docs/interconnect?hl=fr
- Cloud hybride AWS - Sur site et en périphérie - Amazon Web Services, Site commercial, https://aws.amazon.com/fr/hybrid/
- Gestion de cloud hybride avec Anthos - Google Cloud, Site commercial, https://cloud.google.com/anthos?hl=fr
- Single-responsibility Principle, Wikipedia, https://en.wikipedia.org/wiki/Single-responsibility_principle (Consulté le 10 mai 2023)