---
title: Utilisation du protocole SSL avec Dispatcher
seo-title: Utilisation du protocole SSL avec Dispatcher
description: Découvrez comment configurer Dispatcher pour communiquer avec AEM à l’aide de connexions SSL.
seo-description: Découvrez comment configurer Dispatcher pour communiquer avec AEM à l’aide de connexions SSL.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: Utilisateur
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: référence
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
translation-type: ht
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Utilisation du protocole SSL avec Dispatcher {#using-ssl-with-dispatcher}

Utilisez les connexions SSL entre Dispatcher et l’ordinateur de rendu :

* [SSL à sens unique](dispatcher-ssl.md#main-pars-title-1)
* [SSL mutuel](dispatcher-ssl.md#main-pars-title-2)

>[!NOTE]
>
>Les opérations relatives à des certificats SSL sont liées à des produits tiers. Elles ne sont pas couvertes par le contrat d’assistance et de maintenance Adobe Platinum.

## Utilisation du protocole SSL lorsque Dispatcher se connecte à AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configurez Dispatcher de sorte qu’il communique avec l’instance de rendu AEM ou CQ en utilisant des connexions SSL.

Avant de configurer Dispatcher, configurez AEM ou CQ de sorte que ces applications utilisent le protocole SSL :

* AEM 6.2 : [activation de HTTP via SSL](https://helpx.adobe.com/fr/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1 : [activation de HTTP via SSL](https://docs.adobe.com/content/docs/fr/aem/6-1/deploy/configuring/config-ssl.html)
* Anciennes versions d’AEM : voir [cette page](https://helpx.adobe.com/fr/experience-manager/aem-previous-versions.html).

### En-têtes de demande associés au protocole SSL {#ssl-related-request-headers}

Lorsque Dispatcher reçoit une requête HTTPS, il inclut les en-têtes suivants dans la demande suivante qu’il envoie à AEM ou CQ :

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Une demande via Apache 2.2 avec `mod_ssl` inclut des en-têtes similaires à l’exemple suivant :

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configuration de Dispatcher de sorte qu’il utilise le protocole SSL {#configuring-dispatcher-to-use-ssl}

Pour configurer Dispatcher de sorte qu’il se connecte à AEM ou CQ via SSL, le fichier [dispatcher.any](dispatcher-configuration.md) requiert les propriétés suivantes :

* Un hôte virtuel qui gère les requêtes HTTPS.
* La section `renders` de l’hôte virtuel inclut un élément qui identifie le nom et le port de l’hôte de l’instance CQ ou AEM qui utilise HTTPS.
* L’élément `renders` inclut une propriété nommée `secure` d’une valeur de `1`.

Remarque : Créez un autre hôte virtuel pour traiter les requêtes HTTP, si nécessaire.

L’exemple de fichier dispatcher.any suivant affiche les valeurs des propriétés pour la connexion via HTTPS à une instance CQ qui s’exécute sur l’hôte `localhost` et le port `8443` :

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requestss
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "https://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Configuration du protocole SSL mutuel entre Dispatcher et AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Configurez les connexions entre Dispatcher et l’ordinateur de rendu (généralement une instance de publication AEM ou CQ) pour utiliser le protocole SSL mutuel :

* Dispatcher se connecte à l’instance de rendu via SSL.
* L’instance de rendu vérifie la validité du certificat de Dispatcher.
* Dispatcher vérifie que l’autorité de certification du certificat de l’instance de rendu est approuvée.
* (Facultatif) Dispatcher vérifie que le certificat de l’instance de rendu correspond à l’adresse du serveur de l’instance de rendu.

Pour configurer un protocole SSL mutuel, vous avez besoin de certificats signés par une autorité de certification approuvée. Les certificats auto-signés ne sont pas appropriés. Vous pouvez agir comme autorité de certification ou utiliser les services d’une autorité de certification tierce pour la signature des certificats. Pour configurer un protocole SSL mutuel, vous avez besoin des éléments suivants :

* Certificats signés pour l’instance de rendu et Dispatcher
* Certificat de l’autorité de certification (si vous agissez comme autorité de certification)
* Bibliothèques OpenSSL pour générer l’autorité de certification, les certificats et les demandes de certificats

Procédez comme suit pour configurer le protocole SSL mutuel :

1. [Installez](dispatcher-install.md) la version la plus récente de Dispatcher pour votre plate-forme. Utilisez un binaire de Dispatcher qui prend en charge le protocole SSL (SSL se trouve dans le nom du fichier, par exemple dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar).
1. [Créez ou obtenez une certification signée par une autorité de certification](dispatcher-ssl.md#main-pars-title-3) pour Dispatcher et l’instance de rendu.
1. [Créez un magasin de clés contenant le certificat du rendu](dispatcher-ssl.md#main-pars-title-6) et configurez le service HTTP pour l’utiliser.
1. [Configurez le module de serveur web de Dispatcher](dispatcher-ssl.md#main-pars-title-4) pour le protocole SSL mutuel.

### Création ou obtention de certificats signés par une autorité de certification  {#creating-or-obtaining-ca-signed-certificates}

Créez ou obtenez des certificats signés par une autorité de certification qui authentifient l’instance de publication et Dispatcher.

#### Création de l’autorité de certification  {#creating-your-ca}

Si vous agissez comme autorité de certification, utilisez [OpenSSL](https://www.openssl.org/) pour créer l’autorité de certification qui signe les certificats du serveur et du client. (vous devez disposer des bibliothèques OpenSSL). Si vous utilisez une autorité de certification tierce, ne suivez pas cette procédure.

1. Ouvrez un terminal et modifiez le répertoire actuel par le répertoire qui contient le fichier CA.sh, par exemple `/usr/local/ssl/misc`.
1. Pour créer l’autorité de certification, saisissez la commande suivante, puis indiquez les valeurs lorsque vous y êtes invité :

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Plusieurs propriétés du fichier openssl.cnf contrôlent le comportement du script CA.sh. Vous devez modifier ce fichier selon vos besoins avant de créer votre autorité de certification.

#### Création de certificats  {#creating-the-certificates}

Utilisez OpenSSL pour créer des demandes de certificat à envoyer à l’autorité de certification tierce ou à signer avec votre autorité de certification.

Lorsque vous créez un certificat, OpenSSL utilise la propriété Nom commun pour identifier le détenteur du certificat. Pour le certificat de l’instance de rendu, utilisez le nom d’hôte de l’ordinateur de l’instance en tant que Nom commun si vous configurez Dispatcher de sorte à accepter le certificat uniquement s’il correspond au nom d’hôte de l’instance de publication. (Voir la propriété [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11).)

1. Ouvrez un terminal et définissez le répertoire actuel sur le répertoire contenant le fichier CH.sh des bibliothèques d’OpenSSL.
1. Entrez la commande suivante et indiquez les valeurs lorsque vous y êtes invité. Le cas échéant, utilisez le nom d’hôte de l’instance de publication comme Nom commun. Le nom d’hôte est le nom résolvable DNS pour l’adresse IP du rendu :

   ```shell
   ./CA.sh -newreq
   ```

   Si vous utilisez une autorité de certification tierce, envoyez le fichier newreq.pem à l’autorité de certification pour signature. Si vous agissez comme autorité de certification, passez à l’étape 3.

1. Saisissez la commande suivante pour signer le certificat à l’aide du certificat de l’autorité de certification :

   ```shell
   ./CA.sh -sign
   ```

   Deux fichiers nommés newcert.pem et newkey.pem sont créés dans le répertoire qui contient les fichiers de gestion de l’autorité de certification. Il s’agit respectivement du certificat public et de la clé privée de l’ordinateur de rendu.

1. Renommez newcert.pem en rendercert.pem et newkey.pem en renderkey.pem.
1. Répétez les étapes 2 et 3 pour créer un nouveau certificat et une nouvelle clé publique pour le module de Dispatcher. Assurez-vous que vous utilisez un Nom commun qui est spécifique à l’instance de Dispatcher.
1. Renommez newcert.pem en dispcert.pem et newkey.pem en dispkey.pem.

### Configuration du protocole SSL sur l’ordinateur de rendu  {#configuring-ssl-on-the-render-computer}

Configurez le protocole SSL sur l’instance de rendu à l’aide des fichiers rendercert.pem et renderkey.pem.

#### Conversion du certificat du rendu au format JKS  {#converting-the-render-certificate-to-jks-format}

Utilisez la commande suivante pour convertir le certificat du rendu, qui est un fichier PEM, en un fichier PKCS#12. Incluez également le certificat de l’autorité de certification qui a signé le certificat du rendu :

1. Dans une fenêtre de terminal, définissez le répertoire actuel sur l’emplacement du certificat du rendu et de la clé privée.
1. Entrez la commande suivante pour convertir le certificat du rendu, qui est un fichier PEM, en un fichier PKCS#12. Incluez également le certificat de l’autorité de certification qui a signé le certificat du rendu :

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Saisissez la commande suivante pour convertir le fichier PKCS#12 au format Java KeyStore (JKS) :

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Le fichier Java Keystore est créé à l’aide d’un alias par défaut. Modifiez l’alias, le cas échéant :

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Ajout du certificat de l’autorité de certification au TrustStore du rendu  {#adding-the-ca-cert-to-the-render-s-truststore}

Si vous agissez comme autorité de certification, importez le certificat dans un magasin de clés. Ensuite, configurez la machine virtuelle Java exécutant l’instance de rendu pour approuver le magasin de clés.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Utilisez un éditeur de texte pour ouvrir le fichier cacert.pem et supprimer l’intégralité du texte qui précède la ligne suivante :

   `-----BEGIN CERTIFICATE-----`

1. Utilisez la commande suivante pour importer le certificat dans un magasin de clés :

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Pour configurer la machine virtuelle Java qui exécute l’instance de rendu de sorte qu’elle approuve le magasin de clés, utilisez la propriété système suivante :

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Par exemple, si vous utilisez le script crx-quickstart/bin/quickstart pour démarrer l’instance de publication, vous pouvez modifier la propriété CQ_JVM_OPTS :

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configuration de l’instance de rendu  {#configuring-the-render-instance}

Utilisez le certificat de rendu avec les instructions de la section *Activation du protocole SSL sur l’instance Publish* pour configurer le service HTTP de l’instance de rendu de sorte qu’il utilise le protocole SSL :

* AEM 6.2 : [activation de HTTP via SSL](https://helpx.adobe.com/fr/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1 : [activation de HTTP via SSL](https://docs.adobe.com/fr/content/docs/fr/aem/6-1/deploy/configuring/config-ssl.html)
* Anciennes versions d’AEM : voir [cette page.](https://helpx.adobe.com/fr/experience-manager/aem-previous-versions.html)

### Configuration du protocole SSL pour le module de Dispatcher {#configuring-ssl-for-the-dispatcher-module}

Pour configurer Dispatcher de sorte qu’il utilise le protocole SSL mutuel, préparez le certificat de Dispatcher, puis configurez le module de serveur web.

### Création d’un certificat de Dispatcher unifié  {#creating-a-unified-dispatcher-certificate}

Combinez le certificat de Dispatcher et la clé privée non chiffrée dans un seul fichier PEM. Utilisez un éditeur de texte ou la commande `cat` pour créer un fichier semblable à l’exemple suivant :

1. Ouvrez un terminal et définissez le répertoire actuel sur l’emplacement du fichier dispkey.pem.
1. Pour déchiffrer la clé privée, saisissez la commande suivante :

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Utilisez un éditeur de texte ou la commande `cat` pour combiner la clé privée non chiffrée avec le certificat dans un fichier unique semblable à l’exemple suivant :

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Spécification du certificat à utiliser pour Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Ajoutez les propriétés suivantes à la [configuration du module de Dispatcher](dispatcher-install.md#main-pars-55-35-1022) (dans `httpd.conf`):

* `DispatcherCertificateFile` : chemin d’accès au fichier de certificat unifié de Dispatcher, contenant le certificat public et la clé privée non chiffrée. Ce fichier est utilisé lorsque le serveur SSL demande le certificat client de Dispatcher.
* `DispatcherCACertificateFile` : chemin d’accès au fichier de certificat de l’autorité de certification, utilisé si le serveur SSL présente une autorité de certification qui n’est pas approuvée par une autorité racine.
* `DispatcherCheckPeerCN` : spécification de l’activation (`On`) ou de la désactivation (`Off`) de la vérification des noms d’hôte pour les certificats du serveur distant.

Le code suivant est un exemple de configuration :

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```

