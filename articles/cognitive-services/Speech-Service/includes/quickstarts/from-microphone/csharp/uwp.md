---
title: 'Démarrage rapide : Reconnaître la voix à partir d’un micro, C# (UWP) - Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 0d1da9a9ef32aed1975595bb15909b9531ab2400
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505618"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, effectuez les étapes suivantes :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=uwp)

Si vous l’avez déjà fait, c’est parfait. Poursuivons.

## <a name="open-your-project-in-visual-studio"></a>Ouvrez votre projet dans Visual Studio.

La première étape consiste à vérifier que votre projet est ouvert dans Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Commencer par du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet.

1. Dans l’**Explorateur de solutions**, ouvrez `MainPage.xaml`.

2. Dans la vue XAML du concepteur, insérez l’extrait de code XAML suivant dans la balise **Grid** (entre `<Grid>` et `</Grid>`) :

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. Dans l’**Explorateur de solutions**, ouvrez le fichier source code-behind `MainPage.xaml.cs`. (Il se trouve sous `MainPage.xaml`.)

4. Remplacez le code par le code de base suivant :

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Créer une configuration Speech

Avant de pouvoir initialiser un objet `SpeechRecognizer`, vous devez créer une configuration qui utilise votre clé d’abonnement et la région de votre abonnement. Insérez ce code dans la méthode `RecognizeSpeechAsync()`.

> [!NOTE]
> Cet exemple utilise la méthode `FromSubscription()` pour générer la `SpeechConfig`. Pour obtenir la liste complète des méthodes disponibles, consultez [SpeechConfig, classe](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Initialiser un SpeechRecognizer

Créons maintenant un `SpeechRecognizer`. Cet objet est créé à l’intérieur d’une instruction using pour garantir la bonne libération des ressources non managées. Insérez ce code dans la méthode `RecognizeSpeechAsync()`, juste en dessous de votre configuration Speech.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Reconnaître une expression

À partir de l’objet `SpeechRecognizer`, vous devez appeler la méthode `RecognizeOnceAsync()`. Cette méthode permet au service Speech de savoir que vous envoyez une seule expression pour reconnaissance, et d’arrêter la reconnaissance une fois que l’expression a été identifiée.

À l’intérieur de l’instruction using, ajoutez le code suivant : [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Afficher les résultats de la reconnaissance (ou les erreurs)

Une fois que le résultat de la reconnaissance est retourné par le service Speech, vous pouvez l’utiliser. Nous allons faire simple et afficher le résultat dans le panneau d’état.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

Vous êtes maintenant prêt à générer et à tester votre application.

1. Dans la barre de menus, choisissez **Générer** > **Générer la solution** pour générer l’application. Le code doit maintenant se compiler sans erreurs.

1. Choisissez **Déboguer** > **Démarrer le débogage** (ou appuyez sur **F5**) pour démarrer l’application. La fenêtre **helloworld** s’affiche.

   ![Exemple d’application de reconnaissance vocale UWP en C# - Démarrage rapide](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Sélectionnez **Activer le microphone** et, quand la demande d’autorisation d’accès apparaît, sélectionnez **Oui**.

   ![Demande d’autorisation d’accès au microphone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Sélectionnez **Speech recognition with microphone input** (Reconnaissance vocale avec entrée par micro) et prononcez une phrase ou quelques mots en anglais dans le micro de votre appareil. Votre production orale est transmise aux services vocaux, et transcrite en texte qui apparaît dans la fenêtre.

   ![Interface utilisateur de la reconnaissance vocale](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
