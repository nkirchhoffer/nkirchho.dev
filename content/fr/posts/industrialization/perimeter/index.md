---
title: "Commercialiser un projet personnel - Périmètre"
date: 2022-08-24T16:05:30+02:00
draft: false
showTableOfContents: true
tags: ['industrialization']
description: "Vous avez eu une idée d'application de génie et souhaitez l'industrialiser ? Vous êtes au bon endroit 😊"
showTaxonomies: true
---
Les projets personnels sont hype en 2022, ils permettent de construire un portfolio et d'ajouter de la valeur à nos dossiers pour le recrutement, ou ils sont simplement devenus une passion pour nombre de gens, comme moi, qui s'adonnent à bricoler dans leur temps libre. Mais, parfois on sent avoir une bonne idée, à tel point qu'on peut vouloir l'industrialiser pour la commercialiser. Et cette série d'articles, illustrée d'exemples et d'expériences vécues, va relater le processus de A à Z !

{{< alert >}}
Même si ce premier article ne l'est pas forcément, cette série d'articles est destinée à un public de développeurs qui aiment bien toucher à tout. Tous les concepts techniques ne seront pas explicités à 100%
{{< /alert >}}

## Landing Zone

En début d'année, pour mon anniversaire, j'avais une idée folle mais très facilement réalisable : je voulais supprimer la tâche fastidieuse d'ajouter les musiques que me suggéraient mes amis à la file d'attente Spotify pendant nos soirées. Après avoir réalisé un prototype que j'ai mis en pratique à cette soirée, j'étais très satisfait et enthousiaste par rapport au résultat concret, et j'ai eu beaucoup de retours positifs et d'idées d'améliorations.

Après m'être approché de potentiels clients et d'autres enthousiastes, j'ai donc décidé d'industrialiser ce projet, et cette série d'articles vous donnera mon retour d'expérience sur la chose !

Cette série adressera principalement le défi technique derrière une mise en production, particulièrement en partant d'un prototype brouillon réalisé rapidement. Mais j'ai à coeur de transmettre une vision globale autour d'une telle démarche, donc ce premier article concernera principalement la mise en place d'un **périmètre** autour de l'application pour la situer sur un marché. 

{{< badge >}}
Exemple
{{< /badge >}}

Pour avoir des exemples concrets tout en n'interférant pas avec mon projet réel, j'ai mis au point une application basique qui va nous servir d'exemple pour chaque étape de cette série d'articles. Il s'agit d'un site web permettant de créer des to-do lists et de les partager avec des personnes à l'aide d'un lien et d'un identifiant unique, dont voici un aperçu.

{{< figure
    src="/posts/industrialization/perimeter/todo.png"
    alt="screenshot du site de to-do list"
    caption="Screenshot du site de to-do lists partagées"
    >}}

