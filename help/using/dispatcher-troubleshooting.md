---
title: Résolution des problèmes liés à Dispatcher
seo-title: Résolution des problèmes liés à AEM Dispatcher
description: Découvrez comment résoudre les problèmes liés à Dispatcher.
seo-description: Découvrez comment résoudre les problèmes liés à AEM Dispatcher.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: référence
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: ht
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Résolution des problèmes liés à Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Les versions du dispatcher sont indépendantes d’AEM. Cependant, la documentation du dispatcher est incluse dans la documentation d’AEM. Utilisez toujours la documentation du dispatcher incluse dans la documentation de la dernière version d’AEM.
>
>Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

>[!NOTE]
>
>Pour plus d’informations, voir également les sections [Base de connaissances de Dispatcher](https://docs.adobe.com/content/docs/fr/dispatcher/disp-tshoot.html), [Dépannage des problèmes de purge du répartiteur](hhttps://helpx.adobe.com/fr/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) et la [FAQ des problèmes fréquents de Dispatcher](dispatcher-faq.md).

## Vérification de la configuration de base {#check-the-basic-configuration}

Comme toujours, les premières étapes consistent à vérifier les principes de base :

* [Confirmation du fonctionnement de base](#ConfirmBasicOperation)
* Passez en revue tous les fichiers journaux du serveur web et de Dispatcher. Si nécessaire, augmentez le paramètre `loglevel` utilisé pour la [journalisation](#Logging) de Dispatcher.

* [Vérifiez votre configuration](#ConfiguringtheDispatcher) :

   * Disposez-vous de plusieurs instances de Dispatcher ?

      * Avez-vous déterminé quelle est instance de Dispatcher qui gère le site web ou la page en cours de test ?
   * Avez-vous implémenté des filtres ?

      * Ont-ils une incidence sur ce que vous êtes en train de tester ?


## Outils de diagnostic IIS  {#iis-diagnostic-tools}

IIS propose divers outils de trace, en fonction de la version :

* IIS 6 : vous pouvez télécharger et configurer des outils de diagnostic IIS.
* IIS 7 : la trace est parfaitement intégrée

Ces outils peuvent vous aider à surveiller l’activité.

## IIS et « Erreur 404 - Page introuvable » (404 Not Found)  {#iis-and-not-found}

Lorsque vous utilisez IIS, une erreur `404 Not Found` peut survenir dans plusieurs cas de figure. Si cela se produit, consultez les articles suivants de la base de connaissances.

* [IIS 6/7 : la méthode POST renvoie 404](https://helpx.adobe.com/fr/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6 : les requêtes aux URL qui contiennent le chemin de base `/bin` renvoient `404 Not Found`](https://helpx.adobe.com/fr/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Vous devez également vérifier que la racine du cache de Dispatcher et la racine du document IIS sont placées dans le même répertoire.

## Problèmes lors de la suppression des modèles de processus {#problems-deleting-workflow-models}

**Symptômes**

Problèmes lors de la tentative de suppression des modèles de processus lorsque vous accédez à une instance de création AEM à l’aide de Dispatcher.

**Étapes à reproduire :**

1. Connectez-vous à l’instance de création (vérifiez que les demandes sont redirigées vers Dispatcher).
1. Créez un processus ; par exemple, en définissant le titre processusASupprimer.
1. Vérifiez que le processus a été correctement créé.
1. Sélectionnez un processus puis cliquez dessus avec le bouton droit, puis cliquez sur **Supprimer**.

1. Cliquez sur **Oui** pour confirmer.
1. La boîte de message d’erreur suivante s’affiche :\
    « `ERROR 'Could not delete workflow model!!` ».

**Résolution**

Ajoutez les en-têtes suivants à la section `/clientheaders` du fichier `dispatcher.any` :

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## Interférence avec mod_dir (Apache) {#interference-with-mod-dir-apache}

Ceci décrit comment Dispatcher interagit avec `mod_dir` dans le serveur web Apache, car cela peut entraîner des effets variés, potentiellement inattendus :

### Apache 1.3 {#apache}

Dans Apache 1.3, `mod_dir` traite chaque demande pour laquelle l’URL est mappée à un répertoire du système de fichiers.

mod_dir :

* redirige la demande vers un fichier `index.html` existant ;
* génère une liste de répertoires.

Lorsque Dispatcher est activé, il traite ces demandes en s’enregistrant lui-même en tant que gestionnaire pour le type de contenu `httpd/unix-directory`.

### Apache 2.x {#apache-x}

Dans Apache 2.x, les choses sont différentes. Un module peut gérer différentes étapes de la demande, telles les corrections d’URL. `mod_dir` gère cette étape en redirigeant une demande (lorsque l’URL est mappée à un répertoire) vers l’URL comportant un signe `/` ajouté.

Dispatcher n’intercepte pas la correction `mod_dir` mais gère entièrement la demande vers l’URL redirigée (c’est-à-dire avec le signe `/` ajouté). Un problème peut se poser si le serveur distant (par exemple AEM) gère les requêtes envoyées à `/a_path` différemment des requêtes envoyées à `/a_path/` (lorsque `/a_path` est mappé sur un répertoire existant).

Si cela se produit, vous devez effectuer l’une des opérations suivantes :

* Désactivez `mod_dir` pour la sous-arborescence `Directory` ou `Location` gérée par Dispatcher.

* Utilisez `DirectorySlash Off` pour configurer `mod_dir` de manière à ne pas ajouter le signe `/`.
