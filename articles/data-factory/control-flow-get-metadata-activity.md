---
title: Activité d’obtention des métadonnées dans Azure Data Factory
description: Découvrez comment utiliser l’activité d’obtention des métadonnées dans un pipeline Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: e891f6675920e7bb90d2a6d007676cdd65f19917
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679900"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Activité d’obtention des métadonnées dans Azure Data Factory

Vous pouvez utiliser l’activité d’obtention des métadonnées pour récupérer les métadonnées de n’importe quelle donnée dans Azure Data Factory. Vous pouvez utiliser cette activité dans les scénarios suivants :

- Valider les métadonnées de n’importe quelle donnée
- Déclencher un pipeline quand des données sont prêtes/disponibles

La fonctionnalité suivante est disponible dans le flux de contrôle :

- Vous pouvez utiliser la sortie de l’activité d’obtention des métadonnées dans des expressions conditionnelles pour effectuer la validation.
- Vous pouvez déclencher un pipeline quand une condition est remplie via une bouche Do Until.

## <a name="capabilities"></a>Fonctionnalités

L’activité d’obtention des métadonnées sélectionne un jeu de données en tant qu’entrée et retourne les informations de métadonnées en tant que sortie. Pour l’instant, les connecteurs suivants et les métadonnées récupérables correspondantes sont pris en charge. La taille maximale des métadonnées retournées est de 1 Mo.

>[!NOTE]
>Si vous exécutez l’activité d’obtention des métadonnées sur un runtime d’intégration auto-hébergé, les dernières fonctionnalités sont prises en charge sur la version 3.6 ou ultérieure.

### <a name="supported-connectors"></a>Connecteurs pris en charge

**Stockage Fichier**

| Connecteur/Métadonnées | itemName<br>(fichier/dossier) | itemType<br>(fichier/dossier) | size<br>(fichier) | created<br>(fichier/dossier) | lastModified<br>(fichier/dossier) |childItems<br>(dossier) |contentMD5<br>(fichier) | structure<br/>(fichier) | columnCount<br>(fichier) | exists<br>(fichier/dossier) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [stockage d’objets blob Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Système de fichiers](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Pour Amazon S3 et Google Cloud Storage, `lastModified` s’applique au compartiment et à la clé, mais pas au dossier virtuel, et `exists` s’applique au compartiment et à la clé, mais pas au préfixe ou au dossier virtuel.
- Pour le stockage Blob Azure, `lastModified` s’applique au conteneur et au blob, mais pas au dossier virtuel.

**Base de données relationnelle**

| Connecteur/Métadonnées | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Database Managed Instance](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Options de métadonnées

Vous pouvez spécifier les types de métadonnées suivants dans la liste de champs de l’activité d’obtention des métadonnées pour récupérer les informations correspondantes :

| Type de métadonnées | Description |
|:--- |:--- |
| itemName | Nom du fichier ou dossier. |
| itemType | Type du fichier ou dossier. La valeur retournée est `File` ou `Folder`. |
| size | Taille du fichier en octets. S’applique aux fichiers uniquement. |
| created | Date/heure de création du fichier ou du dossier. |
| lastModified | Date/heure de dernière modification du fichier ou du dossier. |
| childItems | Liste des sous-dossiers et fichiers dans le dossier concerné. S’applique aux dossiers uniquement. La valeur retournée est une liste contenant le nom et le type de chaque élément enfant. |
| contentMD5 | MD5 du fichier. S’applique aux fichiers uniquement. |
| structure | Structure de données du fichier ou de la table de base de données relationnelle. La valeur retournée est une liste de noms et de types de colonnes. |
| columnCount | Nombre de colonnes dans le fichier ou la table relationnelle. |
| exists| Indique si un fichier, un dossier ou une table existe ou non. Notez que si `exists` est spécifié dans la liste de champs de l’activité d’obtention des métadonnées, l’activité n’échouera pas, même si le fichier, le dossier ou la table n’existe pas. `exists: false` sera alors retourné dans la sortie. |

>[!TIP]
>Si vous souhaitez vérifier qu’un fichier, un dossier ou une table existe, spécifiez `exists` dans la liste de champs de l’activité d’obtention des métadonnées. Vous pouvez ensuite vérifier le résultat de `exists: true/false` dans la sortie de l’activité. Si `exists` n’est pas spécifié dans la liste de champs, l’activité d’obtention des métadonnées échouera si l’objet est introuvable.

>[!NOTE]
>Quand vous récupérez des métadonnées à partir de magasins de fichiers et configurez `modifiedDatetimeStart` ou `modifiedDatetimeEnd`, le `childItems` dans la sortie incluent uniquement les fichiers du chemin spécifié, dont l’heure de dernière modification est comprise dans la plage spécifiée. Il n’inclut pas les éléments des sous-dossiers.

## <a name="syntax"></a>Syntaxe

**Activité d’obtention des métadonnées**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriétés type

Actuellement, l’activité d’obtention des métadonnées peut retourner les types d’informations de métadonnées suivants :

Propriété | Description | Obligatoire
-------- | ----------- | --------
fieldList | Les types d’informations de métadonnées requis. Pour plus d’informations sur les métadonnées prises en charge, consultez la section [Options de métadonnées](#metadata-options) de cet article. | OUI 
dataset | Le jeu de données de référence à partir duquel les métadonnées doivent être récupérées par l’activité d’obtention des métadonnées. Pour plus d’informations sur les connecteurs pris en charge, consultez la section [Fonctionnalités](#capabilities). Reportez-vous aux rubriques spécifiques aux connecteurs pour plus d’informations sur la syntaxe de jeu de données. | OUI
formatSettings | S’applique lors de l’utilisation du type de format Jeu de données. | Non
storeSettings | S’applique lors de l’utilisation du type de format Jeu de données. | Non

## <a name="sample-output"></a>Exemple de sortie

Les résultats de l’activité d’obtention des métadonnées sont indiqués dans la sortie de l’activité. Voici deux exemples illustrant les nombreuses options de métadonnées. Pour utiliser le résultat dans une activité suivante, utilisez le modèle `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Obtenir les métadonnées d’un fichier

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obtenir les métadonnées d’un dossier

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les ressources suivantes pour en savoir plus sur les autres activités de flux de contrôle prises en charge par Data Factory :

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Activité ForEach](control-flow-for-each-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
