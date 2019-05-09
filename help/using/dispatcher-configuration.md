---
title: Configuration de Dispatcher
seo-title: Configuration de Dispatcher
description: Découvrez comment configurer Répartiteur.
seo-description: Découvrez comment configurer Répartiteur.
uuid: 253 ef 0 f 7-2491-4 cec-ab 22-97439 df 29 fd 6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
topic-tags: répartiteur
content-type: référence
discoiquuid: aeffee 8 e-bb 34-42 a 7-9 a 5 e-b 7 d 0 e 848391 a
translation-type: tm+mt
source-git-commit: bd8fff69a9c8a32eade60c68fc75c3aa411582af

---


# Configuration de Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher qui est incluse dans la documentation d’une précédente version d’AEM.

Les sections suivantes décrivent comment configurer les différents aspects de Dispatcher.

## Prise en charge d’IPv6 et IPv4 {#support-for-ipv-and-ipv}

Vous pouvez installer tous les éléments d’AEM et de Dispatcher sur des réseaux IPv4 et IPv6. Voir [IPV4 et IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Fichiers de configuration de Dispatcher {#dispatcher-configuration-files}

Par défaut, la configuration du répartiteur est stockée dans le fichier `dispatcher.any` texte, bien que vous puissiez modifier le nom et l&#39;emplacement de ce fichier lors de l&#39;installation.

Le fichier de configuration contient une série de propriétés à une ou plusieurs valeurs qui contrôlent le comportement de Dispatcher :

* Les noms de propriété sont précédés d&#39;une barre oblique `/`.
* Les propriétés à plusieurs valeurs englobent les éléments enfants à l&#39;aide d&#39;accolades `{ }`.

Une configuration peut être structurée comme suit :

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website 
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement 
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

Vous pouvez inclure d’autres fichiers qui contribuent à la configuration :

* Si le fichier de configuration est volumineux, vous pouvez le diviser en plusieurs fichiers plus petits (qui sont plus faciles à gérer), que vous incluez.
* Pour inclure des fichiers générés automatiquement.

Par exemple, pour inclure le fichier myfarm. any dans la configuration /farms, utilisez le code suivant :

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Utilisez l’astérisque (*) comme caractère générique pour spécifier une plage de fichiers à inclure.

Par exemple, si les fichiers allant de `farm_1.any` à `farm_5.any` contiennent la configuration de fermes de serveurs un à cinq, vous pouvez les inclure comme suit :

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Utilisation de variables d’environnement {#using-environment-variables}

Vous pouvez utiliser des variables d’environnement dans des propriétés à valeur de chaîne dans le fichier dispatcher.any au lieu de coder en dur les valeurs. Pour inclure la valeur d&#39;une variable d&#39;environnement, utilisez le format `${variable_name}`.

Par exemple, si le fichier dispatcher. any se trouve dans le même répertoire que le répertoire du cache, la valeur suivante de [la](dispatcher-configuration.md#main-pars-title-30) propriété docroot peut être utilisée :

```xml
/docroot "${PWD}/cache"
```

Autre exemple : si vous créez une variable d’environnement nommée `PUBLISH_IP` qui stocke le nom d’hôte de l’instance de publication AEM, la configuration de la propriété [/renders](dispatcher-configuration.md#main-pars-127-25-0008) suivante peut être utilisée :

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Attribution d&#39;un nom à l&#39;instance Répartiteur {#naming-the-dispatcher-instance-name}

Utilisez `/name` la propriété pour spécifier un nom unique pour identifier votre instance Répartiteur. `/name` La propriété est une propriété de niveau supérieur dans la structure de configuration.

## Définition des exploitations {#defining-farms-farms}

La `/farms` propriété définit un ou plusieurs jeux de comportements Répartiteur, où chaque jeu est associé à différents sites Web ou URL. La `/farms` propriété peut inclure une seule ou plusieurs exploitations :

* Utilisez une seule ferme de serveurs lorsque vous souhaitez que Dispatcher traite toutes vos pages ou tous vos sites web de la même manière.
* Créez plusieurs fermes de serveurs lorsque différentes zones de votre site web ou différents sites web nécessitent un comportement différent de Dispatcher.

`/farms` La propriété est une propriété de niveau supérieur dans la structure de configuration. Pour définir une batterie, ajoutez une propriété enfant à `/farms` la propriété. Utilisez un nom de propriété qui identifie la ferme de serveurs de manière unique dans l’instance de Dispatcher.

`/*farmname*` La propriété est à plusieurs valeurs et contient d&#39;autres propriétés qui définissent le comportement du répartiteur :

* Les URL des pages pour lesquelles la ferme de serveurs s’applique.
* Une ou plusieurs URL de service (généralement des instances de publication AEM) à utiliser pour le rendu des documents.
* Les statistiques à utiliser pour l’équilibrage de charge des rendus de plusieurs documents.
* Plusieurs autres comportements, tels que les fichiers à mettre en cache et à quel emplacement.

La valeur peut inclure n’importe quel caractère alphanumérique (a-z, 0-9). L&#39;exemple suivant illustre la définition squelette de deux exploitations nommées `/daycom` et `/docsdaycom`:

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>Si vous utilisez plusieurs batterie de rendu, la liste est évaluée de bas en haut. Ceci est particulièrement pertinent lors de la définition [d&#39;hôtes virtuels](dispatcher-configuration.md#main-pars-117-15-0006) pour vos sites Web.

Chaque propriété /farm peut contenir les propriétés enfants suivantes :

| Nom de la propriété | Description |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Page d’accueil par défaut (facultative) (IIS uniquement) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | En-têtes provenant de la requête HTTP client à transférer. |
| [/virtualhosts](#identifying-virtual-hosts-virtual-hosts) | Hôtes virtuels pour cette ferme de serveurs. |
| [/sessionmanagement](#enabling-secure-sessions-session-management) | Prise en charge de la gestion et de l’authentification des sessions. |
| [/renders](#defining-page-renderers-renders) | Serveurs qui fournissent le rendu des pages (généralement des instances de publication AEM). |
| [/filter](#configuring-access-to-content-filter) | Définit les URL auxquelles Dispatcher accorde l’accès. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configure l’accès aux URL de redirection vers un microsite. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagate-syndpost) | Prise en charge du transfert des demandes de syndication. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configure le comportement de la mise en cache. |
| [/statistics](#configuring-load-balancing-statistics) | Définition des catégories de statistiques pour les calculs d’équilibrage de charge. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-sticky-connections-for) | Dossier contenant des documents persistants. |
| [/health_check](#specifying-a-health-check-page) | URL à utiliser pour déterminer la disponibilité du serveur. |
| [/retryDelay](#specifying-the-page-retry-delay) | Délai avant de réessayer de se connecter suite à un échec. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Pénalités qui affectent les statistiques de calculs de l’équilibrage de charge. |
| [/failover](#using-the-fail-over-mechanism) | Renvoie les demandes à différents rendus lorsque la demande d’origine échoue. |

## Spécification d’une page par défaut (IIS uniquement) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>`/homepage`Le paramètre (IIS uniquement) ne fonctionne plus. Vous devez plutôt utiliser le [module Réécriture d&#39;URL IIS](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Si vous utilisez Apache, utilisez le `mod_rewrite` module. Voir la documentation sur le site Web d&#39;Apache pour en savoir plus sur `mod_rewrite` ( [Apache 2.4, par exemple](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Lors de l&#39;utilisation `mod_rewrite`, il est conseillé d&#39;utiliser l&#39;indicateur** [&#39;passthrough| PT&#39; (passer au gestionnaire suivant)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)** pour forcer le moteur de réécriture à définir `uri` le champ de `request_rec` la structure interne sur la valeur du `filename` champ.

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## Spécification des en-têtes HTTP à transférer {#specifying-the-http-headers-to-pass-through-clientheaders}

La `/clientheaders` propriété définit une liste d&#39;en-têtes HTTP que Répartiteur transmet de la requête HTTP client au rendu (instance AEM).

Par défaut, Dispatcher transfère les en-têtes HTTP standard à l’instance AEM. Dans certains cas, vous souhaiterez peut-être transférer d’autres en-têtes ou supprimer des en-têtes spécifiques :

* Ajoutez des en-têtes, par exemple des en-têtes personnalisés, que votre instance AEM attend dans la demande HTTP.
* Supprimez des en-têtes, par exemple les en-têtes d’authentification, qui ne sont appropriés que pour le serveur web.

Si vous personnalisez le groupe d’en-têtes à transférer, vous devez définir une liste complète d’en-têtes, y compris ceux qui sont normalement inclus par défaut.

Par exemple, une instance Répartiteur qui gère les demandes d&#39;activation de page pour les instances de publication requiert l&#39; `PATH` en-tête de `/clientheaders` la section. L’en-tête `PATH` permet la communication entre l’agent de réplication et Dispatcher.

Le code suivant est un exemple de configuration pour `/clientheaders`:

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }
```

## Identification des hôtes virtuels {#identifying-virtual-hosts-virtualhosts}

La `/virtualhosts` propriété définit une liste de toutes les combinaisons de nom d&#39;hôte/URI que Répartiteur accepte pour cette batterie. Vous pouvez utiliser un astérisque (*) comme caractère générique. Les valeurs de la propriété /`virtualhosts` utilisent le format suivant :

```xml
[scheme]host[uri][*]
```

* `scheme`: (Optional) Either `https://` or `https://.`
* `host` : nom ou adresse IP de l’ordinateur hôte ainsi que le numéro de port, le cas échéant. (Voir [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)
* `uri` : (facultatif) chemin d’accès aux ressources.

L&#39;exemple de configuration suivant gère les requêtes des domaines.com et. ch de mycompany, ainsi que tous les domaines de mysubdivision :

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

La configuration suivante traite *toutes* les requêtes :

```xml
   /virtualhosts
    {
    "*"
    }
```

### Résolution de l’hôte virtuel {#resolving-the-virtual-host}

Lorsque Répartiteur reçoit une requête HTTP ou HTTPS, elle trouve la valeur d&#39;hôte virtuel qui correspond le mieux aux en-têtes `host,``uri`et `scheme` aux en-têtes de la requête. Le répartiteur évalue les valeurs des `virtualhosts` propriétés dans l&#39;ordre suivant :

* Dispatcher commence par la ferme de serveur la moins élevée et progresse vers les valeurs plus élevées dans le fichier dispatcher.any.
* Pour chaque ferme de serveurs, Dispatcher commence par la valeur la plus élevée de la propriété `virtualhosts` et progresse vers les valeurs moins élevées de la liste.

Dispatcher détecte la valeur d’hôte virtuel correspondant le mieux comme suit :

* L’hôte virtuel rencontré en premier qui correspond aux parties `host`, `scheme` et `uri` de la demande est utilisé.
* Si aucune `virtualhosts` valeur n&#39;est définie `scheme` et `uri` que des parties correspondent à la fois à `scheme` la requête et `uri` à la requête, le premier hôte virtuel rencontré correspondant à la `host` requête est utilisé.
* Si aucune valeur `virtualhosts` ne comporte une partie host qui correspond à la partie host de la demande, l’hôte virtuel le plus élevé de la ferme de serveurs la plus élevée est utilisé.

Par conséquent, vous devez définir l’hôte virtuel par défaut dans la partie supérieure de la propriété `virtualhosts` dans la ferme de serveurs la plus élevée de votre fichier dispatcher.any.

### Exemple de résolution du nom d’hôte virtuel {#example-virtual-host-resolution}

L&#39;exemple suivant représente un extrait de code provenant d&#39;un fichier dispatcher. any qui définit deux exploitations Répartiteur et chaque batterie définit une `virtualhosts` propriété.

```xml
/farms
  {
  /myProducts 
    { 
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany 
    { 
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

Dans cet exemple, le tableau suivant affiche les hôtes virtuels qui sont résolus pour les demandes HTTP données :

| URL de la demande | Hôte virtuel résolu |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/*;` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Activation des sessions sécurisées - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized`**doit** être définie `"0"` dans `/cache` la section pour activer cette fonction.

Créez une session sécurisée pour accéder à la batterie de rendu afin que les utilisateurs doivent se connecter pour accéder à n&#39;importe quelle page de la batterie. Une fois connecté, les utilisateurs peuvent accéder à toutes les pages de la batterie. Voir [Création d’un groupe d’utilisateurs fermé](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) pour plus d’informations sur l’utilisation de cette fonction avec des groupes d’utilisateurs fermés.

`/sessionmanagement` La propriété est une sous-propriété de `/farms`.

>[!CAUTION]
>
>Si des sections de votre site web utilisent des conditions d’accès différentes, vous devez définir plusieurs fermes de serveurs.

**/sessionmanagement** comporte plusieurs sous-paramètres :

**/directory** (obligatoire)

Répertoire qui stocke les informations de session. Si le répertoire n’existe pas, il est créé.

**/encode** (facultatif)

Comment les informations de session sont codées. Utilisez « md5 » pour un chiffrement reposant sur l’algorithme md5 ou « hex » pour un codage hexadécimal. Si vous chiffrez les données de session, un utilisateur ayant accès au système de fichiers ne peut pas lire le contenu de la session. La valeur par défaut est « md5 ».

**/header** (facultatif)

Nom de l’en-tête HTTP ou du cookie qui stocke les informations d’autorisation. Si vous stockez les informations dans l&#39;en-tête http, utilisez `HTTP:<*header-name*>`. Pour stocker les informations dans un cookie, utilisez `Cookie:<header-name>`. Si vous n’indiquez pas de valeur, `HTTP:authorization` est utilisé.

**/timeout** (facultatif)

Nombre de secondes avant l’expiration de la session suite à sa dernière utilisation. Si rien n’est indiqué, « 800 » est utilisé. Ainsi, la session expire un peu plus de 13 minutes après la dernière demande de l’utilisateur.

Un exemple de configuration se présente comme suit :

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## Définition des rendus de page {#defining-page-renderers-renders}

La propriété /renders définit l’URL à laquelle Dispatcher envoie les demandes de rendu d’un document. L&#39;exemple de `/renders` section suivant identifie une instance AEM unique pour le rendu :

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

La section d’exemple suivante /renders identifie une instance AEM qui s’exécute sur le même ordinateur que Dispatcher :

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }
```

La section d’exemple suivante /renders répartit les demandes de rendu à égalité entre deux instances AEM :

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }
```

### Options de rendu {#renders-options}

**/timeout**

Spécifie le délai d&#39;expiration de connexion accédant à l&#39;instance AEM en millisecondes. La valeur par défaut est « 0 », mettant Dispatcher indéfiniment en attente.

**/receiveTimeout**

Spécifie la durée en millisecondes qu&#39;une réponse peut prendre. La valeur par défaut est « 600000 » ; Dispatcher attend alors 10 minutes. Un paramètre de « 0 » élimine complètement le délai.\
Si le délai est atteint pendant l’analyse des en-têtes de réponse, un état HTTP 504 (passerelle erronée) est renvoyé. Si le délai est atteint alors que le corps de la réponse est lu, Dispatcher renvoie la réponse incomplète au client, mais supprime tout fichier cache pouvant avoir été écrit.

**/ipv4**

Indique si Dispatcher utilise la fonction `getaddrinfo` (pour IPv6) ou la fonction `gethostbyname` (pour IPv4) pour obtenir l’adresse IP du rendu. Une valeur de 0 provoque l’utilisation de `getaddrinfo`. Une valeur de 1 provoque l’utilisation de `gethostbyname`. La valeur par défaut est 0.

La fonction getaddrinfo renvoie une liste d’adresses IP. Dispatcher effectue une itération au sein de la liste d’adresses jusqu’à ce qu’il établisse une connexion TCP/IP. Par conséquent, la propriété ipv4 est importante lorsque le nom d’hôte du rendu est associé à plusieurs adresses IP et que l’hôte, en réponse à la fonction getaddrinfo, renvoie une liste d’adresses IP toujours dans le même ordre. Dans ce cas, utilisez la fonction gethostbyname afin que l’adresse IP à laquelle Dispatcher se connecte devienne aléatoire.

Amazon Elastic Load Balancing (ELB) est un service qui répond à la fonction getaddrinfo avec une liste d’adresses IP potentiellement dans le même ordre.

**/secure**

Si `/secure` la propriété a la valeur « 1 », Répartiteur utilise HTTPS pour communiquer avec l&#39;instance AEM. Pour plus d&#39;informations, voir [Configuration du répartiteur pour utiliser SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Avec Dispatcher version **4.1.6**, vous pouvez configurer la `/always-resolve` propriété comme suit :

* Lorsqu&#39;elle est définie sur « 1 », elle résout le nom d&#39;hôte sur chaque requête (le répartiteur ne met jamais en cache une adresse IP). L&#39;appel supplémentaire requis pour obtenir les informations d&#39;hôte pour chaque requête peut avoir un impact léger sur les performances.
* Si la propriété n&#39;est pas définie, l&#39;adresse IP est mise en cache par défaut.

De même, cette propriété peut être utilisée dans le cas de problèmes de résolution d&#39;IP dynamiques, comme indiqué dans l&#39;exemple suivant :

```xml
/rend {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configuration de l&#39;accès au contenu {#configuring-access-to-content-filter}

Utilisez `/filter` la section pour spécifier les requêtes HTTP que Répartiteur accepte. Les autres demandes sont renvoyées au serveur web avec le code d’erreur 404 (page introuvable). Si aucune `/filter` section n&#39;existe, toutes les requêtes sont acceptées.

**Remarque :** Les demandes pour le [fichier stat](dispatcher-configuration.md#main-pars-title-28) sont toujours rejetées.

>[!CAUTION]
>
>Consultez la liste de contrôle de sécurité [du répartiteur](security-checklist.md) pour en savoir plus lors de la limitation de l&#39;accès à l&#39;aide du Répartiteur. Lisez également [la liste de cheklist d&#39;AEM Security](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) pour obtenir des détails de sécurité supplémentaires concernant votre installation AEM.

La section /filter se compose d’une série de règles qui refusent ou accordent l’accès au contenu en fonction de modèles situés dans la partie des lignes de demandes de la requête HTTP. Vous devez utiliser une stratégie de liste blanche pour la section /filter :

* Tout d’abord, refusez l’accès à l’ensemble des éléments.
* Accordez l’accès au contenu en fonction de vos besoins.

### Définition d’un filtre {#defining-a-filter}

Chaque élément de `/filter` la section comprend un type et un modèle qui correspondent à un élément spécifique de la ligne de requête ou à la totalité de la ligne de requête. Chaque filtre peut contenir les éléments suivants :

* **Type**: Le `/type` paramètre indique si vous autorisez ou refusez l&#39;accès aux requêtes correspondant au modèle. La valeur peut être `allow` ou `deny`.

* **Elément de la ligne de requête :** Inclure `/method`, `/url`ou `/query`un `/protocol` modèle de filtrage des requêtes selon ces parties spécifiques de la partie de demande de ligne de requête de la requête HTTP. Le filtrage sur des éléments de la ligne de demande (plutôt que sur la ligne entière) correspond à la méthode préférée de filtrage.

* **Propriété glob**: `/glob` La propriété est utilisée pour correspondre à la ligne de demande entière de la requête HTTP.

Pour plus d’informations sur les propriétés /glob, voir [Création de modèles pour les propriétés glob](#designing-patterns-for-glob-properties). Les règles d’utilisation de caractères génériques dans les propriétés /glob s’appliquent également aux modèles pour les éléments correspondants de la ligne de demande.

>[!NOTE]
>
>A compter de Dispatcher version 4.2.0, plusieurs améliorations ont été apportées aux configurations des filtres et aux capacités de consignation :
>
>* [Prise en charge des expressions régulières POSIX](dispatcher-configuration.md#main-pars-title-1996763852)
>* [Prise en charge des éléments supplémentaires de filtrage de l’URL de demande](dispatcher-configuration.md#main-pars-title-694578373)
>* [Journalisation de trace](dispatcher-configuration.md#main-pars-title-1950006642)
>



#### Partie de la ligne de demande des requêtes HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 définit la [ligne de requête](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) comme suit :

*Method Request-URI HTTP-Version*&lt;CRLF&gt;

Les caractères &lt; CRLF &gt; repèrent un retour chariot suivi d&#39;un flux de ligne. L’exemple suivant est la ligne de demande qui est reçue lorsqu’un client demande la page fr du site Geometrixx-Outoors :

GET /content/geometrixx-outdoors/fr.html HTTP.1.1&lt;CRLF&gt;

Vos modèles doivent prendre en compte les caractères d&#39;espace dans la ligne de requête et les caractères &lt; CRLF &gt;.

#### Exemple de filtre : Tout refuser {#example-filter-deny-all}

La section d’exemple de filtre suivante entraîne le refus des demandes par Dispatcher pour tous les fichiers. Vous devez refuser l’accès à tous les fichiers, puis activer l’accès à des zones spécifiques.

```xml
  /0001  { /glob "*" /type "deny" }
```

Les demandes concernant une zone explicitement refusée renvoient le code d’erreur 404 (page introuvable).

#### Exemple de filtre : Refuser l’accès à des zones spécifiques {#example-filter-deny-acess-to-specific-areas}

Les filtres permettent également de refuser l’accès à divers éléments, par exemple à des pages ASP et à des zones sensibles de l’instance de publication. Le filtre suivant refuse l&#39;accès aux pages ASP :

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Exemple de filtre : Activer les demandes POST {#example-filter-enable-post-requests}

L’exemple de filtre suivant permet d’envoyer des données de formulaire par la méthode POST :

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Exemple de filtre : Activer l’accès à la console Processus {#example-filter-allow-access-to-the-workflow-console}

L’exemple suivant illustre un filtre utilisé pour refuser l’accès externe à la console Processus :

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Si votre instance de publication utilise un contexte d’application web (publication par exemple), il peut également être ajouté à la définition du filtre.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Si vous devez encore accéder à des pages uniques au sein de la zone à accès limité, vous pouvez leur accorder l’accès. Par exemple, pour accorder l’accès à l’onglet Archive dans la console Processus, ajoutez la section suivante :

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Lorsque plusieurs modèles de filtres s’appliquent à une demande, le dernier modèle de filtre qui s’applique est celui en vigueur.

#### Exemple de filtre : Utilisation d’expressions régulières {#example-filter-using-regular-expressions}

Ce filtre permet des extensions dans des répertoires de contenu non publics à l’aide d’une expression régulière, définie ici entre guillemets simples :

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Exemple de filtre : Filtrer des éléments supplémentaires d’une URL de demande {#example-filter-filter-additional-elements-of-a-request-url}

L&#39;une des améliorations introduites dans Dispatcher 4.2.0 est la possibilité de filtrer d&#39;autres éléments de l&#39;URL de requête. Les nouveaux éléments présentés sont les suivants :

* path
* selectors
* extension
* suffix

Vous pouvez les configurer en ajoutant la propriété du même nom à la règle de filtrage : `/path`, `/selectors`et `/extension``/suffix` respectivement.

Vous trouverez ci-dessous un exemple de règle qui bloque le contenu à partir du `/content` chemin et de sa sous-arborescence, à l&#39;aide de filtres pour le chemin, les sélecteurs et les extensions :

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Section d’exemple /filter {#example-filter-section}

Lors de la configuration de Dispatcher, limitez l’accès externe autant que possible. L’exemple suivant fournit un accès minimal aux visiteurs externes :

* `/content`
* contenu divers tel que des conceptions et des bibliothèques client ; par exemple :

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Après avoir créé des filtres, [testez l&#39;accès aux pages](dispatcher-configuration.md#main-pars-title-19) pour vérifier que votre instance AEM est sécurisée.

La section /filter suivante du fichier dispatcher.any peut être utilisée comme base dans le fichier de [configuration de Dispatcher.](dispatcher-configuration.md)

Cet exemple se base sur le fichier de configuration par défaut fourni avec Dispatcher. C’est un exemple d’utilisation dans un environnement de production. Les éléments préfixés avec # sont désactivés (commentés), vous devez prendre soin si vous décidez d&#39;activer l&#39;un de ces éléments (en supprimant le # sur cette ligne) car cela peut avoir un impact sur la sécurité.

Vous devez refuser l’accès à tous les éléments, puis accorder l’accès à des éléments (limités) spécifiques :

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001 { /type "deny" /glob "*" }
      
      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console
        
      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only
      
#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features 
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>Lorsque le filtre est utilisé avec Apache, concevez les modèles d’URL de filtre selon la propriété DispatcherUseProcessedURL du module de Dispatcher. (Voir [Serveur Web Apache - Configuration de votre serveur Web Apache pour Répartiteur](dispatcher-install.md#main-pars-55-35-1022)).

>[!NOTE]
>
>Les filtres 0030 et 0031 relatifs aux médias dynamiques s’appliquent à AEM 6.0 et versions ultérieures.

Tenez compte des recommandations suivantes si vous choisissez d’étendre l’accès :

* L&#39;accès externe doit `/admin`*toujours* être complètement désactivé si vous utilisez CQ version 5.4 ou une version antérieure.

* Il faut se montrer prudent lorsque vous accordez l’accès aux fichiers dans `/libs`. L’accès doit être accordé sur une base individuelle.
* Refusez l’accès à la configuration de réplication afin de la rendre invisible :

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Refusez l’accès au proxy inverse de Google Gadgets :

   * `/libs/opensocial/proxy*`

Selon votre installation, il peut exister d&#39;autres ressources sous `/libs`ou `/apps` ailleurs qui doivent être disponibles. Vous pouvez utiliser le fichier `access.log` en tant que méthode permettant de déterminer les ressources accessibles en externe.

>[!CAUTION]
>
>L’accès aux consoles et aux répertoires peut présenter un risque de sécurité pour les environnements de production. Sauf si vous avez des justifications explicites, il doit rester désactivé (commenté).

>[!CAUTION]
>
>Si vous [utilisez des rapports dans un environnement](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) de publication, configurez Répartiteur pour refuser l&#39;accès `/etc/reports` aux visiteurs externes.

### Restriction des chaînes de requête {#restricting-query-strings}

Depuis Dispatcher version 4.1.5, utilisez `/filter` la section pour limiter les chaînes de requête. Il est vivement recommandé d&#39;autoriser explicitement les chaînes de requête et d&#39;exclure les allocations génériques par le biais `allow` d&#39;éléments de filtre.

Une entrée unique peut avoir *un glob* ou une combinaison *de méthode*,*url*,*requête* et *version* , mais pas les deux. L&#39;exemple suivant illustre la chaîne `a=*` de requête et refuse toutes les autres chaînes de requête pour les URL qui résolvent le `/etc` nœud :

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Si une règle contient a `/query`, elle correspond uniquement aux requêtes contenant une chaîne de requête et correspond au modèle de requête fourni.
>
>Dans l&#39;exemple ci-dessus, si les requêtes auxquelles `/etc` il n&#39;existe aucune chaîne de requête doivent également être autorisées, les règles suivantes sont requises :


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Test de sécurité de Dispatcher {#testing-dispatcher-security}

Les filtres de Dispatcher doivent bloquer l’accès aux pages et scripts suivants sur les instances de publication AEM. Utilisez un navigateur web pour tenter d’ouvrir les pages suivantes en tant que visiteur du site et vérifier qu’un code 404 est renvoyé. Si un autre résultat est obtenu, ajustez vos filtres.

Notez que le rendu de page normal doit s’afficher pour /content/add_valid_page.html?debug=layout.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{.}/libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /cfontent/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json?statement=//*
* /content/add_valid_page.qu%65ry.js%6Fn?statement=//*
* /content/add_valid_page.query.json ? statement =//*[@ transportpassword]/(@ transportpassword % 20|% 20@transportUri % 20|% 20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html?debug=layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /bienvenue

Émettez la commande suivante dans un terminal ou une invite de commande pour déterminer si l’accès en écriture anonyme est activé. Vous ne devriez pas pouvoir saisir de données dans le nœud.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Émettez la commande suivante dans un terminal ou une invite de commande afin d’essayer d’invalider le cache de Dispatcher. Assurez-vous de recevoir une réponse 404 du code :

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Activation de l&#39;accès aux URL Vanity {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The com.adobe.granite.dispatcher.vanityurl.content package needs to be made public before publishing this contnet.</p>

 -->

Configurez Dispatcher pour autoriser l’accès aux URL de redirection vers un microsite qui sont configurées pour les pages CQ ou AEM.

Lorsque l’accès aux URL de redirection vers un microsite est activé, Dispatcher appelle régulièrement un service qui s’exécute sur l’instance de rendu afin d’obtenir une liste des URL de redirection vers un microsite. Dispatcher stocke cette liste dans un fichier local. Lorsqu&#39;une requête d&#39;une page est refusée en raison d&#39;un filtre dans `/filter` la section, Répartiteur consulte la liste des URL mineures. Si l’URL refusée se trouve dans la liste, Dispatcher autorise l’accès à l’URL de redirection vers un microsite.

Pour autoriser l&#39;accès aux URL mineures, ajoutez `/vanity_urls` une section à la `/farms` section, comme dans l&#39;exemple suivant :

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

La `/vanity_urls` section contient les propriétés suivantes :

* `/url`: Chemin d&#39;accès au service d&#39;URL Vanity qui s&#39;exécute sur l&#39;instance de rendu. La valeur de cette propriété doit être `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: Chemin d&#39;accès au fichier local dans lequel Répartiteur stocke la liste des URL mineures. Vérifiez que Dispatcher dispose d’un accès en écriture à ce fichier.
* `/delay`: (Secondes) Heure entre les appels au service d&#39;URL Vanity.

>[!NOTE]
>
>Si votre rendu est une instance d&#39;AEM, vous devez installer le package [vanityurls-Composants](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) pour installer le service d&#39;URL Vanity. (voir [Connexion à Package Share](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare)).

Utilisez la procédure suivante pour autoriser l’accès aux URL de redirection vers un microsite.

1. Si votre service de rendu est une instance AEM, installez le package com. adobe. granite. dispatcher. vanityurl. content sur l&#39;instance de publication (voir la remarque ci-dessus).
1. Pour chaque URL Vanity configurée pour une page AEM ou CQ, assurez-vous que ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` la configuration refuse l&#39;URL. Si nécessaire, ajoutez un filtre qui refuse l’URL.
1. Ajoutez la `/vanity_urls` section ci-dessous `/farms`.
1. Redémarrez le serveur web Apache.

## Transfert des demandes de syndication - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Les demandes de syndication sont généralement prévues uniquement pour Dispatcher. De ce fait, par défaut, elles ne sont pas envoyées au rendu (par exemple, une instance AEM).

Le cas échéant, définissez la propriété /propagateSyndPost sur « 1 » pour transférer les demandes de syndication à Dispatcher. Si les demandes POST sont définies, vous devez vous assurer qu’elles ne sont pas refusées dans la section filter.

## Configuration du cache de Dispatcher - /cache {#configuring-the-dispatcher-cache-cache}

La `/cache` section contrôle la manière dont Répartiteur met en cache les documents. Configurez plusieurs sous-propriétés pour implémenter vos stratégies de mise en cache :


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /rules
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /gracePeriod


Un exemple de section cache pourrait ressembler à ce qui suit :

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"          
  /allowAuthorized "0"
      
  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>Pour la mise en cache sensible aux autorisations, lisez [Mise en cache de contenu sécurisé](permissions-cache.md).

### Indication du répertoire du cache {#specifying-the-cache-directory}

`/docroot` La propriété identifie le répertoire dans lequel les fichiers mis en cache sont stockés.

>[!NOTE]
>
>La valeur doit être exactement le même chemin d’accès que la racine du document du serveur web, de sorte que Dispatcher et le serveur web traitent les mêmes fichiers.\
>Le serveur web est chargé de diffuser le code d’état correct lorsque le fichier en cache de Dispatcher est utilisé. C’est pourquoi il est important qu’il puisse également le trouver.

Si vous utilisez plusieurs fermes, chaque batterie doit utiliser une racine de document différente.

### Dénomination du fichier stat {#naming-the-statfile}

`/statfile` La propriété identifie le fichier à utiliser comme fichier status. Dispatcher utilise ce fichier pour enregistrer l’heure de la mise à jour du contenu la plus récente. Le fichier stat peut être n’importe quel fichier du serveur web.

Le fichier stat n’a aucun contenu. Lorsque le contenu est mis à jour, Dispatcher met à jour l’horodatage. Le fichier status par défaut est nommé. stat et est stocké dans docroot. Dispatcher bloque l’accès au fichier stat.

>[!NOTE]
>
>Si `/statfileslevel` est configuré, Répartiteur ignore la `/statfile` propriété et utilise. stat comme nom.

### Distribution de documents obsolètes lorsque des erreurs se produisent {#serving-stale-documents-when-errors-occur}

La `/serveStaleOnError` propriété contrôle si Répartiteur renvoie des documents invalidés lorsque le serveur de rendu renvoie une erreur. Par défaut, lorsqu’un fichier stat est modifié et invalide un contenu mis en cache, Dispatcher supprime le contenu mis en cache la prochaine fois qu’il est demandé.

Si `/serveStaleOnError` le paramètre est défini sur « 1 », Répartiteur ne supprime pas le contenu invalidé du cache, sauf si le serveur de rendu renvoie une réponse réussie. Une réponse 5 xx d&#39;AEM ou un délai d&#39;expiration de connexion provoque l&#39;affichage du contenu obsolète par Répartiteur et l&#39;état HTTP de 111 (échec de la validation).

### Mise en cache lors de l’utilisation de l’authentification {#caching-when-authentication-is-used}

La `/allowAuthorized` propriété contrôle si les requêtes contenant l&#39;une des informations d&#39;authentification suivantes sont mises en cache :

* L&#39; `authorization` en-tête.
* Cookie nommé `authorization`.
* Cookie nommé `login-token`.

Par défaut, les demandes qui incluent ces informations d’authentification ne sont pas mises en cache, car l’authentification n’est pas effectuée lorsqu’un document mis en cache est renvoyé au client. Cette configuration empêche Dispatcher de diffuser des documents mis en cache aux utilisateurs qui n’ont pas les droits requis.

Cependant, si vos droits autorisent la mise en cache des documents authentifiés, définissez /allowauthorized sur 1 :

`/allowAuthorized "1"`

>[!NOTE]
>
>Pour activer la gestion des sessions (à l&#39;aide `/sessionmanagement` de la propriété), la `/allowAuthorized` propriété doit être définie `"0"`sur.

### Spécification des documents à mettre en cache {#specifying-the-documents-to-cache}

`/rules` La propriété contrôle les documents mis en cache selon le chemin du document. Quelle que soit la propriété /rules, Dispatcher ne met jamais en cache un document dans les cas suivants :

* Si l’URI de demande contient un point d’interrogation (?).\
   Cela indique généralement une page dynamique, par exemple un résultat de recherche qui n’a pas besoin d’être mis en cache.
* L’extension de fichier est manquante.\
   Le serveur web a besoin de l’extension pour déterminer le type de document (type MIME).
* L’en-tête d’authentification est défini (vous pouvez le configurer).
* Si l&#39;instance AEM répond aux en-têtes suivants :

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Les méthodes GET ou HEAD (pour les en-têtes HTTP) sont mises en cache par le répartiteur. Pour plus d&#39;informations sur la mise en cache de l&#39;en-tête de réponse, reportez-vous à [la section Mise en cache des en-têtes](dispatcher-configuration.md#caching-http-response-headers) de réponse HTTP.

Chaque élément de la propriété /rules inclut un modèle [glob](#designing-patterns-for-glob-properties) et un type :

* Le modèle de glob est utilisé pour faire correspondre le chemin du document.
* Le type indique si les documents correspondant au modèle de glob doivent être mis en cache. La valeur peut être soit autoriser (mettre en cache le document), soit refuser (pour toujours générer le document).

Si vous n’avez pas de pages dynamiques (en plus de celles déjà exclues par les règles ci-dessus), vous pouvez configurer Dispatcher pour qu’il mette tout en cache. La section rules se présente alors comme suit :

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Pour plus d&#39;informations sur les propriétés de glob, voir [Conception de modèles pour les propriétés de glob](#designing-patterns-for-glob-properties).

Si certaines sections de la page sont dynamiques (par exemple une application d’actualités) ou au sein d’un groupe d’utilisateurs fermé, vous pouvez définir des exceptions :

>[!NOTE]
>
>Les groupes d’utilisateurs fermés ne doivent pas être mis en cache, car les droits d’utilisateur ne sont pas vérifiés pour les pages mises en cache.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Compression**

Sur les serveurs web Apache , vous pouvez compresser les documents mis en cache. La compression permet à Apache de renvoyer le document dans un formulaire compressé si le client l&#39;interroge. La compression est effectuée automatiquement en activant le module `mod_deflate`Apache, par exemple :

```xml
AddOutputFilterByType DEFLATE text/plain
```

Le module est installé par défaut avec Apache 2. x.

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### Invalidation des fichiers par niveau de dossier {#invalidating-files-by-folder-level}

Utilisez `/statfileslevel` la propriété pour invalider les fichiers mis en cache en fonction de leur chemin d&#39;accès :

* Le répartiteur crée `.stat`des fichiers dans chaque dossier du dossier docroot au niveau spécifié. Le dossier docroot correspond au niveau 0.
* Les fichiers sont invalidés en touchant `.stat` le fichier. La date de dernière modification `.stat` du fichier est comparée à la date de dernière modification d&#39;un document mis en cache. Le document est réextrait si `.stat` le fichier est plus récent.

* Lorsqu&#39;un fichier situé à un niveau donné est invalidé, **tous** `.stat` les fichiers de docroot **au** niveau du fichier invalidé ou configuré `statsfilevel` (selon ce qui est plus petit) sont touchés.

   * Par exemple : Si vous définissez `statfileslevel` la propriété sur 6 et qu&#39;un fichier est invalidé au niveau 5, chaque `.stat` fichier de docroot à 5 sera touché. Suite à cet exemple, si un fichier est invalidé au niveau 7 alors chaque. `stat` de docroot à 6 sera touché (depuis `/statfileslevel = "6"`).

Seules les ressources** le long du chemin d&#39;accès** au fichier invalidé sont affectées. Examinez l&#39;exemple suivant : un site Web utilise la structure `/content/myWebsite/xx/.` Si vous définissez `statfileslevel` comme 3, un `.stat`fichier est créé comme suit :

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Lorsqu&#39;un fichier dans est `/content/myWebsite/xx` invalidé, chaque `.stat` fichier de la docroisot vers le bas `/content/myWebsite/xx`est affecté. This would be the case only for `/content/myWebsite/xx` and not for example `/content/myWebsite/yy` or `/content/anotherWebSite`.

>[!NOTE]
>
>L&#39;invalidation peut être évitée en envoyant un en-tête `CQ-Action-Scope:ResourceOnly`supplémentaire. Vous pouvez ainsi vider des ressources spécifiques sans invalider d&#39;autres parties du cache. Pour plus d&#39;informations, reportez-vous [à cette page](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) et [Invalider manuellement le cache](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) du répartiteur.

>[!NOTE]
>
>Si vous spécifiez une valeur pour `/statfileslevel` la propriété, `/statfile` la propriété est ignorée.

### Invalidation automatique des fichiers mis en cache {#automatically-invalidating-cached-files}

La `/invalidate` propriété définit les documents qui sont automatiquement invalidés lorsque le contenu est mis à jour.

Avec l’invalidation automatique, Dispatcher ne supprime pas les fichiers mis en cache après une mise à jour du contenu, mais vérifie leur validité lorsqu’ils sont demandés par la suite. Les documents du cache qui ne sont pas invalidés automatiquement restent dans le cache jusqu’à ce qu’une mise à jour du contenu les supprime de façon explicite.

L’invalidation automatique est généralement utilisée pour les pages HTML. Les pages HTML contiennent souvent des liens vers d’autres pages. Il est dès lors difficile de déterminer si une mise à jour du contenu affecte une page. Pour s&#39;assurer que toutes les pages appropriées sont invalidées lorsque le contenu est mis à jour, invalidez automatiquement toutes les pages HTML. La configuration suivante invalide toutes les pages HTML :

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Pour plus d&#39;informations sur les propriétés de glob, voir [Conception de modèles pour les propriétés de glob](#designing-patterns-for-glob-properties).

Cette configuration entraîne l&#39;activité suivante lorsque /content/geometrixx/en est activé :

* Tous les fichiers dotés du modèle fr.* sont supprimés du dossier /content/geometrixx/.
* Le dossier /content/geometrixx/en/_jcr_content est supprimé.
* Tous les autres fichiers correspondant à la configuration /invalidate ne sont pas supprimés immédiatement. Ces fichiers sont supprimés lorsque la demande suivante se produit. Dans notre exemple, /content/geometrixx.html n’est pas supprimé immédiatement. Il est supprimé lorsque /content/geometrixx.html est demandé.

Si vous proposez des fichiers PDF et ZIP générés automatiquement en téléchargement, vous devrez peut-être les invalider automatiquement. Un exemple de configuration se présente comme suit :

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

L’intégration d’AEM à Adobe Analytics fournit des données de configuration dans un fichier analytics.sitecatalyst.js du site web. L’exemple de fichier dispatcher.any fourni avec Dispatcher comprend la règle suivante d’invalidation pour ce fichier :

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Utilisation de scripts d’invalidation personnalisés {#using-custom-invalidation-scripts}

La propriété /invalidateHandler permet de définir un script qui est appelé pour chaque demande d’invalidation reçue par Dispatcher.

Elle est appelée avec les arguments suivants :

* Poignée\
    Chemin d’accès au contenu qui est invalidé.
* Action\
    Action de réplication (par exemple Activer, Désactiver).
* Portée d&#39;action\
    Domaine de l’action de réplication (vide, sauf si un en-tête de `CQ-Action-Scope: ResourceOnly` est envoyé). Pour plus d’informations, voir [Invalidation de pages mises en cache depuis AEM](page-invalidate.md).

Ceci peut être utilisé pour plusieurs cas d’utilisation différents, tels que l’invalidation de caches spécifiques à d’autres applications, ou pour traiter les cas où la place d’une URL externalisée d’une page dans le docroot ne correspond pas au chemin d’accès au contenu.

L’exemple de script ci-dessous enregistre chaque demande d’invalidation dans un fichier.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### exemple de script de gestionnaire d’invalidation {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitation des clients qui peuvent vider le cache {#limiting-the-clients-that-can-flush-the-cache}

La propriété /allowedClients définit les clients spécifiques autorisés à vider le cache. Les modèles d’expansion de nom de fichier (glob) sont comparés à l’IP.

L’exemple suivant :

1. refuse l’accès à tous les clients ;
1. autorise explicitement l’accès à l’hôte local.

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Pour plus d&#39;informations sur les propriétés de glob, voir [Conception de modèles pour les propriétés de glob](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Il est recommandé de définir la propriété /allowedClients.
>
>Si cela n’est pas fait, les clients peuvent envoyer un appel pour effacer le cache. Si cet appel se fait à plusieurs reprises, cela peut considérablement affecter les performances du site.

### Ignorer les paramètres d’URL {#ignoring-url-parameters}

La section `ignoreUrlParams` définit les paramètres d’URL qui sont ignorés lorsque vous déterminez si une page est mise en cache ou exclue du cache :

* Lorsqu’une URL de demande contient des paramètres qui sont tous ignorés, la page est mise en cache.
* Lorsqu’une URL de demande contient un ou plusieurs paramètres qui ne sont pas ignorés, la page n’est pas mise en cache.

Lorsqu’un paramètre est ignoré pour une page, la page est mise en cache la première fois qu’elle est demandée. Les demandes ultérieures de la page diffusent la page mise en cache, quelle que soit la valeur du paramètre de la demande.

Pour spécifier les paramètres qui sont ignorés, ajoutez les règles glob à la propriété `ignoreUrlParams` :

* Pour ignorer un paramètre, créez une propriété glob qui active le paramètre.
* Pour empêcher la page d’être mise en cache, créez une propriété glob qui refuse le paramètre.

Dans l’exemple suivant, Dispatcher ignore le paramètre « q », de sorte que les URL de demande qui incluent le paramètre q sont mises en cache :

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Avec l&#39;exemple `ignoreUrlParams` de valeur, la requête HTTP suivante provoque la mise en cache de la page car `q` le paramètre est ignoré :

```xml
GET /mypage.html?q=5
```

En utilisant l&#39;exemple `ignoreUrlParams` de valeur, la requête HTTP suivante provoque **la mise** en cache de la page car `p` le paramètre n&#39;est pas ignoré :

```xml
GET /mypage.html?q=5&p=4
```

Pour plus d&#39;informations sur les propriétés de glob, voir [Conception de modèles pour les propriétés de glob](#designing-patterns-for-glob-properties).

### Mise en cache des en-têtes de réponse HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Cette fonction est disponible avec la version **4.1.11** de Dispatcher.

`/headers` La propriété vous permet de définir les types d&#39;en-têtes HTTP qui seront mis en cache par le répartiteur. Lors de la première requête à une ressource mise en mémoire cache, tous les en-têtes correspondant à l&#39;une des valeurs configurées (voir l&#39;exemple de configuration ci-dessous) sont stockés dans un fichier distinct, à côté du fichier cache. Lors des demandes suivantes à la ressource mise en cache, les en-têtes stockés sont ajoutés à la réponse.

Voici un exemple de configuration par défaut :

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}
```

>[!NOTE]
>
>Sachez également que les caractères de globalisation des fichiers ne sont pas autorisés. Pour plus d&#39;informations, voir [Conception de modèles pour les propriétés de glob](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Si vous avez besoin du répartiteur pour stocker et diffuser des en-têtes de réponse etag à partir d&#39;AEM, procédez comme suit :
>
>* Ajoutez le nom d&#39;en-tête dans la `/cache/headers`section.
>* Ajoutez la directive [Apache suivante](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) dans la section Répartiteur :
>



```xml
FileETag none
```

### Autorisations du fichier du cache répartiteur {#dispatcher-cache-file-permissions}

`mode` La propriété spécifie les autorisations de fichier appliquées aux nouveaux répertoires et fichiers dans le cache. Ce paramètre est limité par le `umask` processus d&#39;appel. Il s&#39;agit d&#39;un nombre octal créé à partir de la somme d&#39;une ou de plusieurs valeurs suivantes :

* 0400 Autoriser la lecture par propriétaire.
* 0200 Autoriser l&#39;écriture par propriétaire.
* 0100 Autoriser le propriétaire à rechercher des répertoires.
* 0040 Autoriser les membres du groupe.
* 0020 Autoriser l&#39;écriture par membres du groupe.
* 0010 Autorisez les membres du groupe à rechercher dans le répertoire.
* 0004 Allow read by others.
* 0002 Autoriser l&#39;écriture par d&#39;autres utilisateurs.
* 0001 Permettre à d&#39;autres personnes de rechercher dans le répertoire.

La valeur par défaut est 0755, ce qui permet au propriétaire de lire, d&#39;écrire ou de rechercher, ainsi que le groupe et d&#39;autres personnes à lire ou à rechercher.

### Limitation du fichier Throttling. stat {#throttling-stat-file-touching}

Avec `/invalidate` la propriété par défaut, chaque activation invalide efficacement tous `.html` les fichiers (lorsque leur chemin correspond à `/invalidate` la section). Sur un site Web présentant un trafic important, plusieurs activations consécutives augmentent la charge du processeur sur le serveur principal. Dans ce cas, il serait souhaitable de réduire le volume `.stat` des fichiers pour maintenir le site Web réactif. Vous pouvez le faire à l&#39;aide de `/gracePeriod` la propriété.

La `/gracePeriod` propriété définit le nombre de secondes d&#39;une ressource obsolète et auto-invalidée à partir du cache après la dernière activation. La propriété peut être utilisée dans une configuration où un lot d&#39;activations invaliderait autrement le cache entier. La valeur recommandée est de 2 secondes.

Pour plus d&#39;informations, lisez également les sections `/invalidate` et `/statfileslevel`les sections ci-dessus.

## Configuration d’une invalidation temporelle du cache - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Si la propriété `enableTTL` est définie, elle évalue les en-têtes de réponse du serveur principal et, s’ils contiennent un âge maximal `Cache-Control` ou une date `Expires`, un fichier vide auxiliaire en regard du fichier de cache est créé, avec l’heure de modification définie sur la date d’expiration. Lorsque le fichier mis en cache est demandé après l’heure de modification, il est automatiquement redemandé depuis le serveur principal.

Vous pouvez activer la fonction en ajoutant la ligne suivante au fichier `dispatcher.any` :

```xml
/enableTTL "1"
```

>[!NOTE]
>
>Cette fonction est disponible avec la version **4.1.11** de Dispatcher.

## Configuration de l’équilibrage de charge - /statistics {#configuring-load-balancing-statistics}

La `/statistics` section définit des catégories de fichiers pour lesquels Répartiteur note la réactivité de chaque rendu. Dispatcher utilise les scores pour déterminer le rendu auquel envoyer une demande.

Chaque catégorie que vous créez définit un modèle glob. Dispatcher compare l’URI du contenu demandé à ces modèles pour déterminer la catégorie de contenu requis :

* L’ordre des catégories détermine l’ordre dans lequel ils sont comparés à l’URI.
* Le modèle de première catégorie correspondant à l’URI est la catégorie du fichier. Aucun autre modèle de catégorie n’est évalué.

Dispatcher prend en charge un maximum de 8 catégories de statistiques. Si vous définissez plus de 8 catégories, seules les 8 premières sont utilisées.

**Sélection du rendu**

Chaque fois que Dispatcher a besoin d’une page rendue, il utilise l’algorithme suivant pour sélectionner le rendu :

1. Si la demande contient le nom du rendu dans un cookie `renderid`, Dispatcher utilise ce rendu.
1. Si la demande n’inclut pas de cookie `renderid`, Dispatcher compare les statistiques de rendu :

   1. Dispatcher détermine la catégorie de l’URI de demande.
   1. Dispatcher détermine le rendu qui a le score de réponse le plus faible pour cette catégorie, puis sélectionne ce dernier.

1. Si aucun rendu n’est sélectionné, utilisez le premier de la liste.

Le score d’une catégorie de rendu est basé sur les temps de réponse précédents, ainsi que sur les précédentes connexions échouées et réussies tentées par Dispatcher. Pour chaque tentative, le score de la catégorie de l’URI demandé est mis à jour.

>[!NOTE]
>
>Si vous n’utilisez pas l’équilibrage de charge, vous pouvez ignorer cette section.

### Définition des catégories de statistiques {#defining-statistics-categories}

Définissez une catégorie pour chaque type de document pour lequel vous voulez conserver les statistiques pour la sélection du rendu. La section /statistics contient une section /categories. Pour définir une catégorie, ajoutez une ligne sous la section /categories au format suivant :

`/name { /glob "pattern"}`

La catégorie `name` doit être unique dans la batterie. La `pattern` section est décrite dans [la section Conception de modèles pour les propriétés](#designing-patterns-for-glob-properties) de glob.

Pour déterminer la catégorie d’un URI, Dispatcher compare l’URI à chaque modèle de catégorie jusqu’à ce qu’une correspondance soit trouvée. Dispatcher commence par la première catégorie de la liste et continue dans l’ordre. Par conséquent, placez en premier les catégories contenant plusieurs modèles spécifiques.

Par exemple, Répartiteur le fichier dispatcher. any par défaut définit une catégorie HTML et une autre catégorie. La catégorie HTML est plus précise et, de ce fait, elle s’affiche en premier :

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

L’exemple suivant comprend également une catégorie pour les pages de recherche :

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Impact de l’indisponibilité du serveur sur les statistiques de Dispatcher {#reflecting-server-unavailability-in-dispatcher-statistics}

`/unavailablePenalty` La propriété définit l&#39;heure (en dixièmes de seconde) appliquée aux statistiques de rendu lorsqu&#39;une connexion au rendu échoue. Dispatcher ajoute la durée à la catégorie de statistiques correspondant à l’URI demandé.

Par exemple, la pénalité est appliquée lorsque la connexion TCP/IP au nom d’hôte/port indiqué ne peut pas être établie car AEM ne fonctionne pas (et ne lit pas) ou en raison d’un problème lié au réseau.

`/unavailablePenalty` La propriété est un enfant direct de `/farm` la section (un frère de `/statistics` la section).

Si aucune `/unavailablePenalty` propriété n&#39;existe, la valeur « 1 » est utilisée.

```xml
/unavailablePenalty "1"
```

## Identification d’un dossier de connexions persistantes - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

La `/stickyConnectionsFor` propriété définit un dossier contenant des documents bascules ; est accessible à l&#39;aide de l&#39;URL. Dispatcher envoie toutes les demandes d’un utilisateur unique qui se trouvent dans ce dossier à la même instance de rendu. Les connexions persistantes garantissent que les données de session sont présentes et cohérentes pour tous les documents. Ce mécanisme utilise le cookie `renderid`.

L&#39;exemple suivant définit une connexion bascule au dossier /products :

```xml
/stickyConnectionsFor "/products"
```

Lorsqu&#39;une page est composée de contenu provenant de plusieurs nœuds de contenu, incluez `/paths` la propriété qui répertorie les chemins d&#39;accès au contenu. Par exemple, une page contient du contenu, `/content/image``/content/video`et `/var/files/pdfs`. La configuration suivante active les connexions persistantes pour tout le contenu de la page :

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### Httponly {#httponly}

Lorsque les connexions bascules sont activées, le module répartiteur définit `renderid` le cookie. Ce cookie ne dispose pas de `httponly` l&#39;indicateur qui doit être ajouté pour améliorer la sécurité. Pour ce faire, définissez `httpOnly` la propriété dans `/stickyConnections` le nœud d&#39;un `dispatcher.any` fichier de configuration. La valeur de la propriété (0 ou 1) définit si l&#39;attribut est annexé au `renderid``HttpOnly` cookie. La valeur par défaut est 0, ce qui signifie que l&#39;attribut ne sera pas ajouté.

Pour plus d&#39;informations sur l&#39; `httponly` indicateur, lisez [cette page](https://www.owasp.org/index.php/HttpOnly).

### sécurisé {#secure}

Lorsque les connexions bascules sont activées, le module répartiteur définit `renderid` le cookie. Ce cookie ne dispose pas de **l&#39;indicateur sécurisé** qui doit être ajouté pour améliorer la sécurité. Pour ce faire, définissez `secure` la propriété dans `/stickyConnections` le nœud d&#39;un `dispatcher.any` fichier de configuration. La valeur de la propriété (0 ou 1) définit si l&#39;attribut est annexé au `renderid``secure` cookie. La valeur par défaut est 0, ce qui signifie que l&#39;attribut sera ajouté si * * la requête entrante est sécurisée. Si la valeur est définie sur 1, l&#39;indicateur sécurisé est ajouté, que la requête entrante soit sécurisée ou non.

## Gestion des erreurs de connexion au serveur de rendu {#handling-render-connection-errors}

Configurez le comportement de Dispatcher lorsque le serveur de rendu renvoie une erreur 500 ou n’est pas disponible.

### Définition d’une page de contrôle de l’intégrité {#specifying-a-health-check-page}

Utilisez `/health_check` la propriété pour spécifier une URL vérifiée lorsqu&#39;un code d&#39;état 500 se produit. Si cette page renvoie également un code d&#39;état 500, l&#39;instance est considérée comme indisponible et une pénalité de temps configurable ( `/unavailablePenalty`) est appliquée au rendu avant de relancer la tentative.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Définition du délai entre deux tentatives de connexion à une page {#specifying-the-page-retry-delay}

La `retryDelay` propriété définit la durée (en secondes) pendant laquelle Répartiteur attend entre les tentatives de connexion avec les rendus de la batterie. Pour chaque série, le nombre maximal de tentatives de connexion de Dispatcher à un rendu est le nombre de rendus de la ferme de serveurs.

Le répartiteur utilise la valeur `"1"` de s&#39; `/retryDelay` il n&#39;est pas explicitement défini. La valeur par défaut est appropriée dans la plupart des cas.

```xml
/retryDelay "1"
```

### Configuration du nombre de tentatives {#configuring-the-number-of-retries}

`/numberOfRetries` La propriété définit le nombre maximal de tentatives de connexions effectuées par Répartiteur avec les rendus. Si Dispatcher ne parvient pas à se connecter à un rendu après ce nombre de tentatives, il renvoie une réponse d’échec.

Pour chaque série, le nombre maximal de tentatives de connexion de Dispatcher à un rendu est le nombre de rendus de la ferme de serveurs. Par conséquent, le nombre maximal de tentatives de connexion du répartiteur est ( `/numberOfRetries`) x (le nombre de rendus).

Si la valeur n&#39;est pas explicitement définie, la valeur par défaut est **5**.

```xml
/numberOfRetries "5"
```

### Utilisation du mécanisme de basculement {#using-the-failover-mechanism}

Activez le mécanisme de basculement sur la ferme de serveurs de Dispatcher afin de renvoyer les demandes à différents rendus lorsque la demande d’origine échoue. Lorsque le basculement est activé, Dispatcher a le comportement suivant :

* Lorsqu’une demande à un rendu renvoie l’état HTTP 503 (NON DISPONIBLE), Dispatcher envoie la demande vers un autre rendu.
* Lorsqu&#39;une requête à un rendu renvoie l&#39;état HTTP 50 x (autre que 503), Répartiteur envoie une demande de la page configurée pour `health_check` la propriété.

   * Si le contrôle de l’intégrité renvoie 500 (INTERNAL_SERVER_ERROR), Dispatcher envoie la demande d’origine vers un rendu différent.
   * Si le contrôle de l’intégrité renvoie l’état HTTP 200, Dispatcher renvoie l’erreur HTTP 500 initiale au client.

Pour activer le basculement, ajoutez la ligne suivante à la ferme de serveurs (ou au site web) :

```xml
/failover "1" 
```

>[!NOTE]
>
>Pour essayer de relancer les requêtes HTTP qui contiennent un corps, Répartiteur envoie un `Expect: 100-continue` en-tête de demande au rendu avant de limiter le contenu réel. CQ 5.5 avec CQSE répond alors immédiatement avec 100 (CONTINUER) ou un code d’erreur. D’autres conteneurs de servlet doivent également prendre en charge ces opérations.

## Ignorer les erreurs d’interruption - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Cette option n’est généralement pas nécessaire. Vous ne devez utiliser cette option que lorsque les messages de journal suivants s’affichent :
>
>`Error while reading response: Interrupted system call`

Tout appel à un système orienté fichiers peut être interrompu `EINTR` si l’objet de l’appel au système se trouve sur un système distant accessible au moyen de NFS. On détermine si ces appels à un système peuvent expirer ou être interrompus par la façon dont le système de fichiers sous-jacent a été installé sur la machine locale.

Utilisez le paramètre /ignoreEINTR si l’instance comporte une telle configuration et si le journal contient le message suivant :

`Error while reading response: Interrupted system call`

En interne, Dispatcher lit la réponse depuis le serveur distant (c’est-à-dire AEM) en utilisant une boucle qui peut être représentée ainsi :

`while (response not finished) {  
read more data  
}`

De tels messages peuvent être générés lorsque des interruptions `EINTR` se produisent dans la section « `read more data` » et sont provoqués par la réception d’un signal avant que des données n’aient été reçues.

Pour ignorer ces arrêts, vous pouvez ajouter le paramètre suivant à `dispatcher.any` (avant `/farms`) :

`/ignoreEINTR "1"`

Ce paramètre `/ignoreEINTR` permet `"1"` au répartiteur de continuer à tenter de lire les données jusqu&#39;à ce que la réponse complète soit lue. La valeur par défaut est 0 et désactive l’option.

## Création de modèles pour les propriétés glob {#designing-patterns-for-glob-properties}

Plusieurs sections du fichier de configuration de Dispatcher utilisent les propriétés `glob` comme critères de sélection des demandes du client. Les valeurs des propriétés glob sont des modèles que Dispatcher compare à un aspect de la demande, par exemple au chemin d’accès à la ressource demandée ou à l’adresse IP du client. Par exemple, les éléments de `/filter` la section utilisent des modèles de glob pour identifier les chemins des pages sur lesquelles le répartiteur agit ou rejette.

Les valeurs glob peuvent inclure des caractères génériques et des caractères alphanumériques pour définir le modèle.

| Caractère générique | Description | Exemples |
|--- |--- |--- |
| `*` | Correspond à aucune ou à plusieurs instances contiguës de n’importe quel caractère de la chaîne. Le dernier caractère de la correspondance est déterminé par l’une des situations suivantes : <br/>Un caractère dans la chaîne correspond au caractère suivant du modèle et le caractère du modèle présente les caractéristiques suivantes :<br/><ul><li>N’est pas un *</li><li>N’est pas un ?</li><li>Caractère littéral (y compris un espace) ou une classe de caractère.</li><li>La fin du modèle est atteinte.</li></ul>Dans une classe de caractères, le caractère est interprété littéralement. | `*/geo*` Correspond à n&#39;importe quelle page sous le `/content/geometrixx` nœud et au `/content/geometrixx-outdoors` nœud. Les requêtes HTTP suivantes correspondent au modèle de glob : <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>Correspond à n&#39;importe quelle page sous le `/content/geometrixx-outdoors` nœud. Par exemple, la requête HTTP suivante correspond au modèle de glob : <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Correspond à tout caractère unique. Utilisation en dehors des classes de caractères. Dans une classe de caractères, ce caractère est interprété littéralement. | `*outdoors/??/*`<br/> Correspond aux pages du site geometrixx-outdoor dans n’importe quelle langue. Par exemple, la requête HTTP suivante correspond au modèle de glob : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>La requête suivante ne correspond pas au modèle de glob : <br/><ul><li>&quot;GET /content/geometrixx-outdoors/fr.html&quot;</li></ul> |
| `[ and ]` | Marque le début et la fin d’une classe de caractères. Les classes de caractères peuvent inclure une ou plusieurs plages de caractères et des caractères uniques.<br/>Une correspondance se produit si le caractère cible correspond à n’importe quel caractère de la classe de caractères ou d’une plage définie.<br/>Si le crochet fermant n’est pas inclus, le modèle ne produit pas de correspondance. | `*[o]men.html*`<br/> Correspond à la requête HTTP suivante :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Ne correspond pas à la requête HTTP suivante :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>Correspond aux requêtes HTTP suivantes : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Indique une plage de caractères. À utiliser dans des classes de caractères.  En dehors d’une classe de caractères, ce caractère est interprété littéralement. | `*[m-p]men.html*` Correspond à la requête HTTP suivante : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Ne correspond pas à la requête HTTP suivante :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Annule le caractère ou la classe de caractères qui suit. À utiliser uniquement pour annuler des caractères et des plages de caractères dans des classes de caractères. Équivalent à la `^ wildcard`valeur. <br/>En dehors d’une classe de caractères, ce caractère est interprété littéralement. | `*[!o]men.html*`<br/> Correspond à la requête HTTP suivante : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Ne correspond pas à la requête HTTP suivante : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Ne correspond pas à la requête HTTP suivante :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` ou `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Annule le caractère ou la plage de caractères qui suit. À utiliser uniquement pour annuler des caractères et des plages de caractères dans des classes de caractères. Equivalent au `!` caractère générique. <br/>En dehors d’une classe de caractères, ce caractère est interprété littéralement. | Les exemples de caractère `!` générique s&#39;appliquent, remplaçant les `!` caractères des modèles par `^` des caractères. |


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## Journalisation {#logging}

Dans la configuration du serveur web, vous pouvez définir :

* L’emplacement du fichier journal de Dispatcher.
* Le niveau du journal.

Reportez-vous à la documentation du serveur web et au fichier lisez-moi de l’instance de Dispatcher pour plus d’informations.

**Journaux pivotés/redirigés d’Apache**

Si vous utilisez un serveur web **Apache**, vous pouvez utiliser la fonctionnalité standard pour les journaux pivotés et/ou redirigés. Par exemple, utilisation des journaux redirigés :

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Cette ligne de commande provoque automatiquement la rotation :

* du fichier journal de Dispatcher ; avec un horodatage dans l’extension (logs/dispatcher.log%Y%m%d) ;
* chaque semaine (60 x 60 x 24 x 7 = 604 800 secondes).

Consultez la documentation du serveur web Apache sur la rotation des journaux et les journaux redirigés ; par exemple[ Apache 2.2](https://httpd.apache.org/docs/2.2/logs.html).

>[!NOTE]
>
>Lors de l’installation, le niveau de journalisation par défaut est élevé (par exemple niveau 3 = Débogage), de sorte que Dispatcher consigne toutes les erreurs et tous les avertissements. Cette option est particulièrement utile lors des étapes initiales.
>
>Toutefois, cette opération nécessite des ressources supplémentaires, de sorte que lorsque Dispatcher fonctionne sans problème *selon vos besoins*, vous pouvez (devez) réduire le niveau du journal.

### Journalisation de trace {#trace-logging}

Entre autres améliorations pour Dispatcher, la version 4.2.0 introduit également la journalisation de suivi.

Il s’agit d’un niveau supérieur à la journalisation du débogage, affichant des informations supplémentaires dans les journaux. Il ajoute la journalisation :

* aux valeurs des en-têtes distribués ;
* à la règle appliquée pour une action.

Vous pouvez activer la journalisation de trace en définissant le niveau de journalisation sur `4` dans le serveur web.

Vous trouverez ci-dessous des exemples de journaux avec la trace activée :

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

Et un événement consigné lorsqu’un fichier qui correspond à une règle de blocage est demandé :

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Confirmation du fonctionnement de base {#confirming-basic-operation}

Pour confirmer le fonctionnement de base et l’interaction du serveur web, de Dispatcher et de l’instance AEM, procédez comme suit :

1. Définissez `loglevel` la variable sur `3`.

1. Démarrez le serveur web. Cela lance également Dispatcher.
1. Démarrez l’instance AEM.
1. Vérifiez le journal et les fichiers d’erreurs du serveur web et de Dispatcher.\
   Selon votre serveur Web, les messages tels que :\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   et:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Naviguez sur le site web via le serveur web. Vérifiez que le contenu s’affiche selon vos besoins.\
   Par exemple, sur une installation locale où AEM s&#39;exécute sur le port `4502` et le serveur Web sur `80` l&#39;accès à la console Sites Web à l&#39;aide des deux :\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Les résultats doivent être identiques. Confirmez l’accès à d’autres pages avec le même mécanisme.

1. Assurez-vous que le répertoire du cache est rempli.
1. Activez une page pour vérifier que le cache est vidé correctement.
1. Si tout fonctionne correctement, vous pouvez réduire le niveau du journal `loglevel` sur `0`.

## Utilisation de plusieurs instances de Dispatcher {#using-multiple-dispatchers}

Avec des configurations complexes, vous pouvez utiliser plusieurs instances de Dispatcher. Par exemple, vous pouvez utiliser :

* une instance pour publier un site web sur l’intranet ;
* une seconde instance, à une autre adresse et avec d’autres paramètres de sécurité, pour publier le même contenu sur Internet.

Dans ce cas, veillez à ce que chaque demande passe par une seule instance de Dispatcher. Une instance de Dispatcher ne traite pas les demandes provenant d’une autre instance. Par conséquent, assurez-vous que les deux instances de Dispatcher accèdent directement au site web AEM.

## Débogage {#debugging}

Lors de l&#39;ajout de l&#39;en-tête `X-Dispatcher-Info` à une requête, Répartiteur répond si la cible a été mise en cache, renvoyée par le cache ou non. L&#39;en-tête de réponse `X-Cache-Info` contient ces informations dans un formulaire lisible. Vous pouvez utiliser ces en-têtes de réponse pour déboguer des problèmes impliquant des réponses mises en cache par le Répartiteur.

Cette fonctionnalité n&#39;est pas activée par défaut. Par conséquent, pour que l&#39;en-tête `X-Cache-Info` de réponse soit inclus, la batterie doit contenir l&#39;entrée suivante :

```xml
/info "1"
```

Par exemple :

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

En outre, l&#39; `X-Dispatcher-Info` en-tête n&#39;a pas besoin d&#39;une valeur, mais si vous utilisez `curl` à des fins de test, vous devez fournir une valeur pour envoyer l&#39;en-tête, par exemple :

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Vous trouverez ci-dessous une liste contenant les en-têtes de réponse `X-Dispatcher-Info` renvoyés :

* **mis en cache**\
   Le fichier cible est contenu dans le cache et le répartiteur a déterminé qu&#39;il est valide pour le diffuser.
* **mise en cache**\
   Le fichier cible n&#39;est pas contenu dans le cache et le répartiteur a déterminé qu&#39;il est valide pour mettre en cache la sortie et le diffuser.
* **mise en cache : stat file est plus récent Le**
fichier cible est contenu dans le cache ; toutefois, il est invalidé par un fichier stat plus récent. Le répartiteur supprimera le fichier cible, le recrée de la sortie et le distribue.
* **non plafonné : aucune racine
de document** La configuration de la batterie ne contient pas de racine de document (élément `cache.docroot`de configuration).
* **non plafonné : chemin du fichier cache trop long**\
   Le fichier cible - la concaténation de la racine du document et du fichier URL - dépasse le fichier le plus long possible - nom sur le système.
* **non plafonné : chemin d&#39;accès au fichier temporaire trop long**\
   Le fichier temporaire - le modèle de nom dépasse le fichier le plus long possible - nom sur le système. Le répartiteur crée d&#39;abord un fichier temporaire avant de créer ou de remplacer réellement le fichier mis en cache. Le fichier temporaire - nom est le fichier cible - nom avec les caractères `_YYYYXXXXXX` ajoutés à celui-ci, où il `Y``X` sera remplacé pour créer un nom unique.
* **non plafonné : URL de requête sans extension**\
   L&#39;URL de requête n&#39;a pas d&#39;extension ou un chemin d&#39;accès suit l&#39;extension de fichier, par exemple : `/test.html/a/path`.
* **non plafonné : La requête n&#39;était pas un GET ou un HEAD**
La méthode HTTP n&#39;est ni une instruction GET, ni un HEAD. Le répartiteur suppose que la sortie contiendra des données dynamiques qui ne doivent pas être mises en cache.
* **non plafonné : la requête contenait une chaîne de requête**\
   La requête contenait une chaîne de requête. Le répartiteur suppose que la sortie dépend de la chaîne de requête donnée et ne cache donc pas.
* **non plafonné : Le gestionnaire de session n&#39;a pas authentifié**\
   Le cache de la batterie est géré par un gestionnaire de session (la configuration contient un `sessionmanagement` nœud) et la requête ne contient pas les informations d&#39;authentification appropriées.
* **non plafonné : demande contient une autorisation**\
   La batterie n&#39;est pas autorisée à mettre en cache la sortie ( `allowAuthorized 0`). La demande contient des informations d&#39;authentification.
* **non plafonné : target est un répertoire**\
   Le fichier cible est un répertoire. This might point to some conceptual mistake, where a URL and some sub-URL both contain cacheable output, for example if a request to `/test.html/a/file.ext` comes first and contains cacheable output, the dispatcher will not be able to cache the output of a subsequent request to `/test.html`.
* **non plafonné : URL de requête comporte une barre oblique à la fin**\
   L&#39;URL de requête comporte une barre oblique à la fin.
* **non plafonné : URL de requête non dans les règles de cache**\
   Les règles de cache de la batterie refusent explicitement la mise en cache de la sortie d&#39;une URL de requête.
* **non plafonné : vérificateur d&#39;autorisation refusé**\
   Le vérificateur d&#39;autorisation de la batterie a refusé l&#39;accès au fichier mis en cache.
* **non plafonné : session non valide Le**
cache de la batterie est géré par un gestionnaire de session (la configuration contient un `sessionmanagement` nœud) et la session de l&#39;utilisateur n&#39;est plus valide ou n&#39;est plus valide.
* **non plafonné : contient le`no_cache `** serveur distant renvoyait un `Dispatcher: no_cache` en-tête, empêchant le répartiteur de mettre en cache la sortie.
* **non plafonné : length content length est égal à zéro**
La longueur du contenu de la réponse est zéro ; le répartiteur ne créera pas de fichier de longueur nulle.
