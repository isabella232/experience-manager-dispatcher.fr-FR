---
title: Problèmes fréquents du répartiteur
seo-title: Problèmes fréquents pour le répartiteur AEM
description: Problèmes fréquents pour le répartiteur AEM
seo-description: Problèmes fréquents pour le répartiteur Adobe AEM
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# FAQ sur les problèmes fréquents du répartiteur AEM

![Configuration de Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introduction

### Qu&#39;est-ce que le répartiteur ?

Le Répartiteur est l&#39;outil d&#39;équilibrage de charge et/ou de mise en cache d&#39;Adobe Experience Manager qui permet de créer un environnement de création Web rapide et dynamique. Pour la mise en cache, le répartiteur fonctionne comme faisant partie d&#39;un serveur HTTP, tel qu&#39;Apache, dans le but de stocker (ou « mettre en cache ») autant de contenus du site Web statique que possible et d&#39;accéder le plus souvent possible au moteur de mise en page du site Web. Dans un rôle d&#39;équilibrage de charge, le répartiteur répartit les requêtes des utilisateurs (chargement) sur différentes instances AEM (rendu).

Pour la mise en cache, le module Répartiteur utilise la capacité du serveur Web à diffuser du contenu statique. Le Répartiteur place les documents mis en cache dans la racine du document du serveur Web.

### Comment le répartiteur effectue-t-il la mise en cache ?

Le répartiteur utilise la capacité du serveur Web pour diffuser du contenu statique. Le répartiteur stocke les documents mis en cache dans la racine du document du serveur Web. Dispatcher dispose de deux méthodes principales pour mettre à jour le contenu du cache lorsque des modifications sont apportées au site web.

* **Les mises à jour** de contenu suppriment les pages qui ont été modifiées, ainsi que les fichiers qui y sont directement associés.
* **Auto-Invalidation** invalide automatiquement les parties du cache qui peuvent être prêtes à l&#39;emploi (date) après une mise à jour. Par exemple, il signale efficacement les pages appropriées comme étant prêtes à l&#39;emploi, sans supprimer quoi que ce soit.

### Quels sont les avantages de l&#39;équilibrage de la charge ?

L&#39;équilibrage de charge répartit les requêtes utilisateur (chargement) sur plusieurs instances AEM. La liste suivante décrit les avantages de l&#39;équilibrage de charge :

* **Augmentation de la puissance de traitement**: En pratique, le répartiteur partage les requêtes de document entre plusieurs instances d&#39;AEM. Etant donné que chaque instance comporte moins de documents à traiter, les temps de réponse sont plus courts. Dispatcher conserve les statistiques internes pour chaque catégorie de document afin qu’il puisse estimer la charge et distribuer les requêtes efficacement.
* **Couverture fiable accrue**: Si le Répartiteur ne reçoit pas de réponses d&#39;une instance, il transmet automatiquement les requêtes à l&#39;une des autres instances. Par conséquent, si une instance n’est plus disponible, le seul effet est un ralentissement du site, proportionnel à la puissance de calcul perdue.

>[!NOTE]
>
>Pour plus d&#39;informations, reportez-vous à [la page Aperçu du répartiteur](dispatcher.md)

## Installation et configuration

### Où télécharger le module Répartiteur ?

Vous pouvez télécharger le dernier module Répartiteur à partir de la page [Notes](release-notes.md) de mise à jour du répartiteur.

### Comment installer le module Répartiteur ?

Reportez-vous à [la](dispatcher-install.md) page Installation du répartiteur

### Comment configurer le module Répartiteur ?

Voir la page [Configuration du répartiteur](dispatcher-configuration.md) .

### Comment configurer le répartiteur pour l&#39;instance d&#39;auteur ?

Voir [Utilisation du répartiteur avec une instance d&#39;auteur](dispatcher.md#using-a-dispatcher-with-an-author-server) pour connaître les étapes détaillées.

### Comment configurer le Répartiteur avec plusieurs domaines ?

Vous pouvez configurer le répartiteur CQ avec plusieurs domaines, à condition que les domaines respectent les conditions suivantes :

* Le contenu Web des deux domaines est stocké dans un référentiel AEM unique.
* Les fichiers du cache de Dispatcher peuvent être invalidés séparément pour chaque domaine

Lecture [avec Répartiteur avec plusieurs domaines](dispatcher-domains.md) pour plus d&#39;informations.

### Comment configurer le Répartiteur, de telle sorte que toutes les requêtes d&#39;un utilisateur soient acheminées vers la même instance de publication ?

Vous pouvez utiliser [la fonction de connexion](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) bascule, ce qui garantit que tous les documents d&#39;un utilisateur sont traités sur la même instance d&#39;AEM. Cette fonction est importante si vous utilisez des pages personnalisées et des données de session. Les données sont stockées sur l&#39;instance. Par conséquent, les demandes suivantes émanant du même utilisateur doivent renvoyer à cette instance ou les données sont perdues.

Comme les connexions bascules limitent la capacité du répartiteur à optimiser les requêtes, vous devez utiliser cette approche uniquement si nécessaire. Vous pouvez spécifier le dossier contenant les documents « bascules », afin que tous les documents de ce dossier soient traités sur la même instance pour un utilisateur.

### Puis-je utiliser des connexions bascules et la mise en cache en tandem ?

Pour la plupart des pages qui utilisent des connexions bascules, désactivez la mise en cache. Sinon, la même instance de la page s&#39;affiche pour tous les utilisateurs, quel que soit le contenu de la session.

Pour certaines applications, il peut être possible d&#39;utiliser à la fois des connexions bascules et une mise en cache. Par exemple, si vous affichez un formulaire qui écrit des données à une session, vous pouvez utiliser des connexions bascules et une mise en cache.

### Un répartiteur et une instance AEM Publish peuvent-ils résider sur le même ordinateur physique ?

Oui, si l&#39;ordinateur est suffisamment puissant. Toutefois, il est recommandé de configurer le Répartiteur et l&#39;instance AEM Publish sur différents ordinateurs.

En général, l&#39;instance de publication réside à l&#39;intérieur du pare-feu et le répartiteur réside dans le DMZ. Si vous décidez de disposer à la fois de l&#39;instance Publication et du Répartiteur sur la même machine physique, assurez-vous que les paramètres du pare-feu interdisent l&#39;accès direct à l&#39;instance Publication à partir de réseaux externes.

### Puis-je mettre en cache uniquement les fichiers avec des extensions spécifiques ?

Oui. Par exemple, si vous souhaitez mettre en cache uniquement les fichiers GIF, spécifiez *. gif dans la section cache du fichier de configuration dispatcher. any.

### Comment supprimer des fichiers du cache ?

Vous pouvez supprimer des fichiers du cache à l&#39;aide d&#39;une requête HTTP. Lorsque la requête HTTP est reçue, Répartiteur supprime les fichiers du cache. Le répartiteur met à nouveau les fichiers en cache uniquement lorsqu&#39;il reçoit une demande client pour la page. La suppression de fichiers mis en cache de cette façon est adaptée aux sites Web qui ne sont pas susceptibles de recevoir des requêtes simultanées pour la même page.

La syntaxe HTTP est la suivante :

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Le répartiteur supprime les fichiers et dossiers mis en cache dont les noms correspondent à la valeur de l&#39;en-tête de poignée CQ. Par exemple, une poignée CQ-Handof `/content/geomtrixx-outdoors/en` correspond aux éléments suivants :

Tous les fichiers (d&#39;une extension de fichier) nommés en dans le répertoire
geometrixx-outdoors Tout répertoire nommé `_jcr_content` sous le répertoire en (qui, s&#39;il existe, contient des rendus en mémoire cache des sous-nœuds de la page)
Le répertoire en sera uniquement supprimé si le `CQ-Action` paramètre est `Delete` ou `Deactivate`.

Pour plus d&#39;informations sur cette rubrique, voir [Invalidation manuelle du cache du répartiteur](page-invalidate.md).

### Comment mettre en cache la mise en cache sensible aux autorisations ?

Voir la page [Mise en cache de contenu](permissions-cache.md) sécurisé.

### Comment sécuriser les communications entre les instances Répartiteur et CQ ?

Voir la liste de contrôle de sécurité [du répartiteur](security-checklist.md) et les [pages de liste de contrôle](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security-checklist.html) de sécurité AEM.

### Le problème du répartiteur `jcr:content` a été remplacé par `jcr%3acontent`

**Question**: Nous avons récemment rencontré un problème au niveau du répartiteur, dans lequel l&#39;un des appels ajax qui obtient un référentiel CQ de formulaire de données contenait `jcr:content` un problème et qui était codé pour `jcr%3acontent` produire un jeu de résultats incorrect.

**Réponse**: Utilisez `ResourceResolver.map()` la méthode pour obtenir une URL « conviviale » à utiliser/recevoir des requêtes d&#39;origine et pour résoudre le problème de mise en cache avec Répartiteur. La méthode map () code le `:` deux-points aux traits de soulignement et la méthode resolve () les décode au format lisible SLING JCR. Vous devez utiliser la méthode map () pour générer l&#39;URL utilisée dans l&#39;appel Ajax.

Plus lire : [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Vider le répartiteur

### Comment configurer les agents de purge du répartiteur sur une instance de publication ?

Voir la page [Réplication](https://helpx.adobe.com/content/help/en/experience-manager/6-4/sites/deploying/using/replication.html#ConfiguringyourReplicationAgents) .

### Comment résoudre les problèmes de blocage du répartiteur ?

[Reportez-vous à cet article](https://helpx.adobe.com/content/help/en/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) de dépannage qui répond aux questions suivantes :

* Comment déboguer une situation où aucun contenu n&#39;est enregistré dans le cache Répartiteur ?
* Comment déboguer une situation où les fichiers de cache ne sont pas mis à jour ?
* Comment puis-je déboguer une situation qui ne fonctionne pas en rapport avec la purge du répartiteur ?

Si les opérations de suppression incitent le répartiteur à vider, [utilisez la solution dans cette publication de blog de la communauté de Sensei Martin](https://mkalugin-cq.blogspot.in/2012/04/i-have-been-working-on-following.html).

### Comment vider les ressources DAM du cache Répartiteur ?

Vous pouvez utiliser la fonctionnalité de réplication de chaînes. Lorsque cette fonction est activée, l&#39;agent de purge du répartiteur envoie une demande flush lorsqu&#39;une réplication est reçue de l&#39;auteur.

Pour l’activer :

1. [Suivez les étapes ci-dessous](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) pour créer des agents de purge lors de la publication
1. Accédez à chaque configuration de l&#39;agent et, dans l&#39;onglet **Déclencheurs** , cochez la case **À réception** .

## Divers

Comment le répartiteur détermine-t-il si un document est à jour ?
Pour déterminer si un document est à jour, le répartiteur effectue les actions suivantes :

Il vérifie si le document est soumis à l’invalidation automatique. Si ce n’est pas le cas, le document est considéré comme étant à jour.
Si le document est configuré pour l’invalidation automatique, Dispatcher vérifie s’il est plus ancien ou plus récent que la dernière modification disponible. S’il est plus ancien, Dispatcher demande la version actuelle à l’instance AEM et remplace la version dans le cache.

### Comment le répartiteur renvoie-t-il des documents ?

You can define whether the Dispatcher caches a document by using the [Dispatcher configuration](dispatcher-configuration.md) file, `dispatcher.any`. Dispatcher vérifie la demande au niveau de la liste des documents pouvant être mis en cache. Si le document ne figure pas dans cette liste, Dispatcher demande le document à l’instance AEM.

`/rules` La propriété contrôle les documents mis en cache selon le chemin du document. Quelle `/rules` que soit la propriété, le répartiteur ne met jamais en cache un document dans les cas suivants :

* Si l&#39;URI de requête contient un point `(?)`d&#39;interrogation.
* Cela indique généralement une page dynamique, par exemple un résultat de recherche qui n’a pas besoin d’être mis en cache.
* L’extension de fichier est manquante.
* Le serveur web a besoin de l’extension pour déterminer le type de document (type MIME).
* L’en-tête d’authentification est défini (vous pouvez le configurer).
* Si l&#39;instance AEM répond aux en-têtes suivants :
   * no-cache
   * no-store
   * must-revalidate

Le répartiteur stocke les fichiers mis en cache sur le serveur Web comme s&#39;ils faisaient partie d&#39;un site Web statique. Si un utilisateur demande un document mis en cache, le répartiteur vérifie s&#39;il existe dans le système de fichiers du serveur Web. Si tel est le cas, le répartiteur renvoie les documents. Dans le cas contraire, le répartiteur demande le document à partir de l&#39;instance AEM.

>[!NOTE]
>
>Les méthodes GET ou HEAD (pour les en-têtes HTTP) sont mises en cache par le répartiteur. Pour plus d&#39;informations sur la mise en cache de l&#39;en-tête de réponse, reportez-vous à [la section Mise en cache des en-têtes](dispatcher-configuration.md#caching-http-response-headers) de réponse HTTP.

### Puis-je implémenter plusieurs répartiteurs dans une configuration ?

Oui. Dans ce cas, assurez-vous que les deux répartiteurs peuvent accéder directement au site Web AEM. Un Répartiteur ne peut pas traiter les requêtes provenant d&#39;un autre Répartiteur.

