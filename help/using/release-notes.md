---
title: Notes de mise à jour d’AEM Dispatcher
seo-title: AEM Dispatcher Release Notes
description: Notes de mise à jour spécifiques à Adobe Experience Manager Dispatcher
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 5430d53571414cf3bf764bb8523c252bb77a3bf2
workflow-type: tm+mt
source-wordcount: '1037'
ht-degree: 51%

---

# Notes de mise à jour d’AEM Dispatcher{#aem-dispatcher-release-notes}

## Informations sur la version {#release-information}

|  |  |
|--- |--- |
| Produits | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4.3.5 |
| Type | Version mineure |
| Date  | 04 avril 2022 |
| URL de téléchargement | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilité | AEM 6.1 ou version ultérieure |

## Configuration requise et conditions préalables {#system-requirements-and-prerequisites}

Veuillez consulter la [Plateformes prises en charge](https://helpx.adobe.com/fr/experience-manager/6-4/sites/deploying/using/technical-requirements.html)pour plus d’informations sur les conditions requises et les conditions préalables.

Adobe recommande vivement d’utiliser la dernière version d’AEM Dispatcher afin de bénéficier des dernières fonctionnalités, des correctifs de bogues les plus récents et des meilleures performances possibles.

## Instructions d’installation {#installation-instructions}

Pour obtenir des instructions détaillées, voir [Installation de Dispatcher](dispatcher-install.md).

## Historique des versions {#release-history}

### Version 4.3.5 (2022-Apr-04) {#apr}

**Améliorations** :

* DISP-954 - Prise en charge de l’invalidation même si l’expiration n’est pas dépassée
* DISP-949 - Dispatcher renvoie 200 au lieu de 404 même si la demande du POST est bloquée par le filtre

### Version 4.3.4 (2021-Nov-29) {#nov}

**Correctifs** :

* DISP-833 - Les en-têtes X-Forwarded-Host peuvent contenir une liste de noms d’hôtes séparés par des virgules.
* DISP-835 - DispatcherUseForwardedHost ajoute un en-tête d’hôte s’il arrive en dernier

**Améliorations** :

* DISP-874 - Crée une configuration Dispatcher pour activer ou désactiver l’implémentation de DISP-818 par le biais d’un indicateur `DispatcherRestrictUncacheableContent`. La valeur par défaut est Off. Lorsqu’elle est activée, elle supprime tous les en-têtes de mise en cache définis par mod qui arrivent à expiration pour le contenu pouvant être mis en cache. Il s’agit d’un comportement différent de celui de la version 4.3.3 (où la valeur par défaut était Activé), mais identique à celui des versions antérieures à la version 4.3.3 (où la valeur par défaut était Désactivé). Maintien `DispatcherRestrictUncacheableContent`La valeur par défaut de &quot;Désactivé&quot; est l’approche recommandée, de sorte que le cache du navigateur dispose d’une plus grande flexibilité. Si, lors de la mise à niveau de la version 4.3.3 vers la version 4.3.4, vous souhaitez conserver le même comportement que dans la version 4.3.3, vous devez définir explicitement `DispatcherRestrictUncacheableContent` sur Activé.
* DISP-841 - Dispatcher ne respecte pas /serverStaleOnError pour le code de réponse 504
* DISP-874 - Créez une configuration Dispatcher pour activer ou désactiver l’implémentation de DISP-818.
* DISP-883 - Trace affichant la décomposition de requête d’URL dans Dispatcher
* DISP-944 - URL Vanity de préchargement

### Version 4.3.3 (18 octobre 2019) {#october}

**Correctifs** :

* DISP-739 - Dispatcher LogLevel : **level** ne fonctionne pas
* DISP-749 - Le dispatcher Alpine Linux se bloque avec le niveau de journal de trace

**Améliorations** :

* DISP-813 - Prise en charge dans Dispatcher pour openssl 1.1.x
* DISP-814 - Erreurs Apache 40x lors des vidages du cache
* DISP-818 - mod_expires ajoute des en-têtes de contrôle du cache pour le contenu pouvant être mis en cache
* DISP-821 - Ne pas stocker le contexte du journal dans le socket
* DISP-822 - Dispatcher doit utiliser le sondage au lieu de sélectionner
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825 - Enregistrer un message spécial lorsqu&#39;il n&#39;y a plus d&#39;espace sur le disque
* DISP-826 - Prise en charge des URI de récupération avec une chaîne de requête

**Nouvelles fonctionnalités**:

* DISP-703 - Ratio d’accès au cache spécifique à la ferme
* DISP-827 - Serveur local pour le test
* DISP-828 - Création d’une image Docker de test pour Dispatcher

### Version 4.3.2 (31 janvier 2019) {#jan}

**Correctifs** :

* DISP-734 - Dispatcher provoque un blocage dans insert_ output_ filter s’il n’est pas défini comme gestionnaire.
* DISP-735 - Les RE ne fonctionnent pas sur Alpine Linux
* DISP-740 - Le chargement de Dispatcher dans macOS Mojave est désactivé par défaut
* DISP-742 - Les requêtes bloquées peuvent provoquer un risque de fuite d’informations sur les ressources protégées

**Améliorations** :

* DISP-746 - Les chaînes non marquées dans dispatcher.any génèrent un avertissement.

**Nouvelle fonctionnalité** :

* DISP-747 - Fournit des informations de requête dans l’environnement Apache

### Version 4.3.1 (16 octobre 2018) {#oct}

**Correctifs** :

* DISP-656 - Dispatcher diffuse un en-tête ETag incorrect
* DISP-694 - Suppression des avertissements lorsque les connexions persistantes deviennent obsolètes
* DISP-714 - La gestion des sessions basées sur un cookie ne fonctionne pas dans IIS
* DISP-715 - Drapeau sécurisé pour le cookie de rendu
* DISP-720 - Les fichiers temporaires non fermés peuvent entraîner un épuisement (trop de fichiers ouverts)
* DISP-721 - Dispatcher interrompt le poll() lorsque Apache redémarre normalement le fichier enfant
* DISP-722 - Les fichiers cache sont créés avec le mode octal 0600
* DISP-723 - Délai implicite de 10 minutes (et nouvelle tentative) lorsque le délai d’expiration du rendu est défini sur 0
* DISP-725 - Caractères de fin après la conversion silencieuse des chaînes en valeur sans nom
* DISP-726 - Consignez un avertissement lorsqu’aucune ferme de serveurs ne correspond réellement à l’hôte entrant
* DISP-727 - Dispatcher vérifie la longueur du contenu de requête pour les fichiers cache vides
* DISP-730 - Erreur 404 générée lorsque vous essayez d’accéder au fichier d’en-tête sur Dispatcher
* DISP-731 - Dispatcher est vulnérable à l’injection de journal
* DISP-732 - Dispatcher doit supprimer le « / » consécutif dans l’URL
* DISP-733 - Dispatcher doit définir (calculer) un en-tête Age

**Améliorations** :

* DISP-656 - Dispatcher diffuse un en-tête ETag incorrect
* DISP-694 - Suppression des avertissements lorsque les connexions persistantes deviennent obsolètes
* DISP-715 - Drapeau sécurisé pour le cookie de rendu
* DISP-722 - Les fichiers cache sont créés avec le mode octal 0600
* DISP-726 - Consignez un avertissement lorsqu’aucune ferme de serveurs ne correspond réellement à l’hôte entrant

### Version 4.3.0 (13 juin 2018) {#jun}

**Correctifs** :

* DISP-682 - Niveau de journal numérique incorrectement appliqué
* DISP-685 - Les fichiers binaires Solaris SPARC 32 bits comportent une référence non définie à __divdi3
* DISP-688 - Dispatcher ne renvoie pas l’en-tête « X-Cache-Info » dans la réponse 404
* DISP-690 - L’en-tête Last-Modified ne peut pas être mis en cache
* DISP-691 - Violation des accès dans w3wp.exe
* DISP-693 - Mise à jour des détails architecturaux des serveurs solaris sur la page de téléchargement de Dispatcher
* DISP-695 - Problème lié au niveau Dispatcherlog dans le module Dispatcher 4.2.3
* DISP-698 - Dispatcher TTL doit prendre en charge les directives s-maxage et privées
* DISP-700 - Le module ne fonctionne pas correctement sur Alpine Linux
* DISP-704 - Les requêtes du navigateur contenant %2b sont envoyées non codées au serveur de publication
* DISP-705 - Blocage de Dispatcher suite à un free double ou à une corruption (fasttop)
* DISP-706 - Lors d’une invalidation, Dispatcher suit des liens symboliques de référence qui risquent de provoquer une boucle infinie
* DISP-709 - Blocage de certaines extensions d’URL de redirection vers des microsites
* DISP-710 - Compilations pour Linux, non utilisables sur Cent SE 6

**Améliorations** :

* DISP-652 - Dispatcher diffuse un en-tête de date incorrect

## Ressources utiles {#helpful-resources}

* [Présentation d’AEM Dispatcher](dispatcher.md)

## Téléchargements {#downloads}

### Apache 2.4 {#apache}

| Plate-forme | Architecture | Prise en charge OpenSSL | Télécharger |
|---|---|---|---|
| Linux | i686 (32 bits) | Aucune | [dispatcher-apache2.4-linux-i686-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.5.tar.gz) |
| Linux | i686 (32 bits) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz) |
| Linux | i686 (32 bits) | 1,1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz) |
| Linux | x86_64 (64 bits) | Aucune | [dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz) |
| Linux | x86_64 (64 bits) | 1,0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz) |
| Linux | x86_64 (64 bits) | 1,1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz) |
| Linux | aarch64 (64 bits) | Aucune | [dispatcher-apache2.4-linux-aarch64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.5.tar.gz) |
| Linux | aarch64 (64 bits) | 1,0 | [dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.5.tar.gz) |
| Linux | aarch64 (64 bits) | 1,1 | [dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.5.tar.gz) |
| Mac OS | arm64 (64 bits) | Aucune | [dispatcher-apache2.4-darwin-arm64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.5.tar.gz) |
| Mac OS | x86_64 (64 bits) | Aucune | [dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz) |

### IIS {#iis}

| Plate-forme | Architecture | Prise en charge OpenSSL | Télécharger |
|---|---|---|---|
| Windows | x86 (32 bits) | Aucune | [dispatcher-iis-windows-x86-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.5.zip) |
| Windows | x86 (32 bits) | 1,0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip) |
| Windows | x86 (32 bits) | 1,1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip) |
| Windows | x64 (64 bits) | Aucune | [dispatcher-iis-windows-x64-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.5.zip) |
| Windows | x64 (64 bits) | 1,0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip) |
| Windows | x64 (64 bits) | 1,1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip) |
