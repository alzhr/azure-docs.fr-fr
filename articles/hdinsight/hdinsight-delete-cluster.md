---
title: Guide pratique pour supprimer un cluster HDInsight - Azure
description: Informations sur les différentes méthodes que vous pouvez utiliser pour supprimer un cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a28d59cb35004fac6b069f2aa41042b4d46e443f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091537"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Suppression d’un cluster HDInsight à l’aide de votre navigateur, PowerShell ou l’interface de ligne de commande Azure

La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Dans ce document, vous découvrirez comment supprimer un cluster à l’aide du [Portail Azure](https://portal.azure.com), [du module Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview) et de [l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> La suppression d’un cluster HDInsight ne supprime pas les comptes Stockage Azure ou le Data Lake Storage associés au cluster. Vous pouvez réutiliser les données stockées dans ces services à l’avenir.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, accédez à **Tous les services** > **Analytique** > **Clusters HDInsight** et sélectionnez votre cluster.

3. Dans la vue par défaut, sélectionnez l’icône **Supprimer**. Suivez les instructions pour supprimer votre cluster.

    ![HDInsight - Bouton de suppression de cluster](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Module Azure PowerShell Az

Remplacez `CLUSTERNAME` par le nom de votre cluster HDInsight dans le code ci-dessous. À partir d’une invite PowerShell, entrez la commande suivante pour supprimer le cluster :

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>D’Azure CLI

Remplacez `CLUSTERNAME` par le nom de votre cluster HDInsight et `RESOURCEGROUP` par le nom de votre groupe de ressources dans le code ci-dessous.  À partir d’une invite de commande, entrez la commande suivante pour supprimer le cluster :

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
