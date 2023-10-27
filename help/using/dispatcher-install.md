---
title: Installation de Dispatcher
seo-title: Installing AEM Dispatcher
description: Découvrez comment installer le module Dispatcher sur le serveur Microsoft Internet Information, le serveur web Apache et le serveur-iplanet web Sun Java.
seo-description: Learn how to install the AEM Dispatcher module on Microsoft Internet Information Server, Apache Web Server and Sun Java Web Server-iPlanet.
uuid: 2384b907-1042-4707-b02f-fba2125618cf
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
discoiquuid: f00ad751-6b95-4365-8500-e1e0108d9536
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
source-git-commit: 3bb9cb81ac98147bf12e9370d02002dd91ee374e
workflow-type: tm+mt
source-wordcount: '3726'
ht-degree: 62%

---

# Installation de Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Utilisez la page [Notes de mises à jour de Dispatcher](release-notes.md) pour obtenir le dernier fichier d’installation de Dispatcher pour votre système d’exploitation et votre serveur web. Les numéros de version de Dispatcher sont indépendants des numéros de version de Adobe Experience Manager et sont compatibles avec les versions Adobe Experience Manager 6.x, 5.x et Adobe CQ 5.x.

>[!NOTE]
>
>Notez que Adobe Experience Manager 6.5 nécessite Dispatcher version 4.3.2 ou ultérieure. Cela dit, les versions de Dispatcher sont indépendantes de AEM, par exemple, la version 4.3.2 de Dispatcher est également compatible avec Adobe Experience Manager 6.4.

La convention d’affectation de nom de fichier suivante est utilisée :

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Par exemple, le fichier `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` contient la version 4.3.1 de Dispatcher pour un serveur web Apache 2.4 qui fonctionne sous Linux i686, empaqueté à l’aide du format **tar**.

Le tableau suivant indique l’identifiant de serveur web utilisé dans les noms de fichier pour chaque serveur web :

| Serveur web | Kit d’installation |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;other parameters> |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**iis**-&lt;autres paramètres> |
| Serveur-iplanet web Sun Java | dispatcher-**ns**-&lt;autres paramètres> |

>[!CAUTION]
>
>Vous devez installer la dernière version de Dispatcher disponible pour votre plateforme. Chaque année, vous devez mettre à niveau votre instance de Dispatcher afin d’utiliser la dernière version pour tirer parti des améliorations du produit.

