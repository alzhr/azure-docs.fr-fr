---
title: Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory
description: Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387215"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory



![Options de branche](media/data-flow/menu.png "menu")

La création de branches prend votre flux de données actuel et le réplique dans un autre flux. Utilisez une nouvelle branche pour effectuer plusieurs ensembles d’opérations et de transformations sur le même flux de données.

Exemple : Votre flux de données a une transformation source avec un ensemble sélectionné de colonnes et de conversions de types de données. Vous placez alors une colonne dérivée qui suit immédiatement cette source. Dans la colonne dérivée, vous créez un nouveau champ qui combine un prénom et un nom pour faire un nouveau champ « nom complet ».

Vous pouvez traiter ce nouveau flux avec un ensemble de transformations et un récepteur sur une ligne et utiliser la nouvelle branche pour créer une copie de ce flux où vous pouvez transformer ces mêmes données avec un autre ensemble de transformations. En transformant ces données copiées dans une branche distincte, vous pouvez par la suite les réceptionner à un autre emplacement.

> [!NOTE]
> « Nouvelle branche » s’affiche uniquement en tant qu’action sur le menu Transformation + en cas de transformation en aval de l’emplacement actuel où vous tentez de créer une branche. Vous ne verrez donc pas d’option « Nouvelle branche » à la fin si vous n’ajoutez pas de transformation après la sélection.

![Branche](media/data-flow/branch2.png "Branche 2")
