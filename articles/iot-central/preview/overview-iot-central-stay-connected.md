---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article résume la façon dont Azure IoT Central vous aide à maintenir la connexion et l’intégrité de vos appareils.
author: aaronbjork
ms.author: abjork
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: ''
ms.openlocfilehash: 20001247c608a52ffd18920c0b6b1f1aabd28313
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894389"
---
# <a name="stay-connected-with-azure-iot-central-preview-features"></a>Rester connecté à Azure IoT Central (fonctionnalités en préversion)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Cet article fournit une vue d’ensemble sur la façon dont Azure IoT Central vous aide à maintenir la connexion et l’intégrité de vos appareils.

Pour toute solution IoT conçue pour fonctionner à grande échelle, il est important de disposer d’une approche bien structurée de la gestion des appareils. Il ne suffit pas de connecter les appareils avec la puissance suffisante, il faut une approche pour les maintenir connectés et en bon état au fur et à mesure que votre solution évolue, grandit et vieillit. Azure IoT Central fournit les fonctionnalités nécessaires pour garantir que les appareils de votre solution IoT sont bien traités pendant tout le cycle de vie de l’application.

## <a name="dashboards"></a>Tableaux de bord 
Les [tableaux de bord](howto-manage-devices.md#import-devices) intégrés fournissent une surface d’exposition personnalisable pour superviser la télémétrie et l’intégrité des appareils. Commencez avec un tableau de bord prédéfini provenant d’un [modèle d’application](howto-use-app-templates.md) ou créez vos propres tableaux de bord adaptés aux besoins de votre application. Les tableaux de bord peuvent être partagés avec tous les utilisateurs de votre application, ou gardés privés.

## <a name="rules-and-actions"></a>Règles et actions 
Identifiez rapidement les appareils nécessitant une attention particulière en générant des [règles personnalisées](tutorial-create-telemetry-rules.md), basées sur la télémétrie et l’état des appareils. Configurez des actions pour informer les personnes appropriées, en veillant à ce que les mesures correctives interviennent en temps voulu.

## <a name="jobs"></a>Tâches 
Les [travaux](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) vous permettent d’effectuer des mises à jour simples ou en bloc sur les propriétés, les paramètres et les commandes des appareils. 

## <a name="user-roles-and-permissions"></a>Rôles et autorisations d’utilisateur
Les [rôles et autorisations](howto-manage-users-roles.md) vous donnent la possibilité d’adapter l’expérience de chaque utilisateur. Créez des rôles directement par le biais de l’interface utilisateur et attribuez aisément les autorisations appropriées. 