Cette application est composée d'un front-end et d'une API. Le front-end utilise [Vue](https://vuejs.org) avec Vue Router et [Bulma](https://bulma.io). Le back-end est développé sur [NodeJS](https://nodejs.org) avec [Express](https://expressjs.com) et utilise [MongoDB](https://mongodb.org) pour le stockage persistent des données utilisateurs.

Le code source est entièrement open et je vous invite à l'utiliser librement :
- [https://github.com/nkirchhoffer/idt-example-frontend](https://github.com/nkirchhoffer/idt-example-frontend)
- [https://github.com/nkirchhoffer/idt-example-backend](https://github.com/nkirchhoffer/idt-example-backend)

Nous sommes maintenant prêts à entamer la démarche de commercialisation de notre prototype de to-do lists !

## Étape 0 : Croire en son projet

Cette étape n'en est pas vraiment une, c'est pourquoi il s'agit de "l'étape 0". Mais pourtant, elle est bien essentielle à la bonne réalisation de votre projet. 

Croyez en votre idée !

Si le fait d'industrialiser cette idée vous passe par la tête, c'est qu'elle est légitime, vous croyez en son potentiel et à sa capacité à plaire à de potentiels utilisateurs. Il ne faut donc pas baisser les bras ! 

Entourez-vous de personnes qui sauront vous adresser des critiques constructives, qui sauront vous dire les choses telles qu'elles sont pour vous faire grandir et qui vous aideront à garder le moral. En effet, un projet d'une telle ampleur n'est pas toujours facile à réaliser, surtout selon les "règles de l'art" ! Un bon entourage est essentiel pour garder la tête haute et rester concentré.e sur ses objectifs !

## Étape 1 : Rédiger un "état de l'art"

L'état de l'art (ou "State of the Art") vous permet de comprendre l'avancée de vos travaux à un instant donné, à quel point vous en êtes et vers où vous souhaitez vous diriger.

Bien que vous n'ayez techniquement pas "avancé" au moment où vous décidez d'industrialiser votre projet, il est toujours notable de réaliser cet exercice afin d'avoir une vue globale de votre futur produit, et des étapes à compléter avant de pouvoir réellement vous lancer dans les travaux de construction.

Dans mon cas, vu que mon projet personnel était déjà abouti à un état de "prototype", j'ai une base de code sur laquelle m'appuyer, ainsi qu'un set de features qui compose le coeur de l'application.

Voici le framework que j'ai appliqué à mon projet :
- Quelles sont les fonctionnalités en place sur mon prototype ?
- Quelles sont les limites techniques de mon prototype ?
- Quels sont les aspects positifs de mon prototype ?
- Ai-je une identité pour mon produit ?
  - Un nom ?
  - Une charte graphique ?

{{< badge >}}
Exemple
{{< /badge >}}
Appliqué à notre chère application de to-do listes, voici l'état de l'art à cette étape : 
- Fonctionnalités déjà implémentées
  - En tant qu'utilisateur, je peux créer une to-do list dotée d'un nom
  - En tant qu'utilisateur, je peux partager une to-do list avec son lien unique
  - En tant qu'utilisateur, je peux créer une tâche en y indiquant un objectif textuel
  - En tant qu'utilisateur, je peux marquer une tâche comme réalisée
- Limites techniques
  - Il n'y a pas de gestion d'authentification, ni d'utilisateurs, ni de permissions
  - L'API n'est pas versionnée
  - Il n'y a aucun test ni standard de codebase mis en place 
- Aspects positifs
  - Mon code source me permet une mise à l'échelle simplifiée
  - La structure de mon code permet des modifications structurelles importantes (comme un changement de système de base de données par exemple)
- Le produit n'a pas de nom propre, pas de logo, pas de couleurs définies, mais les émojis sont un aspect du design (important)

Répondre à ces questions vous aidera à établir un cahier des charges précis de votre produit final et ainsi vous aidera à vous positionner sur un potentiel marché.

## Étape 2 : Construire un cahier des charges

Le cahier des charges de votre produit reflète la vision propre que vous en avez. Il doit indiquer tous les détails qui définissent votre produit, à qui il est adressé, qui s'en servira, etc. 

Un cahier des charges doit être le résultat d'une réflexion challengée sur chacun des aspects de votre produit, quelle est l'utilité de telle feature, est-ce que le public visé correspond réellement à votre produit ? N'hésitez pas à réfléchir à chacune des questions qui vous traverse l'esprit afin de préciser votre cahier des charges. L'exercice est encore plus fructueux lorsqu'il est mené à plusieurs, vous évitez ainsi le biais de confirmation.

Voici comment j'ai rédigé mon cahier des charges :

- Nom du produit
- Rapide description
- Public visé (catégorie sociale, par ex "étudiants", communauté à intérêt propre - par ex, un forum de jeux vidéo, ou encore certains types de professionnels, par ex. les cafés, les bars et restaurants)
- Les types d'utilisateurs
  - Votre équipe (qui doit administrer l'application - back-office)
  - Le client (celui qui achète votre produit)
  - L'utilisateur final (peut être votre client - il utilise l'application en tant que telle)
- Établissez chacune de vos fonctionnalités sous la forme d'User Stories (de manière informelle), voici quelques exemples
  - En tant qu'utilisateur final, je désire ajouter un titre Spotify à la file d'attente distribuée
  - En tant que client, je souhaite consulter le statut de mon abonnement, la facturation et administrer mon offre
  - En tant que membre de l'équipe du produit, je souhaite consulter le chiffre d'affaires du produit sur une période donnée

{{< badge >}}
Exemple
{{< /badge >}}
Le cahier des charges appliqué à mon prototype d'exemple est disponible ici : [Todoing.pdf](/posts/industrialization/perimeter/Todoing.pdf)

Il n'y a aucune limite à un vrai cahier des charges, tout ce que vous souhaitez que votre produit soit doit figurer dans votre cahier des charges. Il est la représentation écrite de votre produit final, il va guider vos équipes dans la réalisation de celui-ci et sera ce que vous venderez à vos clients.

Il est même recommandé d'inclure votre charte graphique dans votre cahier des charges, mais votre étude de marché est l'étape qui va réellement orienter votre charte graphique. Cependant, il est toujours bon de considérer un nom de marque potentiel, de vérifier sa disponibilité et d'acheter les noms de domaine correspondant assez vite.

## Étape 3 : Réaliser une étude de marché

Maintenant que le périmètre de votre produit final est bien défini, vous pouvez attaquer l'étape déterminante de votre projet. Sans elle, vous naviguez un peu sans boussole, à voguer sur des eaux troubles dans lesquelles vous vous perdrez facilement.

Il est important que votre cahier des charges soit défini et précis pour effectuer une étude de marché, sinon votre conclusion peut reposer sur des constats incomplets. Avec un cahier des charges non-exhaustif, vous risquez aisément de confondre votre produit avec un autre. Par exemple, en établissant un cahier des charges très bref et non-exhaustif de Twitter, sur lequel je définis principalement la particularité de "Poster du contenu sur Internet", on peut confondre ce produit avec Facebook, qui peut être défini selon le même cahier des charges.

Internet est très vaste, et votre produit existe déjà probablement sous une autre forme si vous résumez rapidement son cahier des charges. Tout réside dans la nuance, c'est ce qui fera que vous vous démarquerez dans un marché potentiellement concurrentiel.

Pour réaliser une bonne étude de marché, n'hésitez pas à parcourir les bases de données publiques, comme celles de l'INPI (Institut National de la Propriété Industrielle) ou Google Patents. Elles possèdent souvent un moteur de recherche très complet qui vous permet de rechercher des mots-clés précis qui définissent réellement votre produit final.

Si vous ne voulez pas vous attarder sur cet aspect de votre projet, et que vous en avez les moyens, car elle en reste cruciale, vous pouvez faire réaliser l'étude de marché par un cabinet de consultation externe.

Si vous la faites vous-mêmes, voici les points que je juge importants pour réaliser une bonne étude de marché :
- Analysez les acteurs de votre potentiel marché, rédigez des fiches sur leur activité, les produits qu'ils vendent et les publics qu'ils ciblent
- Faites un comparatif entre les produits de chaque acteur et votre produit à vous, établissez clairement les nuances sur papier
- Comparez le public visé et quel type de population vos concurrents ne ciblent pas, afin de vous démarquer sur un marché convergé

Si en tous points, votre produit est similaire à celui d'un de vos concurrents, et que vous ne disposez pas à priori de technique révolutionnaire afin de proposer un moindre coût (on reviendra sur ce point plus tard), il n'est peut-être pas prudent de lancer votre produit alors que vous n'apportez rien d'autre.

Si vous trouvez une nuance que vous jugez pertinente et qui peut devenir un réel argument de vente dans le futur, alors votre produit est légitime et vous pouvez continuer le processus d'industrialisation sans grandement changer votre cahier des charges !

{{< badge >}}
Exemple
{{< /badge >}}
Pour les listes de to-do, la concurrence est rude ! Il existe beaucoup de produits répondant à un cahier des charges similaires.

- Les acteurs : [Todoist](https://todoist.com), [Trello](https://trello.com), [Microsoft To Do](https://todo.microsoft.com), [Google Tasks](https://en.wikipedia.org/wiki/Google_Tasks), et beaucoup d'autres...
- Comparaisons avec notre produit : 
  - Todoist : permet de créer diverses listes, permet la collaboration, dispose davantage de fonctionnalités non-prévues par notre cahier des charges (+ complet), s'embarque en extension navigateur, sur mobile, sur web... (s'adresse aussi aux pros)
  - Trello : permet d'organiser les tâches en plusieurs listes sur un tableau, permet d'attribuer des tâches à des collaborateurs, permet de prévoir une date limite aux tâches, permet de les catégoriser
  - Microsoft To Do : permet les mêmes choses que Todoist (s'adresse aussi aux pros)
  - Google Tasks : version Google ayant les mêmes fonctionnalités que tout le reste, permet aussi de créer des tâches depuis le chat Google Workspace

Toutes les solutions ont une alternative aux professionnels, cependant, ces produits proposent un nombre de fonctionnalités ne permettant pas de les qualifier comme "minimalistes". Notre produit veut réellement se démarquer par cet atout qui va devenir un argument de vente.

{{< alert >}}
En redevenant sérieux quelques instants, on se rend bien compte que le marché des "to-do lists" est hyper convergé, il semble très risqué de s'y aventurer...
{{< /alert >}}

Une fois que vous avez déterminé votre place (ou non) au sein d'un marché précis, dont vous connaissez les acteurs, les relations entre les acteurs, et les enjeux liés à chacun d'eux, vous pouvez développer une vraie marque, composée d'un nom, d'un logo, d'une identité visuelle, de slogans, etc...

L'ordre de préciser un cahier des charges puis de faire une étude de marché semblait logique pour moi, dans un sens, mais une étude de marché peut révéler un manque d'offre pour un public visé selon une demande précise, libre à vous d'orienter le cahier des charges pour combler ce manque d'offre !

## Conclusion

Nous avons donc parcouru quelques étapes permettant de délimiter le périmètre de notre projet avant de l'industrialiser concrètement ! Même si elles sont fastidieuses, j'ai essayé de vous faire comprendre en quoi elles peuvent être essentielles afin de vraiment constituer une fiche produit de votre application.

À la fin de cet article, nous disposons : 
1. D'un prototype élémentaire fonctionnel (au début de l'article aussi...)
2. D'un état de l'art indiquant les éléments déjà à notre disposition
3. D'un cahier des charges indiquant ce que l'on veut que notre projet soit (qui va influencer - et être influencé - par notre étude de marché)
4. D'une liste d'acteurs, de leurs produits, de leurs relations au sein d'un marché précis, qui va orienter notre produit vers une stratégie marketing précise 

Le prochain article va concerner la conception de l'application en tant que telle, d'un point de vue technique et architecturel. On va aborder des aspects critiques, tels que les architectures distribuées, la mise à l'échelle, l'authentification des utilisateurs et la sécurisation par le biais de systèmes de permissions élaborés.