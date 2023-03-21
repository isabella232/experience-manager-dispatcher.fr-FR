---
title: Problèmes fréquents de Dispatcher
seo-title: Top issues for AEM Dispatcher
description: Problèmes fréquents d’AEM Dispatcher
seo-description: Top issues for Adobe AEM Dispatcher
exl-id: 4dcc7318-aba5-4b17-8cf4-190ffefbba75
source-git-commit: f83b02d74a22e055b486305dfe5420e152efb452
workflow-type: tm+mt
source-wordcount: '1578'
ht-degree: 66%

---

# FAQ sur les problèmes fréquents d’AEM Dispatcher

![Configuration de Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introduction

### Qu’est-ce que Dispatcher ?

Dispatcher est un outil de mise en cache et/ou d’équilibrage de charge Adobe Experience Manager qui permet de créer un environnement de création web dynamique et rapide. Pour la mise en cache, Dispatcher fonctionne comme faisant partie d’un serveur HTTP, tel qu’Apache. Il a pour but de stocker (ou de &quot;mettre en cache&quot;) autant de contenu statique que possible du site web et d’accéder aussi rarement que possible au moteur de mise en page du site web. Dans un rôle d’équilibrage de charge, Dispatcher répartit les requêtes utilisateur (charge) sur différentes instances AEM (rendus).

Pour la mise en cache, le module Dispatcher utilise la capacité du serveur web à fournir du contenu statique. Dispatcher place les documents mis en cache à la racine du document du serveur web.

### Comment Dispatcher effectue-t-il la mise en cache ?

Dispatcher utilise la capacité du serveur web pour diffuser du contenu statique. Dispatcher stocke les documents mis en cache dans la racine du document du serveur web. Dispatcher dispose de deux méthodes principales pour mettre à jour le contenu du cache lorsque des modifications sont apportées au site web.

* **Mises à jour du contenu** supprimez les pages qui ont été modifiées et les fichiers qui y sont directement associés.
* **L’invalidation automatique** invalide automatiquement les parties du cache susceptibles d’être obsolètes après une mise à jour. Par exemple, l’invalidation automatique marque les pages correspondantes comme étant obsolètes, sans rien supprimer.

### Quels sont les avantages de la répartition de charge ?

L’équilibrage de charge répartit les requêtes utilisateur (charge) sur plusieurs instances AEM. La liste suivante décrit les avantages de l’équilibrage de charge :

* **Puissance de traitement accrue**: En pratique, cette méthode signifie que Dispatcher partage des demandes de document entre plusieurs instances d’AEM. Chaque instance ayant désormais moins de documents à traiter, les délais de réponse sont plus rapides. Dispatcher conserve les statistiques internes pour chaque catégorie de document afin qu’il puisse estimer la charge et distribuer les requêtes efficacement.
* **Couverture de sécurité intégrée accrue**: Si Dispatcher ne reçoit pas de réponses d’une instance, il transmet automatiquement les requêtes à l’une des autres instances. Par conséquent, si une instance n’est plus disponible, le seul effet est un ralentissement du site, proportionnel à la puissance de calcul perdue.

>[!NOTE]
>
>Pour plus d’informations, voir la [page Aperçu de Dispatcher](dispatcher.md)

## Installation et configuration d’AEM

### Où télécharger le module Dispatcher ?

Vous pouvez télécharger le dernier module Dispatcher sur la page [Notes de mise à jour de Dispatcher](release-notes.md).

### Comment installer le module Dispatcher ?

Voir la page [Installation de Dispatcher](dispatcher-install.md)

### Comment configurer le module Dispatcher ?

Voir la page [Configuration de Dispatcher](dispatcher-configuration.md).

### Comment configurer le Dispatcher pour l’instance d’auteur ?

Voir [Utilisation de Dispatcher avec une instance d’auteur](dispatcher.md#using-a-dispatcher-with-an-author-server) pour connaître les étapes détaillées.

### Comment configurer Dispatcher avec plusieurs domaines ?

Vous pouvez configurer Dispatcher CQ avec plusieurs domaines, à condition que les domaines respectent les conditions suivantes :

* Le contenu web des deux domaines est stocké dans un référentiel AEM unique
* Les fichiers du cache de Dispatcher peuvent être invalidés séparément pour chaque domaine

Pour plus d’informations, voir [Utiliser Dispatcher avec plusieurs domaines](dispatcher-domains.md).

### Comment configurer Dispatcher de sorte que toutes les requêtes d’un utilisateur soient acheminées vers la même instance de publication ?

Vous pouvez utiliser la fonctionnalité de [connexions persistantes](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor), qui garantit que tous les documents d’un utilisateur sont traités sur la même instance d’AEM. Cette fonctionnalité est importante si vous utilisez des pages personnalisées et des données de session. Les données sont stockées sur l’instance. Par conséquent, les requêtes suivantes, émanant du même utilisateur, doivent renvoyer à cette instance. Dans le cas contraire, les données seront perdues.

Les connexions persistantes limitent la capacité de Dispatcher à optimiser les requêtes. Vous devez les utiliser uniquement en cas de besoin. Vous pouvez spécifier le dossier qui contient les documents « persistants », assurant ainsi que tous les documents de ce dossier sont composés sur la même instance pour chaque utilisateur.

### Puis-je utiliser des connexions persistantes et la mise en cache en tandem ?

Désactivez la mise en cache de la plupart des pages qui utilisent des connexions persistantes. Sinon, la même instance de la page s’affichera pour tous les utilisateurs, quel que soit le contenu de la session.

Pour certaines applications, il peut être possible d’utiliser à la fois des connexions persistantes et une mise en cache. Par exemple, si vous affichez un formulaire qui écrit des données à une session, vous pouvez utiliser des connexions persistantes et une mise en cache.

### Dispatcher et une instance AEM de publication peuvent-ils résider sur le même ordinateur physique ?

Oui, si l’ordinateur est suffisamment puissant. Toutefois, il est recommandé de configurer Dispatcher et l’instance AEM de publication sur différents ordinateurs.

En règle générale, l’instance de publication se trouve à l’intérieur du pare-feu et Dispatcher se trouve dans la zone démilitarisée. Si vous décidez de disposer à la fois de l’instance de publication et de Dispatcher sur la même machine physique, assurez-vous que les paramètres du pare-feu interdisent l’accès direct à l’instance de publication à partir de réseaux externes.

### Puis-je mettre en cache uniquement les fichiers avec des extensions spécifiques ?

Oui. Par exemple, si vous souhaitez mettre en cache uniquement les fichiers GIF, spécifiez *.gif dans la section cache du fichier de configuration dispatcher.any.

### Comment supprimer des fichiers du cache ?

Vous pouvez supprimer des fichiers du cache à l’aide d’une requête HTTP. Lorsque la requête HTTP est reçue, Dispatcher supprime les fichiers du cache. Dispatcher met à nouveau les fichiers en cache uniquement lorsqu’il reçoit une requête client pour la page. Cette méthode de suppression de fichiers mis en cache convient aux sites web peu susceptibles de recevoir des requêtes simultanées pour la même page.

La requête HTTP présente la syntaxe suivante :

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher supprime les fichiers et les dossiers mis en cache et dont les noms correspondent à la valeur de l’en-tête CQ. Par exemple, une CQ-Handle de `/content/geomtrixx-outdoors/en` correspond aux éléments suivants :

Tous les fichiers (quelle que soit leur extension) nommés en dans le répertoire geometrixx-outdoors. N’importe quel répertoire nommé `_jcr_content` sous le répertoire en (qui, s’il existe, contient les rendus mis en cache des sous-noeuds de la page).
Répertoire `en` n’est supprimé que si la variable `CQ-Action` is `Delete` ou `Deactivate`.

Pour plus d’informations sur cette rubrique, voir [Invalidation manuelle du cache de Dispatcher](page-invalidate.md).

### Comment implémenter la mise en cache sensible aux autorisations ?

Voir la page [Mise en cache de contenu sécurisé](permissions-cache.md).

### Comment sécuriser les communications entre les instances Dispatcher et CQ ?

Voir [Liste de contrôle de sécurité de Dispatcher](security-checklist.md) et le [Liste de contrôle AEM sécurité](https://experienceleague.adobe.com/docs/experience-manager-64/administering/security/security-checklist.html?lang=fr) pages.

### Le problème lié à `jcr:content` de Dispatcher a été remplacé par `jcr%3acontent`

**Question**: L’entreprise a récemment rencontré un problème au niveau de Dispatcher. Un des appels AJAX qui obtenait un référentiel CQ de formulaire de données avait `jcr:content` dans . qui a été codé en `jcr%3acontent` ce qui entraîne un jeu de résultats incorrect.

**Réponse**: Utilisation `ResourceResolver.map()` pour obtenir une URL &quot;conviviale&quot; à utiliser/émettre des requêtes de et pour résoudre le problème de mise en cache avec Dispatcher. La méthode map() code la variable `:` deux-points en caractères de soulignement et la méthode resolve() les décode au format lisible SLING JCR. Utilisez la méthode map() pour générer l’URL utilisée dans l’appel Ajax.

En savoir plus : [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Purge de Dispatcher

### Comment configurer les agents de purge de Dispatcher sur une instance de publication ?

Voir [Réplication](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/replication.html?lang=en#configuring-your-replication-agents) page.

### Comment résoudre les problèmes de purge de Dispatcher ?

[Consultez ces articles de dépannage](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]).

Si les opérations de suppression incitent Dispatcher à vider, [utilisez la solution dans cette publication de blog de la communauté de Sensei Martin](https://mkalugin-cq.blogspot.com/2012/04/i-have-been-working-on-following.html).

### Comment vider les ressources DAM du cache de Dispatcher ?

Vous pouvez utiliser la fonctionnalité de « réplication de chaînes ». Lorsque cette fonction est activée, l’agent de purge de Dispatcher envoie une demande de purge lorsqu’une réplication est reçue de l’auteur.

Pour l’activer :

1. [Suivez les étapes ci-dessous](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) pour créer des agents de purge sur publication.
1. Accédez à la configuration de chaque agent et au **Triggers** , vérifiez les **À réception** de la boîte.

## Divers

Comment Dispatcher détermine-t-il si un document est à jour ?
Pour déterminer si un document est à jour, Dispatcher effectue les actions suivantes :

Il vérifie si le document est soumis à l’invalidation automatique. Si ce n’est pas le cas, le document est considéré comme étant à jour.
Si le document est configuré pour l’invalidation automatique, Dispatcher vérifie s’il est plus ancien ou plus récent que la dernière modification disponible. S’il est plus ancien, Dispatcher demande la version actuelle à l’instance AEM et remplace la version dans le cache.

### Comment Dispatcher renvoie-t-il des documents ?

Vous pouvez définir si Dispatcher met en cache un document à l’aide [du fichier de configuration](dispatcher-configuration.md) de Dispatcher `dispatcher.any`. Dispatcher vérifie la demande par rapport à la liste des documents pouvant être mis en cache. Si le document ne figure pas dans cette liste, Dispatcher demande le document à partir de l’instance AEM.

La propriété `/rules` contrôle les documents qui sont mis en cache selon le chemin d’accès au document. Quelle que soit la propriété `/rules`, Dispatcher ne procède jamais à la mise en cache d’un document dans les cas suivants :

* L’URI de requête contient une `(?)` point d’interrogation.
* Elle indique une page dynamique, telle qu’un résultat de recherche qui n’a pas besoin d’être mis en cache.
* L’extension de fichier est manquante.
* Le serveur web a besoin de l’extension pour déterminer le type de document (type MIME).
* L’en-tête d’authentification est défini (configurable).
* Si l’instance AEM répond avec les en-têtes suivants :
   * no-cache
   * no-store
   * must-revalidate

Dispatcher stocke les fichiers mis en cache sur le serveur web comme s’ils faisaient partie d’un site web statique. Si l’utilisateur demande un document pouvant être mis en cache, Dispatcher vérifie si le document existe dans le système de fichiers du serveur web. Si tel est le cas, Dispatcher renvoie les documents. Sinon, Dispatcher demande le document à l’instance AEM.

>[!NOTE]
>
>Les méthodes GET ou HEAD (pour les en-têtes HTTP) sont mises en cache par Dispatcher. Pour plus d’informations sur la mise en cache des en-têtes de réponse, voir [Mise en cache des en-têtes de réponse HTTP](dispatcher-configuration.md#caching-http-response-headers).

### Puis-je implémenter plusieurs Dispatcher dans une configuration ?

Oui. Dans ce cas, assurez-vous que les deux Dispatcher peuvent accéder directement au site web AEM. Un Dispatcher ne peut pas traiter les requêtes provenant d’un autre Dispatcher.
