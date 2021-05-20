---
title: Optimisation d’un site web pour les performances du cache
seo-title: Optimisation d’un site web pour les performances du cache
description: Apprenez comment concevoir votre site web afin de tirer le meilleur parti des avantages de la mise en cache.
seo-description: Dispatcher propose un certain nombre de mécanismes intégrés que vous pouvez utiliser pour optimiser les performances. Apprenez comment concevoir votre site web afin de tirer le meilleur parti des avantages de la mise en cache.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 2ca816ac0776d72be651b76ff4f45e0c3ed1450f
workflow-type: tm+mt
source-wordcount: '1167'
ht-degree: 100%

---


# Optimisation d’un site web pour les performances du cache {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

Dispatcher propose un certain nombre de mécanismes intégrés que vous pouvez utiliser pour optimiser les performances. Cette section vous indique comment concevoir votre site web afin de profiter au mieux des avantages de la mise en cache.

>[!NOTE]
>
>Il peut être utile de vous rappeler que le dispatcher stocke le cache sur un serveur web standard. Cela signifie que :
>
>* vous pouvez mettre en cache tous les éléments que vous pouvez enregistrer en tant que page et demander à l’aide d’une URL ;
>* vous ne pouvez pas enregistrer d’autres éléments, tels que des en-têtes HTTP, des cookies, des données de session et des données de formulaire.
>
>En général, de nombreuses stratégies de mise en cache impliquent de sélectionner les URL appropriées et de ne pas s’en tenir à ces informations supplémentaires.

## Utilisation d’un codage cohérent de page  {#using-consistent-page-encoding}

Les en-têtes de requête HTTP ne sont pas mis en cache. Par conséquent, des problèmes peuvent survenir si vous enregistrez des informations de codage de page dans l’en-tête. Dans ce cas, lorsque Dispatcher diffuse une page du cache, le codage par défaut du serveur web est utilisé pour la page. Il existe deux méthodes pour contourner ce problème :

