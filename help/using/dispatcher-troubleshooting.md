---
title: Résolution des problèmes liés à Dispatcher
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
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 43%

---

# Résolution des problèmes liés à Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Cependant, la documentation de Dispatcher est incluse dans la documentation d’AEM. Utilisez toujours la documentation du dispatcher incluse dans la documentation de la dernière version d’AEM.
>
>Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

>[!NOTE]
>
>Vérifiez les [Base de connaissances de Dispatcher](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), [Résolution des problèmes de purge de Dispatcher](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) et le [FAQ sur les problèmes fréquents de Dispatcher](dispatcher-faq.md) pour plus d’informations.

## Vérification de la configuration de base {#check-the-basic-configuration}

Comme toujours, les premières étapes consistent à vérifier les principes de base :

* [Confirmation du fonctionnement de base](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Vérifiez tous les fichiers journaux de votre serveur web et de Dispatcher. Si nécessaire, augmentez la variable `loglevel` utilisé pour Dispatcher [connexion](/help/using/dispatcher-configuration.md#logging).

* [Vérifiez votre configuration](/help/using/dispatcher-configuration.md) :

   * Disposez-vous de plusieurs instances de Dispatcher ?

      * Avez-vous déterminé quelle est instance de Dispatcher qui gère le site web ou la page en cours de test ?
   * Avez-vous implémenté des filtres ?

      * Ces filtres ont-ils un impact sur le sujet que vous étudiez ?


## Outils de diagnostic IIS  {#iis-diagnostic-tools}

IIS propose divers outils de trace, en fonction de la version :

* IIS 6 : vous pouvez télécharger et configurer des outils de diagnostic IIS.
* IIS 7 : la trace est parfaitement intégrée

Ces outils peuvent vous aider à surveiller l’activité.

## IIS et « Erreur 404 - Page introuvable » (404 Not Found)  {#iis-and-not-found}

Lorsque vous utilisez IIS, vous pouvez rencontrer des problèmes `404 Not Found` renvoyée dans divers scénarios. Si cela se produit, consultez les articles suivants de la base de connaissances.

* [IIS 6/7 : la méthode POST renvoie une erreur 404.](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6 : Requêtes aux URL contenant le chemin d’accès de base `/bin` renvoyer une `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Vérifiez également que la racine du cache de Dispatcher et la racine du document IIS sont définies sur le même répertoire.

## Problèmes lors de la suppression des modèles de processus {#problems-deleting-workflow-models}

**Symptômes**

Problèmes lors de la tentative de suppression des modèles de processus lorsque vous accédez à une instance de création AEM à l’aide de Dispatcher.

**Étapes à reproduire :**

1. Connectez-vous à votre instance d’auteur (vérifiez que les demandes sont acheminées via Dispatcher).
1. créer un workflow ; par exemple, avec le titre défini sur workflowToDelete.
1. Vérifiez que le processus a été correctement créé.
1. Sélectionnez le workflow et cliquez dessus avec le bouton droit, puis cliquez sur **Supprimer**.

1. Cliquez sur **Oui** pour confirmer.
1. Une boîte de message d’erreur s’affiche. Elle affiche les informations suivantes :\
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

Ce processus décrit la manière dont Dispatcher interagit avec `mod_dir` à l’intérieur du serveur web Apache, car il peut entraîner des effets variés, potentiellement inattendus :

### Apache 1.3 {#apache}

Dans Apache 1.3, `mod_dir` gère chaque demande pour laquelle l’URL est mappée à un répertoire du système de fichiers.

mod_dir :

* redirige la demande vers un fichier `index.html` existant ;
* génère une liste de répertoires.

Lorsque Dispatcher est activé, il traite ces requêtes en s’enregistrant lui-même en tant que gestionnaire pour le type de contenu. `httpd/unix-directory`.

### Apache 2.x {#apache-x}

Dans Apache 2.x, les choses sont différentes. Un module peut gérer différentes étapes de la demande, telles les corrections d’URL. Le `mod_dir` gère cette étape en redirigeant une requête (lorsque l’URL est mappée à un répertoire) vers l’URL comportant une `/` ajouté.

Dispatcher n’intercepte pas la variable `mod_dir` correction, mais gère entièrement la requête vers l’URL redirigée (c’est-à-dire, avec `/` annexé). Ce processus peut poser un problème si le serveur distant (par exemple, AEM) traite les demandes envoyées à la fonction `/a_path` différemment des requêtes envoyées à `/a_path/` (lorsque `/a_path` est mappé à un répertoire existant).

Si cette situation se produit, vous devez :

* disable `mod_dir` pour le `Directory` ou `Location` Sous-arborescence gérée par Dispatcher

* Utilisez `DirectorySlash Off` pour configurer `mod_dir` de manière à ne pas ajouter le signe `/`.
