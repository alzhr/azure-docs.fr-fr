---
title: Aide-mémoire de l’algorithme Machine Learning
titleSuffix: Azure Machine Learning
description: Un aide-mémoire imprimable de l'algorithme Machine Learning vous permet de choisir l'algorithme adapté à votre modèle prédictif dans le concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 11/04/2019
ms.openlocfilehash: 10714e155bebdd7acc5d7febb345c17fa81bab13
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511346"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Aide-mémoire de l’algorithme Machine Learning pour le concepteur Azure Machine Learning

**L’Aide-mémoire d’algorithme Azure Machine Learning Studio** vous permet de choisir l’algorithme adapté à un modèle d’analyse prédictif.

Azure Machine Learning possède une grande bibliothèque d’algorithmes à partir des familles ***classification***, ***systèmes de recommandation***, ***clustering***,  ***détection des anomalies***, ***régression*** et ***analyse de texte***. Chacun est conçu pour traiter un type de problème de Machine Learning différent.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Télécharger : Aide-mémoire de l’algorithme Machine Learning

**Téléchargez l’aide-mémoire ici : [Aide-mémoire de l’algorithme Machine Learning (11 x 17 pouces)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Aide-mémoire de l’algorithme Machine Learning : Découvrez comment choisir un algorithme Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Téléchargez et imprimez l’Aide-mémoire d’algorithme Machine Learning au format tabloïd pour l’avoir à portée de main et obtenir de l’aide lors du choix d’un algorithme.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Comment utiliser l’aide-mémoire de l’algorithme Machine Learning

Les suggestions proposées dans cet aide-mémoire d'algorithme sont des règles de base approximatives. Certaines peuvent être contournées et d’autres ignorées. Elles visent à proposer un point de départ. N’hésitez pas à comparer plusieurs algorithmes avec vos données. Il est tout simplement indispensable de comprendre les principes de chaque algorithme et le système qui a généré les données.

Chaque algorithme Machine Learning a son propre style ou biais inductif. Plusieurs algorithmes peuvent être appropriés pour un problème spécifique et un algorithme peut être un meilleur choix que d’autres. Mais il n’est pas toujours possible de savoir au préalable lequel convient le mieux. Dans ce cas, plusieurs algorithmes sont affichés dans l’aide-mémoire. Une stratégie appropriée consiste à essayer un algorithme et, si les résultats ne sont pas satisfaisants, à essayer les autres. 

Pour en savoir plus sur les algorithmes dans Azure Machine Learning, accédez à la [référence des algorithmes et des modules](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Types de Machine Learning

Il existe trois catégories principales d’apprentissage automatique : l’*apprentissage supervisé*, l’*apprentissage non supervisé* et l’*apprentissage par renforcement*.

### <a name="supervised-learning"></a>Apprentissage supervisé

Dans l’apprentissage supervisé, chaque point de données est étiqueté ou associé à une catégorie ou une valeur qui vous intéresse. La définition d’une image comme un « chat » ou un « chien » est un exemple d’étiquette de catégorie. Le prix de vente associé à un véhicule d’occasion est un exemple d’étiquette de valeur. L’objectif de l’apprentissage supervisé est d’étudier de nombreux exemples étiquetés comme ceux-ci, pour pouvoir effectuer des prévisions sur les points de données futurs. Par exemple pour identifier de nouvelles photos avec le bon animal ou affecter des prix de vente corrects à des véhicules d’occasion. Il s’agit d’un type d’apprentissage automatique utile et apprécié.

### <a name="unsupervised-learning"></a>Apprentissage non supervisé

Dans l’apprentissage non supervisé, les points de données n’ont aucune étiquette associée. En effet, l’objectif d’un algorithme d’apprentissage non supervisé est d’organiser les données d’une certaine façon ou de décrire sa structure. Cela peut signifier un regroupement en clusters, comme le fait l’algorithme des k-moyennes ou la recherche de différentes manières de visualiser des données complexes afin d’en simplifier l’affichage.

### <a name="reinforcement-learning"></a>Apprentissage par renforcement

Dans l’apprentissage par renforcement, l’algorithme choisit une action en réponse à chaque point de données. Il s’agit d’une approche courante en robotique, où le jeu des lectures des capteurs à un moment donné est un point de données et où l’algorithme doit choisir l’action suivante du robot. Il est également adapté aux applications d’Internet des objets. L’algorithme d’apprentissage reçoit également un signal de récompense quelques instants plus tard, qui indique la qualité de la décision. En fonction de ce signal, l’algorithme modifie sa stratégie pour atteindre la récompense la plus élevée. 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Studio dans Azure Machine Learning et le Portail Azure](service/overview-what-is-azure-ml.md).

* Consultez la liste des algorithmes et des modules dans la [référence des algorithmes et des modules](algorithm-module-reference/module-reference.md).

* [Tutoriel : Générer un modèle de prévision dans le concepteur Azure Machine Learning](service/ui-tutorial-automobile-price-train-score.md).

* [En savoir plus sur la comparaison entre le Deep Learning et le Machine Learning](service/concept-deep-learning-vs-machine-learning.md).
