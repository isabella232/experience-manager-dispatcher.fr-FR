---
title: Invalidation de pages mises en cache depuis AEM
seo-title: Invalidation des pages mises en cache d'Adobe AEM
description: Découvrez comment configurer l'interaction entre Répartiteur et AEM pour assurer une gestion efficace du cache.
seo-description: Découvrez comment configurer l'interaction entre le répartiteur Adobe AEM et AEM pour assurer une gestion efficace du cache.
uuid: 66533299-55 c 0-4864-9 beb -77 e 281 af 9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Utilisateur
products: SG_ EXPERIENCEMANAGER/RÉPARTICHER
topic-tags: répartiteur
content-type: référence
discoiquuid: 79 cd 94 be-a 6 bc -4 d 34-bfe 9-393 b 4107925 c
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Invalidation de pages mises en cache depuis AEM {#invalidating-cached-pages-from-aem}

Lors de l&#39;utilisation du répartiteur avec AEM, l&#39;interaction doit être configurée pour garantir une gestion efficace du cache. En fonction de votre environnement, la configuration peut également améliorer les performances.

## Configuration des comptes d’utilisateur AEM {#setting-up-aem-user-accounts}

Le compte d’utilisateur `admin` par défaut est utilisé pour authentifier les agents de réplication qui sont installés par défaut. Vous devez créer un compte utilisateur dédié pour l’utiliser avec des agents de réplication. [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps)

Pour plus d&#39;informations, reportez-vous à [la section Configuration des utilisateurs](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) de réplication et de transport de la liste de contrôle de sécurité AEM.

## Invalidation du cache de Dispatcher depuis l’environnement de création {#invalidating-dispatcher-cache-from-the-authoring-environment}

Un agent de réplication de l’instance de création AEM envoie une demande d’invalidation du cache à Dispatcher lorsqu’une page est publiée. La demande entraîne Dispatcher à actualiser le fichier dans le cache lorsque du contenu nouveau est publié.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Procédez comme suit pour configurer un agent de réplication sur l&#39;instance d&#39;auteur AEM pour invalider le cache Répartiteur lors de l&#39;activation de la page :

1. Ouvrez la console d’outils AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Ouvrez l&#39;agent de réplication requis sous Outils/réplication/Agents sur l&#39;auteur. Vous pouvez utiliser l&#39;agent du répartiteur Flush installé par défaut.
1. Cliquez sur Modifier puis, dans l’onglet Paramètres, assurez-vous que l’option **Activé** est sélectionnée.

1. (Facultatif) Pour activer les demandes d&#39;invalidation de chemin d&#39;alias ou de chemin Vanity, sélectionnez l&#39; **option de mise à jour** Alias.
1. Dans l’onglet Transfert, saisissez l’URI requis pour accéder à Dispatcher.\
   Si vous utilisez l&#39;agent Flush standard du répartiteur, vous devrez probablement mettre à jour le nom d&#39;hôte et le port ; par exemple, https:// &lt;*dispatcherhost*&gt; : &lt;*Portapache*&gt;/dispatcher/invalidate. cache

   **Remarque :** Pour les agents Flush Répartiteur, la propriété URI est utilisée uniquement si vous utilisez des entrées virtualhost basées sur un chemin pour différencier les exploitations. Utilisez ce champ pour cibler la batterie à invalider. Par exemple, la batterie # 1 dispose d&#39;un hôte virtuel de `www.mysite.com/path1/*` la batterie et la batterie n ° 2 possède un hôte virtuel de `www.mysite.com/path2/*`. Vous pouvez utiliser une URL pour `/path1/invalidate.cache` cibler la première batterie et `/path2/invalidate.cache` cibler la deuxième batterie. Pour plus d’informations, voir [Utilisation de Dispatcher avec plusieurs domaines](dispatcher-domains.md).

1. Configurez les autres paramètres selon vos besoins.
1. Cliquez sur OK pour activer l&#39;agent.

Vous pouvez également accéder à l&#39;agent de purge du répartiteur et le configurer depuis l&#39;interface [utilisateur d&#39;AEM Touch](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent).

Pour plus d&#39;informations sur la manière d&#39;activer l&#39;accès aux URL mineures, voir [Activation des URL de création de liens mineurs](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>Il n’est pas obligatoire que l’agent de vidage du cache de Dispatcher dispose d’un nom d’utilisateur et d’un mot de passe, mais s’ils sont configurés, ils seront envoyés avec l’authentification de base.

Il existe deux problèmes potentiels liés à cette approche :

* Dispatcher doit être accessible à partir de l’instance de création. Si votre réseau (par exemple, le pare-feu) est configuré de sorte que l’accès entre les deux est limité, cela peut ne pas être le cas.

* La publication et l’invalidation du cache ont lieu en même temps. En fonction de la synchronisation, un utilisateur peut demander une page immédiatement après sa suppression du cache et juste avant que la nouvelle page ne soit publiée. AEM renvoie désormais l’ancienne page et Dispatcher la met à nouveau en cache. Cela est davantage problématique pour les sites volumineux.

## Invalidation du cache de Dispatcher depuis une instance de publication {#invalidating-dispatcher-cache-from-a-publishing-instance}

Dans certains cas, vous pouvez améliorer les performances en transférant la gestion des caches de l’environnement de création à une instance de publication. C’est alors l’environnement de publication (et non l’environnement de création AEM) qui envoie une demande d’invalidation du cache à Dispatcher lorsqu’une page publiée est reçue.

Ces cas incluent :

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Empêcher les conflits de synchronisation possibles entre Dispatcher et l’instance de publication (voir [Invalidation du cache de Dispatcher depuis l’environnement de création](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* Le système inclut plusieurs instances de publication qui se trouvent sur des serveurs très performants et une seule instance de création.

>[!NOTE]
>
>La décision d’utiliser cette méthode doit être prise par un administrateur AEM expérimenté.

Le vidage de Dispatcher est contrôlé par un agent de réplication s’exécutant sur l’instance de publication. Toutefois, la configuration est effectuée dans l’environnement de création, puis transférée en activant l’agent :

1. Ouvrez la console d’outils AEM.
1. Ouvrez l&#39;agent de réplication requis sous Outils/réplication/Agents sur la publication. Vous pouvez utiliser l&#39;agent du répartiteur Flush installé par défaut.
1. Cliquez sur Modifier puis, dans l’onglet Paramètres, assurez-vous que l’option **Activé** est sélectionnée.
1. (Facultatif) Pour activer les demandes d&#39;invalidation de chemin d&#39;alias ou de chemin Vanity, sélectionnez l&#39; **option de mise à jour** Alias.
1. Dans l’onglet Transfert, saisissez l’URI requis pour accéder à Dispatcher.\
   Si vous utilisez l&#39;agent Flush standard du répartiteur, vous devrez probablement mettre à jour le nom d&#39;hôte et le port ; par exemple, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Remarque :** Pour les agents Flush Répartiteur, la propriété URI est utilisée uniquement si vous utilisez des entrées virtualhost basées sur un chemin pour différencier les exploitations. Utilisez ce champ pour cibler la batterie à invalider. Par exemple, la batterie # 1 dispose d&#39;un hôte virtuel de `www.mysite.com/path1/*` la batterie et la batterie n ° 2 possède un hôte virtuel de `www.mysite.com/path2/*`. Vous pouvez utiliser une URL pour `/path1/invalidate.cache` cibler la première batterie et `/path2/invalidate.cache` cibler la deuxième batterie. Pour plus d’informations, voir [Utilisation de Dispatcher avec plusieurs domaines](dispatcher-domains.md).

1. Configurez les autres paramètres selon vos besoins.
1. Répétez la procédure pour chaque instance de publication affectée.

Après la configuration, lorsque vous faites passer la page de l’état de création à celui de publication, l’agent lance une réplication standard. Le journal comprend des messages indiquant des demandes provenant du serveur de publication, comme illustré dans l’exemple suivant :

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidation manuelle du cache de Dispatcher {#manually-invalidating-the-dispatcher-cache}

Pour invalider (ou vider) le cache de Dispatcher sans activer une page, vous pouvez envoyer une demande HTTP à Dispatcher. Par exemple, vous pouvez créer une application AEM qui permet aux administrateurs ou à d’autres applications de vider le cache.

La demande HTTP entraîne Dispatcher à supprimer des fichiers spécifiques du cache. Éventuellement, Dispatcher actualise alors le cache avec une nouvelle copie.

### Suppression de fichiers mis en cache {#delete-cached-files}

Envoyez une requête HTTP qui entraîne Dispatcher à supprimer des fichiers du cache. Le répartiteur met à nouveau les fichiers en cache uniquement lorsqu&#39;il reçoit une demande client pour la page. La suppression de fichiers mis en cache est appropriée pour les sites Web qui ne reçoivent pas de requêtes simultanées pour la même page.

La requête HTTP se présente ainsi :

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher vide (supprime) les fichiers et les dossiers mis en cache dont les noms correspondent à la valeur de l’en-tête `CQ-Handler`. Par exemple, a `CQ-Handle` of `/content/geomtrixx-outdoors/en` match the following items :

* Tous les fichiers (d&#39;une extension de fichier) nommés `en` dans `geometrixx-outdoors` le répertoire

* Tout répertoire nommé «  `_jcr_content` » sous le répertoire en (qui, s&#39;il existe, contient des rendus mis en cache des sous-nœuds de la page)

Tous les autres fichiers du cache du répartiteur (ou jusqu&#39;à un niveau précis, selon `/statfileslevel` le paramètre) sont invalidés en touchant `.stat` le fichier. La date de dernière modification de ce fichier est comparée à la date de dernière modification d&#39;un document mis en cache et le document est réextrait si `.stat` le fichier est plus récent. Pour plus d’informations, voir [Invalidation des fichiers par niveau de dossier](dispatcher-configuration.md#main-pars_title_26).

L’invalidation (c’est-à-dire la modification des fichiers .stat) peut être évitée en envoyant un en-tête supplémentaire `CQ-Action-Scope: ResourceOnly`. Ceci peut être utilisé pour vider des ressources spécifiques sans invalider d’autres parties du cache, comme les données JSON qui sont créées dynamiquement et nécessitent un vidage normal indépendant du cache (par exemple, des données obtenues à partir d’un système tiers pour afficher l’actualité, les cours de la bourse, etc.).

### Suppression et nouvelle mise en cache de fichiers {#delete-and-recache-files}

Envoyez une requête HTTP qui entraîne Dispatcher à supprimer des fichiers mis en cache, à récupérer immédiatement le fichier et à le remettre en cache. Supprimez les fichiers et remettez-les immédiatement en cache lorsque les sites web sont susceptibles de recevoir des demandes client simultanées pour la même page. La remise en cache immédiate garantit que Dispatcher récupère et mette en cache la page une seule fois, plutôt qu’à chaque demande client simultanée.

**Remarque :** La suppression et la mise en cache de fichiers ne doivent être exécutées que sur l&#39;instance de publication. Lorsqu&#39;il est exécuté à partir de l&#39;instance d&#39;auteur, les conditions de concurrence surviennent lorsque des tentatives de recto des ressources se produisent avant qu&#39;elles aient été publiées.

La requête HTTP se présente ainsi :

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

Les chemins d’accès aux pages à remettre en cache immédiatement sont répertoriés sur des lignes distinctes dans le corps du message. La valeur de `CQ-Handle` est le chemin d’accès d’une page qui invalide les pages à remettre en cache. (voir `/statfileslevel` le paramètre de [l&#39;élément](dispatcher-configuration.md#main-pars_146_44_0010) de configuration Cache). L&#39;exemple de message HTTP suivant supprime et recense les `/content/geometrixx-outdoors/en.html page`éléments suivants :

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Exemple de servlet de vidage {#example-flush-servlet}

Le code suivant met en œuvre un servlet qui envoie une demande d’invalidation à Dispatcher. La servlet reçoit un message de demande contenant `handle` et `page` paramètres. Ces paramètres fournissent la valeur de l’en-tête `CQ-Handle` et le chemin d’accès à la page à remettre en cache, respectivement. Le servlet utilise ces valeurs pour créer la requête HTTP pour Dispatcher.

Lorsque le servlet est déployé sur l’instance de publication, l’URL suivante entraîne Dispatcher à supprimer la page /content/geometrixx-outdoors/fr.html, puis à mettre en cache une nouvelle copie.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Cet exemple de servlet n’est pas sécurisé et ne présente que l’utilisation du message de requête HTTP POST. Votre solution doit sécuriser l’accès au servlet.


```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```

