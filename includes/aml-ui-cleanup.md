---
title: Fichier Include
description: Fichier Include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 96ede63b097999247675364217cf458a268e54d9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929671"
---
>[!IMPORTANT]
>Vous pouvez utiliser les ressources que vous avez créées comme prérequis pour d’autres didacticiels et articles de guides pratiques Azure Machine Learning.

### <a name="delete-everything"></a>Tout supprimer

Si vous n’avez pas l’intention d’utiliser les éléments que vous avez créés, supprimez l’intégralité du groupe de ressources pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** sur le côté gauche de la fenêtre.
 
   ![Supprimer un groupe de ressources dans le portail Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Dans la liste, sélectionnez le groupe de ressources créé.

1. Sélectionnez **Supprimer le groupe de ressources**.

La suppression du groupe de ressources supprime également toutes les ressources créées dans le concepteur. 

### <a name="delete-individual-assets"></a>Supprimer des ressources individuelles

Dans le concepteur où vous avez créé votre expérience, supprimez des ressources individuelles en les sélectionnant, puis en sélectionnant le bouton **Supprimer**.

La cible de calcul que vous avez créée ici *est automatiquement mise à l’échelle* sur zéro nœud quand elle n’est pas utilisée. Cette action est effectuée pour réduire les frais. Si vous souhaitez supprimer la cible de calcul, procédez comme suit :

![Supprimer des ressources](./media/aml-ui-cleanup/delete-asset.png)

Vous pouvez désinscrire des jeux de données de votre espace de travail en sélectionnant chaque jeu de données, puis **Annuler l’enregistrement**.

![Désinscrire le jeu de données](./media/aml-ui-cleanup/unregister-dataset.png)

Pour supprimer un jeu de données, accédez au compte de stockage à l’aide du portail Azure ou de l’Explorateur Stockage Azure et supprimez manuellement ces ressources.


