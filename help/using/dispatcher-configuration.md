---
title: Configurer Dispatcher
description: Découvrez comment configurer Dispatcher. Découvrez la prise en charge d’IPv4 et d’IPv6, des fichiers de configuration, des variables d’environnement, de l’attribution de noms à l’instance, de la définition de batteries, de l’identification des hôtes virtuels, etc.
exl-id: 91159de3-4ccb-43d3-899f-9806265ff132
source-git-commit: 410346694a134c0f32a24de905623655f15269b4
workflow-type: ht
source-wordcount: '8857'
ht-degree: 100%

---

# Configurer Dispatcher {#configuring-dispatcher}

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Vous avez été redirigé vers cette page si vous avez suivi un lien vers la documentation de Dispatcher incluse dans la documentation d’une précédente version d’AEM.

Les sections suivantes décrivent comment configurer divers aspects de Dispatcher.

## Prise en charge d’IPv6 et IPv4  {#support-for-ipv-and-ipv}

Vous pouvez installer tous les éléments d’AEM et de Dispatcher sur des réseaux IPv4 et IPv6. Voir [IPV4 et IPV6](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=fr#ipv-and-ipv).

## Fichiers de configuration de Dispatcher {#dispatcher-configuration-files}

Par défaut, la configuration de Dispatcher est stockée dans le fichier texte `dispatcher.any`, bien que vous puissiez modifier le nom et l’emplacement de ce fichier au cours de l’installation.

Le fichier de configuration contient une série de propriétés à une ou plusieurs valeurs qui contrôlent le comportement de Dispatcher :

* Les noms des propriétés comportent un préfixe avec une barre oblique `/`.
* Les propriétés à plusieurs valeurs placent les éléments enfants dans des accolades `{ }`.

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

* Si le fichier de configuration est volumineux, vous pouvez le diviser en plusieurs fichiers plus petits (qui sont plus faciles à gérer), et inclure chacun d’entre eux.
* Pour inclure des fichiers générés automatiquement.

Par exemple, pour inclure le fichier myFarm.any dans la configuration de /farms, utilisez le code suivant :

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Pour spécifier une plage de fichiers à inclure, utilisez l’astérisque (`*`) comme caractère générique.

Par exemple, si les fichiers allant de `farm_1.any` à `farm_5.any` contiennent la configuration des batteries 1 à 5, vous pouvez les inclure comme suit :

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Utiliser les variables d’environnement {#using-environment-variables}

Vous pouvez utiliser des variables d’environnement dans des propriétés à valeur de chaîne dans le fichier dispatcher.any au lieu de coder en dur les valeurs. Pour inclure la valeur d’une variable d’environnement, utilisez le format `${variable_name}`.

Par exemple, si le fichier dispatcher.any se trouve dans le même répertoire que le répertoire de cache, la valeur de la propriété [docroot](#specifying-the-cache-directory) suivante peut être utilisée :

```xml
/docroot "${PWD}/cache"
```

Autre exemple : si vous créez une variable d’environnement nommée `PUBLISH_IP` qui stocke le nom d’hôte de l’instance de publication AEM, la configuration de la propriété [/renders](#defining-page-renderers-renders) suivante peut être utilisée :

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Attribuer un nom à l’instance de Dispatcher {#naming-the-dispatcher-instance-name}

Utilisez la propriété `/name` pour indiquer un nom unique permettant d’identifier votre instance de Dispatcher. La propriété `/name` est une propriété de niveau supérieur dans la structure de configuration.

## Définir des batteries {#defining-farms-farms}

La propriété `/farms` définit un ou plusieurs groupes de comportements de Dispatcher, chaque groupe étant associé à différents sites web ou URL. La propriété `/farms` peut inclure une ou plusieurs fermes de serveurs :

* Utilisez une seule batterie lorsque vous souhaitez que Dispatcher gère toutes vos pages web ou sites web de la même manière.
* Créez plusieurs batteries lorsque différentes zones de votre site web ou différents sites web nécessitent un comportement différent de Dispatcher.

La propriété `/farms` est une propriété de niveau supérieur dans la structure de configuration. Pour définir une ferme de serveurs, ajoutez une propriété enfant à la propriété `/farms`. Utilisez un nom de propriété qui identifie la ferme de serveurs de manière unique dans l’instance de Dispatcher.

La propriété `/farmname` est composée de plusieurs valeurs et contient d’autres propriétés définissant le comportement de Dispatcher :

* Les URL des pages auxquelles la batterie s’applique.
* Une ou plusieurs URL de service (généralement des instances de publication AEM) à utiliser pour le rendu des documents.
* Statistiques à utiliser pour équilibrer la charge de plusieurs moteurs de rendu de documents.
* Plusieurs autres comportements, tels que les fichiers à mettre en cache et où les mettre en cache.

La valeur peut inclure n’importe quel caractère alphanumérique (a-z, 0-9). L’exemple suivant montre la définition du squelette pour deux fermes de serveurs appelées `/daycom` et `/docsdaycom` :

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
>Si vous utilisez plusieurs fermes de serveurs de rendu, la liste est évaluée de manière ascendante. Ce flux est pertinent lors de la définition des [hôtes virtuels](#identifying-virtual-hosts-virtualhosts) pour vos sites web.

Chaque propriété /farm peut contenir les propriétés enfants suivantes :

| Nom de la propriété | Description |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Page d’accueil par défaut (facultative) (IIS uniquement) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | En-têtes provenant de la requête HTTP client à transférer. |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | Hôtes virtuels de cette batterie. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Prise en charge de la gestion et de l’authentification des sessions. |
| [/renders](#defining-page-renderers-renders) | Serveurs qui fournissent les pages rendues (généralement les instances de publication AEM). |
| [/filter](#configuring-access-to-content-filter) | Définit les URL auxquelles Dispatcher autorise l’accès. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configure l’accès aux URL de redirection vers un microsite. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Prise en charge du transfert des demandes de syndication. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configure le comportement de la mise en cache. |
| [/statistics](#configuring-load-balancing-statistics) | Définition des catégories de statistiques pour les calculs d’équilibrage de charge. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | Dossier qui contient les documents persistants. |
| [/health_check](#specifying-a-health-check-page) | URL à utiliser pour déterminer la disponibilité du serveur. |
| [/retryDelay](#specifying-the-page-retry-delay) | Délai avant une nouvelle tentative d’une connexion ayant échoué. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Pénalités qui affectent les statistiques pour les calculs d’équilibrage de charge. |
| [/failover](#using-the-failover-mechanism) | Renvoyez les requêtes vers différents rendus en cas d’échec de la requête d’origine. |
| [/auth_checker](permissions-cache.md) | Pour la mise en cache sensible aux autorisations, lisez [Mise en cache de contenu sécurisé](permissions-cache.md). |

## Spécifier une page par défaut (IIS uniquement) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>Le paramètre `/homepage` (IIS uniquement) ne fonctionne plus. Vous devez plutôt utiliser le [Module de réécriture d’URL IIS](https://learn.microsoft.com/fr-fr/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Si vous utilisez Apache, utilisez le module `mod_rewrite`. Voir la documentation sur le site web d’Apache pour en savoir plus sur `mod_rewrite` (par exemple, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Lorsque vous utilisez `mod_rewrite`, il est recommandé d’utiliser le drapeau « passthrough|PT » (passthrough vers le gestionnaire suivant) pour forcer le moteur de réécriture à définir le champ `uri` de la structure interne `request_rec` sur la valeur du champ `filename`.

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

## Spécifier les en-têtes HTTP à transférer {#specifying-the-http-headers-to-pass-through-clientheaders}

La propriété `/clientheaders` définit une liste d’en-têtes HTTP que Dispatcher transfère de la demande HTTP client vers le rendu (instance AEM).

Par défaut, Dispatcher transmet les en-têtes HTTP standard à l’instance AEM. Dans certains cas, vous souhaiterez peut-être transférer des en-têtes supplémentaires ou supprimer des en-têtes spécifiques :

* Ajoutez les en-têtes, tels que les en-têtes personnalisés, que votre instance AEM attend dans la requête HTTP.
* Supprimez les en-têtes, tels que les en-têtes d’authentification qui ne concernent que le serveur web.

Si vous personnalisez le groupe d’en-têtes à transférer, vous devez définir une liste complète d’en-têtes, y compris ceux qui sont normalement inclus par défaut.

Par exemple, une instance de Dispatcher qui gère les demandes d’activation de pages pour les instances de publication nécessite l’en-tête `PATH` dans la section `/clientheaders`. L’en-tête `PATH` permet la communication entre l’agent de réplication et Dispatcher.

Le code suivant est un exemple de configuration pour `/clientheaders` :

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

## Identifier les hôtes virtuels {#identifying-virtual-hosts-virtualhosts}

La propriété `/virtualhosts` définit une liste de toutes les combinaisons de nom d’hôte/URI que Dispatcher accepte pour cette batterie. Vous pouvez utiliser un astérisque (`*`) comme caractère générique. Les valeurs de la propriété /`virtualhosts` utilisent le format suivant :

```xml
[scheme]host[uri][*]
```

* `scheme` : (facultatif) soit `https://` soit `https://.`
* `host` : nom ou adresse IP de l’ordinateur hôte ainsi que le numéro de port, le cas échéant. (Voir[ https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23).)
* `uri` : (facultatif) chemin d’accès aux ressources.

L’exemple de configuration suivant traite des requêtes pour les domaines `.com` et `.ch` de myCompany, ainsi que tous les domaines de mySubDivision :

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

La configuration suivante traite *toutes* les requêtes :

```xml
   /virtualhosts
    {
    "*"
    }
```

### Résoudre l’hôte virtuel {#resolving-the-virtual-host}

Lorsque Dispatcher reçoit une requête HTTP ou HTTPS, il trouve la valeur d’hôte virtuel qui correspond le mieux aux en-têtes `host,`, `uri` et `scheme` de la requête. Dispatcher évalue les valeurs dans les propriétés `virtualhosts` dans l’ordre suivant :

* Dispatcher commence par la ferme de serveurs la moins élevée et progresse vers les valeurs plus élevées dans le fichier dispatcher.any.
* Pour chaque ferme de serveurs, Dispatcher commence par la valeur la plus élevée de la propriété `virtualhosts` et progresse vers les valeurs moins élevées de la liste.

Dispatcher détecte la valeur d’hôte virtuel correspondant le mieux comme suit :

* L’hôte virtuel rencontré en premier qui correspond aux parties `host`, `scheme` et `uri` de la demande est utilisé.
* Si aucune valeur `virtualhosts` ne comporte les parties `scheme` et `uri` qui correspondent aux parties `scheme` et `uri` de la requête, l’hôte virtuel rencontré en premier qui correspond à la partie `host` de la requête est utilisé.
* Si aucune valeur `virtualhosts` ne comporte une partie host qui correspond à la partie host de la demande, l’hôte virtuel le plus élevé de la batterie la plus élevée est utilisé.

Par conséquent, vous devez définir l’hôte virtuel par défaut dans la partie supérieure de la propriété `virtualhosts` dans la batterie la plus élevée de votre fichier `dispatcher.any`.

### Exemple de résolution du nom d’hôte virtuel {#example-virtual-host-resolution}

L’exemple suivant représente un extrait de code provenant d’un fichier `dispatcher.any` qui définit deux batteries de Dispatcher et chaque batterie définit une propriété `virtualhosts`.

```xml
/farms
  {
  /myProducts
    {
    /virtualhosts
      {
      "www.mycompany.com/products/*"
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
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

À l’aide de cet exemple, le tableau suivant montre les hôtes virtuels résolus pour les requêtes HTTP données :

| URL de la requête | Hôte virtuel résolu |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Activer des sessions sécurisées - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>Définissez `/allowAuthorized` sur `"0"` dans la section `/cache` pour activer cette fonctionnalité. Comme détaillé dans la section [Mise en cache lorsque l’authentification est utilisée](#caching-when-authentication-is-used), lorsque vous définissez `/allowAuthorized 0 `, les requêtes qui incluent des informations d’authentification ne sont **pas** mises en cache. Si une mise en cache sensible aux autorisations est requise, consultez la page [Mise en cache du contenu sécurisé](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/permissions-cache.html?lang=fr).

Créez une session sécurisée pour l’accès à la batterie de rendus, de sorte que les utilisateurs et les utilisatrices doivent ouvrir une session pour accéder à n’importe quelle page de la batterie. Après avoir ouvert une session, les utilisateurs et utilisatrices peuvent accéder à toutes les pages de la batterie. Voir [Création d’un groupe d’utilisateurs et d’utilisatrices fermé](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/cug.html?lang=fr#creating-the-user-group-to-be-used) pour plus d’informations sur l’utilisation de cette fonctionnalité avec les groupes d’utilisateurs et d’utilisatrices fermés. Consultez également la [Liste de contrôle de sécurité](/help/using/security-checklist.md) de Dispatcher avant la mise en ligne.

La propriété `/sessionmanagement` est une sous-propriété de `/farms`.

>[!CAUTION]
>
>Si des sections de votre site web utilisent des conditions d’accès différentes, vous devez définir plusieurs batteries.

**/sessionmanagement** comporte plusieurs sous-paramètres :

**/directory** (obligatoire)

Répertoire qui stocke les informations de session. Si le répertoire n’existe pas, il est créé.

>[!CAUTION]
>
> Lorsque vous configurez le sous-paramètre de répertoire, **ne pointez pas** vers le dossier racine (`/directory "/"`), car cela risque de poser de sérieux problèmes. Vous devez toujours spécifier le chemin d’accès au dossier où sont stockées les informations de session. Par exemple :

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (facultatif)

Mode de codage des informations de session. Utilisez `md5` pour le chiffrement à l’aide de l’algorithme md5, ou `hex` pour un codage hexadécimal. Si vous chiffrez les données de session, un utilisateur ou une utilisatrice ayant accès au système de fichiers ne peut pas lire le contenu de la session. La valeur par défaut est `md5`.

**/header** (facultatif)

Nom de l’en-tête HTTP ou du cookie qui stocke les informations d’autorisation. Si vous stockez les informations dans l’en-tête http, utilisez `HTTP:<header-name>`. Pour stocker les informations dans un cookie, utilisez `Cookie:<header-name>`. Si vous n’indiquez pas de valeur, `HTTP:authorization` est utilisé.

**/timeout** (facultatif)

Nombre de secondes avant que la session ne s’arrête lorsqu’elle a été utilisée en dernier. Si la valeur n’est pas spécifiée, `"800"` est utilisé, de sorte que la session expire un peu plus de 13 minutes après la dernière requête de l’utilisateur ou l’utilisatrice.

Une configuration peut, par exemple, être structurée comme suit :

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  /encode "md5"
  /header "HTTP:authorization"
  /timeout "800"
  }
```

## Définir des rendus de page {#defining-page-renderers-renders}

La propriété /renders définit l’URL à laquelle Dispatcher envoie les demandes de rendu d’un document. La section d’exemple suivante `/renders` identifie une seule instance AEM pour le rendu :

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

Indique le délai de connexion (en millisecondes) pour accéder à l’instance AEM. La valeur par défaut est `"0"`, ce qui amène Dispatcher à attendre indéfiniment.

**/receiveTimeout**

Indique la durée (en millisecondes) autorisée d’une réponse. La valeur par défaut est `"600000"`, ce qui amène Dispatcher à attendre pendant 10 minutes. Un paramètre de `"0"` élimine le délai d’expiration.

Si le délai est atteint pendant l’analyse des en-têtes de réponse, un état HTTP 504 (passerelle erronée) est renvoyé. Si le délai d’expiration est atteint pendant la lecture du corps de la réponse, Dispatcher renvoie la réponse incomplète au client. Il supprime également tout fichier du cache qui peut avoir été écrit.

**/ipv4**

Indique si Dispatcher utilise la fonction `getaddrinfo` (pour IPv6) ou la fonction `gethostbyname` (pour IPv4) pour obtenir l’adresse IP du rendu. Une valeur de 0 provoque l’utilisation de `getaddrinfo`. Une valeur de `1` provoque l’utilisation de `gethostbyname`. La valeur par défaut est `0`.

La fonction `getaddrinfo` renvoie une liste d’adresses IP. Dispatcher itère la liste des adresses jusqu’à établir une connexion TCP/IP. Par conséquent, la propriété `ipv4` est importante lorsque le nom d’hôte du rendu est associé à plusieurs adresses IP. L’hôte, en réponse à la fonction `getaddrinfo`, renvoie une liste d’adresses IP toujours dans le même ordre. Dans ce cas, vous devez utiliser la fonction `gethostbyname`, de sorte que l’adresse IP à laquelle Dispatcher se connecte soit randomisée.

Amazon Elastic Load Balancing (ELB) est un service qui répond à getaddrinfo avec une liste d’adresses IP potentiellement dans le même ordre.

**/secure**

Si la propriété `/secure` a une valeur de `"1"`, Dispatcher utilise HTTPS pour communiquer avec l’instance AEM. Pour plus de détails, voir aussi [Configuration de Dispatcher pour l’utilisation de SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Avec la version **4.1.6** de Dispatcher, vous pouvez configurer la propriété `/always-resolve` comme suit :

* Lorsqu’elle est définie sur `"1"`, le nom d’hôte sera résolu à chaque requête (Dispatcher ne met jamais d’adresse IP en cache). Il peut y avoir un léger impact sur les performances en raison de l’appel supplémentaire nécessaire pour obtenir les informations d’hôte pour chaque requête.
* Si la propriété n’est pas définie, l’adresse IP est mise en cache par défaut.

En outre, cette propriété peut être utilisée si vous rencontrez des problèmes de résolution d’adresse IP dynamique, comme illustré dans l’exemple suivant :

```xml
/renders {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configurer l’accès au contenu {#configuring-access-to-content-filter}

Utilisez la section `/filter` pour définir les requêtes HTTP que Dispatcher accepte. Les autres demandes sont renvoyées au serveur web avec le code d’erreur 404 (page introuvable). Si aucune section `/filter` n’existe, toutes les demandes sont acceptées.

**Remarque :** les demandes pour le [fichier stat](#naming-the-statfile) sont toujours rejetées.

>[!CAUTION]
>
>Voir [Liste de contrôle de sécurité de Dispatcher](security-checklist.md) pour en savoir plus sur la limitation de l’accès en utilisant Dispatcher. Pour plus de détails sur la sécurité de votre installation AEM, lisez aussi la [Liste de contrôle de sécurité d’AEM](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/security-checklist.html?lang=fr#security).

La section `/filter` se compose d’une série de règles qui refusent ou autorisent l’accès au contenu en fonction des modèles de la partie ligne de demande de la requête HTTP. Utilisez une stratégie de liste autorisée pour votre section `/filter` :

* Tout d’abord, refusez l’accès à tout.
* Autorisez l’accès au contenu selon les besoins.

>[!NOTE]
>
>Purgez le cache à chaque fois qu’une modification est apportée aux règles de filtrage.

### Définir un filtre  {#defining-a-filter}

Chaque élément de la section `/filter` comprend un type et un modèle associé à un élément spécifique de la ligne de requête ou à l’intégralité de la ligne de demande. Chaque filtre peut contenir les éléments suivants :

* **Type** : la propriété `/type` indique s’il faut accorder ou refuser l’accès aux requêtes qui correspondent au modèle. La valeur peut être `allow` ou `deny`.

* **Élément de la ligne de requête :** incluez `/method`, `/url`, `/query` ou `/protocol` ainsi qu’un modèle pour le filtrage des requêtes selon ces parties spécifiques de la ligne de demande de la requête HTTP. Le filtrage sur des éléments de la ligne de demande (plutôt que sur la ligne entière) correspond à la méthode préférée de filtrage.

* **Éléments avancés de la ligne de demande :** depuis Dispatcher 4.2.0, quatre nouveaux éléments de filtre sont disponibles. Ces nouveaux éléments sont `/path`, `/selectors`, `/extension` et `/suffix` respectivement. Incluez un ou plusieurs de ces éléments pour contrôler davantage les modèles d’URL.

>[!NOTE]
>
>Pour plus dʼinformations sur la partie de la ligne de demande à laquelle chacun de ces éléments fait référence, consultez la page wiki [Décomposition des URL Sling](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html).

* **Propriété glob** : la propriété `/glob` est utilisée pour la correspondance avec l’ensemble de la ligne de demande de la requête HTTP.

>[!CAUTION]
>
>Le filtrage avec les propriétés glob est obsolète dans Dispatcher. Ainsi, vous devez éviter de les utiliser dans les sections `/filter`, car cela peut entraîner des problèmes de sécurité. Donc, au lieu de :
>
>`/glob "* *.css *"`
>
>use
>
>`/url "*.css"`

#### Partie de la ligne de demande des requêtes HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 définit la [ligne de demande](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) comme suit :

`Method Request-URI HTTP-Version<CRLF>`

Les caractères `<CRLF>` représentent un retour chariot suivi d’un saut de ligne. L’exemple suivant est la ligne de demande reçue lorsqu’une personne demande la page en anglais américain du site WKND :

`GET /content/wknd/us/en.html HTTP.1.1<CRLF>`

Vos modèles doivent prendre en considération les caractères d’espace dans la ligne de demande ainsi que les caractères `<CRLF>`.

#### Guillemets doubles ou guillemets simples {#double-quotes-vs-single-quotes}

Lors de la création de vos règles de filtrage, utilisez des guillemets doubles `"pattern"` pour les motifs simples. Si vous utilisez Dispatcher 4.2.0 ou version ultérieure et que votre motif inclut une expression régulière, vous devez placer l’expression régulière `'(pattern1|pattern2)'` entre des guillemets simples.

#### Expressions régulières {#regular-expressions}

Après la version 4.2.0 de Dispatcher, vous pouvez inclure les expressions régulières POSIX étendues dans vos modèles de filtre.

#### Résoudre des problèmes de filtres {#troubleshooting-filters}

Si vos filtres ne se déclenchent pas comme prévu, activez l’option [Journalisation de trace](#trace-logging) sur Dispatcher pour déterminer le filtre qui intercepte la requête.

#### Exemple de filtre : Tout refuser {#example-filter-deny-all}

L’exemple suivant de section de filtre amène Dispatcher à refuser les requêtes pour tous les fichiers. Refusez l’accès à tous les fichiers, puis autorisez l’accès à des zones spécifiques.

```xml
/0001  { /type "deny" /url "*"  }
```

Les requêtes concernant une zone explicitement refusée renvoient le code d’erreur 404 (page introuvable).

#### Exemple de filtre : Refuser l’accès à des zones spécifiques {#example-filter-deny-access-to-specific-areas}

Les filtres permettent également de refuser l’accès à divers éléments, par exemple à des pages ASP et à des zones sensibles de l’instance de publication. Le filtre suivant refuse l’accès aux pages ASP :

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Exemple de filtre : Activer les requêtes POST {#example-filter-enable-post-requests}

L’exemple de filtre suivant permet d’envoyer des données de formulaire par la méthode POST :

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Exemple de filtre : Activer l’accès à la console de workflow {#example-filter-allow-access-to-the-workflow-console}

L’exemple suivant illustre un filtre utilisé pour autoriser l’accès externe à la console de workflow :

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Si votre instance de publication utilise un contexte d’application web (par exemple, publication), elle peut également être ajoutée à votre définition de filtre.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Si vous devez accéder à des pages uniques dans la zone restreinte, vous pouvez y autoriser l’accès. Par exemple, pour autoriser l’accès à l’onglet Archive dans la console de workflow, ajoutez la section suivante :

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Lorsque plusieurs modèles de filtres s’appliquent à une requête, le dernier modèle appliqué est celui en vigueur.

#### Exemple de filtre : Utilisation d’expressions régulières {#example-filter-using-regular-expressions}

Ce filtre permet des extensions dans des répertoires de contenu non publics à l’aide d’une expression régulière, définie ici entre guillemets simples :

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Exemple de filtre : Filtrer des éléments supplémentaires d’une URL de demande  {#example-filter-filter-additional-elements-of-a-request-url}

Voici un exemple de règle qui bloque la récupération de contenu à partir du chemin `/content` et de sa sous-arborescence, en utilisant des filtres par chemin, sélecteurs et extensions :

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy|sysview|docview|query|jcr:content|_jcr_content|search|childrenlist|ext|assets|assetsearch|[0-9-]+)'
        /extension '(json|xml|html|feed))'
        }
```

### Exemple de section /filter {#example-filter-section}

Lors de la configuration de Dispatcher, vous devez restreindre l’accès externe autant que possible. L’exemple suivant offre un accès minimal aux visiteurs et visiteuses externes :

* `/content`
* contenu divers tel que des conceptions et des bibliothèques clientes. Par exemple :

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Une fois que vous avez créé des filtres, [testez l’accès à la page](#testing-dispatcher-security) pour vérifier que votre instance AEM est sécurisée.

La section `/filter` suivante du fichier `dispatcher.any` peut être utilisée comme base dans votre [fichier de configuration Dispatcher](#dispatcher-configuration-files).

Cet exemple se base sur le fichier de configuration par défaut fourni avec Dispatcher. C’est un exemple d’utilisation dans un environnement de production. Les éléments précédés de `#` sont désactivés (commentés). Faites preuve de prudence si vous décidez d’activer l’un de ces éléments (en supprimant le `#` sur cette ligne). Cela peut avoir un impact sur la sécurité.

Vous devez refuser l’accès à tous les éléments, puis accorder l’accès à des éléments spécifiques (limités) :

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
      /0001  { /type "deny" /url "*"  }

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
>Lorsque le filtre est utilisé avec Apache, concevez les modèles d’URL de filtre selon la propriété DispatcherUseProcessedURL du module de Dispatcher. (Voir [Serveur web Apache - Configuration du serveur web Apache pour Dispatcher](dispatcher-install.md##apache-web-server-configure-apache-web-server-for-dispatcher)).

<!----
>[!NOTE]
>
>Filters `0030` and `0031` regarding Dynamic Media are applicable to AEM 6.0 and higher. -->

Tenez compte des recommandations suivantes si vous choisissez d’étendre l’accès :

* Désactivez l’accès externe à `/admin` si vous utilisez la version 5.4 de CQ ou une version antérieure.

* Il faut se montrer prudent lorsque vous accordez l’accès aux fichiers dans `/libs`. L’accès doit être autorisé sur une base individuelle.
* Refusez l’accès à la configuration de réplication afin qu’elle ne soit pas visible :

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Refusez l’accès au proxy inverse de Google Gadgets :

   * `/libs/opensocial/proxy*`

En fonction de l’installation, il peut y avoir des ressources supplémentaires sous `/libs`, `/apps` ou ailleurs. Faites en sorte qu’elles soient disponibles. Vous pouvez utiliser le fichier `access.log` en tant que méthode permettant de déterminer les ressources accessibles en externe.

>[!CAUTION]
>
>L’accès aux consoles et aux répertoires peut présenter un risque de sécurité pour les environnements de production. À moins que vous n’ayez des justifications explicites, ils doivent rester désactivés (commentés).

>[!CAUTION]
>
>Si vous [utilisez des rapports dans un environnement de publication](https://experienceleague.adobe.com/docs/experience-manager-65/administering/operations/reporting.html?lang=fr#using-reports-in-a-publish-environment), vous devez configurer Dispatcher pour refuser l’accès à `/etc/reports` aux visiteurs et visiteuses externes.

### Restreindre des chaînes de requête {#restricting-query-strings}

Depuis la version 4.1.5 de Dispatcher, utilisez la section `/filter` pour limiter les chaînes de requête. Il est fortement recommandé d’autoriser explicitement les chaînes de requête et d’exclure l’allocation générique par l’intermédiaire des éléments de filtre `allow`.

Une seule entrée peut avoir `glob` ou une combinaison de `method`, `url`, `query`, et `version`, mais pas les deux. L’exemple suivant autorise la chaîne de requête `a=*` et refuse toutes les autres chaînes de requête des URL qui se résolvent sur le nœud `/etc` :

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Si une règle contient `/query`, elle ne correspond qu’aux requêtes contenant une chaîne de requête et correspondant au modèle de requête fourni.
>
>Dans l’exemple ci-dessus, si les requêtes en direction de `/etc` qui ne comportent aucune chaîne de requête doivent également être autorisées, les règles suivantes sont requises :
>

```xml
/filter {  
>/0001 { /type "deny" /method "*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type "deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Tester la sécurité de Dispatcher {#testing-dispatcher-security}

Les filtres de Dispatcher doivent bloquer l’accès aux pages et scripts suivants sur les instances de publication AEM. Utilisez un navigateur web pour tenter d’ouvrir les pages suivantes en tant que visiteur ou visiteuse du site et vérifier qu’un code 404 est renvoyé. Si un autre résultat est obtenu, ajustez vos filtres.

Le rendu de page normal doit s’afficher pour `/content/add_valid_page.html?debug=layout`.

* `/admin`
* `/system/console`
* `/dav/crx.default`
* `/crx`
* `/bin/crxde/logs`
* `/jcr:system/jcr:versionStorage.json`
* `/_jcr_system/_jcr_versionStorage.json`
* `/libs/wcm/core/content/siteadmin.html`
* `/libs/collab/core/content/admin.html`
* `/libs/cq/ui/content/dumplibs.html`
* `/var/linkchecker.html`
* `/etc/linkchecker.html`
* `/home/users/a/admin/profile.json`
* `/home/users/a/admin/profile.xml`
* `/libs/cq/core/content/login.json`
* `/content/../libs/foundation/components/text/text.jsp`
* `/content/.{.}/libs/foundation/components/text/text.jsp`
* `/apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata`
* `/libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet`
* `/content.pages.json`
* `/content.languages.json`
* `/content.blueprint.json`
* `/content.-1.json`
* `/content.10.json`
* `/content.infinity.json`
* `/content.tidy.json`
* `/content.tidy.-1.blubber.json`
* `/content/dam.tidy.-100.json`
* `/content/content/geometrixx.sitemap.txt `
* `/content/add_valid_page.query.json?statement=//*`
* `/content/add_valid_page.qu%65ry.js%6Fn?statement=//*`
* `/content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)`
* `/content/add_valid_path_to_a_page/_jcr_content.json`
* `/content/add_valid_path_to_a_page/jcr:content.json`
* `/content/add_valid_path_to_a_page/_jcr_content.feed`
* `/content/add_valid_path_to_a_page/jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename._jcr_content.feed`
* `/content/add_valid_path_to_a_page/pagename.jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename.docview.xml`
* `/content/add_valid_path_to_a_page/pagename.docview.json`
* `/content/add_valid_path_to_a_page/pagename.sysview.xml`
* `/etc.xml`
* `/content.feed.xml`
* `/content.rss.xml`
* `/content.feed.html`
* `/content/add_valid_page.html?debug=layout`
* `/projects`
* `/tagging`
* `/etc/replication.html`
* `/etc/cloudservices.html`
* `/welcome`

Pour déterminer si l’accès en écriture anonyme est activé, lancez la commande suivante dans un terminal ou une invite de commande. Vous ne devriez pas être en mesure d’écrire des données sur le nœud.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Pour tenter d’invalider le cache de Dispatcher et de vous assurer que vous recevez une réponse 403 du code, exécutez la commande suivante dans un terminal ou une invite de commande :

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Activer l’accès aux URL de redirection {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configurez Dispatcher pour activer l’accès aux URL de redirection configurées pour vos pages AEM.

Lorsque l’accès aux URL de redirection est activé, Dispatcher appelle régulièrement un service qui s’exécute sur l’instance de rendu pour obtenir une liste des URL de redirection. Dispatcher stocke cette liste dans un fichier local. Lorsqu’une demande de page est refusée en raison d’un filtre de la section `/filter`, Dispatcher consulte la liste des URL de redirection vers un microsite. Si l’URL refusée se trouve dans la liste, Dispatcher autorise l’accès à l’URL de redirection vers un microsite.

Pour autoriser l’accès aux URL de redirection vers un microsite, ajoutez une section `/vanity_urls` à la section `/farms`, comme illustré dans l’exemple suivant :

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

La section `/vanity_urls` contient les propriétés suivantes :

* `/url` : chemin d’accès au service URL de redirection vers un microsite qui s’exécute sur une instance de rendu. La valeur de cette propriété doit être `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file` : chemin d’accès au fichier local sur lequel Dispatcher stocke la liste des URL de redirection vers un microsite. Vérifiez que Dispatcher dispose d’un accès en écriture à ce fichier.
* `/delay` : (en secondes) durée entre les appels au service URL de redirection vers un microsite.

>[!NOTE]
>
>Si votre rendu est une instance AEM, vous devez installer le [package VanityURLS-Components depuis la Distribution logicielle](https://experience.adobe.com/#/downloads/content/software-distribution/en/aem.html?package=/content/software-distribution/en/details.html/content/dam/aem/public/adobe/packages/granite/vanityurls-components) pour activer le service d’URL de redirection. (Voir [Distribution logicielle](https://experienceleague.adobe.com/docs/experience-manager-65/administering/contentmanagement/package-manager.html?lang=fr#software-distribution) pour plus d’informations.)

Utilisez la procédure suivante pour autoriser l’accès aux URL de redirection.

1. Si votre service de rendu est une instance AEM, installez le package `com.adobe.granite.dispatcher.vanityurl.content` sur l’instance de publication (voir la remarque ci-dessus).
1. Pour chaque URL de redirection vers un microsite que vous avez configurée pour une page d’AEM ou de CQ, assurez-vous que la configuration de [`/filter`](#configuring-access-to-content-filter) refuse l’URL. Si nécessaire, ajoutez un filtre qui refuse l’URL.
1. Ajoutez la section `/vanity_urls` sous la section `/farms`.
1. Redémarrez le serveur web Apache.

## Transfert des demandes de syndication - /propagateSyndPost  {#forwarding-syndication-requests-propagatesyndpost}

Les requêtes de syndication sont généralement prévues uniquement pour Dispatcher. Par défaut, elles ne sont donc pas envoyées au moteur de rendu (par exemple, une instance AEM).

Si nécessaire, définissez la propriété `/propagateSyndPost` sur « `"1"` » pour transférer les requêtes de syndication à Dispatcher. Si les requêtes POST sont définies, vous devez vous assurer qu’elles ne sont pas refusées dans la section filter.

## Configurer le cache de Dispatcher - /cache {#configuring-the-dispatcher-cache-cache}

La section `/cache` contrôle la manière dont Dispatcher met en cache les documents. Configurez plusieurs sous-propriétés pour implémenter vos stratégies de mise en cache :

* `/docroot`
* `/statfile`
* `/serveStaleOnError`
* `/allowAuthorized`
* `/rules`
* `/statfileslevel`
* `/invalidate`
* `/invalidateHandler`
* `/allowedClients`
* `/ignoreUrlParams`
* `/headers`
* `/mode`
* `/gracePeriod`
* `/enableTTL`

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

### Indication du répertoire du cache  {#specifying-the-cache-directory}

La propriété `/docroot` identifie le répertoire dans lequel les fichiers mis en cache sont stockés.

>[!NOTE]
>
>La valeur doit être exactement le même chemin d’accès que la racine du document du serveur web, de sorte que Dispatcher et le serveur web traitent les mêmes fichiers.\
>Le serveur web est chargé de diffuser le code d’état correct lorsque le fichier en cache de Dispatcher est utilisé. C’est pourquoi il est important qu’il puisse également le trouver.

Si vous utilisez plusieurs batteries, chacune doit utiliser une racine de document différente.

### Nommer le fichier stat {#naming-the-statfile}

La propriété `/statfile` identifie le fichier à utiliser en tant que fichier stat. Dispatcher utilise ce fichier pour enregistrer l’heure de la mise à jour de contenu la plus récente. Le fichier stat peut être n’importe quel fichier sur le serveur web.

Le fichier stat n’a aucun contenu. Lorsque le contenu est mis à jour, Dispatcher met à jour la date et l’heure. Le fichier stat par défaut est nommé `.stat` et est stocké dans le docroot. Dispatcher bloque l’accès au fichier stat.

>[!NOTE]
>
>Si `/statfileslevel` est configuré, Dispatcher ignore la propriété `/statfile` et utilise `.stat` comme nom.

### Distribuer des documents obsolètes lorsque des erreurs se produisent {#serving-stale-documents-when-errors-occur}

La propriété `/serveStaleOnError` contrôle si Dispatcher renvoie des documents invalidés lorsque le serveur de rendu renvoie une erreur. Par défaut, lorsqu’un fichier stat est modifié et invalide un contenu mis en cache, Dispatcher supprime le contenu mis en cache la prochaine fois qu’il est demandé.

Si `/serveStaleOnError` est définit sur « `"1"` », Dispatcher ne supprime pas le contenu invalidé du cache à moins que le serveur de rendu ne renvoie une réponse réussie. Une réponse 5xx d’AEM ou une expiration du délai de connexion entraîne Dispatcher à diffuser du contenu obsolète et à répondre avec l’état HTTP 111 (Échec de la revalidation).

### Mettre en cache lors de l’utilisation de l’authentification {#caching-when-authentication-is-used}

La propriété `/allowAuthorized` contrôle si les requêtes contenant les informations d’authentification suivantes sont mises en cache :

* En-tête `authorization`.
* Cookie appelé `authorization`.
* Cookie appelé `login-token`.

Par défaut, les requêtes contenant ces informations d’authentification ne sont pas mises en cache, car l’authentification n’est pas effectuée lorsqu’un document mis en cache est renvoyé au client. Cette configuration empêche Dispatcher de diffuser des documents mis en cache aux utilisateurs et utilisatrices qui ne disposent pas des droits nécessaires.

Toutefois, si vos besoins permettent la mise en cache de documents authentifiés, définissez `/allowAuthorized` sur un :

`/allowAuthorized "1"`

>[!NOTE]
>
>Pour activer la gestion de sessions (à l’aide de la propriété `/sessionmanagement`), la propriété `/allowAuthorized` doit être définie sur `"0"`.

### Spécifier les documents à mettre en cache {#specifying-the-documents-to-cache}

La propriété `/rules` contrôle les documents qui sont mis en cache selon le chemin d’accès au document. Quelle que soit la propriété `/rules`, Dispatcher ne procède jamais à la mise en cache d’un document dans les cas suivants :

* Si l’URI de requête contient un point d’interrogation (`?`).
   * Cela indique généralement une page dynamique, par exemple un résultat de recherche qui n’a pas besoin d’être mis en cache.
* L’extension de fichier est manquante.
   * Le serveur web a besoin de l’extension pour déterminer le type de document (type MIME).
* L’en-tête d’authentification est défini (configurable).
* Si l’instance AEM répond avec les en-têtes suivants :

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Les méthodes GET ou HEAD (pour les en-têtes HTTP) sont mises en cache par Dispatcher. Pour plus d’informations sur la mise en cache des en-têtes de réponse, voir [Mise en cache des en-têtes de réponse HTTP](#caching-http-response-headers).

Chaque élément de la propriété `/rules` inclut un motif [`glob`](#designing-patterns-for-glob-properties) et un type :

* Le motif `glob` est utilisé pour faire correspondre le chemin d’accès au document.
* Le type indique si les documents qui correspondent au motif `glob` doivent être mis en cache. La valeur peut être `allow` (pour mettre en cache le document) ou `deny` (pour toujours rendre le document).

Si vous ne disposez pas de pages dynamiques (au-delà des pages déjà exclues par les règles ci-dessus), vous pouvez configurer Dispatcher pour tout mettre en cache. La section des règles se présente comme suit :

```xml
/rules
  {
    /0000  {  /glob "*"   /type "allow" }
  }
```

Pour plus d’informations sur les propriétés glob, voir [Création de modèles pour les propriétés glob](#designing-patterns-for-glob-properties).

Si certaines sections de votre page sont dynamiques (une application d’actualités, par exemple) ou au sein d’un groupe d’utilisateurs et d’utilisatrices fermé, vous pouvez définir des exceptions :

>[!NOTE]
>
>Ne mettez pas en cache les groupes d’utilisateurs et d’utilisatrices fermés, car les droits d’utilisation ne sont pas vérifiés pour détecter les pages mises en cache.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }
  }
```

**Compression**

Sur les serveurs web Apache, vous pouvez compresser les documents mis en cache. La compression permet à Apache de renvoyer le document sous forme compressée si cela est demandé par le client. La compression se fait automatiquement en activant le module Apache `mod_deflate`, par exemple :

```xml
AddOutputFilterByType DEFLATE text/plain
```

Le module est installé par défaut avec Apache 2.x.

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

### Invalider des fichiers par niveau de dossier {#invalidating-files-by-folder-level}

Utilisez la propriété `/statfileslevel` pour invalider une sélection de fichiers mis en cache en fonction de leur chemin d’accès :

* Dispatcher crée des fichiers`.stat` dans chaque dossier du dossier docroot au niveau que vous indiquez. Le dossier docroot correspond au niveau 0.
* Les fichiers sont invalidés en touchant le fichier `.stat`. La date de dernière modification du fichier `.stat` est comparée à celle d’un document mis en cache. Le document est à nouveau récupéré si le fichier `.stat` est plus récent.

* Lorsqu’un fichier situé à un certain niveau est invalidé, **tous** les fichiers `.stat` de docroot **jusqu’au** niveau du fichier invalidé ou du fichier `statsfilevel` configuré (celui qui est le plus petit) seront touchés.

   * Par exemple : si vous définissez la propriété `statfileslevel` sur 6 et qu’un fichier est invalidé au niveau 5, tous les fichiers `.stat` de docroot jusqu’au niveau 5 seront touchés. Si vous continuez avec cet exemple, si un fichier est invalidé au niveau 7, tous les fichiers `stat` de docroot jusqu’au niveau 6 sont touchés (depuis `/statfileslevel = "6"`).

Seules les ressources **situées le long du chemin** vers le fichier invalidé sont affectées. Prenons l’exemple suivant : un site web utilise la structure `/content/myWebsite/xx/.`. Si vous définissez `statfileslevel` sur 3, un fichier `.stat` est créé comme suit :

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Lorsqu’un fichier dans `/content/myWebsite/xx` est invalidé, tous les fichiers `.stat` de docroot à `/content/myWebsite/xx` sont modifiés. Ce scénario s’applique uniquement pour `/content/myWebsite/xx` et non par exemple pour `/content/myWebsite/yy` ou `/content/anotherWebSite`.

>[!NOTE]
>
>L’invalidation peut être empêchée en envoyant un en-tête `CQ-Action-Scope:ResourceOnly` supplémentaire. Cette méthode peut être utilisée pour purger des ressources particulières sans invalider d’autres parties du cache. Pour plus de détails, voir [cette page](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) et [Invalidation manuelle du cache de Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/page-invalidate.html?lang=fr#configuring).

>[!NOTE]
>
>Remarque : si vous indiquez une valeur pour la propriété `/statfileslevel`, la propriété `/statfile` sera ignorée.

### Invalidation automatique des fichiers mis en cache {#automatically-invalidating-cached-files}

La propriété `/invalidate` définit les documents qui sont automatiquement invalidés lorsque le contenu est mis à jour.

Avec l’invalidation automatique, Dispatcher ne supprime pas les fichiers mis en cache après une mise à jour du contenu, mais vérifie leur validité la prochaine fois qu’ils sont demandés. Les documents du cache qui ne sont pas invalidés automatiquement restent dans le cache jusqu’à ce qu’une mise à jour du contenu les supprime explicitement.

L’invalidation automatique est généralement utilisée pour les pages HTML. Les pages HTML contiennent souvent des liens vers d’autres pages, ce qui rend difficile de déterminer si une mise à jour du contenu affecte une page. Pour vous assurer que toutes les pages pertinentes sont invalidées lorsque le contenu est mis à jour, invalidez automatiquement toutes les pages HTML. La configuration suivante invalide toutes les pages HTML :

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Pour plus d’informations sur les propriétés glob, voir [Création de modèles pour les propriétés glob](#designing-patterns-for-glob-properties).

Cette configuration provoque l’activité suivante lorsque `/content/wknd/us/en` est activé :

* Tous les fichiers avec le motif en.* sont supprimés du dossier `/content/wknd/us`.
* Le dossier `/content/wknd/us/en./_jcr_content` est supprimé.
* Tous les autres fichiers correspondant à la configuration `/invalidate` ne sont pas immédiatement supprimés. Ces fichiers sont supprimés lors de la requête suivante. Dans l’exemple, `/content/wknd.html` n’est pas supprimé ; il est supprimé lorsque `/content/wknd.html` est demandé.

Si vous proposez des fichiers PDF et ZIP générés automatiquement au téléchargement, vous devrez peut-être également invalider automatiquement ces fichiers. Voici un exemple de configuration :

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

L’intégration d’AEM à Adobe Analytics fournit des données de configuration dans un fichier `analytics.sitecatalyst.js` sur votre site web. Le fichier d’exemple `dispatcher.any` fourni avec Dispatcher comprend la règle d’invalidation suivante pour ce fichier :

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Utiliser des scripts d’invalidation personnalisés {#using-custom-invalidation-scripts}

La propriété `/invalidateHandler` vous permet de définir un script appelé pour chaque requête d’invalidation reçue par Dispatcher.

Elle est appelée avec les arguments suivants :

* Poignée - Le chemin d’accès au contenu qui est invalidé.
* Action - L’action de réplication (par exemple, Activer, Désactiver).
* Portée de l’action - La portée de l’action de réplication (vide, sauf si un en-tête de `CQ-Action-Scope: ResourceOnly` est envoyé). Pour plus d’informations, voir [Invalider des pages mises en cache depuis AEM](page-invalidate.md).

Cette méthode peut être utilisée pour couvrir plusieurs cas d’utilisation différents. Par exemple, l’invalidation d’autres caches spécifiques à l’application, ou la gestion de cas où l’URL externalisée d’une page, ainsi que sa place dans le docroot, ne correspondent pas au chemin d’accès au contenu.

L’exemple de script ci-dessous consigne chaque requête d’invalidation à un fichier.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### Exemple de script de gestionnaire d’invalidation {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limiter les clients qui peuvent vider le cache {#limiting-the-clients-that-can-flush-the-cache}

La propriété `/allowedClients` définit les clients spécifiques autorisés à vider le cache. Les modèles d’extension métacaractère sont comparés à l’adresse IP.

L’exemple suivant :

1. refuse l’accès à tout client ;
1. autorise explicitement l’accès à localhost.

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Pour plus d’informations sur les propriétés glob, voir [Création de modèles pour les propriétés glob](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Il est recommandé de définir la propriété `/allowedClients`.
>
>Dans le cas contraire, n’importe quel client peut émettre un appel pour effacer le cache. Si cela est fait à plusieurs reprises, les performances du site peuvent être considérablement affectées.

### Ignorer les paramètres d’URL {#ignoring-url-parameters}

La section `ignoreUrlParams` définit les paramètres d’URL qui sont ignorés lorsque vous déterminez si une page est mise en cache ou exclue du cache :

* Lorsqu’une URL de requête contient des paramètres qui sont tous ignorés, la page est mise en cache.
* Lorsqu’une URL de requête contient un ou plusieurs paramètres qui ne sont pas ignorés, la page n’est pas mise en cache.

Si un paramètre est ignoré pour une page, la page est mise en cache lorsqu’elle est demandée pour la première fois. Les requêtes suivantes sont diffusées à la page mise en cache, quelle que soit la valeur du paramètre dans la requête.

>[!NOTE]
>
>Il est recommandé de configurer le paramètre `ignoreUrlParams` comme dans une liste autorisée. Ainsi, tous les paramètres de requête sont ignorés. Seuls les paramètres de requête connus ou attendus seront acceptés. Pour plus d’informations et pour consulter des exemples, voir [cette page](https://github.com/adobe/aem-dispatcher-optimizer-tool/blob/main/docs/Rules.md#dot---the-dispatcher-publish-farm-cache-should-have-its-ignoreurlparams-rules-configured-in-an-allow-list-manner).

Pour spécifier les paramètres qui sont ignorés, ajoutez les règles glob à la propriété `ignoreUrlParams` :

* Pour mettre une page en cache en dépit de la requête contenant un paramètre d’URL, créez une propriété glob qui autorise le paramètre (à ignorer).
* Pour empêcher la mise en cache de la page, créez une propriété glob qui refuse le paramètre (à ignorer).

>[!NOTE]
>
>Lorsque vous configurez la propriété glob, faites-la correspondre au nom du paramètre de requête. Par exemple, pour ignorer le paramètre « p1 » de l’URL suivante `http://example.com/path/test.html?p1=test&p2=v2`, la propriété glob doit être :
> `/0002 { /glob "p1" /type "allow" }`

Dans l’exemple suivant, Dispatcher ignore tous les paramètres, à l’exception du paramètre `nocache`. Par conséquent, les URL de requête qui incluent le paramètre `nocache` ne sont jamais mises en cache par Dispatcher :

```xml
/ignoreUrlParams
{
    # ignore-all-url-parameters-by-dispatcher-and-requests-are-cached
    /0001 { /glob "*" /type "allow" }
    # allow-the-url-parameter-nocache-to-bypass-dispatcher-on-every-request
    /0002 { /glob "nocache" /type "deny" }
}
```

Si l’on reprend l’exemple de configuration `ignoreUrlParams` ci-dessus, la requête HTTP suivante provoque la mise en cache de la page, car le paramètre `willbecached` est ignoré :

```xml
GET /mypage.html?willbecached=true
```

Si l’on reprend l’exemple de configuration `ignoreUrlParams`, la requête HTTP suivante **ne provoque pas** la mise en cache de la page, car le paramètre `nocache` n’est pas ignoré :

```xml
GET /mypage.html?nocache=true
GET /mypage.html?nocache=true&willbecached=true
```

Pour plus d’informations sur les propriétés glob, voir [Création de modèles pour les propriétés glob](#designing-patterns-for-glob-properties).

### Mettre en cache des en-têtes de réponse HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Cette fonction est disponible avec la version **4.1.11** de Dispatcher.

La propriété `/headers` permet de définir les types d’en-têtes HTTP qui vont être mis en cache par Dispatcher. Lors de la première requête à une ressource non mise en cache, tous les en-têtes correspondant à l’une des valeurs configurées (voir l’exemple de configuration ci-dessous) sont stockés dans un fichier séparé, à côté du fichier cache. Lors des requêtes ultérieures à la ressource mise en cache, les en-têtes stockés sont ajoutés à la réponse.

Voici ci-dessous un exemple de la configuration par défaut :

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
>Les caractères d’extension métacaractère de fichier ne sont pas autorisés. Pour plus de détails, voir [Conception de modèles pour les propriétés glob](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Si Dispatcher est requis pour stocker et livrer les en-têtes de réponse ETag d’AEM, procédez comme suit :
>
>* Ajoutez le nom de l’en-tête dans la section`/cache/headers`.
>* Ajoutez la [directive Apache](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) suivante dans la section relative à Dispatcher :
>
>```xml
>FileETag none
>```

### Autorisations de fichier de cache de Dispatcher {#dispatcher-cache-file-permissions}

La propriété `mode` définit les autorisations de fichier appliquées aux nouveaux répertoires et fichiers du cache. Ce paramètre est limité par le `umask` du processus appelant. Il s’agit d’un nombre octal construit à partir de la somme d’une ou de plusieurs des valeurs suivantes :

* `0400` Autoriser la lecture par la personne propriétaire.
* `0200` Autoriser l’écriture par la personne propriétaire.
* `0100` Autoriser la personne propriétaire à effectuer une recherche dans les répertoires.
* `0040` Autoriser la lecture par les personnes membres du groupe.
* `0020` Autoriser l’écriture par les personnes membres du groupe.
* `0010` Autoriser les personnes membres du groupe à effectuer une recherche dans le répertoire.
* `0004` Autoriser la lecture par d’autres personnes.
* `0002` Autoriser l’écriture par d’autres personnes.
* `0001` Autoriser d’autres personnes à effectuer une recherche dans le répertoire.

La valeur par défaut est `0755`. Elle donne des autorisations en lecture, écriture et recherche à la personne propriétaire et des autorisations en lecture et en recherche au groupe et à d’autres personnes.

### Limiter le fichier .stat touchant {#throttling-stat-file-touching}

Si la propriété `/invalidate` est définie par défaut, chaque activation invalide effectivement tous les fichiers `.html` (si leur chemin correspond à la section `/invalidate`). Sur un site web avec un trafic considérable, de multiples activations ultérieures augmenteront la charge du processeur sur le serveur principal. Dans un tel scénario, il serait souhaitable de « limiter » le fichier `.stat` touchant pour que le site web reste réactif. Vous pouvez le faire en utilisant la propriété `/gracePeriod`.

La propriété `/gracePeriod` définit le nombre de secondes pendant lesquelles une ressource obsolète à invalidation automatique peut toujours être servie à partir du cache après la dernière activation. La propriété peut être utilisée dans une configuration où un lot d’activations invaliderait de manière répétée le cache entier. La valeur recommandée est de 2 secondes.

Pour plus de détails, vous pouvez aussi lire les sections `/invalidate` et `/statfileslevel` ci-dessus.

### Configurer une invalidation temporelle du cache - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

L’invalidation temporelle du cache dépend de la propriété `/enableTTL` et de la présence d’en-têtes d’expiration standard de la norme HTTP. Si vous définissez la propriété sur 1 (`/enableTTL "1"`), elle évalue les en-têtes de réponse du serveur principal. Si les en-têtes contiennent une date `Cache-Control`, `max-age` ou `Expires`, un fichier vide auxiliaire en regard du fichier mis en cache est créé, avec l’heure de modification égale à la date d’expiration. Lorsque le fichier mis en cache est demandé après l’heure de modification, il est automatiquement redemandé depuis le serveur principal.

Avant la version 4.3.5 de Dispatcher, la logique d’invalidation TTL ne reposait que sur la valeur TTL configurée. Avec Dispatcher 4.3.5, la TTL définie **et** les règles d’invalidation du cache de Dispatcher sont prises en compte. Par conséquent, pour un fichier mis en cache :

1. Si `/enableTTL` est défini sur 1, l’expiration du fichier est vérifiée. Si le fichier a expiré conformément à la TTL définie, aucune autre vérification n’est effectuée et le fichier mis en cache est redemandé depuis le serveur principal.
2. Si le fichier n’a pas expiré, ou si `/enableTTL` n’est pas configuré, les règles d’invalidation du cache standard sont appliquées, telles que les règles définies par [/statfileslevel](#invalidating-files-by-folder-level) et [/invalidate](#automatically-invalidating-cached-files). Ce flux signifie que Dispatcher peut invalider les fichiers pour lesquels la TTL n’a pas expiré.

Cette nouvelle mise en œuvre prend en charge les cas d’utilisation où les fichiers ont une TTL plus longue (par exemple, sur le réseau CDN) mais peuvent toujours être invalidés même si la TTL n’a pas expiré. Elle privilégie l’actualisation du contenu par rapport au taux d’accès au cache sur Dispatcher.

À l’inverse, si **seule** la logique d’expiration doit être appliquée à un fichier, définissez alors `/enableTTL` sur 1 et excluez ce fichier du mécanisme d’invalidation du cache standard. Par exemple, vous pouvez effectuer les actions suivantes :

* Pour ignorer le fichier, configurez les [règles d’invalidation](#automatically-invalidating-cached-files) dans la section du cache. Dans l’extrait de code ci-dessous, tous les fichiers se terminant par `.example.html` sont ignorés et expirent uniquement lorsque la TTL définie est dépassée.

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
   /0002  { /glob "*.example.html" /type "deny" }
  }
```

* Concevez la structure du contenu de telle sorte que vous puissiez définir un [/statfilelevel](#invalidating-files-by-folder-level) élevé et que le fichier ne soit pas automatiquement invalidé.

Cela garantit que l’invalidation de fichier `.stat` n’est pas utilisée et que seule l’expiration TTL est active pour les fichiers spécifiés.

>[!NOTE]
>
>Gardez à l’esprit que le paramétrage de `/enableTTL` sur 1 active la mise en cache TTL uniquement du côté de Dispatcher. Les informations TTL contenues dans le fichier supplémentaire (voir ci-dessus) ne sont donc fournies à aucun autre useragent demandant un tel type de fichier à Dispatcher. Si vous souhaitez fournir des en-têtes de mise en cache à des systèmes en aval comme un réseau de diffusion de contenu ou un navigateur, vous devez configurer la section `/cache/headers` en conséquence.

>[!NOTE]
>
>Cette fonctionnalité est disponible dans la version **4.1.11** ou ultérieure de Dispatcher.

## Configurer l’équilibrage de charge - /statistics {#configuring-load-balancing-statistics}

La section `/statistics` définit les catégories de fichiers pour lesquelles Dispatcher note la réactivité de chaque rendu. Dispatcher utilise les scores pour déterminer sur quel rendu envoyer une requête.

Chaque catégorie que vous créez définit un motif glob. Dispatcher compare l’URI du contenu demandé à ces modèles afin de déterminer la catégorie du contenu demandé :

* L’ordre des catégories détermine l’ordre dans lequel elles sont comparées à l’URI.
* Le premier motif de catégorie correspondant à l’URI est la catégorie du fichier. Aucun autre motif de catégorie n’est évalué.

Dispatcher prend en charge huit catégories de statistiques au maximum. Si vous définissez plus de huit catégories, seules les 8 premières sont utilisées.

**Sélection de rendu**

Chaque fois que Dispatcher requiert une page rendue, il utilise l’algorithme suivant pour sélectionner le rendu :

1. Si la demande contient le nom du rendu dans un cookie `renderid`, Dispatcher utilise ce rendu.
1. Si la demande n’inclut pas de cookie `renderid`, Dispatcher compare les statistiques de rendu :

   1. Dispatcher détermine la catégorie de l’URI demandé.
   1. Dispatcher détermine quel rendu a le score de réponse le plus bas pour cette catégorie et sélectionne ce rendu.

1. Si aucun rendu n’est encore sélectionné, utilisez le premier rendu de la liste.

Le score de la catégorie d’un rendu est basé sur les temps de réponse précédents, et sur les connexions précédentes ayant échoué et réussi tentées par Dispatcher. Pour chaque tentative, le score de la catégorie de l’URI demandé est mis à jour.

>[!NOTE]
>
>Si vous n’utilisez pas l’équilibrage de charge, vous pouvez ignorer cette section.

### Définition des catégories de statistiques  {#defining-statistics-categories}

Définissez une catégorie pour chaque type de document pour lequel vous souhaitez conserver les statistiques de sélection du rendu. La section `/statistics` contient une section `/categories`. Pour définir une catégorie, ajoutez une ligne sous la section `/categories` au format suivant :

`/name { /glob "pattern"}`

La catégorie `name` doit être unique à la ferme de serveurs. `pattern` est décrit dans la section [Conception de modèles pour les propriétés glob](#designing-patterns-for-glob-properties).

Pour déterminer la catégorie d’un URI, Dispatcher compare l’URI à chaque motif de catégorie jusqu’à ce qu’une correspondance soit trouvée. Dispatcher commence par la première catégorie de la liste et continue dans l’ordre. Par conséquent, placez d’abord les catégories avec des motifs plus spécifiques.

Par exemple, le fichier par défaut `dispatcher.any` de Dispatcher définit une catégorie HTML et une catégorie Autres. La catégorie HTML est plus précise et, de ce fait, elle s’affiche en premier :

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

La propriété `/unavailablePenalty` définit la durée (en dixième de seconde) qui est appliquée aux statistiques de rendu lorsqu’une connexion au rendu échoue. Dispatcher ajoute la durée à la catégorie de statistiques correspondant à l’URI demandé.

Par exemple, la pénalité est appliquée lorsque la connexion TCP/IP au nom d’hôte/port indiqué ne peut pas être établie car AEM ne fonctionne pas (et ne lit pas) ou en raison d’un problème lié au réseau.

La propriété `/unavailablePenalty` est un enfant direct de la section `/farm` (également enfant de la section `/statistics`).

Si aucune propriété `/unavailablePenalty` n’existe, la valeur `"1"` est utilisée.

```xml
/unavailablePenalty "1"
```

## Identifier un dossier de connexions persistantes - /stickyConnectionsFor  {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

La propriété `/stickyConnectionsFor` définit un dossier contenant des documents persistants. Cette propriété est accessible à l’aide de l’URL. Dispatcher envoie toutes les requêtes, d’un utilisateur ou d’une utilisatrice unique qui se trouve dans ce dossier, à la même instance de rendu. Les connexions persistantes garantissent que les données de session sont présentes et cohérentes pour tous les documents. Ce mécanisme utilise le cookie `renderid`.

L’exemple suivant définit une connexion persistante au dossier /products :

```xml
/stickyConnectionsFor "/products"
```

Lorsqu’une page est constituée de contenu provenant de plusieurs nœuds de contenu, incluez la propriété `/paths` répertoriant les chemins d’accès au contenu. Par exemple, une page contient du contenu provenant de `/content/image`, `/content/video` et `/var/files/pdfs`. La configuration suivante active les connexions persistantes pour tout le contenu de la page :

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

Lorsque les connexions persistantes sont activées, le module de Dispatcher définit le cookie `renderid`. Ce cookie n’est pas doté de l’indicateur `httponly`, qui doit être ajouté pour renforcer la sécurité. Pour ajouter l’indicateur `httponly`, définissez la propriété `httpOnly` dans le nœud `/stickyConnections` d’un fichier de configuration `dispatcher.any`. La valeur de la propriété (`0` ou `1`) définit si le cookie `renderid` se fait adjoindre l’attribut `HttpOnly`. La valeur par défaut est `0`, ce qui signifie que l’attribut n’est pas ajouté.

Pour plus d’informations sur l’indicateur `httponly`, consultez [cette page](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Lorsque les connexions persistantes sont activées, le module de Dispatcher définit le cookie `renderid`. Ce cookie n’est pas doté de l’indicateur `secure`, qui doit être ajouté pour renforcer la sécurité. Pour ajouter l’indicateur `secure`, définissez la propriété `secure` dans le nœud `/stickyConnections` d’un fichier de configuration `dispatcher.any`. La valeur de la propriété (`0` ou `1`) définit si le cookie `renderid` se fait adjoindre l’attribut `secure`. La valeur par défaut est `0`, ce qui signifie que l’attribut est ajouté **si** la requête entrante est sécurisée. Si la valeur est définie sur `1`, l’indicateur sécurisé sera ajouté, que la requête entrante soit sécurisée ou non.

## Gérer les erreurs de connexion au serveur de rendu {#handling-render-connection-errors}

Configurez le comportement de Dispatcher lorsque le serveur de rendu renvoie une erreur 500 ou n’est pas disponible.

### Définir une page de contrôle de l’intégrité {#specifying-a-health-check-page}

Utilisez la propriété `/health_check` pour indiquer une URL qui est vérifiée lorsque le code d’état 500 se produit. Si cette page renvoie également le code d’état 500, l’instance est considérée comme non disponible et une pénalité de temps configurable (`/unavailablePenalty`) est appliquée au rendu avant de réessayer.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Définir le délai entre deux tentatives de connexion à une page {#specifying-the-page-retry-delay}

La propriété `/retryDelay` définit le délai (en secondes) pendant lequel Dispatcher attend entre les séries de tentatives de connexion aux rendus de la batterie. Pour chaque série, le nombre maximal de tentatives de connexion de Dispatcher à un rendu est le nombre de rendus de la ferme de serveurs.

Dispatcher utilise la valeur `"1"` si la propriété `/retryDelay` n’est pas explicitement définie. La valeur par défaut est généralement correcte.

```xml
/retryDelay "1"
```

### Configurer le nombre de reprises {#configuring-the-number-of-retries}

La propriété `/numberOfRetries` définit le nombre maximal de séries de tentatives de connexion que Dispatcher exécute avec les rendus. Si Dispatcher ne parvient pas à se connecter à un rendu après ce nombre de reprises, il renvoie une réponse en échec.

Pour chaque série, le nombre maximal de tentatives de connexion de Dispatcher à un rendu est le nombre de rendus de la ferme de serveurs. Par conséquent, le nombre maximal de fois que Dispatcher tente une connexion est (`/numberOfRetries`) x (nombre de rendus).

Si la valeur n’est pas explicitement définie, la valeur par défaut est `5`.

```xml
/numberOfRetries "5"
```

### Utiliser le mécanisme de basculement {#using-the-failover-mechanism}

Pour renvoyer des requêtes à d’autres rendus lorsque la requête d’origine échoue, activez le mécanisme de basculement sur votre batterie Dispatcher. Lorsque le basculement est activé, Dispatcher se comporte comme suit :

* Lorsqu’une requête à un rendu renvoie un état HTTP 503 (INDISPONIBLE), Dispatcher envoie la requête à un autre rendu.
* Lorsqu’une demande à un rendu renvoie l’état HTTP 50x (autre que 503), Dispatcher envoie une demande pour la page qui est configurée pour la propriété `health_check`.
   * Si le contrôle d’intégrité renvoie une erreur 500 (INTERNAL_SERVER_ERROR), Dispatcher envoie la requête d’origine à un autre rendu.
   * Si le contrôle d’intégrité renvoie un statut HTTP 200, Dispatcher renvoie l’erreur HTTP 500 initiale au client.

Pour activer le basculement, ajoutez la ligne suivante à la ferme de serveurs (ou au site web) :

```xml
/failover "1"
```

>[!NOTE]
>
>Pour réessayer les demandes HTTP qui contiennent un corps, Dispatcher envoie un en-tête de demande `Expect: 100-continue` au rendu avant de mettre en file d’attente les contenus réels. CQ 5.5 avec CQSE répond immédiatement avec 100 (CONTINUER) ou un code d’erreur. D’autres conteneurs de servlets sont également pris en charge.

## Ignorer les erreurs d’interruption - /ignoreEINTR  {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Cette option n’est pas nécessaire. Utilisez-la uniquement lorsque vous voyez les messages de journal suivants :
>
>`Error while reading response: Interrupted system call`

Tout appel à un système orienté fichiers peut être interrompu `EINTR` si l’objet de l’appel au système se trouve sur un système distant accessible au moyen de NFS. Ces appels système peuvent expirer ou être interrompus en fonction de la manière dont le système de fichiers sous-jacent a été monté sur l’ordinateur local.

Utilisez le paramètre `/ignoreEINTR` si votre instance comporte une configuration de ce type et que le journal contient le message suivant :

`Error while reading response: Interrupted system call`

En interne, Dispatcher lit la réponse depuis le serveur distant (à savoir, AEM) en utilisant une boucle qui peut être représentée ainsi :

```text
while (response not finished) {  
read more data  
}
```

De tels messages peuvent être générés lorsque l’erreur `EINTR` se produit dans la section « `read more data` » et sont provoqués par la réception d’un signal avant que des données n’aient été reçues.

Pour ignorer ces interruptions, ajoutez le paramètre suivant au `dispatcher.any` (avant `/farms`) :

`/ignoreEINTR "1"`

La définition du paramètre `/ignoreEINTR` sur `"1"` fait en sorte que Dispatcher continue d’essayer de lire des données jusqu’à la lecture de la réponse complète. La valeur par défaut est `0` et désactive l’option.

## Créer des motifs pour les propriétés glob {#designing-patterns-for-glob-properties}

Plusieurs sections du fichier de configuration de Dispatcher utilisent les propriétés `glob` comme critères de sélection des requêtes du client. Les valeurs des propriétés `glob` sont des motifs que Dispatcher compare à un aspect de la requête, par exemple au chemin d’accès à la ressource demandée ou à l’adresse IP du client. Par exemple, les éléments de la section `/filter` utilisent les motifs `glob` pour identifier les chemins d’accès des pages que Dispatcher traite ou rejette.

Les valeurs `glob` peuvent inclure des caractères génériques et des caractères alphanumériques pour définir le motif.

| Caractère générique | Description | Exemples |
|--- |--- |--- |
| `*` | Correspond à aucune ou à plusieurs instances contiguës de n’importe quel caractère de la chaîne. Le dernier caractère de la correspondance est déterminé par l’une des situations suivantes : <br/>un caractère de la chaîne correspond au caractère suivant du motif, et le caractère du motif possède les caractéristiques suivantes :<br/><ul><li>Pas un *</li><li>Pas un ?</li><li>Un caractère littéral (incluant un espace) ou une classe de caractères.</li><li>La fin du motif est atteinte.</li></ul>Dans une classe de caractères, le caractère est interprété littéralement. | `*/geo*`Correspond à n’importe quelle page sous les nœud `/content/geometrixx` et `/content/geometrixx-outdoors`. Les demandes HTTP suivantes correspondent au modèle glob : <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>Représente n’importe quelle page sous le nœud `/content/geometrixx-outdoors`. Par exemple, la demande HTTP suivante correspond au modèle glob :<br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Correspond à tout caractère unique. Utilisation en dehors des classes de caractères. Dans une classe de caractères, ce caractère est interprété littéralement. | `*outdoors/??/*`<br/> correspond aux pages du site geometrixx-outdoors dans n’importe quelle langue. Par exemple, la demande HTTP suivante correspond au modèle glob :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>La demande suivante ne correspond pas au modèle glob : <br/><ul><li>&quot;GET /content/geometrixx-outdoors/fr.html&quot;</li></ul> |
| `[ and ]` | Marque le début et la fin d’une classe de caractères. Les classes de caractères peuvent inclure une ou plusieurs plages de caractères et des caractères uniques.<br/>Une correspondance se produit si le caractère cible correspond à n’importe quel caractère de la classe de caractères ou d’une plage définie.<br/>Si le crochet fermant n’est pas inclus, le modèle ne produit pas de correspondance. | `*[o]men.html*`<br/> correspond à la requête HTTP suivante : <br/>.<ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Ne correspond pas à la requête HTTP suivante :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>Correspond aux requêtes HTTP suivantes : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Indique une plage de caractères. À utiliser dans des classes de caractères. En dehors d’une classe de caractères, ce caractère est interprété littéralement. | `*[m-p]men.html*`Correspond à la requête HTTP suivante : <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Ne correspond pas à la requête HTTP suivante :<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Annule le caractère ou la classe de caractères qui suit. À utiliser uniquement pour annuler des caractères et des plages de caractères dans des classes de caractères. Équivalent au `^ wildcard` <br/>En dehors d’une classe de caractères, ce caractère est interprété littéralement. | `*[!o]men.html*`<br/> correspond à la requête HTTP suivante : <br/>.<ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> ne correspond pas à la requête HTTP suivante : <br/>.<ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> ne correspond pas à la requête HTTP suivante : <br/>.<ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` ou `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Annule le caractère ou la plage de caractères qui suit. À utiliser pour annuler uniquement des caractères et des plages de caractères dans des classes de caractères. Équivalent au caractère générique `!`. <br/>En dehors d’une classe de caractères, ce caractère est interprété littéralement. | Les exemples pour le caractère générique `!` s’appliquent, en remplaçant les caractères `!` dans les exemples de motifs par des caractères `^`. |


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

Si vous utilisez un serveur web **Apache**, vous pouvez utiliser la fonctionnalité standard pour les journaux pivotés, les journaux transmis, ou les deux. Par exemple, avec les journaux transmis :

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Cette fonctionnalité pivote automatiquement :

* fichier journal de Dispatcher, avec la date et l’heure dans l’extension (`logs/dispatcher.log%Y%m%d`) ;
* chaque semaine (60x60x24x7 = 604 800 secondes).

Consultez la documentation du serveur web Apache sur la rotation des journaux et les journaux transmis. Par exemple : [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Lors de l’installation, le niveau de journalisation par défaut est élevé (à savoir, niveau 3 = Déboguer), de sorte que Dispatcher consigne toutes les erreurs et tous les avertissements. Ce niveau est utile lors des étapes initiales.
>
>Toutefois, un tel niveau nécessite des ressources supplémentaires. Lorsque Dispatcher fonctionne sans problème *selon vos besoins*, vous pouvez réduire le niveau de journal.

### Journalisation de trace {#trace-logging}

Entre autres améliorations de Dispatcher, la version 4.2.0 introduit également la journalisation de trace.

Cette fonctionnalité est un niveau supérieur à la journalisation de débogage qui affiche des informations supplémentaires dans les journaux. Elle ajoute la journalisation des informations suivantes :

* les valeurs des en-têtes transférés ;
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

## Confirmation du fonctionnement de base  {#confirming-basic-operation}

Pour confirmer le fonctionnement de base et l’interaction du serveur web, de Dispatcher et de l’instance AEM, procédez comme suit :

1. Définissez le niveau du journal `loglevel` sur `3`.

1. Démarrez le serveur web. Cela permet également de lancer Dispatcher.
1. Démarrez l’instance AEM.
1. Vérifiez les fichiers journaux et d’erreurs de votre serveur web et de Dispatcher.
   * En fonction de votre serveur web, vous devriez voir des messages tels que :
      * `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured` et
      * `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Consultez le site web via le serveur web. Vérifiez que le contenu s’affiche selon les besoins.\
   Par exemple, sur une installation locale où AEM s’exécute sur le port `4502` et le serveur web sur `80`, accédez à la console Sites web à l’aide des éléments suivants :
   * `https://localhost:4502/libs/wcm/core/content/siteadmin.html`
   * `https://localhost:80/libs/wcm/core/content/siteadmin.html`
   * Les résultats devraient être identiques. Confirmez l’accès à d’autres pages avec le même mécanisme.

1. Vérifiez que le répertoire du cache est en cours de remplissage.
1. Pour vérifier que le cache se vide correctement, activez une page.
1. Si tout fonctionne correctement, vous pouvez réduire le `loglevel` sur `0`.

## Utiliser plusieurs instances de Dispatcher {#using-multiple-dispatchers}

Dans des configurations complexes, vous pouvez utiliser plusieurs instances de Dispatcher. Par exemple, vous pouvez utiliser :

* une instance de Dispatcher pour publier un site web sur l’intranet ;
* une seconde instance de Dispatcher, sous une autre adresse et avec des paramètres de sécurité différents, pour publier le même contenu sur Internet.

Dans ce cas, veillez à ce que chaque requête ne passe que par une seule instance de Dispatcher. Une instance de Dispatcher ne gère pas les requêtes provenant d’une autre instance. Par conséquent, assurez-vous que les deux instances de Dispatcher accèdent directement au site web d’AEM.

## Débogage {#debugging}

Lorsque vous ajoutez un en-tête `X-Dispatcher-Info` à une requête, Dispatcher indique si la cible a été mise en cache, renvoyée de la mise en cache ou si elle ne pouvait absolument pas faire l’objet d’une mise en cache. L’en-tête de la réponse `X-Cache-Info` contient ces informations sous une forme lisible. Vous pouvez utiliser ces en-têtes de réponse pour déboguer des problèmes impliquant des réponses mises en cache par Dispatcher.

Cette fonctionnalité n’est pas activée par défaut. Par conséquent, pour que l’en-tête de réponse `X-Cache-Info` soit inclus, la batterie doit contenir l’entrée suivante :

```xml
/info "1"
```

Par exemple,

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

De plus, l’en-tête `X-Dispatcher-Info` n’a pas besoin de valeur, mais si vous utilisez `curl` pour le test, vous devrez fournir une valeur à envoyer à l’en-tête, par exemple :

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/wknd/us/en.html
```

Vous trouverez ci-dessous une liste contenant les en-têtes de réponse que `X-Dispatcher-Info` renvoie :

* **Mis en cache**\
  Le fichier cible est contenu dans le cache et Dispatcher a déterminé que sa diffusion était valide.
* **mise en cache**\
  Le fichier cible n’est pas contenu dans le cache et Dispatcher a déterminé que la mise en cache de la sortie et sa diffusion étaient valides.
* **mise en cache : le fichier stat est plus récent.**
Le fichier cible est contenu dans le cache, mais il est invalidé par un fichier stat plus récent. Dispatcher supprime le fichier cible, recréez-le à partir de la sortie et diffusez-le.
* **mise en cache impossible : pas de racine de document**
La configuration de la ferme de serveurs ne contient pas de racine de document (élément de configuration `cache.docroot`).
* **mise en cache impossible : chemin du fichier de cache trop long**\
  Le fichier cible (concaténation de la racine du document et du fichier URL) dépasse le nom de fichier le plus long possible sur le système.
* **mise en cache impossible : chemin du fichier temporaire trop long**\
  Le modèle de nom de fichier temporaire dépasse la longueur du plus long nom de fichier possible sur le système. Dispatcher crée d’abord un fichier temporaire, avant de créer ou de remplacer effectivement le fichier mis en cache. Le nom de fichier temporaire est le nom du fichier cible auquel sont ajoutés les caractères `_YYYYXXXXXX`, où `Y` et `X` sont remplacés pour créer un nom unique.
* **mise en cache impossible : l’URL de la requête n’a pas d’extension**\
  L’URL de la demande n’a pas d’extension ou un chemin suit l’extension du fichier, par exemple : `/test.html/a/path`.
* **mise en cache impossible : la requête n’était pas un GET ou un HEAD**.
La méthode HTTP n’est ni un GET ni un HEAD. Dispatcher suppose que la sortie contient des données dynamiques qui ne doivent pas être mises en cache.
* **mise en cache impossible : la requête contenait une chaîne de requête**\
  La requête contenait une chaîne de requête. Dispatcher suppose que la sortie dépend de la chaîne de requête donnée et n’effectue donc pas de mise en cache.
* **mise en cache impossible : le gestionnaire de session ne s’est pas authentifié**\
  Le cache de la ferme de serveurs est régi par un gestionnaire de session (la configuration contient un nœud `sessionmanagement`) et la requête ne contenait pas les informations d’authentification appropriées.
* **mise en cache impossible : la requête contient une autorisation**\
  La ferme de serveurs n’est pas autorisée à mettre en cache la sortie ( `allowAuthorized 0`) et la requête contient des informations d’authentification.
* **mise en cache impossible : la cible est un répertoire**\
  Le fichier cible est un répertoire. Cet emplacement peut indiquer une erreur conceptuelle, où une URL et une sous-URL quelconque contiennent toutes deux une sortie pouvant être mise en cache. Par exemple, si une requête pour `/test.html/a/file.ext` s’affiche en premier et contient une sortie pouvant être mise en cache, Dispatcher ne peut pas mettre en cache la sortie d’une requête ultérieure vers `/test.html`.
* **mise en cache impossible : l’URL de la requête est suivie d’une barre oblique**\
  L’URL de la requête est suivie d’une barre oblique.
* **mise en cache impossible : l’URL de la requête ne figure pas dans les règles du cache**\
  Les règles de cache de la ferme de serveurs interdisent explicitement la mise en cache de la sortie de certaines URL de demande.
* **mise en cache impossible : autorisation d’accès refusée**\
  Le vérificateur d’autorisation de la ferme de serveurs a refusé l’accès au fichier mis en cache.
* **mise en cache impossible : session non valide**
Le cache de la ferme de serveurs est régi par un gestionnaire de session (la configuration contient un nœud `sessionmanagement`) et la session de l’utilisateur n’est pas ou plus valide.
* **mise en cache impossible : la réponse contient`no_cache`**.
Le serveur distant a renvoyé un en-tête `Dispatcher: no_cache`, interdisant à Dispatcher de mettre en cache la sortie.
* **mise en cache impossible : la longueur du contenu de la réponse est zéro**.
La longueur du contenu de la réponse est zéro ; Dispatcher ne crée pas de fichier de longueur nulle.
