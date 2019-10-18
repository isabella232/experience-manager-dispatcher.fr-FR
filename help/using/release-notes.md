---
title: Notes de mise à jour d’AEM Dispatcher
seo-title: Notes de mise à jour d’AEM Dispatcher
description: Notes de mise à jour spécifiques à Adobe Experience Manager Dispatcher
seo-description: Notes de mise à jour spécifiques à Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: référence
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: 328bc82673783b4a2df2d68481fa7eec88b74b01

---


# Notes de mise à jour d’AEM Dispatcher{#aem-dispatcher-release-notes}

## Informations sur la version {#release-information}

|  |  |
|--- |--- |
| Produits | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4.3.3 |
| Type | Version mineure |
| Date | 18 octobre 2019 |
| URL de téléchargement | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilité | AEM 6.1 ou version ultérieure |

## Configuration requise et conditions préalables {#system-requirements-and-prerequisites}

Consultez la page Plateformes [](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) prises en charge pour en savoir plus sur les conditions requises.

Adobe recommande vivement d’utiliser la dernière version d’AEM Dispatcher afin de profiter des dernières fonctionnalités, des correctifs de bogues les plus récents et des meilleures performances possible.

## Instructions d’installation {#installation-instructions}

Pour obtenir des instructions détaillées, voir [Installation de Dispatcher](dispatcher-install.md).

## Historique des versions {#release-history}

### Release 4.3.3 (2019-Oct-18) {#october}

**Correctifs** :

* DISP-739 - Répartiteur LogLevel : **level** ne fonctionne pas
* DISP-749 - Le répartiteur Linux alpin se bloque avec le niveau du journal de suivi

**Améliorations** :

* DISP-813 - Prise en charge dans le répartiteur pour openssl 1.1.x
* DISP-814 - Erreurs Apache 40x lors du vidage du cache
* DISP-818 - mod_expires ajoute des en-têtes de contrôle du cache pour le contenu non mis en cache
* DISP-821 - Ne pas stocker le contexte du journal dans le socket
* DISP-822 - Le répartiteur doit utiliser ppoll au lieu de sélectionner
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825 - Message spécial du journal lorsqu'il n'y a plus d'espace sur le disque
* DISP-826 - Prise en charge des URI de récupération avec une chaîne de requête

**Nouvelles fonctionnalités**:

* DISP-703 - Taux d'accès au cache spécifique à la batterie
* DISP-827 - Serveur local pour le test
* DISP-828 - Création d’une image de quai de test pour le répartiteur

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

## Downloads (Téléchargements){#downloads}

### Apache 2.4 {#apache}

| Plate-forme | Architecture | Prise en charge d’OpenSSL | Téléchargement |
|---|---|---|---|
| Linux | i686 (32 bits) | Aucun | [dispatcher-apache2.4-linux-i686-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.3.tar.gz) |
| Linux | i686 (32 bits) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz) |
| Linux | i686 (32 bits) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | Aucun | [dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz) |
| Mac OS | x86_64 (64 bits) | Aucun | [dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz) |

### IIS {#iis}

| Plate-forme | Architecture | Prise en charge d’OpenSSL | Télécharger |
|---|---|---|---|
| Windows | x86 (32 bits) | Aucun | [dispatcher-is-windows-x86-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.0 | [dispatcher-is-windows-x86-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.1 | [dispatcher-is-windows-x86-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip) |
| Windows | x64 (64 bits) | Aucun | [dispatcher-is-windows-x64-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.3.zip) |
| Windows | x64 (64 bits) | 1.0 | [dispatcher-is-windows-x64-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip) |
| Windows | x64 (64 bits) | 1.1 | [dispatcher-is-windows-x64-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip) |
