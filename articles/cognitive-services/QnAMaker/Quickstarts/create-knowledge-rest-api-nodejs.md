---
title: 'Démarrage rapide : QnA Maker avec les API REST pour Node.js'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les API REST QnA Maker pour Node.js. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 4393609bf426c6ae99c48a5d84162526aeff6fb7
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803518"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Démarrage rapide : QnA Maker avec les API REST pour Node.js

Découvrez comment utiliser les API REST QnA Maker pour Node.js. Effectuez les étapes suivantes pour tester l’exemple de code sur différentes tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit. 

Utilisez QnA Maker avec les API REST pour Node.js dans le cadre des opérations suivantes :

* Créer une base de connaissances
* Remplacer une base de connaissances
* Publier une base de connaissances
* Supprimer une base de connaissances
* Télécharger une base de connaissances
* Obtenir l’état d’une opération

[Documentation de référence](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Exemples Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org).
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé et votre point de terminaison (qui incluent le nom de la ressource), sélectionnez **Démarrage rapide** pour votre ressource dans le portail Azure.

## <a name="setting-up"></a>Configuration

### <a name="create-a-qna-maker-azure-resource"></a>Créer une ressource Azure QnA Maker

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour QnA Maker en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. 

Après avoir obtenu une clé à partir de votre ressource, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la ressource, et nommez-les `QNAMAKER_RESOURCE_KEY` et `QNAMAKER_AUTHORING_ENDPOINT`. Utilisez les valeurs de clé et de point de terminaison qui se trouvent dans la page **Démarrage rapide** de la ressource dans le portail Azure.

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init -y` pour créer un fichier `package.json` de nœud. 

```console
npm init -y
```

Ajoutez les packages NPM `reqeuestretry` et `request` :

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec les API REST QnA Maker pour Node.js :

* [Créer une base de connaissances](#create-a-knowledge-base)
* [Remplacer une base de connaissances](#replace-a-knowledge-base)
* [Publier une base de connaissances](#publish-a-knowledge-base)
* [Supprimer une base de connaissances](#delete-a-knowledge-base)
* [Télécharger une base de connaissances](#download-the-knowledge-base)
* [Obtenir l’état d’une opération](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Ajouter les dépendances



Créez un fichier nommé `rest-apis.js`, puis ajoutez l’instruction _requires_ suivante pour créer des requêtes HTTP. 

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Ajouter des informations relatives aux ressources Azure

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Une base de connaissances stocke des paires question-réponse, créées à partir d’un objet JSON de type :

* **Contenu éditorial** 
* **Fichiers** : fichiers locaux qui ne nécessitent pas d’autorisations. 
* **URL** : URL disponibles publiquement.

Utilisez l’[API REST pour créer une base de connaissances](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). 

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Remplacer une base de connaissances

Utilisez l’[API REST pour remplacer une base de connaissances](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publier la base de connaissances. Ce processus rend la base de connaissances disponible à partir d’un point de terminaison de prédiction de requête HTTP.

Utilisez l’[API REST pour publier une base de connaissances](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Télécharger la base de connaissances 

Utilisez l’[API REST pour télécharger une base de connaissances](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Lorsque vous avez fini d’utiliser la base de connaissances, supprimez-la.

Utilisez l’[API REST pour supprimer une base de connaissances](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Les processus de longue durée, tels que le processus de création, retournent un ID d’opération qui doit être vérifié à l’aide d’un appel d’API REST distinct. Cette fonction accepte le corps de la réponse Create. La clé importante est `operationState`, qui détermine si vous devez continuer l’interrogation.

Utilisez l’[API REST pour superviser les opérations qui sont effectuées dans une base de connaissances](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node rest-apis.js` à partir de votre répertoire d’application.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Tutoriel : Créer et interroger une base de connaissances](../tutorials/create-publish-query-in-portal.md)

* [Qu’est-ce que l’API QnA Maker ?](../Overview/overview.md)
* [Modifier une base de connaissances](../how-to/edit-knowledge-base.md)
* [Obtenir une analyse de l'utilisation](../how-to/get-analytics-knowledge-base.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).