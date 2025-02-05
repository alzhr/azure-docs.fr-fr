---
title: 'Didacticiel : Utiliser les événements IoT Hub pour déclencher des applications Azure Logic Apps'
description: 'Didacticiel : À l’aide du service de routage d’événement d’Azure Event Grid, créez des processus automatisés pour effectuer des actions Azure Logic Apps basées sur des événements IoT Hub.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106782"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Tutoriel : Envoyer des notifications par e-mail sur des événements Azure IoT Hub à l’aide de Logic Apps

Azure Event Grid vous permet de réagir aux événements dans IoT Hub en déclenchant des actions dans vos applications d’entreprise en aval.

Cet article présente un exemple de configuration qui utilise IoT Hub et Event Grid. Avant la fin de sa lecture, vous disposerez d’une application logique Azure configurée pour envoyer un e-mail de notification chaque fois qu’un appareil est ajouté à votre hub IoT. 

## <a name="prerequisites"></a>Prérequis

* Un compte e-mail auprès de n’importe quel fournisseur d’e-mail pris en charge par Azure Logic Apps, tel qu’Outlook Office 365, Outlook.com ou Gmail. Ce compte e-mail permet d’envoyer les notifications d’événements. Pour obtenir la liste complète des connecteurs d’application logique pris en charge, consultez la [Vue d’ensemble des connecteurs](https://docs.microsoft.com/connectors/).
* Un compte Azure actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un Hub IoT dans Azure. Si vous n’en avez pas encore créé un, consultez [Prise en main d’IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) pour obtenir une procédure pas à pas. 

## <a name="create-a-logic-app"></a>Créer une application logique

Tout d’abord, créez une application logique et ajoutez un déclencheur Event Grid, qui surveille le groupe de ressources de votre machine virtuelle. 

### <a name="create-a-logic-app-resource"></a>Créer une ressource d’application logique

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Créer une application logique](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Donnez à votre application logique un nom qui est unique dans votre abonnement, puis sélectionnez les mêmes abonnement, groupe de ressources et emplacement que votre hub IoT. 
3. Sélectionnez **Create** (Créer).

4. Une fois la ressource créée, accédez à votre application logique. 

5. Le Concepteur d’applications logiques vous propose des modèles courants pour faciliter vos premiers pas. Dans le Concepteur d’application logique, sous **Modèles**, choisissez **Application logique vide**, afin de développer votre application logique à partir de zéro.

### <a name="select-a-trigger"></a>Sélectionner un déclencheur

Un déclencheur désigne un événement spécifique qui démarre votre application logique. Pour ce didacticiel, le déclencheur qui lance le flux de travail reçoit une demande via HTTP.  

1. Dans la barre de recherche des connecteurs et des déclencheurs, tapez **HTTP**.
2. Sélectionnez **Requête - Lors de la réception d’une requête HTTP** comme déclencheur. 

   ![Sélectionner le déclencheur de requête HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**. 

   ![Sélectionner le déclencheur de requête HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Collez l’exemple de code JSON suivant dans la zone de texte, puis sélectionnez **Terminé** :

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

5. Vous pouvez recevoir une notification contextuelle indiquant **N’oubliez pas d’inclure un en-tête Content-Type défini sur application/JSON dans votre demande**. Vous pouvez ignorer cette suggestion et passer à la section suivante. 

### <a name="create-an-action"></a>Créer une action

Les actions sont toutes les étapes qui se produisent une fois que le déclencheur démarre le flux de travail de l’application logique. Pour ce didacticiel, l’action doit envoyer une notification par e-mail à partir de votre fournisseur d’e-mail. 

1. Sélectionnez **Nouvelle étape**. Cela ouvre une fenêtre pour **choisir une action**.

2. Recherchez **E-mail**.

3. Selon votre fournisseur de messagerie, recherchez et sélectionnez le connecteur correspondant. Ce didacticiel utilise **Office 365 Outlook**. Les étapes pour les autres fournisseurs d’e-mail sont similaires. 

   ![Sélectionner le connecteur du fournisseur d’e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Sélectionner l’action **Envoyer un message électronique**. 

5. Si vous y êtes invité, connectez-vous à votre compte e-mail. 

6. Générez votre modèle d’e-mail. 
   * **À** : entrez l’adresse e-mail à laquelle recevoir les e-mails de notification. Pour ce didacticiel, utilisez un compte e-mail auquel vous pouvez accéder à des fins de test. 
   * **Objet** et **Corps** : écrivez le texte de votre e-mail. Sélectionnez les propriétés JSON à partir de l’outil de sélection pour inclure du contenu dynamique en fonction des données d’événement.  

   Votre modèle d’e-mail peut ressembler à cet exemple :

   ![Renseigner les informations d’e-mail](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Enregistrez votre application logique. 

### <a name="copy-the-http-url"></a>Copier l’URL HTTP

Avant de quitter le Concepteur d’applications logiques, copiez l’URL qu’écoutent vos applications logiques dans l’attente d’un déclencheur. Cette URL vous permet de configurer Event Grid. 

1. Développez la zone de configuration de déclencheur **Lors de la réception d’une demande HTTP** en cliquant dessus. 
2. Copiez la valeur du champ **URL HTTP POST** en sélectionnant le bouton de copie en regard de celui-ci. 

   ![Copier l’URL HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Enregistrez cette URL afin de pouvoir y faire référence dans la section suivante. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurer l’abonnement pour les événements IoT Hub

Dans cette section, vous configurez votre hub IoT pour publier des événements à mesure qu’ils se produisent. 

1. Accédez à votre hub IoT dans le portail Azure. 
2. Sélectionnez **Événements**.

   ![Ouvrir les détails Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Sélectionnez **Abonnement aux événements**. 

   ![Créer un abonnement aux événements](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Créez l’abonnement aux événements avec les valeurs suivantes : 
   * **Type d’événement** : décochez la case S’abonner à tous les types d’événements et sélectionnez **Appareil créé** à partir du menu.
   * **Détails du point de terminaison** : sélectionnez **Webhook** comme type de point de terminaison puis cliquez sur le point de terminaison sélectionné et collez l’URL copiée à partir de votre application logique et confirmez la sélection.

     ![sélectionner l’URL du point de terminaison](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Détails de l’abonnement d’événements** : fournissez un nom descriptif et sélectionnez **Schéma Event Grid**.

   Quand vous avez terminé, votre formulaire doit ressembler à l’exemple suivant : 

    ![Exemple de formulaire d’abonnement aux événements](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Vous pourriez enregistrer l’abonnement aux événements ici et recevoir des notifications pour chaque appareil qui est créé dans votre hub IoT. Pour ce tutoriel, cependant, nous allons utiliser les champs facultatifs pour filtrer des appareils spécifiques. Sélectionnez **Fonctionnalités supplémentaires** en haut du formulaire. 

6. Créez les filtres suivants :

   * **Le sujet commence par** : entrez `devices/Building1_` pour filtrer les événements d’appareil dans l’édifice 1.
   * **Le sujet se termine par** : entrez `_Temperature` pour filtrer les événements d’appareil liés à la température.

5. Sélectionnez **Créer** pour enregistrer l’abonnement aux événements.

## <a name="create-a-new-device"></a>Créer un appareil

Testez votre application logique en créant un appareil pour déclencher un e-mail de notification d’événement. 

1. À partir de votre hub IoT, sélectionnez **Appareils IoT**. 
2. Sélectionnez **Ajouter**.
3. Pour **ID d’appareil**, entrez `Building1_Floor1_Room1_Temperature`.
4. Sélectionnez **Enregistrer**. 
5. Vous pouvez ajouter plusieurs appareils avec différents ID d’appareil pour tester les filtres de l’abonnement aux événements. Essayez ces exemples : 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Une fois que vous avez ajouté quelques appareils à votre hub IoT, vérifiez votre e-mail pour voir quels sont ceux qui ont déclenché l’application logique. 

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Au lieu d’utiliser le portail Azure, vous pouvez effectuer les étapes IoT Hub à l’aide de l’interface de ligne de commande Azure. Pour plus d’informations, consultez les pages de l’interface de ligne de commande Azure consacrées à la [création d’un abonnement aux événements](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) et à la [création d’un appareil IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce didacticiel utilise des ressources qui peuvent entraîner des frais sur votre abonnement Azure. Quand vous avez terminé de tester le didacticiel et vos résultats, désactivez ou supprimez les ressources que vous ne souhaitez pas conserver. 

Pour ne pas perdre le travail effectué sur votre application logique, désactivez-la au lieu de la supprimer. 

1. Accédez à votre application logique.
2. Dans le panneau **Vue d’ensemble**, sélectionner **Supprimer** ou **Désactiver**. 

Chaque abonnement peut avoir un hub IoT gratuit. Si vous avez créé un hub gratuit pour ce didacticiel, vous n’avez pas besoin de le supprimer pour éviter les frais.

1. Accédez à votre hub IoT. 
2. Dans le panneau **Vue d’ensemble**, sélectionnez **Supprimer**. 

Même si vous conservez votre hub IoT, vous pouvez souhaiter supprimer l’abonnement aux événements que vous avez créé. 

1. Dans votre hub IoT, sélectionnez **Grille d’événement**.
2. Sélectionnez l’abonnement aux événements que vous souhaitez supprimer. 
3. Sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur la [réaction aux événements IoT Hub en utilisant Event Grid pour déclencher des actions](../iot-hub/iot-hub-event-grid.md).
* [Apprendre à commander des événements d’état de la connexion et de la déconnexion d’appareils](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Découvrez ce que vous pouvez faire d’autre avec [Event Grid](overview.md).


