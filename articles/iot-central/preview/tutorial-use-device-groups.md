---
title: Utiliser des groupes d’appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, apprenez à utiliser des groupes d’appareils pour analyser la télémétrie des appareils de votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 281806999b08c3babbb753459835850ad9d733eb
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894353"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>Didacticiel : Utiliser des groupes d’appareils pour analyser les données de télémétrie des appareils (fonctionnalités d’évaluation)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Cet article décrit comment utiliser des groupes d’appareils en tant qu’opérateur afin d’analyser la télémétrie des appareils dans votre application Azure IoT Central.

Un groupe d’appareils est une liste d’appareils qui sont regroupés, car ils correspondent à certains critères spécifiés. Les groupes d’appareils vous permettent de gérer, de visualiser et d’analyser des appareils à grande échelle en regroupant les appareils dans des groupes logiques plus petits. Par exemple, vous pouvez créer un groupe d’appareils répertoriant tous les appareils de climatisation à Seattle pour permettre à un technicien de rechercher les appareils dont il est responsable.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe d’appareils
> * Utiliser un groupe d’appareils pour analyser la télémétrie des appareils

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez suivre les démarrages rapides [Créer une application Azure IoT Central](./quick-deploy-iot-central.md) et [Ajouter un appareil simulé à votre application IoT Central](./quick-create-pnp-device.md) pour créer le modèle d’appareil **Environment Sensor** (Capteur environnemental) à utiliser.

## <a name="create-simulated-devices"></a>Créer des appareils simulés

Avant de créer un groupe d’appareils, ajoutez au moins cinq appareils simulés à partir du modèle d’appareil **Capteur d’environnement** à utiliser dans ce didacticiel :

![Cinq appareils capteurs environnementaux simulés](./media/tutorial-use-device-groups/simulated-devices.png)

Pour quatre des capteurs d’environnement, utilisez l’affichage **Propriétés de Capteur environnemental** pour définir le nom du client sur **Contoso** :

![Définir le nom du client sur Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Créer un groupe d’appareils

Pour créer un groupe d’appareils :

1. Choisissez **Groupe d’appareils** dans le volet gauche.

1. Sélectionnez **+Nouveau**.

    ![Nouveau groupe d’appareils](media/tutorial-use-device-groups/image1.png)

1. Donnez un nom à votre groupe d’appareils, par exemple **Appareils Contoso**. Vous pouvez également ajouter une description. Un groupe d’appareils peut contenir seulement des appareils d’un même modèle d’appareil. Choisissez le modèle d’appareil **Capteur environnemental** à utiliser pour ce groupe.

1. Créez la requête pour identifier les appareils appartenant à **Contoso** mepour le groupe d’appareils en sélectionnant la propriété **Customer Na**, l’opérateur de comparaison **Equals**, et **Contoso** comme valeur. Vous pouvez ajouter plusieurs requêtes, et les appareils qui répondent à **tous** les critères sont placés dans le groupe d’appareils. Le groupe d’appareils que vous créez est accessible à toute personne ayant accès à l’application : toutes ces personnes peuvent donc voir, modifier ou supprimer le groupe d’appareils.

    ![Requête de groupe d’appareils](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > Le groupe d’appareils est une requête dynamique. Chaque fois que vous visualisez la liste des appareils, des appareils différents peuvent figurer dans la liste. La liste varie selon les appareils qui répondent actuellement aux critères de la requête.

1. Choisissez **Enregistrer**.

> [!NOTE]
> Pour les appareils Azure IoT Edge, sélectionnez les modèles Azure IoT Edge pour créer un groupe d’appareils.

## <a name="analytics"></a>Analytics

Vous pouvez utiliser **Analytics** avec un groupe d’appareils pour analyser les données de télémétrie des appareils dans le groupe. Par exemple, vous pouvez tracer la température moyenne signalée par tous les capteurs environnementaux de Contoso.

Pour analyser les données de télémétrie d’un groupe d’appareils :

1. Choisissez **Analytics** dans le volet gauche.

1. Sélectionnez le groupe d’appareils intitulé **Appareils Contoso** que vous avez créé. Ajoutez ensuite les types de données de télémétrie **Température** et **Humidité** :

    ![Créer des analyses](./media/tutorial-use-device-groups/create-analysis.png)

    Utilisez les icônes en forme de roue dentée en regard des types données de télémétrie pour sélectionner un type d’agrégation. La valeur par défaut est **Moyenne**. Utilisez **Fractionner par** pour modifier la façon dont les données agrégées sont affichées. Par exemple, si vous sélectionnez **Analyser** alors que vous fractionnez par ID d’appareil, un tracé s’affiche pour chaque appareil.

1. Sélectionnez **Analyser** pour afficher les valeurs de télémétrie moyennes :

    ![Afficher l’analyse](./media/tutorial-use-device-groups/view-analysis.png)

    Vous pouvez personnaliser l’affichage, modifier la période affichée et exporter les données.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des groupes d’appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](tutorial-create-telemetry-rules.md)