>[!NOTE]
>
>Les clients effectuant une mise à niveau spécifique de la version 4.3.3 vers la version 4.3.4 remarqueront un comportement différent dans la manière dont les en-têtes de mise en cache sont définis pour le contenu pouvant être mis en cache. Pour en savoir plus sur ce changement, voir [Notes de mise à jour](/help/using/release-notes.md#nov) page.

Chaque archive contient les fichiers suivants :

* les modules de Dispatcher ;
* un exemple de fichier de configuration
* fichier LISEZMOI contenant les instructions d’installation et les informations de dernière minute
* le fichier MODIFICATIONS qui répertorie les problèmes résolus dans les versions actuelle et antérieures.

>[!NOTE]
>
>Avant de commencer l’installation, consultez le fichier LISEZMOI pour vérifier toutes les modifications de dernière minute/les notes spécifiques aux plates-formes.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific web server installation procedures.</p>

 -->

## Microsoft Internet Information Server {#microsoft-internet-information-server}

Pour plus d&#39;informations sur l&#39;installation de ce serveur web, consultez les ressources suivantes :

* Microsoft de sa propre documentation sur le serveur d’informations Internet
* [&quot;Site officiel Microsoft IIS&quot;](https://www.iis.net/)

### Composants IIS requis {#required-iis-components}

Les versions 8.5 et 10 d’IIS nécessitent que les composants IIS suivants soient installés :

* Extensions ISAPI

Vous devez également ajouter le rôle Serveur Web (IIS) . Utilisez Server Manager pour ajouter le rôle et les composants.

## Microsoft IIS - Installation du module de Dispatcher  {#microsoft-iis-installing-the-dispatcher-module}

L’archive requise pour Microsoft Internet Information Server est la suivante :

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

Le fichier .zip contient les fichiers suivants :

| File | Description |
|--- |--- |
| `disp_iis.dll` | Fichier de bibliothèque de liens dynamiques de Dispatcher. |
| `disp_iis.ini` | Fichier de configuration d’IIS. Cet exemple peut être mis à jour en fonction de vos besoins. **Remarque** : Le fichier ini doit avoir la même racine de nom (name-root) que le fichier dll. |
| `dispatcher.any` | Exemple de fichier de configuration pour Dispatcher. |
| `author_dispatcher.any` | Exemple de fichier de configuration pour Dispatcher fonctionnant avec l’instance de création. |
| LISEZMOI | Fichier Lisezmoi contenant les instructions d’installation et les informations de dernière minute. **Remarque** : Consultez le fichier avant de commencer l’installation. |
| MODIFICATIONS | Fichier Modifications qui répertorie les problèmes résolus dans les versions actuelle et antérieures. |

Procédez comme suit pour copier les fichiers de Dispatcher vers l’emplacement correct.

1. Utilisez l’Explorateur Windows pour créer le répertoire `<IIS_INSTALLDIR>/Scripts` ; par exemple `C:\inetpub\Scripts`.

1. Extrayez les fichiers suivants du package de Dispatcher dans ce répertoire Scripts :

   * `disp_iis.dll`
   * `disp_iis.ini`
   * L’un des fichiers suivants selon si Dispatcher fonctionne avec une instance AEM de création ou de publication :
      * Instance de création: `author_dispatcher.any`
      * Instance de publication: `dispatcher.any`

## Microsoft IIS - Configuration du fichier INI de Dispatcher {#microsoft-iis-configure-the-dispatcher-ini-file}

Modifiez le fichier `disp_iis.ini` pour configurer l’installation de Dispatcher. Le format de base du fichier `.ini` se présente comme suit :

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

Le tableau suivant décrit chaque propriété.

| Paramètre | Description |
|--- |--- |
| configpath | Emplacement du fichier `dispatcher.any` dans le système de fichiers local (chemin d’accès absolu). |
| logfile | Emplacement du fichier `dispatcher.log`. Si cet emplacement n’est pas défini, les messages du journal se déplacent vers le journal des événements de Windows. |
| loglevel | Définit le niveau de journalisation utilisé pour générer les messages dans le journal des événements. Les valeurs suivantes peuvent être spécifiées : Niveau de journal du fichier journal : <br/>0 - messages d’erreur uniquement. <br/>1 - Erreurs et avertissements. <br/>2 - Erreurs, avertissements et messages d’information <br/>3 - Erreurs, avertissements, informations et messages de débogage. <br/>**Remarque :** Il est conseillé de définir le niveau de journal sur 3 pendant l’installation et le test, puis de repasser à 0 lors de l’exécution dans un environnement de production. |
| replaceauthorization | Indique le mode de traitement des en-têtes d’autorisation dans la requête HTTP. Les valeurs suivantes sont valides :<br/>0 - Les en-têtes d’autorisation ne sont pas modifiés. <br/>1 - Remplace n’importe quel en-tête appelé « Authorization » autre que l’en-tête « Basic » par son équivalent `Basic <IIS:LOGON\_USER>`.<br/> |
| servervariables | Définit le mode de traitement des variables de serveur.<br/>0 - Les variables du serveur IIS ne sont envoyées ni à Dispatcher ni à AEM. <br/>1 - Toutes les variables du serveur IIS (telles que `LOGON\_USER, QUERY\_STRING, ...`) sont envoyées à Dispatcher, ainsi que les en-têtes de requêtes (et également à l’instance AEM si elle n’est pas mise en cache).  <br/>Les variables de serveur incluent `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` et beaucoup d’autres. Voir la documentation d’IIS pour obtenir la liste complète des variables, avec des informations. |
| enable_chunked_transfer | Définit s’il faut activer (1) ou désactiver le transfert fragmenté (0) pour la réponse du client. La valeur par défaut est 0. |

Un exemple de configuration :

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configuration de Microsoft IIS {#configuring-microsoft-iis}

Configurez IIS pour intégrer le module ISAPI de Dispatcher. Dans IIS, vous utilisez le mappage d&#39;application générique.

### Configuration de l’accès anonyme - IIS 8.5 et 10 {#configuring-anonymous-access-iis-and}

L’agent de réplication de purge par défaut sur l’instance d’auteur est configuré de sorte qu’il n’envoie pas d’informations d’identification de sécurité avec les demandes de purge. Par conséquent, le site web que vous utilisez dans le cache de Dispatcher doit autoriser l’accès anonyme.

Si votre site web utilise une méthode d’authentification, l’agent de réplication de purge doit être configuré en conséquence.

1. Ouvrez le Gestionnaire IIS et sélectionnez le site web que vous utilisez comme cache de Dispatcher.
1. En mode Affichage des fonctionnalités , dans la section IIS, double-cliquez sur Authentification.
1. Si l’option Authentification anonyme n’est pas activée, sélectionnez Authentification anonyme et, dans la zone Actions, cliquez sur Activer.

### Intégration du module ISAPI de Dispatcher - IIS 8.5 et 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Procédez comme suit pour ajouter le module ISAPI de Dispatcher à IIS.

1. Ouvrez le Gestionnaire IIS.
1. Sélectionnez le site web que vous utilisez comme cache de Dispatcher.
1. En mode Affichage des fonctionnalités, dans la section IIS, double-cliquez sur Mappages de gestionnaire.
1. Dans le panneau Actions de la page Correspondances de gestionnaires, cliquez sur Ajouter un mappage de script générique, ajoutez les valeurs de propriété suivantes, puis cliquez sur OK :

   * Chemin de la requête : &#42;
   * Exécutable : chemin d’accès absolu au fichier disp_iis.dll, par exemple `C:\inetpub\Scripts\disp_iis.dll`.
   * Nom : nom descriptif pour le mappage du gestionnaire, par exemple `Dispatcher`.

1. Dans la boîte de dialogue qui s’affiche, pour ajouter la bibliothèque disp_iis.dll à la liste Restrictions ISAPI et CGI, cliquez sur Oui.

   Pour IIS 7.0 et 7.5, la configuration est terminée. Passez aux étapes restantes si vous configurez IIS 8.0.

1. (IIS 8.0) Dans la liste des mappages de gestionnaires, sélectionnez celui que vous venez de créer, puis, dans la zone Actions, cliquez sur Modifier.
1. (IIS 8.0) Dans la boîte de dialogue Modifier le mappage de scripts, cliquez sur le bouton Restrictions des demandes.
1. (IIS 8.0) Pour s’assurer que le gestionnaire est utilisé pour les fichiers et dossiers qui ne sont pas encore mis en cache, annulez la sélection de l’option Appeler le gestionnaire seulement si une demande est mappée à, puis cliquez sur OK.
1. (IIS 8.0) Dans la boîte de dialogue Modifier le mappage de scripts, cliquez sur OK.

### Configuration de l’accès au cache - IIS 8.5 et 10 {#configuring-access-to-the-cache-iis-and}

Accordez à l’utilisateur par défaut du pool d’applications l’accès en écriture au dossier utilisé comme cache de Dispatcher.

1. Cliquez avec le bouton droit sur le dossier racine du site web que vous utilisez comme cache de Dispatcher, puis cliquez sur Propriétés, par exemple `C:\inetpub\wwwroot`.
1. Sous l’onglet Sécurité, cliquez sur Modifier puis, dans la boîte de dialogue Autorisations, cliquez sur Ajouter. Une boîte de dialogue s’ouvre pour sélectionner les comptes d’utilisateur. Cliquez sur le bouton Emplacements, sélectionnez le nom de votre ordinateur, puis cliquez sur OK.

   Laissez cette boîte de dialogue ouverte pendant que vous effectuez l’étape suivante.

1. Dans le Gestionnaire IIS, choisissez le site IIS que vous utilisez pour le cache de Dispatcher et, dans la partie droite de la fenêtre, cliquez sur Paramètres avancés.
1. Sélectionnez la valeur de la propriété Pool d’applications et copiez-la dans le Presse-papiers.
1. Revenez à la boîte de dialogue ouverte. Dans la zone Entrez les noms des objets à sélectionner, saisissez `IIS AppPool\`, puis collez le contenu du Presse-papiers. La valeur doit ressembler à l’exemple suivant :

   `IIS AppPool\DefaultAppPool`

1. Cliquez sur le bouton Vérifier les noms . Lorsque Windows résout le compte utilisateur, cliquez sur OK.
1. Dans la boîte de dialogue Autorisations du dossier de Dispatcher, sélectionnez le compte que vous venez d’ajouter. Activez toutes les autorisations pour le compte, à **l’exception du contrôle total**, puis cliquez sur OK. Cliquez sur OK pour fermer la boîte de dialogue Propriétés du dossier.

### Enregistrement du type Mime JSON - IIS 8.5 et 10 {#registering-the-json-mime-type-iis-and}

Utilisez la procédure suivante pour enregistrer le type MIME JSON, lorsque vous souhaitez que Dispatcher autorise les appels JSON.

1. Dans le Gestionnaire des services Internet, sélectionnez votre site web et, à l’aide de la vue Fonctionnalités, double-cliquez sur Types MIME.
1. Si l’extension JSON ne figure pas dans la liste, dans le panneau Actions, cliquez sur Ajouter, saisissez les valeurs de propriété suivantes, puis cliquez sur OK :

   * Extension de nom de fichier : `.json`
   * MIME Type: `application/json`

### Suppression du segment masqué bin - IIS 8.5 et 10 {#removing-the-bin-hidden-segment-iis-and}

Suivez la procédure ci-dessous pour supprimer le segment masqué `bin`. Les sites web qui ne sont pas nouveaux peuvent contenir ce segment masqué.

1. Dans le Gestionnaire des services Internet, sélectionnez votre site Web et, en mode d’affichage des fonctionnalités, double-cliquez sur Filtrage des demandes.
1. Sélectionnez le segment `bin`, cliquez sur Supprimer, puis sur Oui dans la boîte de dialogue de confirmation.

### Consignation des messages IIS dans un fichier - IIS 8.5 et 10 {#logging-iis-messages-to-a-file-iis-and}

Utilisez la procédure suivante pour écrire des messages de journal de Dispatcher dans un fichier journal plutôt que dans le journal des événements Windows. Vous devez configurer Dispatcher pour utiliser le fichier journal et fournir à IIS un accès en écriture au fichier.

1. Utilisez l’Explorateur Windows pour créer un dossier nommé `dispatcher` sous le dossier des journaux de l’installation d’IIS. Le chemin d’accès à ce dossier pour une installation standard est `C:\inetpub\logs\dispatcher`.

1. Cliquez avec le bouton droit sur le dossier de Dispatcher, puis cliquez sur Propriétés.
1. Sous l’onglet Sécurité, cliquez sur Modifier puis, dans la boîte de dialogue Autorisations, cliquez sur Ajouter. Une boîte de dialogue s’ouvre pour sélectionner les comptes d’utilisateur. Cliquez sur le bouton Emplacements, sélectionnez le nom de votre ordinateur, puis cliquez sur OK.

   Laissez cette boîte de dialogue ouverte pendant que vous effectuez l’étape suivante.

1. Dans le Gestionnaire IIS, choisissez le site IIS que vous utilisez pour le cache de Dispatcher et, dans la partie droite de la fenêtre, cliquez sur Paramètres avancés.
1. Sélectionnez la valeur de la propriété Pool d’applications et copiez-la dans le Presse-papiers.
1. Revenez à la boîte de dialogue ouverte. Dans la zone Entrez les noms des objets à sélectionner, saisissez `IIS AppPool\`, puis collez le contenu du Presse-papiers. La valeur doit ressembler à l’exemple suivant :

   `IIS AppPool\DefaultAppPool`

1. Cliquez sur le bouton Vérifier les noms . Lorsque Windows résout le compte utilisateur, cliquez sur OK.
1. Dans la boîte de dialogue Autorisations du dossier de Dispatcher, sélectionnez le compte que vous venez d’ajouter et activez toutes les autorisations pour le compte. **sauf pour le contrôle total,** et cliquez sur OK. Cliquez sur OK pour fermer la boîte de dialogue Propriétés du dossier.
1. Utilisez un éditeur de texte pour ouvrir le fichier `disp_iis.ini`
1. Ajoutez une ligne de texte similaire à l’exemple suivant pour configurer l’emplacement du fichier journal, puis enregistrez le fichier :

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Étapes suivantes {#next-steps}

Pour pouvoir commencer à utiliser Dispatcher, vous devez connaître :

* [La configuration de Dispatcher](dispatcher-configuration.md)
* [La configuration d’AEM](page-invalidate.md) pour qu’il fonctionne avec Dispatcher.

## Serveur web Apache {#apache-web-server}

>[!CAUTION]
>
>Instructions d’installation sous les deux **Windows** et **Unix** sont couverts ici. Soyez prudent lorsque vous effectuez les étapes.

### Installation du serveur web Apache  {#installing-apache-web-server}

Pour plus d’informations sur l’installation d’un serveur web Apache, lisez le manuel d’installation - [en ligne](https://httpd.apache.org/) ou dans la distribution.

>[!CAUTION]
>
>Si vous créez un fichier binaire Apache en compilant les fichiers source, assurez-vous que vous avez activé la **prise en charge dynamique des modules**. Pour ce faire, utilisez l’une des méthodes suivantes : **—enable-shared** options. Au minimum, incluez le module `mod_so`.
>
>Vous trouverez plus d’informations dans le manuel d’installation du serveur web Apache.

Voir aussi Serveur Apache HTTP [Conseils de sécurité](https://httpd.apache.org/docs/2.4/misc/security_tips.html) et [Rapports de sécurité](https://httpd.apache.org/security_report.html).

### Serveur web Apache - Ajout du module de Dispatcher {#apache-web-server-add-the-dispatcher-module}

Dispatcher se présente comme suit :

* **Windows**: une bibliothèque de liens dynamiques (DLL)
* **Unix**: un objet partagé dynamique (DSO)

Les fichiers d’archive d’installation contiennent les fichiers suivants, selon que vous avez sélectionné Windows ou Unix :

| File | Description |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows : fichier de bibliothèque de liens dynamiques de Dispatcher. |
| dispatcher-apache&lt;x.y>-&lt;rel-nr>.so | Unix : fichier de bibliothèque d’objets partagés de Dispatcher. |
| mod_dispatcher.so | Unix : exemple de lien. |
| http.conf.disp&lt;x> | Exemple de fichier de configuration pour le serveur Apache. |
| dispatcher.any | Exemple de fichier de configuration pour Dispatcher. |
| LISEZMOI | Fichier Lisezmoi contenant les instructions d’installation et les informations de dernière minute. **Remarque** : Consultez le fichier avant de commencer l’installation. |
| MODIFICATIONS | Fichier Modifications qui répertorie les problèmes résolus dans les versions actuelle et antérieures. |

Procédez comme suit pour ajouter Dispatcher à votre serveur web Apache :

1. Placez le fichier Dispatcher dans le répertoire approprié du module Apache :

   * **Windows** : placez `disp_apache<x.y>.dll` `<APACHE_ROOT>/modules`
   * **Unix** : localisez le répertoire `<APACHE_ROOT>/libexec` ou `<APACHE_ROOT>/modules` selon votre installation.\
     Copiez `dispatcher-apache<options>.so` dans ce répertoire.\
     Pour simplifier la maintenance sur le long terme, vous pouvez également créer un lien symbolique nommé `mod_dispatcher.so` dans Dispatcher :\
     `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copiez le fichier dispatcher.any dans le répertoire `<APACHE_ROOT>/conf`.

   **Remarque :** Vous pouvez placer ce fichier dans un autre emplacement tant que la propriété DispatcherLog du module de Dispatcher est configurée en conséquence. (Voir Entrées de configuration spécifiques à Dispatcher ci-dessous.)

### Serveur web Apache - Configuration des propriétés SELinux  {#apache-web-server-configure-selinux-properties}

Si vous exécutez Dispatcher sur RedHat Linux Kernel 2.6 avec des propriétés SELinux activées, vous pouvez rencontrer des messages d’erreur de ce type dans le fichier journal de Dispatcher.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Cela est probablement dû à une sécurité SELinux activée. Ensuite, vous devez effectuer les tâches suivantes :

* Configurez le contexte SELinux du fichier du module de Dispatcher.
* Activez les scripts et modules HTTPD pour établir des connexions réseau.
* Configurez le contexte SELinux du docroot, dans lequel les fichiers mis en cache sont stockés.

Entrez les commandes suivantes dans une fenêtre de terminal en remplaçant `[path to the dispatcher.so file]` par le chemin d’accès au module de Dispatcher que vous avez installé sur le serveur web Apache, et *`path to the docroot`* par le chemin d’accès à l’emplacement du docroot (par exemple `/opt/cq/cache`) :

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_rw_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_rw_content_t "[path to the docroot](/.*)?"
```

### Serveur web Apache - Configuration du serveur web Apache pour Dispatcher. {#apache-web-server-configure-apache-web-server-for-dispatcher}

Le serveur web Apache doit être configuré à l’aide de `httpd.conf`. Le kit d’installation de Dispatcher contient un exemple de fichier de configuration nommé `httpd.conf.disp<x>`.

Les étapes suivantes sont obligatoires :

1. Accéder à `<APACHE_ROOT>/conf`.
1. Ouvrez `httpd.conf`pour modification.
1. Les entrées de configuration suivantes doivent être ajoutées, dans l’ordre indiqué :

   * **LoadModule** pour charger le module au démarrage.
   * Des entrées de configuration spécifiques à Dispatcher, notamment **DispatcherConfig, DispatcherLog** et **DispatcherLogLevel**.
   * **SetHandler** pour activer Dispatcher. **LoadModule**.
   * **ModMimeUsePathInfo** pour configurer le comportement de **mod_mime**.

1. (Facultatif) Il est recommandé de modifier le propriétaire du répertoire htdocs :

   * Le serveur Apache démarre en tant que root, bien que les processus enfants démarrent en tant que démon (à des fins de sécurité). DocumentRoot (`<APACHE_ROOT>/htdocs`) doit appartenir au démon de l’utilisateur :

     ```xml
     cd <APACHE_ROOT>  
     chown -R daemon:daemon htdocs
     ```

**LoadModule**

Le tableau suivant répertorie des exemples qui peuvent être utilisés. Les entrées exactes correspondent à votre serveur web Apache spécifique :

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (en supposant un lien symbolique) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>Le premier paramètre de chaque instruction doit être écrit exactement comme dans les exemples ci-dessus.
>
>Pour plus d’informations sur cette commande, voir les exemples de fichiers de configuration fournis et la documentation du serveur web Apache .

**Entrées de configuration spécifiques à Dispatcher**

Les entrées de configuration spécifiques à Dispatcher sont placées après l’entrée LoadModule . Le tableau suivant présente un exemple de configuration qui s’applique à la fois à Unix et Windows :

**Windows et Unix**

```
...
<IfModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

>[!NOTE]
>
>Les clients effectuant une mise à niveau spécifique de la version 4.3.3 vers la version 4.3.4 remarqueront un comportement différent dans la manière dont les en-têtes de mise en cache sont définis pour le contenu pouvant être mis en cache. Pour en savoir plus sur ce changement, voir [Notes de mise à jour](/help/using/release-notes.md#nov) page.

Vous trouverez les paramètres de configuration dans le tableau suivant :

| Paramètre | Description |
|--- |--- |
| DispatcherConfig | Emplacement et nom du fichier de configuration de Dispatcher. <br/>Lorsque cette propriété est située dans la configuration du serveur principal, tous les hôtes virtuels héritent de la valeur de la propriété. Toutefois, les hôtes virtuels peuvent inclure une propriété DispatcherConfig pour remplacer la configuration du serveur principal. |
| DispatcherLog | Emplacement et nom du fichier journal. |
| DispatcherLogLevel | Niveau de journal du fichier journal :<br/> 0 - Erreurs<br/> 1 - Avertissements<br/> 2 - Infos<br/> 3 - Déboguer <br/>**Remarque** : Il est conseillé de définir le niveau de journal sur 3 pendant l’installation et le test, puis sur 0 lors de l’exécution dans un environnement de production. |
| DispatcherNoServerHeader | *Ce paramètre est obsolète et n’a plus aucun effet.*<br/><br/> Définit l’en-tête du serveur à utiliser : <br/><ul><li>Non défini ou 0 - L’en-tête du serveur HTTP contient la version AEM. </li><li>1 - L’en-tête du serveur Apache est utilisé.</li></ul> |
| DispatcherDeclineRoot | Définit le refus ou l’acceptation des demandes à la racine « / » :<br/>**0** - accepte des requêtes à / <br/>**1** - les requêtes à / ne sont pas gérées par Dispatcher ; utilisez mod_alias pour le mappage correct. |
| DispatcherUseProcessedURL | Définit s’il faut utiliser des URL prétraitées pour tout traitement supplémentaire par Dispatcher :<br/>**0** - utilise l’URL d’origine transmise au serveur web. <br/>**1** - Dispatcher utilise l’URL déjà traitée par les gestionnaires qui précèdent Dispatcher (c’est-à-dire `mod_rewrite`) à la place de l’URL d’origine transmise au serveur. Par exemple, l’URL d’origine ou l’URL traitée est mise en correspondance avec des filtres de Dispatcher. L’URL est également utilisée comme base de la structure de fichiers du cache.   Pour plus d’informations sur mod_rewrite, voir la documentation sur le site web d’Apache, par exemple Apache 2.4. Lors de l’utilisation de mod_rewrite, il est conseillé d’utiliser l’indicateur &#39;passthrough | PT&#39; (transmis au gestionnaire suivant) pour forcer le moteur de réécriture à définir le champ uri de la structure request_rec interne sur la valeur du champ de nom de fichier. |
| DispatcherPassError | Définit comment prendre en charge les codes d’erreur pour le traitement de ErrorDocument :<br/>**0** - Dispatcher met en file d’attente toutes les réponses d’erreur envoyées au client. <br/>**1** - Dispatcher ne met pas en file d’attente une réponse d’erreur envoyée au client (dont le code d’état est supérieur ou égal à 400), mais transfère le code d’état à Apache qui permet, par exemple, à une directive ErrorDocument de traiter ce code d’état. <br/>**Plage de codes** - Indiquez une plage de codes d’erreur pour lesquels la réponse est transmise à Apache. D’autres codes d’erreur sont transmis au client. Par exemple, la configuration suivante transmet les réponses au client pour l’erreur 412 et toutes les autres erreurs sont transmises à Apache : DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Indique le délai d’expiration de la persistance, en secondes. Depuis la version 4.2.0 de Dispatcher, la valeur de persistance par défaut est 60. La valeur 0 désactive la persistance. |
| DispatcherNoCanonURL | Si vous définissez ce paramètre sur On, l’URL brute sera transmise au serveur principal au lieu de celle qui est canonisée et remplacera les paramètres de DispatcherUseProcessedURL. La valeur par défaut est Off. <br/>**Remarque** : Les règles de filtrage de la configuration Dispatcher sont toujours évaluées par rapport à l’URL expurgée et non à l’URL brute. |




>[!NOTE]
>
>Les entrées de chemin d’accès sont relatives au répertoire racine du serveur web Apache.

>[!NOTE]
>
>Les paramètres par défaut de l’en-tête du serveur sont les suivants :  
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`
>
>Indique la version d’AEM (à des fins statistiques). Si vous souhaitez désactiver ces informations dans l’en-tête que vous pouvez définir : 
>
>`ServerTokens Prod`
>
>Voir [Documentation Apache sur la directive ServerTokens (par exemple, pour Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) pour plus d’informations.

**SetHandler**

Après ces entrées, vous devez ajouter une instruction **SetHandler** au contexte de la configuration (`<Directory>`, `<Location>`) pour que Dispatcher traite les requêtes entrantes. L’exemple suivant configure Dispatcher pour traiter les demandes du site web complet :

**Windows et Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

L’exemple suivant configure Dispatcher pour traiter les demandes d’un domaine virtuel :

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**Unix**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
>
>Le paramètre de l’instruction **SetHandler** doit être écrit *exactement comme dans les exemples ci-dessus*, car il s’agit du nom du gestionnaire défini dans le module.
>
>Pour plus d’informations sur cette commande, voir les exemples de fichiers de configuration fournis et la documentation du serveur web Apache .

**ModMimeUsePathInfo**

Après l’instruction **SetHandler**, vous devez également ajouter la définition **ModMimeUsePathInfo**.

>[!NOTE]
>
>Le paramètre `ModMimeUsePathInfo` ne doit être utilisé et configuré que si vous utilisez la version 4.0.9 de Dispatcher ou une version ultérieure.
>
>(Notez que Dispatcher version 4.0.9 a été publié en 2011. Si vous utilisez une ancienne version, la mise à niveau vers une version récente de Dispatcher serait appropriée).

Le paramètre **ModMimeUsePathInfo** doit être défini sur `On` pour toutes les configurations d’Apache :

`ModMimeUsePathInfo On`

Le module mod_mime (voir par exemple : [Module Apache mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) est utilisé pour affecter des métadonnées de contenu au contenu sélectionné pour une réponse HTTP. La configuration par défaut signifie que, lorsque mod_mime détermine le type de contenu, seule la partie de l’URL qui correspond à un fichier ou à un répertoire est prise en considération.

Lorsque le paramètre `On` est défini sur `ModMimeUsePathInfo`, il indique que `mod_mime` doit déterminer le type de contenu en fonction de l’URL *complète*. Cela signifie que des métadonnées sont appliquées aux ressources virtuelles selon leur extension.

L’exemple suivant active **ModMimeUsePathInfo** :

**Windows et Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Activation de la prise en charge HTTPS (Unix et Linux) {#enable-support-for-https-unix-and-linux}

Dispatcher utilise OpenSSL pour implémenter la communication sécurisée sur HTTP. Démarrage à partir de la version de Dispatcher **4.2.0**, OpenSSL 1.0.0 et OpenSSL 1.0.1 sont pris en charge. Dispatcher utilise OpenSSL 1.0.0 par défaut. Pour utiliser OpenSSL 1.0.1, créez des liens symboliques en suivant la procédure suivante, afin que Dispatcher utilise les bibliothèques OpenSSL installées.

1. Ouvrez un terminal et remplacez le répertoire actuel par le répertoire dans lequel les bibliothèques OpenSSL sont installées, par exemple :

   ```shell
   cd /usr/lib64
   ```

1. Pour créer les liens symboliques, saisissez les commandes suivantes :

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>Si vous utilisez une version personnalisée d’Apache, assurez-vous qu’Apache et Dispatcher sont compilés à l’aide de la même version de [OpenSSL](https://www.openssl.org/source/).

### Étapes suivantes {#next-steps-1}

Pour pouvoir commencer à utiliser Dispatcher, vous devez connaître :

* [La configuration de Dispatcher](dispatcher-configuration.md)
* [La configuration d’AEM](page-invalidate.md) pour qu’il fonctionne avec Dispatcher.

## Serveur web Sun Java System/iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>Les instructions relatives aux environnements Windows et Unix sont présentées ici.
>
>Soyez prudent lorsque vous sélectionnez ce qui doit être exécuté.

### Serveur web Sun Java System/iPlanet - Installation du serveur web  {#sun-java-system-web-server-iplanet-installing-your-web-server}

Pour obtenir des informations complètes sur l’installation de ces serveurs web, consultez leur documentation correspondante :

* Serveur web Sun Java System
* Serveur web iPlanet

### Serveur web Sun Java System/iPlanet - Ajout du module de Dispatcher  {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Dispatcher se présente comme suit :

* **Windows**: une bibliothèque de liens dynamiques (DLL)
* **Unix**: un objet partagé dynamique (DSO)

Les fichiers d’archive d’installation contiennent les fichiers suivants, selon que vous avez sélectionné Windows ou Unix :

| File | Description |
|---|---|
| `disp_ns.dll` | Windows : fichier de bibliothèque de liens dynamiques de Dispatcher. |
| `dispatcher.so` | Unix : fichier de bibliothèque d’objets partagés de Dispatcher. |
| `dispatcher.so` | Unix : exemple de lien. |
| `obj.conf.disp` | Exemple de fichier de configuration pour le serveur web iPlanet/Sun Java System. |
| `dispatcher.any` | Exemple de fichier de configuration pour Dispatcher. |
| LISEZMOI | Fichier Lisezmoi contenant les instructions d’installation et les informations de dernière minute. Remarque : Consultez le fichier avant de commencer l’installation. |
| MODIFICATIONS | Fichier Modifications qui répertorie les problèmes résolus dans les versions actuelle et antérieures. |

Procédez comme suit pour ajouter Dispatcher à votre serveur web :

1. Placez le fichier de Dispatcher dans le répertoire `plugin` du serveur web :

### Serveur web Sun Java System/iPlanet - Configuration pour Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Le serveur web doit être configuré à l’aide de `obj.conf`. Le kit d’installation de Dispatcher contient un exemple de fichier de configuration nommé `obj.conf.disp`.

1. Accéder à `<WEBSERVER_ROOT>/config`.
1. Ouvrez `obj.conf`pour modification.
1. Copiez la ligne qui va\
   `Service fn="dispService"`\
   de `obj.conf.disp` à la section d’initialisation de `obj.conf`.

1. Enregistrez les modifications.
1. Ouvrez `magnus.conf` pour modification.
1. Copiez les deux lignes qui vont de\
   `Init funcs="dispService, dispInit"`\
   et\
   `Init fn="dispInit"`\
   de `obj.conf.disp` à la section d’initialisation de `magnus.conf`.

1. Enregistrez les modifications.

>[!NOTE]
>
>Les configurations suivantes doivent toutes se trouver sur une seule ligne et `$(SERVER_ROOT)` et `$(PRODUCT_SUBDIR)` doivent être remplacés par leurs valeurs respectives.

**Init**

Le tableau suivant répertorie des exemples qui peuvent être utilisés. Les entrées exactes dépendent de votre serveur web spécifique :

**Windows et Unix**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

où :

| Paramètre | Description |
|--- |--- |
| config | Emplacement et nom du fichier de configuration `dispatcher.any.` |
| logfile | Emplacement et nom du fichier journal. |
| loglevel | Niveau de journal lors de l’écriture de messages dans le fichier journal :<br/>**0** erreur <br/>**1** avertissement <br/>**2** infos <br/>**3** messages de débogage <br/>**Remarque** : Il est conseillé de définir le niveau de journal sur 3 pendant l’installation et le test, puis sur 0 lors de l’exécution dans un environnement de production. |
| keepalivetimeout | Indique le délai d’expiration de la persistance, en secondes. Depuis la version 4.2.0 de Dispatcher, la valeur de persistance par défaut est 60. La valeur 0 désactive la persistance. |

Selon vos besoins, vous pouvez définir Dispatcher comme service pour vos objets. Pour configurer Dispatcher pour l’ensemble de votre site web, modifiez l’objet par défaut :


**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**Unix**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Étapes suivantes {#next-steps-2}

Pour pouvoir commencer à utiliser Dispatcher, vous devez connaître :

* [La configuration de Dispatcher](dispatcher-configuration.md)
* [La configuration d’AEM](page-invalidate.md) pour qu’il fonctionne avec Dispatcher.
