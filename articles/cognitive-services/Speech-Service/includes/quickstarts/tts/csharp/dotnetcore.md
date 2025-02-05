---
title: 'Démarrage rapide : Synthèse vocale, C# (.NET Core) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Découvrir la synthèse vocale en C# sous .NET Core sur Windows à l’aide du SDK Speech
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504882"
---
> [!NOTE]
> .NET Core est une plateforme .NET à vocation multiplateforme, open source, qui implémente la spécification [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, effectuez les étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `Program.cs` et remplacez tout le code de ce fichier par ce qui suit.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Dans le même fichier, remplacez la chaîne `YourSubscriptionKey` par votre clé d’abonnement.

1. De même, remplacez la chaîne `YourServiceRegion` par la [région](~/articles/cognitive-services/Speech-Service/regions.md) associée à votre abonnement (par exemple, `westus` pour l’abonnement à un essai gratuit).

1. Enregistrez les modifications apportées au projet.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Générez l’application. Dans la barre de menus, choisissez **Générer** > **Générer la solution**. Le code doit se compiler sans erreur.

    ![Capture d’écran de l’application Visual Studio, avec l’option Générer la solution en surbrillance](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build réussie")

1. Lancez l’application. Dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.

    ![Capture d’écran de l’application Visual Studio, avec l’option Démarrer le débogage en surbrillance](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Démarrer l’application en mode débogage")

1. Une fenêtre de console s’affiche, vous invitant à taper du texte. Tapez quelques mots ou une phrase. Le texte que vous avez tapé est transmis aux services Speech et est synthétisé en paroles, qui sont lues sur votre haut-parleur.

    ![Capture d’écran de la sortie de la console après une synthèse réussie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Sortie de la console après une synthèse réussie")

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
