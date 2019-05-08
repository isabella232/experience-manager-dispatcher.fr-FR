---
title: Notes de mise à jour du répartiteur AEM
seo-title: Notes de mise à jour du répartiteur AEM
description: Notes de mise à jour spécifiques à Adobe Experience Manager Dispatcher
seo-description: Notes de mise à jour spécifiques à Adobe Experience Manager Dispatcher
uuid: ae 3 ccf 62-0514-4 c 03-a 3 b 9-71799 a 482 cbd
topic-tags: release-notes
content-type: référence
products: SG_ EXPERIENCEMANAGER/6.4
discoiquuid: ff 3 d 38 e 0-71 c 9-4 b 41-85 f 9-fa 896393 aac 5
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Notes de mise à jour du répartiteur AEM{#aem-dispatcher-release-notes}

## Informations sur la version {#release-information}

|  |
|--- |--- |
| Produits | Répartiteur Adobe Experience Manager (AEM) |
| Version | 4.3.2 |
| Type | Version mineure |
| Date | 31 janvier 2019 |
| URL de téléchargement | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilité | AEM 6.1 ou version ultérieure |

## Configuration requise et conditions préalables {#system-requirements-and-prerequisites}

Pour plus d&#39;informations [sur les exigences et les conditions préalables, consultez](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) la page Plateformes prises en charge.

Adobe recommande vivement d&#39;utiliser la dernière version du répartiteur AEM pour bénéficier des fonctionnalités les plus récentes, des correctifs les plus récents et des meilleures performances possibles.

## Instructions d’installation {#installation-instructions}

Pour obtenir des instructions détaillées, voir [Installation du répartiteur](dispatcher-install.md).

## Historique des versions {#release-history}

### Version 4.3.2 (2019-Janv -31) {#jan}

**Correctifs**:

* DISP -734 - Le répartiteur entraîne un blocage dans insert_ output_ filter s&#39;il n&#39;est pas défini comme gestionnaire.
* DISP -735 - Les res ne fonctionnent pas sur Alpine Linux
* DISP -740 - Le chargement du répartiteur dans macos Mojave est désactivé par défaut
* DISP -742 - Les demandes bloquées peuvent bloquer les informations sur les ressources protégées authentiques

**Améliorations**:

* DISP -746 - Les chaînes non marquées dans dispatcher. any doivent générer un avertissement.

**Nouvelle fonctionnalité**:

* DISP -747 - Fournir des informations de demande dans l&#39;environnement Apache

### Version 4.3.1 (2018-Oct -16) {#oct}

**Correctifs**:

* DISP -656 - Répartiteur diffuse un en-tête etag incorrect
* DISP -694 - Suppression des avertissements lorsque les connexions en vie restent obsolètes
* DISP -714 - La gestion des sessions basée sur un cookie ne fonctionne pas dans IIS
* DISP -715 - Indicateur sécurisé pour le cookie de rendu
* DISP -720 - Les fichiers temporaires non fermés peuvent entraîner une épuisement (trop de fichiers ouverts)
* DISP -721 - Le répartiteur interrompt le sondage () lorsque Apache redémarre normalement enfant
* DISP -722 - Les fichiers cache sont créés avec le mode octal 0600
* DISP -723 - Délai implicite 10 minutes (et nouvelle tentative) lorsque le délai d&#39;expiration du rendu est défini sur 0
* DISP -725 - Caractères de fin après la conversion silencieuse des chaînes en valeur sans nom
* DISP -726 - Consignez un avertissement lorsqu&#39;aucune batterie ne correspond réellement à l&#39;hôte entrant.
* DISP -727 - Le répartiteur vérifie la longueur du contenu de demande pour les fichiers cache vides.
* DISP -730 - 404 lorsque vous essayez d&#39;accéder au fichier d&#39;en-tête sur le répartiteur
* DISP -731 - Le répartiteur est vulnérable à l&#39;injection de journal
* DISP -732 - Le répartiteur doit supprimer le « / » consécutif dans l&#39;URL
* DISP -733 - Le répartiteur doit définir (calculate) un en-tête d&#39;âge

**Améliorations**:

* DISP -656 - Répartiteur diffuse un en-tête etag incorrect
* DISP -694 - Suppression des avertissements lorsque les connexions en vie restent obsolètes
* DISP -715 - Indicateur sécurisé pour le cookie de rendu
* DISP -722 - Les fichiers cache sont créés avec le mode octal 0600
* DISP -726 - Consignez un avertissement lorsqu&#39;aucune batterie ne correspond réellement à l&#39;hôte entrant.

### Version 4.3.0 (2018-Jun -13) {#jun}

**Correctifs**:

* DISP -682 - Niveau de journal numérique incorrectement appliqué
* DISP -685 - 32 bits Solaris SPARC 32 bits ont une référence non définie à__ divdi 3
* DISP -688 - Le répartiteur ne renvoie pas l&#39;en-tête X-Cache-Info dans la réponse 404.
* DISP -690 - L&#39;en-tête Last-Modified n&#39;est pas plafonné
* DISP -691 - Violation des accès dans w 3 wp. exe
* DISP -693 - Mise à jour des détails architecturaux des serveurs solaris sur la page de téléchargement du répartiteur
* DISP -695 - Problème lié au niveau dispatcherlog dans le module Dispatcher 4.2.3
* DISP -698 - Le répartiteur TTL doit prendre en charge les instructions s-maxage et privées.
* DISP -700 - Le module ne fonctionne pas correctement sur Alpine Linux
* DISP -704 - Les requêtes du navigateur contenant % 2 b sont envoyées à l&#39;Editeur non codées
* DISP -705 - Blocage du répartiteur suite à un double gratuit ou à une corruption (fasttop)
* DISP -706 - Lors d&#39;une invalidation, le répartiteur est suivi des liens symptomatiques de référence qui peuvent provoquer une boucle infinie.
* DISP -709 - Bloquer certaines extensions d&#39;URL Vanity
* DISP -710 - Compilations pour Linux non utilisables sur Cent OS 6

**Améliorations**:

* DISP -652 - Répartiteur diffuse un en-tête de date incorrect

## Ressources utiles {#helpful-resources}

* [Présentation du répartiteur AEM](dispatcher.md)

## Downloads (Téléchargements){#downloads}

### Apache 2.4 {#apache}

| Plate-forme | Architecture | Prise en charge SSL | Télécharger |
|---|---|---|---|
| AIX | Powerpc (32 bits) | Non | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | Powerpc (32 bits) | Oui | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | Powerpc (64 bits) | Non | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | Powerpc (64 bits) | Oui | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i 686 (32 bits) | Non | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i 686 (32 bits) | Oui | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 bits) | Non | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 bits) | Oui | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| Mac OS | x 86_ 64 (64 bits) | Non | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Non | [dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Oui | [dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Non | [dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Oui | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | Non | [dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | Oui | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | Non | [dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | Oui | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz) |

### IIS {#iis}

| Plate-forme | Architecture | Prise en charge SSL | Télécharger |
|---|---|---|---|
| Windows | x 86 (32 bits) | Non | [dispatcher-iis-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x 86 (32 bits) | Oui | [dispatcher-iis-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x 64 (64 bits) | Non | [dispatcher-iis-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x 64 (64 bits) | Oui | [dispatcher-iis-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
