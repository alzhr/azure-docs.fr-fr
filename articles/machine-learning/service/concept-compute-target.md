---
title: 'Cibles de calcul : emplacement où entraîner et déployer des modèles'
titleSuffix: Azure Machine Learning
description: Définissez l’emplacement où vous souhaitez déployer votre modèle et en faire l’apprentissage avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 75e789ac1f9c161bc67ba90062be39f986f5b140
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580577"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Qu’est-ce qu’une cible de calcul dans Azure Machine Learning ? 

Une **cible de calcul** est un environnement/une ressource de calcul où vous exécutez votre script d’entraînement ou hébergez votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud. Les cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code.  

Dans un cycle de vie de développement de modèle type, vous pouvez effectuer les opérations suivantes :
1. Commencez par développer et expérimenter une petite quantité de données. À ce stade, nous vous recommandons d’utiliser votre environnement local (ordinateur local ou machine virtuelle cloud) comme cible de calcul. 
2. Passez ensuite à des jeux de données plus volumineux, ou effectuez un entraînement distribué à l’aide d’une des [cibles de calcul d’entraînement](#train).  
3. Une fois que votre modèle est prêt, déployez-le sur un environnement d’hébergement web ou un appareil IoT avec l’une de ces [cibles de calcul de déploiement](#deploy).

Les ressources de calcul utilisées pour vos cibles de calcul sont associées à un [espace de travail](concept-workspace.md). Les cibles de calcul autres que l’ordinateur local sont partagées par les utilisateurs de l’espace de travail.

## <a name="train"></a> Cibles de calcul d’entraînement

La prise en charge d’Azure Machine Learning varie selon les ressources de calcul.  Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

En savoir plus sur [la configuration et l’utilisation d’une cible de calcul pour l’entraînement du modèle](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Cibles de déploiement

Les cibles de calcul suivantes peuvent héberger le déploiement de votre modèle.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Découvrez [où et comment déployer votre modèle sur une cible de calcul](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcul Azure Machine Learning (managé)

Une ressource de calcul managée est créée et managée par Azure Machine Learning. Ce calcul est optimisé pour les charges de travail Machine Learning. Clusters de calcul Azure Machine Learning. Il se peut que d’autres ressources de calcul managées soient ajoutées à l’avenir.

Vous pouvez créer des clusters de calcul Azure Machine Learning dans Azure Machine Learning Studio, dans le portail Azure, dans le SDK, dans un modèle Resource Manager ou dans l’interface CLI.

Une fois créées, ces instances de calcul font automatiquement partie de votre espace de travail, contrairement à d’autres types de cibles de calcul.

Vous pouvez utiliser les clusters de calcul Azure Machine Learning pour l’entraînement et l’inférence par lots (version préliminaire).  Grâce à cette ressource de calcul, vous disposez de ce qui suit :

* Cluster unique ou à plusieurs nœuds
* Mises à l’échelle automatique chaque fois que vous envoyez une exécution 
* Gestion des clusters et planification automatiques des travaux 
* Prise en charge des ressources UC et GPU



## <a name="unmanaged-compute"></a>Calcul non managé

Une cible de calcul non managée n’est *pas* managée par Azure Machine Learning. Vous créez ce type de cible de calcul en dehors d’Azure Machine Learning, puis l’attachez à votre espace de travail. Il se peut que des étapes supplémentaires soient requises pour ces ressources de calcul non managées afin de maintenir ou d’améliorer les performances des charges de travail Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :
* [Configurer une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md)
* [Déployer votre modèle sur une cible de calcul](how-to-deploy-and-where.md)