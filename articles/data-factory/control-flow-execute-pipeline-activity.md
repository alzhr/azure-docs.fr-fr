---
title: Activité d’exécution du pipeline dans Azure Data Factory
description: Découvrez comment vous pouvez utiliser l’activité d’exécution de pipeline pour appeler un pipeline Data Factory à partir d’un autre pipeline Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 093050952ed826a540c35b2b73acd107fafc45ab
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679933"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Activité d’exécution du pipeline dans Azure Data Factory
L’activité d’exécution du pipeline permet à un pipeline Data Factory d’appeler un autre pipeline.

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité d’exécution du pipeline. | Chaîne | OUI
Type | Doit être défini sur : **ExecutePipeline**. | Chaîne | OUI
pipeline | Référence de pipeline au pipeline dépendant que pipeline appelle. Un objet de référence de pipeline comporte deux propriétés : **referenceName** et **type**. La propriété referenceName spécifie le nom du pipeline de référence. La propriété de type doit être définie sur PipelineReference. | PipelineReference | OUI
parameters | Paramètres à passer au pipeline appelé | Objet JSON qui mappe des noms de paramètres à des valeurs d’arguments | Non
waitOnCompletion | Définit si l’exécution de l’activité attend l’exécution du pipeline dépendant. La valeur par défaut est false. | Boolean | Non

## <a name="sample"></a>Exemple
Ce scénario comporte deux pipelines :

- **Pipeline master** : ce pipeline comporte une activité d’exécution du pipeline qui appelle le pipeline. Le pipeline master accepte deux paramètres : `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Pipeline appelé** : ce pipeline comporte une activité de copie qui copie les données d’une source d’objets blob Azure dans le récepteur d’objets blob Azure. Le pipeline appelé accepte deux paramètres : `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Définition du pipeline master

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Définition du pipeline appelé

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Service lié**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Jeu de données source**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Jeu de données récepteur**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Exécution du pipeline

Pour exécuter le pipeline master dans cet exemple, les valeurs suivantes sont transmises aux paramètres masterSourceBlobContainer et masterSinkBlobContainer : 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Le pipeline master transfère ces valeurs au pipeline appelé comme indiqué dans l’exemple suivant : 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
