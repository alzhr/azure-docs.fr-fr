---
title: Aide à la prédiction grâce aux modèles – LUIS
titleSuffix: Azure Cognitive Services
description: Un modèle vous permet d’obtenir plus de précision pour une intention sans fournir de nombreux énoncés en plus.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6234a13d07ac024849d1c890d82ef03e19c11af4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012009"
---
# <a name="patterns-improve-prediction-accuracy"></a>Les modèles améliorent la précision de la prédiction
Les modèles sont conçus pour améliorer la précision lorsque plusieurs énoncés sont très similaires.  Un modèle vous permet d’obtenir plus de précision pour une intention sans fournir de nombreux énoncés en plus. 

## <a name="patterns-solve-low-intent-confidence"></a>Les modèles résolvent les problèmes de faible confiance envers les intentions
Imaginez une application Ressources humaines qui génère des rapports sur l’organigramme en lien avec un employé. Selon le nom et les relations d’un employé, LUIS peut retourner les employés impliqués. Imaginez un employé, Tom, avec une manager nommée Alice et une équipe de subordonnés qui s’appellent Michael, Rebecca et Carl.

![Image de l’organigramme](./media/luis-concept-patterns/org-chart.png)

|Énoncés|Intention prédite|Score d’intention|
|--|--|--|
|Qui est subordonné à Tom ?|GetOrgChart|.30|
|Qui est le subordonné de Tom ?|GetOrgChart|.30|

Si une application présente entre 10 et 20 énoncés de longueurs différentes, avec un ordre de mots, voire même des mots différents (synonymes de « subordonné », « gérer », « rapport »), LUIS peut renvoyer un score de confiance faible. Créez un modèle pour aider LUIS à comprendre l’importance de l’ordre des mots. 

Les modèles peuvent résoudre les situations suivantes : 

* Le score d’intention est faible.
* La bonne intention n’est pas le meilleur score, mais en est trop proche. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Les modèles ne garantissent pas l’intention
Les modèles utilisent un mélange de technologies de prédiction. La définition d’une intention pour un exemple d’énoncé dans un modèle ne garantit pas la prédiction de l’intention, mais constitue un signal fort. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Les modèles n’améliorent pas la détection d’entités apprises automatiquement

Un modèle sert principalement à faciliter la prédiction des intentions et des rôles. L’entité pattern.any permet d’extraire des entités à structure libre. Même s’ils utilisent des entités, les modèles n’aident pas à détecter des entités apprises automatiquement.  

Ne vous attendez pas à constater une amélioration de la prédiction d’entité si vous réduisez plusieurs énoncés dans un modèle unique. Pour que les entités Simple se déclenchent, vous devez ajouter des énoncés ou utiliser des entités de liste, sinon votre modèle ne se déclenchera pas.

## <a name="patterns-use-entity-roles"></a>Les modèles utilisent des rôles d’entités
Si deux ou plusieurs entités dans un modèle sont associées de manière contextuelle, les modèles utilisent des [rôles](luis-concept-roles.md) d’entités pour extraire des informations contextuelles sur les entités.  

## <a name="prediction-scores-with-and-without-patterns"></a>Scores de prédiction avec et sans modèles
Avec suffisamment d’exemples d’énoncés, LUIS doit pouvoir accroître la confiance des prévisions sans modèles. Les modèles augmentent le score de confiance sans avoir à fournir autant énoncés.  

## <a name="pattern-matching"></a>Critères spéciaux
Un modèle est d’abord mis en correspondance selon la détection des entités dans le modèle, puis selon la validation du reste des mots et de l’ordre des mots du modèle. Des entités sont requises dans le modèle pour qu’un modèle corresponde. Le modèle est appliqué au niveau du jeton, pas au niveau du caractère. 

## <a name="pattern-only-apps"></a>Applications comportant uniquement des modèles
Il est possible de créer des applications dont les intentions ne comportent aucun exemple d’énoncé, car il existe un modèle pour chaque intention. Le modèle en question ne doit pas contenir d’entités apprises automatiquement, pour lesquelles des exemples d’énoncés sont nécessaires. 

## <a name="best-practices"></a>Bonnes pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Syntaxe du modèle

Découvrez la syntaxe du modèle à partir des [informations de référence correspondantes](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les modèles :

* [Comment ajouter des modèles](luis-how-to-model-intent-pattern.md)
* [Comment ajouter une entité pattern.any](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Syntaxe des modèles](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Apprenez à implémenter des modèles dans ce tutoriel](luis-tutorial-pattern.md)
