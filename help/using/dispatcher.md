---
title: Vue d’ensemble de Dispatcher
seo-title: Adobe AEM Dispatcher Overview
description: Découvrez comment utiliser Dispatcher pour améliorer la sécurité, la mise en cache et plus encore sur les services cloud d’AEM.
seo-description: This article provides a general overview of Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
exl-id: c9266683-6890-4359-96db-054b7e856dd0
source-git-commit: 7dd2ba37e149af960ba428421d64a5a24542eeeb
workflow-type: ht
source-wordcount: '3054'
ht-degree: 100%

---

# Vue d’ensemble de Dispatcher {#dispatcher-overview}

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

Le Dispatcher est l’outil de mise en cache et d’équilibrage de charge d’Adobe Experience Manager, qui peut être utilisé conjointement avec un serveur web d’entreprise.

La procédure de déploiement de Dispatcher est indépendante du serveur web et de la plateforme du système d’exploitation :

1. En savoir plus sur Dispatcher (cette page). Consultez également les [questions fréquentes sur Dispatcher](/help/using/dispatcher-faq.md).
1. Installez un [serveur web pris en charge](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=fr) selon la documentation du serveur web.
1. [Installez le module Dispatcher](dispatcher-install.md) sur votre serveur web et configurez-le en conséquence.
1. [Configurez Dispatcher](dispatcher-configuration.md) (fichier dispatcher.any).
1. [Configurez AEM](page-invalidate.md) pour que les mises à jour de contenu invalident le cache.