* Si vous utilisez un seul codage, assurez-vous que le codage utilisé sur le serveur web est le même que le codage par défaut du site web AEM.
* Utilisez une balise `<META>` dans la section `head` du code HTML pour définir le codage, comme dans l’exemple suivant :

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Contournement des paramètres d’URL {#avoid-url-parameters}

Si possible, évitez les paramètres d’URL pour les pages que vous souhaitez mettre en cache. Par exemple, si vous disposez d’une galerie d’images, l’URL suivante n’est jamais mise en cache (sauf si le dispatcher [est configuré en conséquence](dispatcher-configuration.md#main-pars_title_24)) :

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Cependant, vous pouvez placer les paramètres suivants dans l’URL de la page, comme suit :

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Cette URL invoque la même page et le même modèle que gallery.html. Dans la définition du modèle, vous pouvez spécifier le script qui effectue le rendu de la page ou utiliser le même script pour toutes les pages.

## Personnalisation par URL   {#customize-by-url}

Si vous autorisez les utilisateurs à modifier la taille de police des caractères (ou toute autre personnalisation de la mise en page), assurez-vous que les différentes personnalisations sont répercutées dans l’URL.

Par exemple, les cookies ne sont pas mis en cache. Par conséquent, si vous stockez la taille de police des caractères dans un cookie (ou un mécanisme similaire), la taille de la police n’est pas conservée pour la page en cache. Ainsi, le dispatcher renvoie de manière aléatoire des documents comportant toutes tailles de police.

L’inclusion de la taille de police dans L’URL sous la forme d’un sélecteur évite ce problème :

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Pour la plupart des aspects de mise en page, il est également possible d’utiliser des feuilles de style et/ou des scripts côté client. Ces deux options fonctionnent généralement bien avec la mise en cache.
>
>Elles sont également utiles pour une version imprimée. Dans ce cas, vous pouvez également utiliser une URL telle que : ``
>
>`www.myCompany.com/news/main.print.html`
>
>En utilisant l’expansion de nom de fichier du script de la définition du modèle, vous pouvez définir un script distinct qui effectue le rendu des pages d’impression.

## Invalidation de fichiers image utilisés comme titres   {#invalidating-image-files-used-as-titles}

Si vous affichez les titres de page ou tout autre texte sous la forme d’images, il est conseillé de stocker les fichiers de manière à ce qu’ils soient supprimés lors de la mise à jour du contenu de la page :

1. Placez le fichier image dans le même dossier que la page.
1. Utilisez le format d’affectation de nom suivant pour le fichier image :

   `<page file name>.<image file name>`

Par exemple, vous pouvez stocker le titre de la page maPage.html dans le fichier maPage.titre.gif. Ce fichier est automatiquement supprimé lorsque la page est mise à jour, de sorte que toute modification du titre de la page est automatiquement répercutée dans le cache.

>[!NOTE]
>
>Le fichier image n’existe pas nécessairement physiquement sur l’instance AEM. Vous pouvez utiliser un script qui crée dynamiquement le fichier image. Dispatcher stocke ensuite le fichier sur le serveur web.

## Invalidation des fichiers image utilisés pour la navigation   {#invalidating-image-files-used-for-navigation}

Si vous utilisez des images pour les entrées de navigation, la méthode est fondamentalement la même qu’avec les titres. Elle est seulement un peu plus complexe. Stockez toutes les images de navigation avec les pages cibles. Si vous utilisez deux images pour les modes normal et actif, vous pouvez utiliser les scripts suivants :

* Un script affichant la page, en mode normal.
* Un script qui traite la demande « .normal » et renvoie l’image normale.
* Un script qui traite la demande « .active » et renvoie l’image activée.

Il est important de créer ces images avec le même descripteur de nommage que la page, pour s’assurer qu’une mise à jour du contenu supprime ces images ainsi que la page.

Pour les pages qui ne sont pas modifiées, les images sont toujours dans le cache, bien que les pages elles-mêmes soient généralement invalidées automatiquement.

## Personnalisation  {#personalization}

Le dispatcher ne peut pas mettre en cache des données personnalisées. Il est donc recommandé de n’utiliser la personnalisation que lorsque cela est nécessaire. Explications :

* Si vous utilisez une page de démarrage personnalisable librement, cette page doit être affichée chaque fois qu’un utilisateur la demande.
* Si, en revanche, vous offrez un choix de 10 pages de démarrage différentes, vous pouvez mettre en cache chacune d’entre elles afin d’améliorer les performances.

>[!NOTE]
>
>Si vous personnalisez chaque page (par exemple en mettant le nom d’utilisateur dans la barre de titre), vous ne pouvez pas la mettre en cache, ce qui peut avoir un impact significatif sur les performances.
>
>Toutefois, si vous devez mettre en place un tel système, vous pouvez :
>
>* Utiliser des iFrames pour partager la page en une partie identique pour tous les utilisateurs et une partie identique pour toutes les pages de l’utilisateur. Vous pouvez ensuite mettre en cache les deux parties.
>* Utiliser du JavaScript côté client pour afficher des informations personnalisées. Cependant, vous devez vous assurer que la page s’affiche toujours correctement si un utilisateur désactive JavaScript.
>



## Connexions persistantes   {#sticky-connections}

Les [connections persistantes](dispatcher.md#TheBenefitsofLoadBalancing) garantissent que les documents d’un utilisateur sont tous composés sur le même serveur. Si un utilisateur quitte ce dossier et y revient ultérieurement, la connexion reste valide. Définissez un dossier pour stocker tous les documents qui nécessitent des connexions persistantes pour le site web. Essayez de ne pas placer d’autres documents dans ce dossier. Si vous utilisez des pages personnalisées et des données de session, cela impacte l’équilibrage de charge.

## Types MIME   {#mime-types}

Pour un navigateur, il existe deux manières de déterminer le type d’un fichier :

1. Grâce à son extension (par exemple .html, .gif, .jpg, etc.)
1. Grâce au type MIME que le serveur envoie avec le fichier.

Pour la plupart des fichiers, le type MIME est implicite dans l’extension du fichier. C’est-à-dire :

1. Grâce à son extension (par exemple .html, .gif, .jpg, etc.)
1. Grâce au type MIME que le serveur envoie avec le fichier.

Si le nom de fichier n’a pas d’extension, il s’affiche en tant que texte brut.

Le type MIME fait partie de l’en-tête HTTP. Par conséquent, Dispatcher ne le met pas en cache. Si votre application AEM renvoie des fichiers qui n’ont pas d’extension reconnue, mais utilisent le type MIME à la place, ces fichiers risquent d’être affichés de manière erronée.

Pour s’assurer que ces fichiers sont correctement mis en cache, suivez les consignes suivantes :

* Assurez-vous que les fichiers ont toujours l’extension appropriée.
* Évitez les scripts génériques de diffusion de fichiers avec une URL de type : download.jsp?file=2214. Réécrivez le script afin d’utiliser les URL contenant la spécification de fichier ; pour l’exemple précédent, il s’agit de download.2214.pdf.

