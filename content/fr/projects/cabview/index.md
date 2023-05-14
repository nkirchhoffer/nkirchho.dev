---
title: "CabView"
date: 2023-05-14T00:00:00+02:00
draft: false
showTableOfContents: true
tags: ['cabview', 'ferrovipathe', 'screensaver']
description: "Bibliothèque de vidéos ferroviaires en cabine"
showTaxonomies: true
---
Le monde ferroviaire nous offre beaucoup, je trouve. De paysages déjà, c'est d'ailleurs la force du projet que je vous présente ici. Mais il ouvre la voie à bien plus que ça : une perspective d'avenir. Dans des temps troubles, une "nouvelle" manière d'organiser le futur est rafraîchissante, et quoi d'autre que le ferroviaire pour la porter.

Mon projet est, lui, beaucoup plus modeste : il consiste à mettre en avant ces inombrables vidéos YouTube réalisées au bord de cabines de diverses locomotives ou matériels, de divers réseaux, de divers pays.

{{< youtube Mw9qiV7XlFs >}}

Itinéraire entre St. Moritz (Suisse) et Tirano (Italie). L'une de mes vidéos préférées par son paysage et sa réalisation.

## Genèse

Je suis de celles et ceux qui ont besoin de travailler sur un contenu de fond : musique, podcasts ou vidéos diverses. Longtemps intéressé par le milieu ferroviaire, je regardais beaucoup de vidéos qu'on appelle des "cab rides", des itinéraires filmés en cabine.

Seulement, l'algorithme YouTube ayant ses propres intérêts, objectifs et fonctionnements - pas toujours alignés avec les miens -, je déplorais de plus en plus l'absence de ces vidéos sur ma page d'accueil. Je me devais alors de faire des recherches spécifiques pour trouver ces fameux cab rides.

Il me fallait un moyen plus efficace de choisir une vidéo afin de ne pas perdre trop de temps avant de pouvoir travailler.

## Solution

J'ai donc décidé de recenser ces vidéos dans une base de données, pour pouvoir en récupérer aléatoirement quand je le désirais. 

L'idée de base était de disposer d'un addon Firefox me permettant d'ajouter, en un clic, une vidéo YouTube dans la base de données. J'ai donc recherché de la documentation sur le sujet, qui, par chance, est bien fournie, notamment sur le [MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Your_first_WebExtension).

Le concept : 
- Quand on charge une vidéo YouTube, l'icône de l'extension devient active
- Quand on clique sur l'icône, une requête est effectuée à une API qui en récupère le lien et le stocke
- Une application web me permet de récupérer une vidéo aléatoirement grâce à l'endpoint `/random`

À l'époque, ça s'arrêtait là. Mais, discutant avec quelques ami.e.s, je me suis dit que je pourrais un peu améliorer le dispositif, notamment en construisant une page d'accueil recensant les différentes vidéos.

Vous pouvez accéder au projet dans son état actuel : https://cabview.nkir.ch

{{< figure
    src="/projects/cabview/homepage.png"
    alt="Page d'accueil de CabView, on y trouve le titre de la page, la description du projet et les vidéos mises en avant"
    caption="Page d'accueil de CabView le 14 mai 2023"
    >}}

Pour ce faire, j'ai choisi de continuer avec NodeJS et Express en back-end, et j'ai décidé d'utiliser Vue en front-end. 

Aujourd'hui, je tends à rendre une décision constructive sur les choix technologiques, notamment dans une démarche de numérique responsable et de sobriété de l'information, des valeurs qui me tiennent particulièrement à coeur.

En faveur de NodeJS et Express :
- Base de code légère permettant de réduire le stockage sur Git
- Runtime rapide permettant de démarrer les instances rapidement et faciliter l'élasticité ainsi que le scale to zero
- Express est assez bas-niveau, on a donc le contrôle sur les notions de cache

En faveur de Vue :
- Base de code légère par rapport à Angular, facilité personnelle avec Vue par rapport à React
- Implémenter des modules réactifs tels que la pagination, le filtrage et la recherche
- Avoir le contrôle sur les requêtes effectuées à l'API

Dans tous les cas, peu importent les technologies, la sobriété repose sur notre propre implémentation des technologies et des différents mécanismes.

À chaque vidéo ajoutée au système, le but est d'en extraire les informations sur l'itinéraire. J'ai exporté les données de l'Open Data SNCF concernant les gares et points d'arrêts en France, il y en a (à date) plus de 3 200 (on pourrait d'ailleurs philosopher et dire que c'est assez peu par rapport aux 34 000 communes françaises, et qu'on sait que toutes les gares RER et Transilien y sont recensées également).

Grâce à l'API YouTube Data v3, à l'aide de l'ID de la vidéo, on en récupère le titre et la durée. Le titre est ensuite décortiqué pour y retrouver des informations sur l'itinéraire : gare de départ, de destination, gares de passage, etc.

Le choix architectural de ce projet met en avant une extraction classique textuelle à l'aide de RegEx et jeux de données plutôt qu'une approche boîte noire par IA et langage naturel. Bien que l'on aurait probablement de meilleurs résultats avec une approche par Machine Learning, une simple requête SQL consolidée consomme davantage moins.

Je travaille actuellement à renforcer le dispositif de détection des différentes gares en ajoutant les points d'arrêts de certains réseaux urbains (tels que le métro parisien, le métro New-Yorkais...) et les gares des pays tels que la Suisse, l'Italie, l'Espagne et la Belgique, que l'on retrouve d'ores et déjà sur la page d'accueil du site.

À ce jour, le projet est hébergé sur les produits serverless de Google Cloud, notamment App Engine pour le déploiement de l'API et du front-end, BigQuery pour le stockage des gares SNCF et Datastore pour le stockage NoSQL des vidéos et de leurs métadonnées. Il sera très probablement migré sur l'infrastructure cloud hybride de [Clebard Cloud](https://clebard.cloud) dès qu'elle sera entièrement opérationnelle.

## La suite

Le projet sera maintenu sur mon temps-libre comme il l'est actuellement, ce qui insinue des périodes d'activité et d'autres d'inactivité, plus ou moins prolongées.

Il est également open-source, et les futures features à implémenter sont listées sur [ce Projet GitHub](https://github.com/users/nkirchhoffer/projects/1/views/1).

Actuellement, la page d'accueil fonctionne avec un infinite scrolling, qui sera bientôt remplacé par une pagination classique dans une démarche numérique responsable.

Les dépôts Git sont disponibles sur les liens suivants : 
- https://github.com/nkirchhoffer/cabview-front
- https://github.com/nkirchhoffer/cabview-server
- https://github.com/nkirchhoffer/cabview-firefox
- https://github.com/nkirchhoffer/cabview-chrome