>[!NOTE]
>
>Pour mieux comprendre le fonctionnement de Dispatcher avec AEM :
>
>* Voir les [Questions aux expertes et experts de la communauté AEM de juillet 2017](https://communities.adobeconnect.com/pf0gem7igw1f/).
>* Accédez à [ce référentiel](https://github.com/adobe/aem-dispatcher-experiments). Il contient une collection d’expériences dans un format de laboratoire « à emporter ».


Utilisez les informations suivantes, selon vos besoins :

* [Liste de contrôle de sécurité de Dispatcher](security-checklist.md)
* [Base de connaissances de Dispatcher](https://helpx.adobe.com/fr/experience-manager/kb/index/dispatcher.html)
* [Optimiser un site web pour les performances du cache](https://experienceleague.adobe.com/docs/experience-manager-65/content/implementing/deploying/configuring/configuring-performance.html?lang=fr)
* [Utiliser Dispatcher avec plusieurs domaines](dispatcher-domains.md)
* [Utiliser le protocole SSL avec Dispatcher](dispatcher-ssl.md)
* [Mettre en œuvre la mise en cache sensible aux autorisations](permissions-cache.md)
* [Résoudre les problèmes liés à Dispatcher](dispatcher-troubleshooting.md)
* [Questions fréquentes sur les problèmes fréquents de Dispatcher](dispatcher-faq.md)

>[!NOTE]
>
>**L’utilisation la plus courante de Dispatcher** consiste à mettre en cache les réponses d’une **instance de publication AEM** pour améliorer la réactivité et la sécurité du site web publié en externe. La majeure partie de la discussion se concentre sur ce cas.
>
>Mais le Dispatcher peut également être utilisé pour augmenter la réactivité de votre **instance de création**, en particulier si un grand nombre d’utilisateurs et d’utilisatrices modifient et mettent à jour votre site web. Pour plus de détails spécifiques à ce cas, voir [Utilisation d’un Dispatcher avec un serveur de création](#using-a-dispatcher-with-an-author-server), ci-dessous.

## Pourquoi utiliser Dispatcher pour mettre en œuvre la mise en cache ?  {#why-use-dispatcher-to-implement-caching}

Il existe deux approches de base en matière de publication web :

* **Serveurs web statiques** : comme Apache ou IIS, ils sont simples, mais rapides.
* **Serveurs de gestion de contenu** : ils fournissent un contenu dynamique, intelligent et en temps réel, mais nécessitent beaucoup plus de temps de calcul et d’autres ressources.

Le Dispatcher permet de créer un environnement à la fois rapide et dynamique. Il fonctionne dans le cadre d’un serveur HTML statique, tel qu’Apache, dans le but de :

* stocker (ou « mettre en cache ») autant de contenu du site que possible, sous la forme d’un site web statique ;
* accéder le moins possible au moteur de disposition.

Cela signifie que :

* le **contenu statique** est géré avec la même rapidité et la même facilité que sur un serveur web statique. Vous pouvez également utiliser les outils d’administration et de sécurité disponibles pour vos serveurs web statiques.

* **le contenu dynamique** est généré en fonction des besoins, sans ralentir le système plus que nécessaire.

Dispatcher contient des mécanismes permettant de générer et mettre à jour du HTML statique basé sur le contenu du site dynamique. Vous pouvez spécifier en détail les documents qui sont stockés sous forme de fichiers statiques et ceux qui sont toujours générés dynamiquement.

Cette section illustre les principes qui sous-tendent ce processus.

### Serveur web statique {#static-web-server}

![](assets/chlimage_1-3.png)

Un serveur web statique, tel qu’Apache ou IIS, délivre des fichiers HTML statiques aux visiteurs et visiteuses de votre site web. Les pages statiques sont créées une seule fois, le même contenu est donc diffusé à chaque requête.

Ce processus est simple et efficace. Si un visiteur ou une visiteuse demande un fichier tel qu’une page HTML, le fichier est extrait directement de la mémoire. Dans le pire des cas, il est lu depuis le disque local. Les serveurs web statiques sont disponibles depuis un certain temps. Il existe donc une large gamme d’outils d’administration et de gestion de la sécurité, et ils sont bien intégrés aux infrastructures réseau.

### Serveurs de gestion de contenu  {#content-management-servers}

![](assets/chlimage_1-4.png)

Si vous utilisez un CMS (serveur de gestion du contenu), tel qu’AEM, un moteur de disposition avancé traite la requête d’un visiteur ou d’une visiteuse. Le moteur lit le contenu d’un référentiel qui, combiné aux styles, formats et droits d’accès, transforme le contenu en un document adapté aux besoins et aux droits du visiteur ou de la visiteuse.

Ce workflow vous permet de créer un contenu plus riche et dynamique, ce qui augmente la flexibilité et les fonctionnalités de votre site web. Cependant, le moteur de disposition nécessite davantage de puissance de traitement qu’un serveur statique. Cette configuration est donc susceptible de provoquer des ralentissements si de nombreuses personnes utilisent le système.

## Procédure de mise en cache par Dispatcher  {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**Répertoire du cache** Pour la mise en cache, le module Dispatcher utilise la capacité du serveur web à délivrer du contenu statique. Dispatcher place les documents mis en cache à la racine du document du serveur web.

>[!NOTE]
>
>Lorsque la configuration de la mise en cache HTTP est manquante, Dispatcher stocke uniquement le code HTML de la page ; il ne stocke pas les en-têtes HTTP. Ce scénario peut créer un problème si vous utilisez différents codages pour le site web, car ces pages risquent d’être perdues. Pour activer la mise en cache des en-têtes HTTP, voir [Configurer le cache de Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=fr).

>[!NOTE]
>
>La localisation de la racine du document du serveur web sur le périphérique de stockage réseau (NAS) entraîne une dégradation des performances. De même, lorsqu’une racine de document située sur le NAS est partagée entre plusieurs serveurs web, des verrous intermittents peuvent se produire lorsque des actions de réplication sont effectuées.

>[!NOTE]
>
>Dispatcher stocke le document mis en cache dans une structure égale à l’URL demandée.
>
>Le système d’exploitation peut appliquer des limitations concernant la longueur du nom de fichier. Autrement dit, cela concerne les cas où une URL comporte de nombreux sélecteurs.

### Méthodes de mise en cache

Dispatcher dispose de deux méthodes principales pour mettre à jour le contenu du cache lorsque des modifications sont apportées au site web.

* Les **mises à jour du contenu** suppriment les pages qui ont été modifiées, ainsi que les fichiers qui leur sont directement associés.
* **L’invalidation automatique** invalide automatiquement les parties du cache susceptibles d’être obsolètes après une mise à jour. En réalité, elles marquent les pages concernées comme étant obsolètes, sans rien supprimer.

### Mises à jour du contenu

Lors d’une mise à jour du contenu, un ou plusieurs documents AEM sont modifiés. AEM envoie une requête de syndication à Dispatcher, qui met à jour le cache en conséquence :

1. Il efface les fichiers modifiés du cache.
1. Il supprime du cache tous les fichiers commençant par la même poignée. Par exemple, si le fichier /en/index.html est mis à jour, tous les fichiers commençant par /en/index. sont supprimés. Ce mécanisme permet de concevoir des sites économes en cache, notamment en matière de navigation dans des images.
1. Il *touche* ce que l’on appelle un **fichier stat**, ce qui met à jour l’horodatage du fichier stat pour indiquer la date de la dernière modification.

Notez également ce qui suit :

* Les mises à jour de contenu sont généralement utilisées avec un système de création qui « sait » ce qui doit être remplacé.
* Les fichiers concernés par une mise à jour de contenu sont supprimés, mais pas remplacés immédiatement. Lorsqu’un tel fichier est à nouveau demandé, Dispatcher récupère le nouveau fichier depuis l’instance AEM et le place dans le cache, écrasant ainsi l’ancien contenu.
* En règle générale, les images générées automatiquement qui incorporent le texte d’une page sont stockées dans des fichiers image commençant par la même poignée, garantissant ainsi que l’association existe pour la suppression. Par exemple, vous pouvez stocker le texte du titre de la page mypage.html sous la forme de l’image mypage.titlePicture.gif dans le même dossier. De cette façon, l’image est automatiquement supprimée du cache chaque fois que la page est mise à jour. Vous avez donc l’assurance que l’image reflète toujours la version actuelle de la page.
* Vous pouvez avoir plusieurs fichiers stat, un par dossier de langue, par exemple. Si une page est mise à jour, AEM recherche le dossier parent suivant contenant un fichier stat, et *touche* (modifie) ce fichier.

### Invalidation automatique

L’invalidation automatique invalide automatiquement certaines parties du cache, sans supprimer physiquement aucun fichier. À chaque mise à jour du contenu, le fichier stat est modifié, son horodatage reflète ainsi la dernière mise à jour du contenu.

Dispatcher comprend une liste de fichiers soumis à l’invalidation automatique. Lorsqu’un document de cette liste est demandé, Dispatcher compare la date du document mis en cache avec l’horodatage du fichier stat :

* si le document mis en cache est plus récent, Dispatcher le renvoie ;
* s’il est plus ancien, Dispatcher récupère la version actuelle de l’instance AEM.

Notez également ce qui suit :

* L’invalidation automatique est généralement utilisée lorsque les interrelations sont complexes, comme dans le cas des pages HTML. Ces pages contiennent des liens et des entrées de navigation. Elles doivent donc généralement être mises à jour après une mise à jour du contenu. Si vous avez généré automatiquement des fichiers PDF ou image, vous pouvez également choisir d’invalider automatiquement ces fichiers.
* L’invalidation automatique n’implique aucune action de Dispatcher au moment de la mise à jour, sauf si ce n’est de modifier le fichier stat. Cependant, une modification du fichier stat rend automatiquement le contenu du cache obsolète, sans le supprimer physiquement du cache.

## Procédure de renvoi des documents par Dispatcher  {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Déterminer si le document est soumis à la mise en cache

Vous pouvez [définir les documents que Dispatcher met en cache dans le fichier de configuration](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=fr). Dispatcher vérifie la demande par rapport à la liste des documents pouvant être mis en cache. Si le document ne figure pas dans cette liste, Dispatcher demande le document à l’instance AEM.

Dispatcher demande toujours le document directement à l’instance AEM dans les cas suivants :

* Si l’URI de demande contient un point d’interrogation « `?` ». Ce scénario indique généralement une page dynamique, par exemple un résultat de recherche qui n’a pas besoin d’être mis en cache.
* L’extension de fichier est manquante. Le serveur web a besoin de l’extension pour déterminer le type de document (type MIME).
* L’en-tête d’authentification est défini (configurable).

>[!NOTE]
>
>Les méthodes GET ou HEAD (pour les en-têtes HTTP) sont mises en cache par Dispatcher. Pour plus d’informations sur la mise en cache des en-têtes de réponse, voir [Mise en cache des en-têtes de réponse HTTP](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=fr).

### Déterminer si un document est mis en cache

Dispatcher stocke les fichiers mis en cache sur le serveur web comme s’ils faisaient partie d’un site web statique. Si l’utilisateur ou l’utilisatrice demande un document pouvant être mis en cache, Dispatcher vérifie si le document existe dans le système de fichiers du serveur web :

* si le document est mis en cache, Dispatcher renvoie le fichier ;
* sinon, Dispatcher demande le document à l’instance AEM.

### Déterminer si le document est à jour

Pour savoir si un document est à jour, Dispatcher effectue deux étapes :

1. Il vérifie si le document est soumis à l’invalidation automatique. Si ce n’est pas le cas, le document est considéré comme étant à jour.
1. Si le document est configuré pour l’invalidation automatique, Dispatcher vérifie s’il est plus ancien ou plus récent que la dernière modification disponible. S’il est plus ancien, Dispatcher demande la version actuelle à l’instance AEM et remplace la version dans le cache.

>[!NOTE]
>
>Les documents sans **invalidation automatique** restent dans le cache jusqu’à ce qu’ils soient physiquement supprimés. Par exemple, par une mise à jour du contenu du site web.

## Avantages de l’équilibrage de charge {#the-benefits-of-load-balancing}

L’équilibrage de charge consiste à répartir la charge de calcul du site web sur plusieurs instances d’AEM.

![](assets/chlimage_1-7.png)

Les avantages sont les suivants :

* **Puissance de traitement accrue**
En pratique, cela signifie que Dispatcher partage des requêtes de document entre plusieurs instances d’AEM. Chaque instance ayant moins de documents à traiter, les délais de réponse sont plus rapides. Dispatcher conserve les statistiques internes pour chaque catégorie de document afin qu’il puisse estimer la charge et distribuer les requêtes efficacement.

* **Couverture de sécurité accrue**
Si Dispatcher ne reçoit aucune réponse de la part d’une instance, il transmet automatiquement les requêtes à l’une des autres instances. Si une instance n’est plus disponible, le seul effet est un ralentissement du site, proportionnel à la puissance de calcul perdue. Cependant, tous les services continuent.

* Vous pouvez également gérer différents sites web sur le même serveur web statique.

>[!NOTE]
>
>Tandis que l’équilibrage des charges répartit efficacement la charge, la mise en cache contribue à la réduire. Essayez donc d’optimiser la mise en cache et de réduire la charge globale avant de configurer l’équilibrage de charge. Une bonne mise en cache peut augmenter les performances de l’équilibreur de charge ou le rendre inutile.

>[!CAUTION]
>
>Même si une seule instance de Dispatcher est capable de saturer la capacité des instances de publication disponibles, dans de rares cas, il peut être judicieux d’équilibrer également la charge entre deux instances de Dispatcher. Les configurations avec plusieurs instances de Dispatcher doivent être examinées avec attention, car une instance supplémentaire peut augmenter la charge sur les instances de publication disponibles et rapidement diminuer les performances de la plupart des applications.

## Exécution de l’équilibrage de charge par Dispatcher  {#how-the-dispatcher-performs-load-balancing}

### Statistiques de performances

Dispatcher conserve des statistiques internes sur la vitesse à laquelle chaque instance d’AEM traite les documents. Sur la base de ces données, Dispatcher établit l’instance qui peut offrir le temps de réponse le plus court pour répondre à une requête. Il réserve ainsi le temps de calcul nécessaire sur cette instance.

Les différents types de requêtes peuvent avoir des délais d’exécution moyens différents. C’est la raison pour laquelle Dispatcher vous permet de spécifier des catégories de documents. Ces catégories sont ensuite prises en compte lors du calcul des estimations de temps. Par exemple, vous pouvez faire la distinction entre les pages HTML et les images, car les temps de réponse habituels peuvent varier.

Si vous utilisez une fonction de recherche élaborée, vous pouvez créer une catégorie pour les requêtes de recherche. Cette méthode aide Dispatcher à envoyer des requêtes de recherche à l’instance qui répond le plus rapidement. Cela permet également d’éviter qu’une instance plus lente ne se bloque lorsqu’elle reçoit plusieurs requêtes de recherche « coûteuses », tandis que les autres reçoivent les requêtes « moins coûteuses ».

### Contenu personnalisé (connexions persistantes)

Les connexions persistantes garantissent que les documents d’une personne soient tous composés sur la même instance d’AEM. Cet aspect est important si vous utilisez des pages et des données de session personnalisées. Les données sont stockées sur l’instance. Les requêtes ultérieures de la même personne doivent donc revenir à cette instance, faute de quoi les données sont perdues.

Les connexions persistantes limitent la capacité de Dispatcher à optimiser les requêtes. Vous devez donc les utiliser uniquement en cas de besoin. Vous pouvez spécifier le dossier qui contient les documents « persistants », pour que tous les documents de ce dossier soient bien composés sur la même instance pour chaque personne.

>[!NOTE]
>
>Pour la plupart des pages qui utilisent des connexions persistantes, vous devez désactiver la mise en cache. Dans le cas contraire, la page apparaît de la même manière pour toutes les personnes, quel que soit le contenu de la session.
>
>Pour *quelques* applications, il est parfois possible d’utiliser à la fois des connexions persistantes et la mise en cache, par exemple, si vous affichez un formulaire qui écrit des données dans la session.

## Utiliser plusieurs instances de Dispatcher {#using-multiple-dispatchers}

Dans des configurations complexes, vous pouvez utiliser plusieurs instances de Dispatcher. Par exemple, vous pouvez utiliser :

* une instance de Dispatcher pour publier un site web sur l’intranet ;
* une seconde instance de Dispatcher, sous une autre adresse et avec des paramètres de sécurité différents, pour publier le même contenu sur Internet.

Dans ce cas, veillez à ce que chaque requête ne passe que par une seule instance de Dispatcher. Une instance de Dispatcher ne gère pas les requêtes provenant d’une autre instance. Par conséquent, assurez-vous que les deux instances de Dispatcher accèdent directement au site web d’AEM.

## Utilisation de Dispatcher avec un CDN  {#using-dispatcher-with-a-cdn}

Un réseau de distribution de contenu (CDN), par exemple Akamai Edge Delivery ou Amazon Cloud Front, distribue du contenu à partir d’un emplacement proche de l’utilisateur final. Ainsi, il :

* accélère les temps de réponse pour les utilisateurs finaux ;
* soulage vos serveurs ;

En tant que composant d’infrastructure HTTP, un réseau CDN fonctionne un peu comme Dispatcher. Lorsqu’un nœud de réseau CDN reçoit une requête, il la sert depuis son cache si cela est possible (la ressource est disponible dans le cache et est valide). Sinon, il s’adresse au serveur suivant le plus proche pour récupérer la ressource et la mettre en cache pour d’autres requêtes, le cas échéant.

Le « serveur suivant le plus proche » dépend de votre configuration spécifique. Par exemple, dans une installation Akamai, la requête peut prendre le chemin suivant :

* Le nœud Akamai Edge
* Couche Akamai Midgres
* Votre pare-feu
* Votre répartiteur de charge
* Dispatcher
* AEM

Habituellement, Dispatcher est le prochain serveur susceptible de servir le document à partir d’un cache et d’influencer les en-têtes de réponse renvoyés au serveur CDN.

## Contrôler un cache CDN  {#controlling-a-cdn-cache}

Il existe plusieurs méthodes de contrôle de la durée pendant laquelle un CDN met en cache une ressource avant qu’elle ne soit récupérée auprès de Dispatcher.

1. Configuration explicite\
   Configurez la durée pendant laquelle des ressources particulières sont conservées dans le cache du réseau CDN, en fonction du type MIME, de l’extension, du type de requête, etc.

1. En-têtes d’expiration et de contrôle du cache\
   La plupart des réseaux CDN honorent les en-têtes HTTP `Expires:` et `Cache-Control:` s’ils sont envoyés par le serveur en amont. Pour suivre cette méthode, vous pouvez, par exemple, utiliser le module Apache [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html).

1. Invalidation manuelle\
   Les CDN permettent de supprimer des ressources du cache via des interfaces web.
1. Invalidation basée sur l’API\
   La plupart des réseaux CDN offrent également une API REST et/ou SOAP qui permet de supprimer des ressources du cache.

Dans une configuration AEM typique, la configuration par extension, par chemin ou par les deux, qui peut être réalisée via les points 1 et 2 ci-dessus, offre la possibilité de définir des périodes de mise en cache raisonnables pour les ressources fréquemment utilisées qui ne changent pas souvent, telles que les images de conception et les bibliothèques clientes. Lorsque de nouvelles versions sont déployées, une invalidation manuelle est généralement requise.

Si cette approche est utilisée pour mettre en cache le contenu géré, cela implique que les modifications du contenu ne sont visibles pour les utilisateurs et utilisatrices finaux qu’une fois que la période de mise en cache configurée a expiré et que le document est à nouveau récupéré auprès de Dispatcher.

Pour un contrôle davantage affiné, l’invalidation basée sur l’API vous permet d’invalider le cache d’un réseau CDN si le cache de Dispatcher est invalidé. Sur la base de l’API du réseau CDN, vous pouvez implémenter vos propres [ContentBuilder](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/ContentBuilder.html) et [TransportHandler](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/TransportHandler.html) (si l’API n’est pas basée sur REST) et configurer un agent de réplication qui les utilisera pour invalider le cache du réseau CDN.

>[!NOTE]
>
>Voir aussi [Sécurité de Dispatcher AEM (CQ) et mise en cache du réseau CDN et du navigateur](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015), ainsi que la présentation enregistrée [Mise en cache de Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2015/aem-dispatcher-caching-new-features-and-optimizations.html?lang=fr).

## Utiliser Dispatcher avec un serveur de création {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>Si vous utilisez [AEM avec l’interface utilisateur tactile](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=fr), ne mettez **pas** en cache le contenu de l’instance de création. Si la mise en cache a été activée pour l’instance de création, vous devez la désactiver et supprimer le contenu du répertoire du cache. Pour désactiver la mise en cache, modifiez le fichier `author_dispatcher.any` et la propriété `/rule` de la section `/cache` comme suit :

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Un Dispatcher peut être utilisé devant une instance de création pour améliorer les performances de création. Pour configurer un Dispatcher de création, procédez comme suit :

1. Installez un Dispatcher sur un serveur web (serveur web Apache ou IIS, voir [Installation de Dispatcher](dispatcher-install.md)).
1. Testez le Dispatcher nouvellement installé par rapport à une instance de publication AEM fonctionnelle. Cela garantit qu’une installation de base correcte a été effectuée.
1. Assurez-vous maintenant que le Dispatcher peut se connecter via TCP/IP à votre instance de création.
1. Remplacez l’exemple de fichier `dispatcher.any` par le fichier `author_dispatcher.any`, fourni avec le [téléchargement de Dispatcher](release-notes.md#downloads).
1. Ouvrez `author_dispatcher.any` dans un éditeur de texte et apportez les modifications suivantes :

   1. Modifiez `/hostname` et `/port` dans la section `/renders` pour qu’ils pointent vers votre instance de création.
   1. Modifiez `/docroot` dans la section `/cache` pour qu’il pointe vers un répertoire de cache. Si vous utilisez [AEM avec l’interface utilisateur tactile](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=fr), voir l’avertissement ci-dessus.
   1. Enregistrez les modifications.

1. Supprimez tous les fichiers existants dans le répertoire `/cache` > `/docroot` que vous avez configuré ci-dessus.
1. Redémarrez le serveur web.

>[!NOTE]
>
>Avec la configuration `author_dispatcher.any` fournie, lorsque vous installez un pack de fonctionnalités CQ5, un correctif ou un package de code d’application qui affecte tout contenu sous `/libs` ou `/apps`, vous devez supprimer les fichiers mis en cache sous ces répertoires dans le cache de votre Dispatcher. Cela garantit que la prochaine fois qu’ils seront demandés, ce sont les fichiers nouvellement mis à niveau qui seront récupérés, et non les anciens fichiers mis en cache.

>[!CAUTION]
>
>Si vous avez utilisé le Dispatcher de création configuré précédemment et si vous avez activé un *agent de purge de Dispatcher*, procédez comme suit :

1. Supprimez ou désactivez l’agent de purge du **Dispatcher de création** sur votre instance de création AEM.
1. Rétablissez la configuration de Dispatcher de création en suivant les nouvelles instructions ci-dessus.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
