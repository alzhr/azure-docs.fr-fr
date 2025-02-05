---
title: Tutoriel – Partager Azure Spatial Anchors entre plusieurs sessions et appareils | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à partager des identificateurs Azure Spatial Anchor entre des appareils Android/iOS dans Unity avec un service back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e8a60d5d90b684698d6fcb612278bcae6d4ed08e
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882300"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Didacticiel : Partager Azure Spatial Anchors entre plusieurs sessions et appareils

Dans ce tutoriel, vous allez apprendre à utiliser [Azure Spatial Anchors](../overview.md) pour créer des ancres au cours d’une session et les localiser ensuite sur le même appareil ou un autre. Ces mêmes ancres pourront aussi être localisées par plusieurs appareils au même emplacement et au même moment.

![Persistance](./media/persistence.gif)

Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils. Quand vous avez terminé, vous disposez d’une application qui peut être déployée sur plusieurs appareils. Les ancres spatiales Azure créées par une instance peuvent être partagées avec d’autres.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer une application web ASP.NET Core dans Azure qui peut être utilisée pour partager des ancres, en les stockant en mémoire pendant un certain temps.
> * Configurer la scène AzureSpatialAnchorsLocalSharedDemo dans l’exemple Unity à partir de nos guides de démarrage rapide pour tirer parti de l’application web de partage des ancres.
> * Déployer et exécuter une application sur un ou plusieurs appareils.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Il est important de noter que vous allez utiliser Unity et une application web ASP.NET Core dans ce tutoriel uniquement pour illustrer un exemple de la façon de partager des identificateurs d’ancre spatiale Azure entre d’autres appareils. Vous pouvez utiliser d’autres langages et technologies back-end pour atteindre le même objectif. Par ailleurs, l’application web ASP.NET Core utilisée dans ce tutoriel a une dépendance sur le kit SDK .NET Core 2.2. Elle s’exécute correctement sur Azure Web Apps standard (pour Windows), mais ne fonctionne pas sur Azure Web Apps pour Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Téléchargement de l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Déployer votre service de partage des ancres

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

Ouvrez Visual Studio, puis ouvrez le projet au dossier `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

Vous devrez créer un groupe de ressources et un plan App Service avant de déployer le service dans VS Code.

### <a name="sign-in-to-azure"></a>Se connecter à Azure

Accédez au <a href="https://portal.azure.com/" target="_blank">portail Azure</a> et connectez-vous à votre abonnement Azure.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

En regard de **Groupe de ressources**, sélectionnez **Nouveau**.

Nommez le groupe de ressources **myResourceGroup**, puis sélectionnez **OK**.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

En regard de **Plan d’hébergement**, sélectionnez **Nouveau**.

Dans la boîte de dialogue **Configurer le plan d’hébergement**, utilisez ces paramètres :

| Paramètre | Valeur suggérée | Description |
|-|-|-|
|Plan App Service| MySharingServicePlan | Nom du plan App Service. |
| Location | USA Ouest | Centre de données dans lequel l’application web est hébergée. |
| Size | Gratuit | [Niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) déterminant les fonctionnalités d’hébergement. |

Sélectionnez **OK**.

Ouvrez Visual Studio Code, puis le projet situé dans le dossier `Sharing\SharingServiceSample`. Suivez <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">ce tutoriel</a> pour déployer le service de partage par le biais de Visual Studio Code. Vous pouvez suivre les étapes à partir de la section « Ouvrir le projet avec Visual Studio Code ». Ne créez pas un autre projet MVC comme expliqué à l’étape ci-dessus, car vous disposez déjà du projet qui doit être déployé et publié : SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Déployer l’exemple d’application

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé une application web ASP.NET Core dans Azure, puis configuré et déployé une application Unity. Vous avez créé des ancres spatiales avec l’application, puis les avez partagées avec d’autres appareils à l’aide de votre application web ASP.NET Core.

Vous pouvez améliorer votre application web ASP.NET Core afin qu’elle utilise Azure Cosmos DB pour rendre le stockage des identificateurs de vos ancres spatiales partagées permanent. L’ajout de la prise en charge d’Azure Cosmos DB permettra à votre application de créer une ancre aujourd’hui et de la relocaliser dans plusieurs jours à partir de l’identificateur d’ancre stocké dans votre application web.

> [!div class="nextstepaction"]
> [Utiliser Azure Cosmos DB pour stocker des ancres](./tutorial-use-cosmos-db-to-store-anchors.md)

