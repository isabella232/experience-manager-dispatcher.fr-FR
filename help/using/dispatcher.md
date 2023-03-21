---
title: Présentation de Dispatcher
seo-title: Adobe AEM Dispatcher Overview
description: Découvrez comment utiliser Dispatcher pour améliorer la sécurité, la mise en cache et plus encore sur les services cloud AEM.
seo-description: This article provides a general overview of Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
exl-id: c9266683-6890-4359-96db-054b7e856dd0
source-git-commit: 7dd2ba37e149af960ba428421d64a5a24542eeeb
workflow-type: tm+mt
source-wordcount: '3154'
ht-degree: 17%

---

# Présentation de Dispatcher {#dispatcher-overview}

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes de AEM. Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

Dispatcher est un outil de mise en cache et d’équilibrage de charge Adobe Experience Manager utilisé avec un serveur web de niveau élevé.

Le processus de déploiement de Dispatcher est indépendant du serveur web et de la plateforme du système d’exploitation choisie :

1. En savoir plus sur Dispatcher (cette page). Voir aussi [questions fréquentes sur Dispatcher](/help/using/dispatcher-faq.md).
1. Installez un [serveur web pris en charge](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=en) selon la documentation du serveur web.
1. [Installez le module Dispatcher](dispatcher-install.md) sur votre serveur web et configurez-le en conséquence.
1. [Configurez Dispatcher](dispatcher-configuration.md) (fichier dispatcher.any).
1. [Configurez AEM](page-invalidate.md) pour que les mises à jour de contenu invalident le cache.

