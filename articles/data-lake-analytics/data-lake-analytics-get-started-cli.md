---
title: Créer et interroger des comptes Azure Data Lake Analytics - Azure CLI
description: Découvrez comment utiliser l’interface de ligne de commande Azure pour créer un compte Azure Data Lake Analytics et envoyer un travail U-SQL.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 94399490453c6a2774f71ef527fd24d543e2a7e2
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316572"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Prise en main d’Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Cet article explique comment utiliser l’interface de ligne de commande Azure CLI pour créer des comptes Azure Data Lake Analytics, envoyer des travaux USQL et utiliser des catalogues. Le travail lit un fichier TSV (valeurs séparées par des tabulations) et le convertit en fichier CSV (valeurs séparées par des virgules). 

## <a name="prerequisites"></a>Prérequis
Avant de débuter, vous avez besoin des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* Cet article nécessite que vous exécutiez Azure CLI version 2.0 ou ultérieure. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 



## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à votre abonnement Azure :

```
azurecli
az login
```

Vous êtes invité à accéder à une URL, et à entrer un code d’authentification.  Ensuite, suivez les instructions pour entrer vos informations d’identification.

Une fois que vous êtes connecté, la commande de connexion répertorie vos abonnements.

Pour utiliser un abonnement spécifique :

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake
Vous devez disposer d’un compte Data Lake Analytics avant de pouvoir exécuter des travaux quelconques. Pour créer un compte Data Lake Analytics, vous devez spécifier les éléments suivants :

* **Groupe de ressources Azure**. Un compte Data Lake Analytics doit être créé au sein d’un groupe de ressources Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.  

Pour répertorier les groupes de ressources existants dans votre abonnement :

```
az group list
```

Pour créer un groupe de ressources :

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Nom du compte Data Lake Analytics**. Chaque compte Data Lake Analytics porte un nom.
* **Emplacement**. Utilisez l’un des centres de données Azure prenant en charge Data Lake Analytics.
* **Compte Data Lake Store par défaut** : Chaque compte Data Lake Analytics possède un compte Data Lake Store par défaut.

Pour répertorier le compte Data Lake Store existant :

```
az dls account list
```

Pour créer un compte Data Lake Store :

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Utilisez la syntaxe suivante pour créer un compte Data Lake Analytics :

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Après avoir créé un compte, vous pouvez utiliser les commandes suivantes pour répertorier les comptes et afficher leurs détails :

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Téléchargement de données vers Data Lake Store
Dans ce didacticiel, vous traitez des journaux d’activité de recherche.  Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Le portail Azure fournit une interface utilisateur pour la copie de fichiers de données d’exemple vers le compte Data Lake Store par défaut, y compris un fichier de journal de recherche. Voir [Préparer des données sources](data-lake-analytics-get-started-portal.md) pour charger les données dans le compte Data Lake Store par défaut.

Pour charger des fichiers à l’aide d’Azure CLI, utilisez les commandes suivantes :

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Analytique Data Lake peut également accéder au stockage d’objets blobs Azure.  Pour télécharger des données dans le Blob Storage Azure, consultez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../storage/common/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envoyer des travaux Analytique Data Lake
Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](https://docs.microsoft.com/u-sql/).

**Pour créer un script de travail Analytique Data Lake**

Créez un fichier texte avec le script U-SQL suivant, puis enregistrez le fichier texte sur votre station de travail :

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()** , puis crée un fichier csv à l’aide d’**Outputters.Csv()** .

Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  Data Lake Analytics crée le dossier de sortie s’il n’existe pas encore.

Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake Store par défaut. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple :

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe des fichiers stockés dans des comptes Azure Storage liés est la suivante :

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Conteneur d’objets Blob Azure avec des objets Blob publics ne sont pas pris en charge.      
> Le conteneur d’objets Blob Azure avec des conteneurs publics n’est pas pris en charge.      
>

**Pour soumettre les travaux**

Pour soumettre un travail, utilisez la syntaxe suivante.

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Par exemple :

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Pour répertorier les travaux et afficher leurs détails**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Pour annuler les travaux**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Récupérer les résultats de travaux

Une fois qu’un travail est terminé, vous pouvez utiliser les commandes suivantes pour répertorier les fichiers sortants et télécharger les fichiers :

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Par exemple :

```
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher le document de référence Azure CLI Data Lake Analytics, consultez [Data Lake Analytics](/cli/azure/dla).
* Pour afficher le document de référence Azure CLI Data Lake Store, consultez [Data Lake Store](/cli/azure/dls).
* Pour voir une requête plus complexe, consultez [Analyse de journaux d’activité des sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
