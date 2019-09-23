---
title: Configuration de Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery)
seo-title: Configuration d’Adobe AEM Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery)
description: Découvrez comment configurer AEM Dispatcher pour empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery).
seo-description: Découvrez comment configurer Adobe AEM Dispatcher pour empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery).
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: référence
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4

---


# Configuration de Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery){#configuring-dispatcher-to-prevent-csrf-attacks}

AEM fournit une infrastructure visant à empêcher les attaques par falsification de requête intersites. Pour utiliser correctement cette structure, vous devez apporter les modifications suivantes à la configuration de Dispatcher :

>[!NOTE]
>
>Prenez soin de mettre à jour les numéros des règles dans les exemples ci-dessous en fonction de la configuration existante. Rappelez-vous que les instances de Dispatcher utilisent la dernière règle correspondante pour accorder une autorisation ou un refus. De ce fait, placez les règles à proximité de la partie inférieure de la liste existante.

1. Dans la section `/clientheaders` de vos fichiers author-farm.any et publish-farm.any, ajoutez l’entrée suivante au bas de la liste :\
   `CSRF-Token`
1. Dans la section /filters de vos fichiers `author-farm.any` et `publish-farm.any` ou `publish-filters.any`, ajoutez la ligne suivante afin d’autoriser les demandes pour `/libs/granite/csrf/token.json` au moyen de Dispatcher.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Dans la section `/cache /rules` de votre fichier `publish-farm.any`, ajoutez une règle permettant d’empêcher Dispatcher de mettre en cache le fichier `token.json`. En général, les auteurs contournent la mise en cache, de sorte que vous n’avez pas à ajouter de règle au fichier `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Pour vérifier que la configuration fonctionne, consultez le fichier dispatcher.log en mode de DÉBOGAGE pour contrôler que le fichier token.json n’est ni mis en cache, ni bloqué par des filtres. Des messages de ce type peuvent apparaître :\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Vous pouvez également vérifier que les demandes réussissent dans le fichier Apache `access_log`. Les requêtes pour``/libs/granite/csrf/token.json doivent renvoyer le code d’état HTTP 200.
