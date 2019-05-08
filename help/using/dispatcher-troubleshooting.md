---
title: Résolution des problèmes liés à Dispatcher
seo-title: Dépannage des problèmes de répartiteur AEM
description: Découvrez comment résoudre les problèmes affectant le répartiteur.
seo-description: Découvrez comment résoudre les problèmes liés aux problèmes de répartiteur AEM.
uuid: 9 c 109 a 48-d 921-4 b 6 e -9626-1158 cebc 41 e 7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Utilisateur
products: SG_ EXPERIENCEMANAGER/RÉPARTICHER
topic-tags: répartiteur
content-type: référence
discoiquuid: a 612 e 745-f 1 e 6-43 de-b 25 a -9 adcaadab 5 cf
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Résolution des problèmes liés à Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Les versions du dispatcher sont indépendantes d’AEM. Cependant, la documentation du dispatcher est incluse dans la documentation d’AEM. Utilisez toujours la documentation du dispatcher incluse dans la documentation de la dernière version d’AEM.
>
>Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation du dispatcher qui est incluse dans la documentation d’une précédente version d’AEM.

>[!NOTE]
>
>Pour plus d&#39;informations, consultez également la [Base de connaissances du répartiteur](https://helpx.adobe.com/cq/kb/index/dispatcher.html), [Résolution des problèmes](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) [de connexion Répartiteur et Questions fréquentes](dispatcher-faq.md) sur Répartiteur.

## Vérification de la configuration de base {#check-the-basic-configuration}

Comme toujours, les premières étapes consistent à vérifier les principes de base :

* [Confirmation du fonctionnement de base](#ConfirmBasicOperation)
* Passez en revue tous les fichiers journaux du serveur web et de Dispatcher. Si nécessaire, augmentez le paramètre `loglevel` utilisé pour la [journalisation](#Logging) de Dispatcher.

* [Vérifiez votre configuration](#ConfiguringtheDispatcher) :

   * Avez-vous plusieurs répartiteurs ?

      * Avez-vous déterminé quelle instance de Dispatcher gère le site web/la page en cours de test ?
   * Avez-vous implémenté des filtres ?

      * Ont-ils une incidence sur ce que vous êtes en train de tester ?


## Outils de diagnostic IIS {#iis-diagnostic-tools}

IIS propose divers outils de trace, en fonction de la version :

* IIS 6 : vous pouvez télécharger et configurer des outils de diagnostic IIS.
* IIS 7 : la trace est parfaitement intégrée

Ces outils peuvent vous aider à surveiller l’activité.

## IIS et « Erreur 404 - Page introuvable » (404 Not Found) {#iis-and-not-found}

Lors de l&#39;utilisation d&#39;IIS, vous pouvez renvoyer `404 Not Found` des données dans divers scénarios. Si cela se produit, consultez les articles suivants de la base de connaissances.

* [IIS 6/7 : la méthode POST renvoie une erreur 404.](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6 : Demandes d&#39;URL contenant le retour de chemin `/bin` de base `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Vous devez également vérifier que la racine du cache de Dispatcher et la racine du document IIS sont placées dans le même répertoire.

## Problèmes lors de la suppression des modèles de processus {#problems-deleting-workflow-models}

**Symptômes**

Problèmes lors de la tentative de suppression des modèles de processus lorsque vous accédez à une instance de création AEM à l’aide de Dispatcher.

**Étapes à reproduire :**

1. Connectez-vous à l’instance de création (vérifiez que les demandes sont redirigées vers Dispatcher).
1. Créez un processus ; par exemple, en définissant le titre workflowToDelete.
1. Vérifiez que le processus a été correctement créé.
1. Sélectionnez un processus puis cliquez dessus avec le bouton droit de la souris, puis cliquez sur **Supprimer**.

1. Cliquez sur **Oui** pour confirmer.
1. Une boîte de message d&#39;erreur s&#39;affiche alors :\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Résolution**

Ajoutez les en-têtes suivants à la `/clientheaders` section de `dispatcher.any` votre fichier :

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

* rediriger la requête vers un `index.html` fichier existant
* génère une liste de répertoires.

Lorsque Dispatcher est activé, il traite ces demandes en s’enregistrant lui-même en tant que gestionnaire pour le type de contenu `httpd/unix-directory`.

### Apache 2.x {#apache-x}

Dans Apache 2.x, les choses sont différentes. Un module peut gérer différentes étapes de la demande, telles les corrections d’URL. `mod_dir` gère cette étape en redirigeant une requête (lorsque l&#39;URL est mappée à un répertoire) à l&#39;URL avec `/` un ajout.

Le répartiteur n&#39;intercepte pas la `mod_dir` correction, mais traite complètement la demande à l&#39;URL redirigée (c&#39;est-à-dire avec `/` ajout). Cela peut poser un problème si le serveur distant (par ex. AEM) gère les requêtes à `/a_path` des demandes différentes à des demandes `/a_path/` (lorsque `/a_path` les mappages sont associés à un répertoire existant).

Si cela se produit, vous devez effectuer l’une des opérations suivantes :

* désactiver `mod_dir` pour la `Directory``Location` sous-arborescence gérée par le répartiteur

* utiliser `DirectorySlash Off` pour configurer `mod_dir` ne pas ajouter `/`