>[!NOTE]
>
>Pour mieux comprendre le fonctionnement de Dispatcher avec AEM :
>
>* Voir [Demander aux experts de la communauté AEM pour juillet 2017](https://communities.adobeconnect.com/pf0gem7igw1f/).
>* Accès [ce référentiel ;](https://github.com/adobe/aem-dispatcher-experiments). Il contient une collection d’expériences dans un format de laboratoire &quot;à emporter&quot;.



Utilisez les informations suivantes, selon vos besoins :

* [Liste de contrôle de sécurité de Dispatcher](security-checklist.md)
* [Base de connaissances de Dispatcher](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html)
* [Optimisation d’un site web pour les performances du cache](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/configuring-performance.html)
* [Utilisation de Dispatcher avec plusieurs domaines](dispatcher-domains.md)
* [Utilisation du protocole SSL avec Dispatcher](dispatcher-ssl.md)
* [Mise en œuvre de la mise en cache sensible aux autorisations](permissions-cache.md)
* [Résolution des problèmes liés à Dispatcher](dispatcher-troubleshooting.md)
* [FAQ sur les problèmes fréquents de Dispatcher](dispatcher-faq.md)

>[!NOTE]
>
>******L’utilisation la plus courante du Dispatcher consiste à mettre en cache les réponses d’une instance de publication AEM** pour améliorer la réactivité et la sécurité du site web publié en externe. La plupart des discussions portent sur cette affaire.
>
>Cependant, Dispatcher peut également être utilisé pour améliorer la réactivité de votre **instance d’auteur**, en particulier si vous avez un grand nombre d’utilisateurs qui modifient et mettent à jour votre site web. Pour plus d’informations sur ce cas, voir [Utilisation de Dispatcher avec un serveur de création](#using-a-dispatcher-with-an-author-server), ci-dessous.

## Pourquoi utiliser Dispatcher pour mettre en œuvre la mise en cache ?  {#why-use-dispatcher-to-implement-caching}

Il existe deux approches de base pour la publication web :

* **Serveurs web statiques**: Apache ou IIS, par exemple, sont simples, mais rapides.
* **Serveurs de gestion de contenu**: qui fournissent du contenu dynamique, en temps réel et intelligent, mais nécessitent beaucoup plus de temps de calcul et d’autres ressources.

Dispatcher permet de créer un environnement à la fois rapide et dynamique. Il fait partie d’un serveur de HTML statique, tel qu’Apache, dans le but :

* stocker (ou &quot;mettre en cache&quot;) autant de contenu du site que possible, sous la forme d’un site web statique ;
* accéder le moins possible au moteur de mise en page ;

Ce qui signifie que :

* **contenu statique** est géré avec la même vitesse et la même facilité que sur un serveur web statique. Vous pouvez également utiliser les outils d’administration et de sécurité disponibles pour vos serveurs web statiques.

* **le contenu dynamique** est généré en fonction des besoins, sans ralentir le système plus que nécessaire.

Dispatcher contient des mécanismes pour générer et mettre à jour un HTML statique en fonction du contenu du site dynamique. Vous pouvez spécifier en détail les documents qui sont stockés en tant que fichiers statiques et qui sont toujours générés dynamiquement.

Cette section illustre les principes qui sous-tendent ce processus.

### Serveur web statique  {#static-web-server}

![](assets/chlimage_1-3.png)

Un serveur web statique, tel qu’Apache ou IIS, diffuse des fichiers de HTML statiques aux visiteurs de votre site web. Les pages statiques sont créées une seule fois, de sorte que le même contenu est diffusé pour chaque requête.

Ce processus est simple et efficace. Si un visiteur demande un fichier tel qu’une page de HTML, le fichier est directement récupéré de la mémoire ; au pire, il est lu à partir du lecteur local. Les serveurs web statiques sont disponibles depuis un certain temps, il existe donc un large éventail d&#39;outils pour l&#39;administration et la gestion de la sécurité, et ils sont bien intégrés aux infrastructures réseau.

### Serveurs de gestion de contenu  {#content-management-servers}

![](assets/chlimage_1-4.png)

Si vous utilisez un CMS (Content Management Server), tel qu’AEM, un moteur de mise en page avancé traite la demande d’un visiteur. Le moteur lit le contenu d’un référentiel qui, combiné à des styles, des formats et des droits d’accès, transforme le contenu en un document adapté aux besoins et aux droits d’un visiteur.

Ce workflow permet de créer du contenu dynamique plus riche, ce qui augmente la flexibilité et les fonctionnalités de votre site web. Toutefois, le moteur de mise en page requiert plus de puissance de traitement qu’un serveur statique. Par conséquent, cette configuration peut être susceptible de ralentir si de nombreux visiteurs utilisent le système.

## Procédure de mise en cache par Dispatcher  {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**Répertoire du cache** Pour la mise en cache, le module Dispatcher utilise la capacité du serveur web à diffuser du contenu statique. Dispatcher place les documents mis en cache dans la racine du document du serveur web.

>[!NOTE]
>
>Lorsque la configuration de la mise en cache HTTP est manquante, Dispatcher stocke uniquement le code HTML de la page ; il ne stocke pas les en-têtes HTTP. Ce scénario peut poser problème si vous utilisez différents codages dans votre site web, car ces pages risquent d’être perdues. Pour activer la mise en cache des en-têtes HTTP, voir [Configuration du cache de Dispatcher.](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=fr)

>[!NOTE]
>
>La localisation de la racine du document du serveur web sur le périphérique de stockage réseau (NAS) entraîne une dégradation des performances. En outre, lorsqu’une racine de document sur le NAS est partagée entre plusieurs serveurs web, des verrous intermittents peuvent se produire lors des actions de réplication.

>[!NOTE]
>
>Dispatcher stocke le document mis en cache dans une structure égale à l’URL demandée.
>
>Il peut y avoir des limites au niveau du système d’exploitation pour la longueur du nom de fichier. En d’autres termes, si vous disposez d’une URL avec de nombreux sélecteurs.

### Méthodes de mise en cache

Dispatcher dispose de deux méthodes principales pour mettre à jour le contenu du cache lorsque des modifications sont apportées au site web.

* **Mises à jour du contenu** supprimez les pages qui ont été modifiées et les fichiers qui y sont directement associés.
* **L’invalidation automatique** invalide automatiquement les parties du cache susceptibles d’être obsolètes après une mise à jour. En d’autres termes, il marque effectivement les pages pertinentes comme étant obsolètes, sans rien supprimer.

### Mises à jour du contenu

Dans une mise à jour de contenu, un ou plusieurs documents AEM changent. AEM envoie une demande de syndication à Dispatcher, qui met à jour le cache en conséquence :

1. Il supprime les fichiers modifiés du cache.
1. Il supprime tous les fichiers commençant par le même descripteur du cache. Par exemple, si le fichier /en/index.html est mis à jour, tous les fichiers commençant par /en/index. sont supprimées. Ce mécanisme vous permet de concevoir des sites qui fonctionnent bien avec le cache, en particulier en ce qui concerne la navigation sur les images.
1. It *touch* le **statfile**, qui met à jour l’horodatage du fichier stat pour indiquer la date de la dernière modification.

Notez les points suivants :

* Les mises à jour de contenu sont généralement utilisées avec un système de création qui &quot;sait&quot; ce qui doit être remplacé.
* Les fichiers affectés par une mise à jour de contenu sont supprimés, mais pas remplacés immédiatement. La prochaine fois qu’un tel fichier est demandé, Dispatcher récupère le nouveau fichier de l’instance AEM et le place dans le cache, en remplaçant l’ancien contenu.
* En règle générale, les images générées automatiquement qui incorporent du texte d’une page sont stockées dans des fichiers image commençant par la même poignée, ce qui garantit que l’association existe pour la suppression. Par exemple, vous pouvez stocker le texte du titre de la page mypage.html en tant qu’image mypage.titlePicture.gif dans le même dossier. Ainsi, l’image est automatiquement supprimée du cache chaque fois que la page est mise à jour. Vous pouvez donc vous assurer que l’image reflète toujours la version actuelle de la page.
* Vous pouvez avoir plusieurs fichiers de statut, par exemple un par dossier de langue. Si une page est mise à jour, AEM recherche le dossier parent suivant contenant un fichier stat, et *touch* ce fichier.

### Invalidation automatique

L’invalidation automatique invalide automatiquement des parties du cache, sans supprimer physiquement aucun fichier. À chaque mise à jour de contenu, le fichier stat est modifié, de sorte que son horodatage reflète la dernière mise à jour du contenu.

Dispatcher comporte une liste de fichiers sujets à l’invalidation automatique. Lorsqu’un document de cette liste est demandé, Dispatcher compare la date du document mis en cache à l’horodatage du fichier stat :

* si le document mis en cache est plus récent, Dispatcher le renvoie.
* s’il est plus ancien, Dispatcher récupère la version actuelle à partir de l’instance AEM.

Notez également ce qui suit :

* L’invalidation automatique est généralement utilisée lorsque les relations sont complexes, telles que les pages de HTML. Ces pages contiennent des liens et des entrées de navigation. Elles doivent donc généralement être mises à jour après une mise à jour du contenu. Si vous avez généré automatiquement des fichiers PDF ou image, vous pouvez également choisir d’invalider automatiquement ces fichiers.
* L’invalidation automatique n’implique aucune action de Dispatcher au moment de la mise à jour, à l’exception de la modification du fichier stat. Toutefois, la modification du fichier stat rend automatiquement le contenu du cache obsolète, sans le supprimer physiquement du cache.

## Procédure de renvoi des documents par Dispatcher  {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Déterminer si le document est soumis à la mise en cache

Vous pouvez [définir les documents mis en cache par Dispatcher dans le fichier de configuration ;](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=fr). Dispatcher vérifie la demande par rapport à la liste des documents pouvant être mis en cache. Si le document ne figure pas dans cette liste, Dispatcher demande le document à partir de l’instance AEM.

Dispatcher demande toujours le document directement à partir de l’instance AEM dans les cas suivants :

* L’URI de demande contient un point d’interrogation &quot;`?`&quot;. Ce scénario indique généralement une page dynamique, telle qu’un résultat de recherche, qui n’a pas besoin d’être mise en cache.
* L’extension de fichier est manquante. Le serveur web a besoin de l’extension pour déterminer le type de document (type MIME).
* L’en-tête d’authentification est défini (configurable).

>[!NOTE]
>
>Les méthodes GET ou HEAD (pour les en-têtes HTTP) sont mises en cache par Dispatcher. Pour plus d’informations sur la mise en cache des en-têtes de réponse, voir [Mise en cache des en-têtes de réponse HTTP](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=fr).

### Déterminer si un document est mis en cache

Dispatcher stocke les fichiers mis en cache sur le serveur web comme s’ils faisaient partie d’un site web statique. Si un utilisateur demande un document pouvant être mis en cache, Dispatcher vérifie si ce document existe dans le système de fichiers du serveur web :

* si le document est mis en cache, Dispatcher renvoie le fichier .
* s’il n’est pas mis en cache, Dispatcher demande le document à l’instance AEM.

### Déterminer si le document est à jour

Pour savoir si un document est à jour, Dispatcher effectue deux étapes :

1. Il vérifie si le document est soumis à l’invalidation automatique. Si ce n’est pas le cas, le document est considéré comme étant à jour.
1. Si le document est configuré pour l’invalidation automatique, Dispatcher vérifie s’il est plus ancien ou plus récent que la dernière modification disponible. S’il est plus ancien, Dispatcher demande la version actuelle à l’instance AEM et remplace la version dans le cache.

>[!NOTE]
>
>Documents sans **invalidation automatique** restent dans le cache jusqu’à ce qu’ils soient physiquement supprimés. Par exemple, par une mise à jour de contenu sur le site web.

## Les avantages de l’équilibrage de charge {#the-benefits-of-load-balancing}

L’équilibrage de charge est la pratique consistant à répartir la charge de calcul du site web sur plusieurs instances d’AEM.

![](assets/chlimage_1-7.png)

Vous gagnez :

* **puissance de traitement accrue**
En pratique, une puissance de traitement accrue signifie que Dispatcher partage des demandes de document entre plusieurs instances d’AEM. Chaque instance ayant désormais moins de documents à traiter, les temps de réponse sont plus rapides. Dispatcher conserve les statistiques internes pour chaque catégorie de document afin qu’il puisse estimer la charge et distribuer les requêtes efficacement.

* **une couverture de sécurité intégrée accrue ;**
Si Dispatcher ne reçoit pas de réponses d’une instance, il transmet automatiquement les requêtes à l’une des autres instances. Si une instance n’est plus disponible, le seul effet est un ralentissement du site, proportionnel à la puissance de calcul perdue. Cependant, tous les services continuent.

* Vous pouvez également gérer différents sites web sur le même serveur web statique.

>[!NOTE]
>
>Bien que l’équilibrage de charge répartit la charge efficacement, la mise en cache permet de réduire la charge. Par conséquent, essayez d’optimiser la mise en cache et de réduire la charge globale avant de configurer l’équilibrage de charge. Une bonne mise en cache peut augmenter les performances de l’équilibreur de charge ou rendre l’équilibrage de charge inutile.

>[!CAUTION]
>
>Bien qu’un seul Dispatcher soit en mesure de saturer la capacité des instances de publication disponibles, il peut s’avérer judicieux, pour certaines applications rares, d’équilibrer également la charge entre deux instances de Dispatcher. Les configurations avec plusieurs dispatchers doivent être prises en compte avec précaution, car un Dispatcher supplémentaire peut augmenter la charge sur les instances de publication disponibles et peut facilement réduire les performances dans la plupart des applications.

## Exécution de l’équilibrage de charge par Dispatcher  {#how-the-dispatcher-performs-load-balancing}

### Statistiques de performances

Dispatcher conserve les statistiques internes sur la vitesse à laquelle chaque instance d’AEM traite les documents. Sur la base de ces données, Dispatcher estime quelle instance peut fournir le temps de réponse le plus rapide lors de la réponse à une requête. Il réserve donc le temps de calcul nécessaire pour cette instance.

Différents types de requêtes peuvent présenter des délais d’exécution moyens différents. Par conséquent, Dispatcher vous permet de spécifier des catégories de documents. Ces catégories sont ensuite prises en compte lors du calcul des estimations temporelles. Vous pouvez, par exemple, faire la distinction entre les pages de HTML et les images, car les temps de réponse standard peuvent très bien différer.

Si vous utilisez une fonction de recherche élaborée, vous pouvez créer une catégorie pour les requêtes de recherche. Cette méthode permet à Dispatcher d’envoyer des requêtes de recherche à l’instance qui répond le plus rapidement. Cela permet également d’éviter qu’une instance plus lente ne bloque lorsqu’elle reçoit plusieurs requêtes de recherche &quot;coûteuses&quot;, tandis que les autres reçoivent les requêtes &quot;moins chères&quot;.

### Contenu personnalisé (connexions persistantes)

Les connexions persistantes garantissent que les documents d’un utilisateur sont tous composés sur la même instance d’AEM. Ce point est important si vous utilisez des pages personnalisées et des données de session. Les données sont stockées sur l’instance. Par conséquent, les demandes ultérieures du même utilisateur doivent revenir à cette instance ou les données sont perdues.

Comme les connexions persistantes limitent la capacité de Dispatcher à optimiser les requêtes, vous ne devez les utiliser que lorsque cela est nécessaire. Vous pouvez spécifier le dossier contenant les documents &quot;persistants&quot;, en vous assurant ainsi que tous les documents de ce dossier sont composés sur la même instance pour chaque utilisateur.

>[!NOTE]
>
>Pour la plupart des pages qui utilisent des connexions persistantes, vous devez désactiver la mise en cache ; dans le cas contraire, la page est identique pour tous les utilisateurs, quel que soit le contenu de la session.
>
>Pour un *some* , il peut être possible d’utiliser à la fois des connexions persistantes et une mise en cache ; par exemple, si vous affichez un formulaire qui écrit des données dans la session.

## Utilisation de plusieurs instances de Dispatcher  {#using-multiple-dispatchers}

Dans les configurations complexes, vous pouvez utiliser plusieurs dispatchers. Par exemple, vous pouvez utiliser :

* une instance de Dispatcher pour publier un site web sur l’intranet ;
* un second dispatcher, sous une autre adresse et avec des paramètres de sécurité différents, pour publier le même contenu sur Internet.

Dans ce cas, veillez à ce que chaque demande ne passe par qu’un seul Dispatcher. Un Dispatcher ne gère pas les requêtes provenant d’un autre Dispatcher. Par conséquent, assurez-vous que les deux dispatchers accèdent directement au site web d’AEM.

## Utilisation de Dispatcher avec un CDN  {#using-dispatcher-with-a-cdn}

Un réseau de distribution de contenu (CDN), par exemple Akamai Edge Delivery ou Amazon Cloud Front, distribue du contenu à partir d’un emplacement proche de l’utilisateur final. Ainsi, il :

* accélère les temps de réponse pour les utilisateurs finaux ;
* soulage vos serveurs ;

En tant que composant d’infrastructure HTTP, un réseau de diffusion de contenu fonctionne comme Dispatcher. Lorsqu’un noeud CDN reçoit une requête, il la diffuse à partir de son cache, si possible (la ressource est disponible dans le cache et est valide). Dans le cas contraire, il se rend sur le serveur le plus proche suivant pour récupérer la ressource et la mettre en cache pour d’autres requêtes, le cas échéant.

Le &quot;serveur le plus proche suivant&quot; dépend de votre configuration spécifique. Par exemple, dans une installation Akamai, la requête peut prendre le chemin suivant :

* Le nœud Akamai Edge
* Le calque Akamai Midgres
* Votre pare-feu
* Votre équilibreur de charge
* Dispatcher
* AEM

En règle générale, Dispatcher est le serveur suivant qui peut diffuser le document à partir d’un cache et influencer les en-têtes de réponse renvoyés au serveur CDN.

## Contrôle d’un cache CDN  {#controlling-a-cdn-cache}

Il existe plusieurs façons de contrôler la durée pendant laquelle un CDN met en cache une ressource avant de la récupérer à partir de Dispatcher.

1. Configuration explicite\
   Configurez la durée pendant laquelle des ressources particulières sont conservées dans le cache du CDN, en fonction du type MIME, de l’extension, du type de requête, etc.

1. En-têtes d’expiration et de contrôle du cache\
   La plupart des CDN honorent `Expires:` et `Cache-Control:` En-têtes HTTP s’ils sont envoyés par le serveur en amont. Cette méthode peut être réalisée, par exemple en utilisant la méthode [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) Module Apache.

1. Invalidation manuelle\
   Les CDN permettent de supprimer des ressources du cache via des interfaces web.
1. Invalidation basée sur l’API\
   La plupart des CDN offrent également une API REST et/ou SOAP qui permet de supprimer des ressources du cache.

Dans une configuration d’AEM type, la configuration par extension, par chemin ou par les deux - ce qui peut être réalisé au moyen des points 1 et 2 ci-dessus - offre des possibilités de définir des périodes de mise en cache raisonnables pour les ressources souvent utilisées qui ne changent pas souvent, telles que les images de conception et les bibliothèques clientes. Lorsque de nouvelles versions sont déployées, une invalidation manuelle est généralement requise.

Si cette approche est utilisée pour mettre en cache du contenu géré, elle implique que les modifications de contenu ne sont visibles par les utilisateurs finaux qu’une fois la période de mise en cache configurée expirée et que le document est à nouveau récupéré à partir de Dispatcher.

Pour un contrôle plus affiné, l’invalidation basée sur l’API vous permet d’invalider le cache d’un réseau de diffusion de contenu, car le cache de Dispatcher est invalidé. En fonction de l’API CDN, vous pouvez mettre en oeuvre vos propres [ContentBuilder](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/ContentBuilder.html) et [TransportHandler](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/TransportHandler.html) (si l’API n’est pas basée sur REST) et configurez un agent de réplication qui utilise ces éléments pour invalider le cache du réseau de diffusion de contenu.

>[!NOTE]
>
>Voir aussi [AEM (CQ) Sécurité du Dispatcher et mise en cache CDN+navigateur](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) et présentation enregistrée [Mise en cache de Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2015/aem-dispatcher-caching-new-features-and-optimizations.html?lang=en).

## Utilisation de Dispatcher avec un serveur de création {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>Si vous utilisez [AEM avec l’interface utilisateur tactile](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=en), do **not** mise en cache du contenu de l’instance d’auteur. Si la mise en cache a été activée pour l’instance d’auteur, vous devez la désactiver et supprimer le contenu du répertoire du cache. Pour désactiver la mise en cache, modifiez la variable `author_dispatcher.any` et modifiez la variable `/rule` de la propriété `/cache` , comme suit :

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Un Dispatcher peut être utilisé devant une instance d’auteur pour améliorer les performances de création. Pour configurer une instance de création de Dispatcher, procédez comme suit :

1. Installez Dispatcher sur un serveur web (un serveur web Apache ou IIS, voir [Installation de Dispatcher](dispatcher-install.md)).
1. Testez Dispatcher nouvellement installé par rapport à une instance de publication AEM opérationnelle. Cela garantit qu’une installation correcte de la ligne de base a été réalisée.
1. Vérifiez maintenant que Dispatcher peut se connecter à votre instance d’auteur via TCP/IP.
1. Remplacer l’exemple `dispatcher.any` avec le fichier `author_dispatcher.any` fourni avec le fichier [Téléchargement de Dispatcher](release-notes.md#downloads).
1. Ouvrez le `author_dispatcher.any` dans un éditeur de texte et apportez les modifications suivantes :

   1. Modifiez la variable `/hostname` et `/port` de `/renders` afin qu’elles pointent vers votre instance d’auteur.
   1. Modifiez la variable `/docroot` de `/cache` afin qu’ils pointent vers un répertoire de cache. Si vous utilisez [AEM avec l’interface utilisateur tactile](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=en), voir l’avertissement ci-dessus.
   1. Enregistrez les modifications.

1. Supprimez tous les fichiers existants dans le répertoire `/cache` > `/docroot` que vous avez configuré ci-dessus.
1. Redémarrez le serveur web.

>[!NOTE]
>
>Avec le `author_dispatcher.any` configuration, lorsque vous installez un module de fonctionnalités, correctif ou package de code d’application CQ5 qui affecte n’importe quel contenu sous `/libs` ou `/apps`, vous devez supprimer les fichiers mis en cache sous ces répertoires dans le cache de Dispatcher. Cela permet de s’assurer que la prochaine fois qu’ils sont demandés, les fichiers récemment mis à niveau sont récupérés, et non les anciens mis en cache.

>[!CAUTION]
>
>Si vous avez utilisé l’instance de création Dispatcher configurée précédemment et si vous avez activé une *Agent de vidage de Dispatcher*, procédez comme suit :

1. Supprimez ou désactivez la fonction **Auteur de Dispatcher** agent de purge sur votre instance d’auteur AEM.
1. Rétablissez la configuration du Dispatcher de création en suivant les nouvelles instructions ci-dessus.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
