---
title: Liste de contrôle de sécurité de Dispatcher
seo-title: Liste de contrôle de sécurité de Dispatcher
description: Liste de contrôle de sécurité qui doit être renseignée avant la production.
seo-description: Liste de contrôle de sécurité qui doit être renseignée avant la production.
uuid: 7 bfa 3202-03 f 6-48 e 9-8 d 2 e -2 a 40 e 137 ecbe
contentOwner: Utilisateur
products: SG_ EXPERIENCEMANAGER/RÉPARTICHER
topic-tags: répartiteur
content-type: référence
discoiquuid: fbfafa 55-c 029-4 ed 7-ab 3 e -1 bebfde 18248
jcr-lastmodifiedby: remove-legacypath -6-1
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Liste de contrôle de sécurité de Dispatcher{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Dispatcher comme système frontal offre une couche supplémentaire de sécurité à l’infrastructure Adobe Experience Manager. Adobe vous recommande vivement de suivre la liste de contrôle suivante avant de passer en production.

>[!CAUTION]
>
>Vous devez également suivre la liste de contrôle de sécurité de votre version d’AEM avant de passer en production. Reportez-vous à la [documentation d’Adobe Experience Manager](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) correspondante.

## Utilisation de la version la plus récente de Dispatcher {#use-the-latest-version-of-dispatcher}

Il est conseillé d’installer la dernière version disponible pour votre plate-forme. Vous devez mettre à niveau votre instance de Dispatcher afin d’utiliser la version la plus récente. Cela vous permet de tirer parti des améliorations du produit et de la sécurité. Voir [Installation de Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Vous pouvez vérifier la version actuelle de votre installation de Dispatcher en consultant le fichier journal de ce dernier.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Pour trouver le fichier journal, consultez la configuration de Dispatcher de `httpd.conf`.

## Limitation des clients qui peuvent vider le cache {#restrict-clients-that-can-flush-your-cache}

Adobe recommande de [limiter les clients qui peuvent vider la mémoire cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Activation du protocole HTTPS pour la sécurité des couches de transfert {#enable-https-for-transport-layer-security}

Adobe conseille d’activer la couche de transfert HTTPS sur les instances de création et de publication.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Limitation de l’accès {#restrict-access}

Lors de la configuration de Dispatcher, vous devez limiter l’accès externe autant que possible. Voir [Exemple de section /filter](dispatcher-configuration.md#main-pars_184_1_title) dans la documentation de Dispatcher.

## S’assurer que l’accès aux URL d’administration est refusé {#make-sure-access-to-administrative-urls-is-denied}

Assurez-vous d’utiliser des filtres pour bloquer l’accès externe aux URL d’administration, par exemple la console web.

Voir [Test de la sécurité de Dispatcher](dispatcher-configuration.md#testing-dispatcher-security) pour obtenir une liste des URL qui doivent être bloquées.

## Utilisation des listes blanches au lieu des listes noires {#use-whitelists-instead-of-blacklists}

Les listes blanches sont le meilleur moyen de fournir un contrôle d’accès puisque, par nature, elles partent du principe que toutes les demandes d’accès doivent être refusées, à moins qu’elles ne se trouvent explicitement sur la liste blanche. Ce modèle fournit un contrôle plus restrictif des nouvelles demandes qui peuvent ne pas avoir encore été testées ou prises en compte lors d’une étape spécifique de la configuration.

## Exécution de Dispatcher avec un utilisateur système dédié {#run-dispatcher-with-a-dedicated-system-user}

Lors de la configuration du répartiteur, assurez-vous que le serveur Web est exécuté par un utilisateur dédié avec les droits les moins appropriés. Il est conseillé d’octroyer uniquement l’accès en écriture au dossier du cache de Dispatcher.

En outre, les utilisateurs IIS doivent configurer leur site web comme suit :

1. Dans le paramètre de chemin d’accès physique du site web, sélectionnez **Se connecter comme utilisateur spécifique**.
1. Définissez l’utilisateur.

## Prévention des attaques par déni de service (DoS) {#prevent-denial-of-service-dos-attacks}

Une attaque par déni de service (DoS) est une tentative de rendre une ressource informatique indisponible à ses utilisateurs ciblés.

Au niveau de Dispatcher, il existe deux méthodes de configuration afin d’empêcher les attaques DoS : [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filtres))

* Utilisez le module mod_rewrite (par exemple [Apache 2.2](https://httpd.apache.org/docs/2.2/mod/mod_rewrite.html)) pour effectuer des validations d’URL (si les règles de modèle d’URL ne sont pas trop complexes).

* Empêchez Dispatcher de mettre en cache les URL dotées d’extensions parasites à l’aide de [filtres](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
    Par exemple, modifiez les règles de mise en cache afin de limiter la mise en cache des types MIME prévus, par exemple :

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   Un exemple de fichier de configuration peut être consulté pour [limiter l’accès externe](#restrict-access). Il comprend les limitations pour les types MIME.

Pour activer la fonctionnalité complète sur les instances de publication en toute sécurité, configurez les filtres pour empêcher l’accès aux nœuds suivants :

* `/etc/`
* `/libs/`

Ensuite, configurez les filtres pour accorder l’accès aux chemins d’accès des nœuds suivants :

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS et JSON)
* `/libs/cq/security/userinfo.json` (Informations utilisateur CQ)
* `/libs/granite/security/currentuser.json` (**les données ne doivent pas être mises en cache**)

* `/libs/cq/i18n/*` (Internalisation)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Configuration de Dispatcher pour empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery){#configure-dispatcher-to-prevent-csrf-attacks}

AEM fournit une [infrastructure](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) visant à empêcher les attaques par falsification de requête intersites. Pour utiliser correctement cette structure, vous devez mettre en liste blanche la prise en charge du jeton CSRF dans Dispatcher. Vous pouvez le faire en procédant comme suit :

1. Création d&#39;un filtre pour autoriser `/libs/granite/csrf/token.json` le chemin ;
1. Ajoutez l&#39; `CSRF-Token` en-tête à la `clientheaders` section de la configuration Répartiteur.

## Prévention du détournement de clic {#prevent-clickjacking}

Pour empêcher le détournement de clic, il est conseillé de configurer le serveur web afin que l’en-tête HTTP `X-FRAME-OPTIONS` soit défini sur `SAMEORIGIN`.

Pour plus [d’informations sur le détournement de clic, voir le site OWASP](https://www.owasp.org/index.php/Clickjacking).

## Test de pénétration {#perform-a-penetration-test}

Adobe recommande vivement d’effectuer un test de pénétration de l’infrastructure AEM avant la mise en production.

