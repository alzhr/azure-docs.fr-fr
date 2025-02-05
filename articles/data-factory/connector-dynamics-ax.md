---
title: Copier des données de Dynamics AX en utilisant Azure Data Factory
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de Dynamics CRM vers des magasins de données récepteurs pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 07edc284c29ca209ee20e5de390e8126993f4ce3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681019"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Copier des données de Dynamics AX en utilisant Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier les données d’une source Dynamics AX. Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Dynamics AX est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de Dynamics AX vers un magasin de données récepteur pris en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Dynamics AX prend en charge la copie de données à partir de Dynamics AX en utilisant le **protocole OData** avec l’**authentification du principal du service**.

>[!TIP]
>Vous pouvez aussi utiliser ce connecteur pour copier des données à partir de **Dynamics 365 for Finance and Operations**. Reportez-vous à [Prise en charge d’OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) et [Méthode d’authentification](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) pour Dynamics 365.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés que vous pouvez utiliser pour définir des entités Data Factory propres au connecteur Dynamics AX.

## <a name="prerequisites"></a>Prérequis

Pour l’authentification de principal de service, effectuez les étapes suivantes :

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accédez à Dynamics AX et accordez à ce principal du service une autorisation appropriée pour accéder à Dynamics AX.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Dynamics AX sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **DynamicsAX**. |OUI |
| url | Point de terminaison OData de l’instance Dynamics AX (ou Dynamics 365 for Finances and Operations). |OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du Portail Azure. | OUI |
| aadResourceId | Spécifiez la ressource AAD pour laquelle vous demandez une autorisation. Par exemple, si votre URL Dynamics est `https://sampledynamics.sandbox.operations.dynamics.com/data/`, la ressource AAD correspondante est généralement `https://sampledynamics.sandbox.operations.dynamics.com`. | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Vous pouvez choisir Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre magasin de données se trouve dans un réseau privé. À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

**Exemple**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Cette section contient la liste des propriétés prises en charge par le jeu de données Dynamics AX.

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). 

Pour copier des données de Dynamics AX, définissez la propriété **type** du jeu de données sur **DynamicsAXResource**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du jeu de données doit être définie sur **DynamicsAXResource**. | OUI |
| path | Chemin de l’entité OData Dynamics AX. | OUI |

**Exemple**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section contient la liste des propriétés prises en charge par la source Dynamics AX.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX comme source

Pour copier des données de Dynamics AX, définissez le type **source** de l’activité de copie sur **DynamicsSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source de l’activité de copie doit être définie sur **DynamicsAXSource**. | OUI |
| query | Options de requête OData pour filtrer les données. Exemple : `"?$select=Name,Description&$top=5"`.<br/><br/>**Remarque**: Le connecteur copie les données à partir de l’URL combinée : `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Pour plus d’informations, consultez [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Non |

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
