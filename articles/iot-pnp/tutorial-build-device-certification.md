---
title: Créer un appareil IoT Plug-and-Play (préversion) prêt pour la certification | Microsoft Docs
description: Avec ce tutoriel destiné aux développeurs d’appareils, vous allez découvrir comment créer un appareil IoT Plug-and-Play (préversion) prêt pour la certification.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4dd5215812f0fd1a43afe0923601417bc8e6916
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569636"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Créer un appareil IoT Plug-and-Play (préversion) prêt pour la certification

Ce tutoriel destiné aux développeurs d’appareils vous explique comment créer un appareil IoT Plug-and-Play (préversion) prêt pour la certification.

Les tests de certification vérifient les points suivants :

- Votre code d’appareil IoT Plug-and-Play est installé sur votre appareil.
- Votre code d’appareil IoT Plug-and-Play est compilé avec le SDK Azure IoT.
- Votre code d’appareil prend en charge le [service Azure IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md).
- Votre code d’appareil implémente l’interface Informations sur l’appareil.
- Le modèle de capacité et le code d’appareil fonctionnent avec IoT Central.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- [Visual Studio Code](https://code.visualstudio.com/download)
- Pack d’extension [Azure IoT Tools pour VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Vous avez également besoin de l’appareil IoT Plug-and-Play créé dans le cadre du [guide démarrage rapide : Utiliser un modèle de capacité d’appareil pour créer un appareil](quickstart-create-pnp-device.md).

## <a name="store-a-capability-model-and-interfaces"></a>Stocker un modèle de capacité et des interfaces

Pour les appareils IoT Plug-and-Play, vous devez créer un modèle de capacité et des interfaces qui définissent les capacités de l’appareil sous forme de fichiers JSON.

Vous pouvez stocker ces fichiers JSON à trois emplacements :

- Dans le référentiel de modèles public
- Dans votre référentiel de modèles d’entreprise
- Sur votre appareil

Actuellement, en vue de la certification de votre appareil, les fichiers doivent être stockés dans votre référentiel de modèles d’entreprise ou le référentiel de modèles public.

## <a name="include-the-required-interfaces"></a>Inclure les interfaces nécessaires

Pour passer le processus de certification, vous devez inclure et implémenter l’interface **Informations sur l’appareil** dans votre modèle de capacité. Cette interface présente l’identification suivante :

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Si vous avez suivi le [guide de démarrage rapide : Utiliser un modèle de capacité d’appareil pour créer un appareil](quickstart-create-pnp-device.md), vous avez déjà inclus l’interface **Informations sur l’appareil** dans votre modèle.

Pour inclure l’interface **Informations sur l’appareil** dans votre modèle d’appareil, ajoutez l’ID d’interface à la propriété `implements` du modèle de capacité :

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Pour voir l’interface **Informations sur l’appareil** dans VS Code :

1. Appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes.

1. Entrez **Plug-and-Play**, puis sélectionnez la commande **IoT Plug-and-Play - Ouvrir le référentiel de modèles**. Sélectionnez **Ouvrir le référentiel de modèles public**. Le référentiel de modèles public s’ouvre dans VS Code.

1. Dans le référentiel de modèles publics, sélectionnez l’onglet **Interfaces**, cliquez sur l’icône de filtre, puis entrez **Informations sur l’appareil** dans le champ de filtre.

1. Pour créer une copie locale de l’interface **Informations sur l’appareil**, sélectionnez-la dans la liste filtrée, puis sélectionnez **Télécharger**. VS Code affiche le fichier d’interface.

Pour afficher l’interface **Informations sur l’appareil** à l’aide d’Azure CLI :

1. [Installez l’extension Azure IoT pour CLI](howto-install-pnp-cli.md).

1. Utilisez la commande Azure CLI suivante pour afficher une interface avec l’ID d’interface Informations sur l’appareil :

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Pour plus d’informations, consultez [Installer et utiliser l’extension Azure IoT pour Azure CLI](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Mettre à jour le code d’appareil

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Autoriser le provisionnement des appareils avec le service Azure IoT Device Provisioning (DPS)

Pour être certifié, l’appareil doit autoriser le provisionnement avec le [service Azure IoT Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Pour ajouter la possibilité d’utiliser le service DPS, vous pouvez générer le stub de code C dans VS Code. Procédez comme suit :

1. Ouvrez le dossier contenant le fichier DCM dans VS Code, appuyez sur **Ctrl+Maj+P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play**, puis sélectionnez **Générer le stub de code de l’appareil**.

1. Choisissez le fichier DCM que vous souhaitez utiliser pour générer le stub de code de l’appareil.

1. Entrez le nom du projet, c’est-à-dire le nom de votre application d’appareil.

1. Choisissez le langage **ANSI C**.

1. Choisissez la méthode de connexion **Avec une clé symétrique DPS (service Device Provisioning)** .

1. Choisissez **Projet CMake sur Windows** ou **Projet CMake sur Linux** comme modèle de projet en fonction du système d’exploitation de votre appareil.

1. VS Code ouvre une nouvelle fenêtre avec les fichiers de stub de code d’appareil générés.

1. Après avoir généré le code, entrez les informations d’identification DPS (**Étendue d’ID DPS**, **Clé symétrique DPS**, **ID d’appareil**) comme paramètres pour l’application. Pour obtenir les informations d’identification à partir du portail de certification, consultez [Connecter et tester votre appareil IoT Plug-and-Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>Implémenter des interfaces standard

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implémenter les interfaces d’informations de modèle et de SDK

L’Azure IoT device SDK implémente les interfaces d’informations de modèle et de SDK. Si vous utilisez la fonction de génération de code dans VS Code, le code de votre appareil utilise le kit IoT Plug-and-Play device SDK.

Si vous avez choisi de ne pas utiliser le kit Azure IoT device SDK, vous pouvez utiliser le code source du SDK comme référence pour votre propre implémentation.

#### <a name="implement-the-device-information-interface"></a>Implémenter l’interface Informations sur l’appareil

Implémentez l’interface **Informations sur l’appareil** sur votre appareil et fournissez des informations spécifiques à l’appareil à partir de ce dernier au moment de l’exécution.

Vous pouvez utiliser un exemple d’implémentation de l’interface **Informations sur l’appareil** pour [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) en tant que référence.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implémenter toutes les capacités définies dans votre modèle

Pendant la certification, votre appareil est testé par programme pour vérifier qu’il implémente les capacités définies dans ses interfaces. Utilisez le code d’état HTTP 501 pour répondre aux requêtes de commandes et de propriétés en lecture-écriture si votre appareil ne les implémente pas.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un appareil IoT Plug-and-Play prêt pour la certification, nous vous recommandons de passer à l’étape ci-après :

> [!div class="nextstepaction"]
> [Savoir comment certifier un appareil](tutorial-certification-test.md)
