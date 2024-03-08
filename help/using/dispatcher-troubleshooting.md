---
title: Résoudre les problèmes liés à Dispatcher
seo-title: Troubleshooting AEM Dispatcher Problems
description: Découvrez comment résoudre les problèmes liés à Dispatcher.
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: ht
source-wordcount: '538'
ht-degree: 100%

---

# Résoudre les problèmes liés à Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Les versions du Dispatcher sont indépendantes d’AEM, mais la documentation du Dispatcher est incluse dans la documentation d’AEM. Utilisez toujours la documentation du Dispatcher incluse dans la documentation pour la dernière version d’AEM.
>
>Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

>[!NOTE]
>
>Pour plus d’informations, voir également les sections [Base de connaissances de Dispatcher](https://helpx.adobe.com/fr/experience-manager/kb/index/dispatcher.html), [Dépannage des problèmes de purge de Dispatcher](https://experienceleague.adobe.com/search.html?lang=fr#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) et la [FAQ des problèmes fréquents de Dispatcher](dispatcher-faq.md).

## Vérifier la configuration de base {#check-the-basic-configuration}

Comme toujours, les premières étapes consistent à vérifier les principes de base :

* [Confirmer le fonctionnement de base](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Passez en revue tous les fichiers journaux du serveur web et de Dispatcher. Si nécessaire, augmentez le paramètre `loglevel` utilisé pour la [journalisation](/help/using/dispatcher-configuration.md#logging) de Dispatcher.

* [Vérifiez votre configuration](/help/using/dispatcher-configuration.md) :

   * Disposez-vous de plusieurs instances de Dispatcher ?

      * Avez-vous déterminé quelle est instance de Dispatcher qui gère le site web ou la page en cours de test ?

   * Avez-vous implémenté des filtres ?

      * Ces filtres ont-ils un impact sur le sujet que vous étudiez ?

## Outils de diagnostic IIS {#iis-diagnostic-tools}

IIS propose divers outils de trace, en fonction de la version :

* IIS 6 : vous pouvez télécharger et configurer des outils de diagnostic IIS.
* IIS 7 : le suivi est entièrement intégré.

Ces outils peuvent vous aider à surveiller l’activité.

## IIS et « Erreur 404 - Page introuvable » (404 Not Found) {#iis-and-not-found}

Lorsque vous utilisez IIS, une erreur `404 Not Found` peut survenir dans plusieurs cas de figure. Si cela se produit, consultez les articles suivants de la base de connaissances.

* [IIS 6/7 : la méthode POST renvoie 404.](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6 : les requêtes aux URL qui contiennent le chemin de base `/bin` renvoient `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html).

Vérifiez également que la racine du cache de Dispatcher et la racine du document IIS sont placées dans le même répertoire.

## Problèmes lors de la suppression de modèles de workflow {#problems-deleting-workflow-models}

**Symptômes**

Problèmes lors de la tentative de suppression de modèles de workflow lors de l’accès à une instance de création AEM via Dispatcher.

**Étapes à reproduire :**

1. Connectez-vous à votre instance de création (vérifiez que les requêtes sont acheminées via Dispatcher).
1. Créez un processus ; par exemple, en définissant le titre workflowToDelete.
1. Vérifiez que le workflow a bien été créé.
1. Sélectionnez un workflow et cliquez dessus avec le bouton droit, puis cliquez sur **Supprimer**.

1. Cliquez sur **Oui** pour confirmer.
1. Une boîte de message d’erreur s’affiche. Elle affiche les informations suivantes :\
    « `ERROR 'Could not delete workflow model!!` ».

**Résolution**

Ajoutez les en-têtes suivants à la section `/clientheaders` du fichier `dispatcher.any` :

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Interférence avec mod_dir (Apache) {#interference-with-mod-dir-apache}

Ce processus décrit comment Dispatcher interagit avec `mod_dir` dans le serveur web Apache, car cela peut entraîner des effets variés, potentiellement inattendus :

### Apache 1.3 {#apache}

Dans Apache 1.3, `mod_dir` traite chaque requête pour laquelle l’URL est mappée à un répertoire du système de fichiers.

mod_dir :

* redirige la demande vers un fichier `index.html` existant ;
* génère une liste de répertoires.

Lorsque Dispatcher est activé, il traite ces demandes en s’enregistrant lui-même en tant que gestionnaire pour le type de contenu `httpd/unix-directory`.

### Apache 2.x {#apache-x}

Dans Apache 2.x, les choses sont différentes. Un module peut gérer différentes étapes de la requête, telles que la correction de l’URL. `mod_dir` gère cette étape en redirigeant une requête (lorsque l’URL est mappée à un répertoire) vers l’URL comportant un signe `/` ajouté.

Dispatcher n’intercepte pas la correction `mod_dir` mais gère entièrement la requête vers l’URL redirigée (c’est-à-dire avec le signe `/` ajouté). Ce processus peut poser un problème si le serveur distant (par exemple AEM) gère les requêtes envoyées à `/a_path` différemment des requêtes envoyées à `/a_path/` (lorsque `/a_path` est mappé sur un répertoire existant).

Dans cette situation, vous devez effectuer l’une des opérations suivantes :

* Désactivez `mod_dir` pour la sous-arborescence `Directory` ou `Location` gérée par Dispatcher.

* Utilisez `DirectorySlash Off` pour configurer `mod_dir` de manière à ne pas ajouter le signe `/`.
