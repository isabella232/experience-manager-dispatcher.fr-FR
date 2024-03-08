---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
workflow-type: ht
source-wordcount: '740'
ht-degree: 100%

---
# Instructions relatives à la contribution à la documentation d’Adobe Experience Manager

## Philosophie de la documentation

Nous savons que les utilisateurs d’Adobe Experience Manager travaillent dans des environnements très concurrentiels, afin de créer des expériences digitales qui les distingueront de leurs concurrents. Par conséquent, lorsqu’Adobe fournit de nouveaux outils avancés dans AEM, il est essentiel que ces outils soient complétés par une documentation précise et claire pour permettre au client d’exploiter immédiatement son investissement AEM et maximiser le ROI.

L’objectif de la documentation AEM est de la placer entre les mains des utilisateurs d’AEM dès que possible. Nous privilégions donc une documentation précise et utilisable, et nous la mettons à jour et l’améliorons constamment.

## Contributions à la documentation

Afin d’améliorer continuellement la documentation d’AEM, toute la communauté des utilisateurs d’AEM peut apporter sa contribution. Que ce soit par le biais de demandes d’extraction ou de demandes, les améliorations apportées à la documentation peuvent être des corrections, des clarifications, des extensions et des exemples supplémentaires.

## Normes de la documentation

Bien que nous nous félicitions des contributions à notre documentation, toute contribution à la documentation d’AEM, sous la forme d’une requête d’extraction ou d’un problème, doit être conforme à nos normes de contribution et de documentation.

Les contributions qui ne satisfont pas à ces normes peuvent être rejetées.

### Nous documenterons les cas d’utilisation standard.

La documentation d’AEM couvre les cas d’utilisation standard. Les cas d’utilisation au-delà de la portée de l’installation et de l’utilisation standard du produit ne font pas partie de la documentation AEM.

### En général, nous ne documentons pas les bogues ni leurs solutions.

La documentation d’AEM couvre les cas d’utilisation standard. Pour cette raison, les bogues, leurs effets et leurs solutions ne sont généralement pas documentés.

Les exceptions à cette règle concernent les notes de mise à jour qui répertorient les problèmes connus ainsi que les solutions possibles après approbation par l’équipe de gestion des produits AEM.

### Les contributions à la documentation ne sont pas destinées à répondre aux questions techniques.

Toute opinion susceptible d’améliorer la documentation AEM est la bienvenue sous forme de contributions. Toutefois, les commentaires, les demandes et les demandes d’extraction sont destinés uniquement aux *contributions*. Ils ne sont pas censés être utilisés pour répondre à vos questions sur l’utilisation d’AEM, pour implémenter votre projet AEM ni pour résoudre des problèmes techniques.

Toute question relative à l’utilisation d’AEM ou à la résolution d’erreurs techniques doit être soumise au moyen du processus d’assistance classique via le [Portail d’assistance entreprise d’Experience Cloud](https://helpx.adobe.com/fr/contact/enterprise-support.ec.html) ou posée à la [communauté Experience Manager](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager).

***Les contributions à la documentation d’AEM ne remplacent pas celles de l’assistance clientèle Adobe*** et toute contribution de ce type qui recherche des réponses aux questions liées au support technique sera rejetée.

### Les contributions doivent clairement référencer les pages de documentation concernées.

Si vous créez une demande pour suggérer des améliorations à la documentation, vous devez inclure des liens vers les pages concernées. Si vous créez un problème à l’aide du lien **Modifier cette page** sur une page de documentation, le problème sera créé automatiquement avec un lien vers la page.

Cette méthode ne s’applique pas aux requêtes d’extraction qui, par nature, font référence à la page ou aux pages concernées.

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

[Le guide de style Microsoft](https://docs.microsoft.com/fr-fr/style-guide/welcome/) est gratuit et concerne la documentation logicielle. Il s’applique à la documentation AEM, dans la mesure du possible.

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