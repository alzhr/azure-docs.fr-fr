---
title: 'Démarrage rapide : Créer une application iOS Unity avec Azure Spatial Anchors | Microsoft Docs'
description: Dans ce guide de démarrage rapide, vous allez apprendre à générer une application iOS avec Unity en utilisant Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: a121cc7bcb9fba3a01c1e71c7b9e6fc67dce0572
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092160"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Démarrage rapide : Créer une application iOS Unity avec Azure Spatial Anchors

Ce guide de démarrage rapide explique comment créer une application iOS Unity à l’aide d’[Azure Spatial Anchors](../overview.md). Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils. Quand vous aurez terminé, vous aurez une application iOS ARKit générée avec Unity capable d’enregistrer et de rappeler une ancre spatiale.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Spatial Anchors
> * Préparer les paramètres de build Unity
> * Configurer l’identificateur et la clé du compte Spatial Anchors
> * Exporter le projet Xcode
> * Déployer et exécuter sur un appareil iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Une machine macOS dotée de <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1+</a> et des dernières versions de <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> et <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git installé via HomeBrew. Entrez la commande suivante sur une seule ligne du Terminal : `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Exécutez ensuite `brew install git`.
- Un appareil iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatible ARKit</a> prêt pour le développement.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Télécharger et ouvrir l’exemple de projet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurer l’identificateur et la clé du compte

Dans le volet **Project**, accédez à `Assets/AzureSpatialAnchors.Examples/Scenes` et ouvrez le fichier de scène `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Enregistrez la scène en sélectionnant **File** -> **Save**.

## <a name="export-the-xcode-project"></a>Exporter le projet Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Suivez les instructions dans l’application pour placer et rappeler une ancre.

Lorsque vous avez terminé, arrêtez l’application en appuyant sur **Stop** dans Xcode.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="rendering-issues"></a>Problèmes de rendu

Pendant l’exécution de l’application, si vous ne voyez pas de caméra en arrière-plan (au lieu de cela, l’arrière-plan est vide, bleu ou présente d’autres textures), vous devez probablement réimporter les ressources dans Unity. Arrêtez l’application. Dans le menu supérieur d’Unity, choisissez **Assets -> Re-import all**. Ensuite, exécutez de nouveau l’application.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problèmes relatifs à CocoaPods sur macOS Catalina (10.15)

Si vous avez récemment effectué une mise à jour vers macOS Catalina (10.15) en ayant déjà installé CocoaPods, ce dernier risque de passer en état défectueux et de configurer incorrectement vos pods et fichiers de projet `.xcworkspace`. Pour résoudre ce problème, vous devez réinstaller CocoaPods en exécutant les commandes suivantes :

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutoriel : Partager Spatial Anchors sur des appareils](../tutorials/tutorial-share-anchors-across-devices.md)
