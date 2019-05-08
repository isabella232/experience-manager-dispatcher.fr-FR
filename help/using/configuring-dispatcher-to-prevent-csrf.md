---
title: Configuration de Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery)
seo-title: Configuration du répartiteur Adobe AEM pour empêcher les attaques CSRF
description: Découvrez comment configurer le répartiteur AEM pour empêcher les attaques multisite par usurpation de requête.
seo-description: Découvrez comment configurer le répartiteur Adobe AEM pour empêcher les attaques multisite par usurpation de requête.
uuid: f 290 bdeb -54 e 2-4649-b 0 fc -6257 b 422 af 2 d
topic-tags: répartiteur
content-type: référence
discoiquuid: d 61 d 021 e-b 338-4 a 1 d -91 ee -55427557 e 931
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Configuration de Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery){#configuring-dispatcher-to-prevent-csrf-attacks}

AEM fournit une infrastructure visant à empêcher les attaques par falsification de requête intersites. Pour utiliser correctement cette structure, vous devez apporter les modifications suivantes à la configuration de Dispatcher :

>[!NOTE]
>
>Prenez soin de mettre à jour les numéros des règles dans les exemples ci-dessous en fonction de la configuration existante. Rappelez-vous que les instances de Dispatcher utilisent la dernière règle correspondante pour accorder une autorisation ou un refus. De ce fait, placez les règles à proximité de la partie inférieure de la liste existante.

1. Dans `/clientheaders` la section de votre auteur-farm. any et de la batterie publish-farm. any, ajoutez l&#39;entrée suivante au bas de la liste :\
   `CSRF-Token`
1. Dans la section /filters de votre `author-farm.any` ou `publish-farm.any``publish-filters.any` fichier, ajoutez la ligne suivante pour autoriser les requêtes `/libs/granite/csrf/token.json` par le biais du répartiteur.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Sous la `/cache /rules` section de votre `publish-farm.any`, ajoutez une règle pour empêcher le répartiteur de mettre en cache `token.json` le fichier. En général, les auteurs contournent la mise en cache, de sorte que vous n’avez pas à ajouter de règle au fichier `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Pour vérifier que la configuration fonctionne, consultez le fichier dispatcher.log en mode de DÉBOGAGE pour vérifier que le fichier token.json n’est pas mis en cache et n’est pas bloqué par des filtres. Vous devriez voir des messages similaires à :\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Vous pouvez également vérifier que les demandes réussissent dans le fichier Apache `access_log`. Les requêtes de «  » doivent renvoyer un code d&#39;état HTTP 200.
