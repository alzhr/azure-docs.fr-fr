---
title: Définir des clés uniques pour un conteneur Azure Cosmos
description: Découvrez comment définir des clés uniques pour un conteneur Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: thweiss
ms.openlocfilehash: 2f61afd12a50b1b135419585bfada52d7ef98f3a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811637"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Définir des clés uniques pour un conteneur Azure Cosmos

Cet article présente les différentes façons de définir des [clés uniques](unique-keys.md) lors de la création d’un conteneur Azure Cosmos. Il est actuellement possible d’effectuer cette opération à l’aide du portail Azure ou de l’un des kits SDK.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-sql-api-dotnet.md#create-account) ou sélectionnez-en un existant.

1. Ouvrez le volet **Explorateur de données**, puis sélectionnez le conteneur avec lequel vous voulez travailler.

1. Cliquez sur **Nouveau conteneur**.

1. Dans la boîte de dialogue **Ajouter un conteneur**, cliquez sur **+ Ajouter une clé unique** pour ajouter une entrée de clé unique.

1. Entrez le ou les chemins de la contrainte de clé unique.

1. Si nécessaire, ajoutez d’autres entrées de clés uniques en cliquant sur **+ Ajouter une clé unique**

    ![Capture d’écran d’entrée de contrainte de clé unique dans le portail Azure](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Utiliser PowerShell

Pour créer un conteneur à clés uniques, consultez [Créer un conteneur Azure Cosmos à clé unique et TTL](manage-with-powershell.md#create-container-unique-key-ttl).

## <a name="use-the-net-sdk-v2"></a>Utiliser le SDK .NET V2

Lors de la création d’un conteneur à l’aide du [SDK .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), vous pouvez utiliser un objet `UniqueKeyPolicy` pour définir des contraintes de clé unique.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Utiliser le kit SDK .NET V3

Quand vous créez un conteneur à l’aide du [kit SDK .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), utilisez l’API Fluent du kit SDK pour déclarer des clés uniques de manière concise et lisible.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Utiliser le SDK Java

Lors de la création d’un conteneur à l’aide du [SDK Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), vous pouvez utiliser un objet `UniqueKeyPolicy` pour définir des contraintes de clé unique.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Utiliser le SDK Node.js

Lors de la création d’un conteneur à l’aide du [SDK Node.js](https://www.npmjs.com/package/@azure/cosmos), vous pouvez utiliser un objet `UniqueKeyPolicy` pour définir des contraintes de clé unique.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Utiliser le SDK Python

Lors de la création d’un conteneur à l’aide du [SDK Python](https://pypi.org/project/azure-cosmos/), vous pouvez spécifier les contraintes de clé unique en tant que partie du dictionnaire passé comme paramètre.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage plus sur le [partitionnement](partition-data.md).
- Explorez le [fonctionnement de l’indexation](index-overview.md).
