---
title: Copier des données vers/à partir de SAP Cloud for Customer avec Azure Data Factory
description: Découvrez comment utiliser Data Factory pour copier des données de SAP Cloud for Customer vers des magasins de données récepteurs pris en charge (ou) de magasins de données sources pris en charge vers SAP Cloud for Customer.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 5c47eead8bc8428f533374b2d4892ed684c1f3c7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680264"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copier des données de SAP Cloud for Customer (C4C) avec Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de/vers SAP Cloud for Customer (C4C). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

>[!TIP]
>Pour en savoir plus sur la prise en charge générale de l’intégration de données SAP par ADF, consultez le livre blanc [Intégration de données SAP à l’aide d’Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) offrant une présentation détaillée, une comparaison et des conseils.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SAP Cloud for Customer est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de SAP Cloud for Customer vers n’importe quel magasin de données récepteur pris en charge, ou de n’importe quel magasin de données source pris en charge vers SAP Cloud for Customer. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur permet à Azure Data Factory de copier des données de/vers SAP Cloud for Customer, notamment les solutions SAP Cloud for Sales, SAP Cloud for Service et SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes donnent des précisions sur les propriétés utilisées pour définir des entités Data Factory propres au connecteur SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié SAP Cloud for Customer :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **SapCloudForCustomer**. | OUI |
| url | URL de l’instance SAP C4C OData. | OUI |
| username | Indiquez le nom d'utilisateur à utiliser pour se connecter à SAP C4C. | OUI |
| password | Indiquez le mot de passe du compte d’utilisateur défini pour username. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour Source, Oui pour Récepteur |

>[!IMPORTANT]
>Pour copier des données dans SAP Cloud for Customer, vous devez [créer explicitement un runtime Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) à un emplacement proche de SAP Cloud for Customer et l’associer au service lié, comme dans l’exemple suivant :

**Exemple :**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données SAP Cloud for Customer.

Pour copier des données de SAP Cloud for Customer, affectez la valeur **SapCloudForCustomerResource** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **SapCloudForCustomerResource** |OUI |
| path | Indiquez le chemin d’accès de l’entité SAP C4C OData. |OUI |

**Exemple :**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par la source SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C comme source

Pour copier des données de SAP Cloud for Customer, affectez la valeur **SapCloudForCustomerSource** au type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **SapCloudForCustomerSource**  | OUI |
| query | Indiquez la requête OData personnalisée permettant de lire les données. | Non |

Exemple de requête permettant d’obtenir des données pour un jour en particulier : `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C comme récepteur

Pour copier des données vers SAP Cloud for Customer, affectez la valeur **SapCloudForCustomerSink** au type récepteur de l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **SapCloudForCustomerSink**  | OUI |
| writeBehavior | Comportement d’écriture de l’opération. Valeurs possibles : « Insert », « Update ». | Non. Valeur par défaut : « Insert ». |
| writeBatchSize | Taille de lot de l’opération d’écriture. La taille de lot offrant les meilleures performances peut être différente selon les tables et les serveurs. | Non. Valeur par défaut : 10. |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mappage des types de données pour SAP Cloud for Customer

Lors de la copie de données de SAP Cloud for Customer, les mappages suivants sont utilisés pour passer des types de données SAP Cloud for Customer aux types de données intermédiaires Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données SAP C4C OData | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Unique |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
