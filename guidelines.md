---
source-git-commit: 36c4ad10a9140d368fdbf3f0939e6756cc2bfbbf
translation-type: tm+mt

---
# Instructions pour contribuer à la documentation AEM

## Philosophie de la documentation AEM

Nous savons que les utilisateurs d’Adobe Experience Manager travaillent dans des environnements très concurrentiels, afin de créer des expériences numériques qui les distingueront de leurs concurrents. Par conséquent, lorsqu’Adobe fournit de nouveaux outils avancés dans AEM, il est essentiel que ces outils soient complétés par une documentation précise et claire pour permettre au client d’exploiter immédiatement son investissement AEM et maximiser le ROI.

L’objectif de la documentation AEM est de la placer entre les mains des utilisateurs d’AEM dès que possible. Par conséquent, nous privilégions une documentation précise et utilisable et nous la mettons à jour et l’améliorons constamment.

## Contributions à la documentation AEM

Afin d’améliorer continuellement la documentation AEM, la communauté complète des utilisateurs d’AEM est la bienvenue pour y contribuer. Que ce soit par le biais de requêtes d’extraction ou de problèmes, les améliorations apportées à la documentation peuvent être des corrections, des clarifications, des extensions et des exemples supplémentaires.

## Normes de la documentation

Bien que nous soyons heureux de recevoir des contributions à notre documentation, toute contribution à la documentation AEM, sous la forme d’une demande d’extraction ou d’un problème, doit être conforme à nos normes de contribution et de documentation.

Les contributions qui ne satisfont pas à ces normes peuvent être rejetées.

### Nous documenterons les cas d’utilisation standard.

La documentation AEM couvre les cas d’utilisation standard. Les cas d’utilisation au-delà de la portée de l’installation et de l’utilisation standard du produit ne font pas partie de la documentation AEM.

### Nous n&#39;avons généralement pas de problèmes  ou leurs solutions.

La documentation AEM couvre les cas d’utilisation standard. Pour cette raison, les bogues, les effets causés par les bogues et les solutions de contournement des bogues ne sont généralement pas documentés.

Les exceptions à cette règle s’appliquent aux notes de mise à jour où des problèmes connus peuvent être répertoriés avec des solutions possibles qui ont été approuvées par la gestion des produits AEM.

### Les contributions à la documentation ne sont pas destinées à répondre aux questions techniques.

Toute opinion susceptible d’améliorer la documentation AEM est la bienvenue sous forme de contributions. Toutefois, les commentaires, les problèmes et les requêtes d’extraction sont destinés uniquement aux *contributions*. Elles ne sont pas destinées à être utilisées pour répondre à vos questions sur l’utilisation d’AEM, la mise en oeuvre de votre projet AEM ou la résolution de problèmes techniques.

Any questions about the usage of AEM or technical errors you may have should be reported through the normal support process via the [Experience Manager Support Portal](https://daycare.day.com/home.html) or discussed in the [Experience Manager community](http://help-forums.adobe.com/content/adobeforums/en/experience-manager-forum/adobe-experience-manager.html).

***Les contributions à la documentation AEM ne remplacent pas l’assistance Adobe*** et toute contribution de ce type visant à obtenir des réponses à des questions d’assistance sera refusée.

### Les contributions doivent clairement référencer les pages de documentation concernées.

Si vous créez un problème pour suggérer des améliorations à la documentation, vous devez inclure des liens vers les pages concernées. Si vous créez un problème à l’aide du lien **Modifier cette page** sur une page de documentation, le problème sera créé automatiquement avec un lien vers la page.

Cela ne s’applique pas aux demandes d’extraction, car les demandes d’extraction par nature référencent la ou les pages concernées.

## Directives relatives à la documentation

Nous demandons que toutes les contributions à notre documentation suivent certaines directives de style.

Suivre ces directives facilite la révision et l’intégration rapide de votre contribution dans notre documentation.

### Langue et style

#### Langue

* La documentation AEM est créée et conservée en anglais américain.
* Veillez à ce que les expressions restent le plus simple possible.
* Restez clair et concis.

Souvenez-vous que les lecteurs de la documentation AEM sont internationaux et peuvent ne pas être des locuteurs anglais natifs ou bilingues. Évitez les expressions familières et restez aussi clair et simple que possible.

#### Suivi du guide de style Microsoft

[Le Manuel de style](https://docs.microsoft.com/en-us/style-guide/welcome/) de Microsoft est un guide de style de documentation disponible gratuitement qui se concentre sur la documentation logicielle et la documentation AEM suit ce guide chaque fois que possible.

### Mise en forme

| Élément | Style |
|---|---|
| Élément ou option de l’interface utilisateur | **gras** |
| Nom de fichier, chemin, entrée utilisateur, paramètre-valeurs | `monospaced` |
| Code, ligne de commande | ```Code Block``` |

### Captures d’écran

Les captures d’écran doivent être utilisées de manière judicieuse et uniquement lorsqu’une description textuelle est insuffisante.

Les marqueurs ou autres annotations dans les captures d’écran (comme les cadres rouges, les flèches ou le texte) ne doivent pas être utilisés. Ainsi, les captures d’écran sont plus faciles à réutiliser ou à répliquer dans les versions localisées de la documentation.

### Références spécifiques à la version

Dans la mesure du possible, évitez toute référence directe à une version spécifique dans tout le contenu de la documentation. La documentation est ainsi plus flexible et extensible pour les versions ultérieures.

### Utilisation de Day, AEM, CQ, CRX

Le produit doit toujours être référencé par son nom complet **Adobe Experience Manager** pour la première fois dans un article et peut ensuite être appelé **AEM**.

Day, logiciel Day, CQ et CRX ne doivent pas être utilisés, sauf lorsqu’ils sont inévitables, par exemple dans les noms de classe ou en faisant référence à l’historique d’AEM.