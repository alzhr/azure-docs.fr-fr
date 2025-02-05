---
title: Connecter Azure Kubernetes Service (AKS) à Azure Database pour PostgreSQL - Serveur unique
description: Découvrez comment connecter Azure Kubernetes Service à Azure Database pour PostgreSQL - Serveur unique
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: a98d9b89db0406d67d1b067c3e53eb5c3dae7957
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65068938"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Connexion entre Azure Kubernetes Service et Azure Database pour PostgreSQL - Serveur unique

Azure Kubernetes Service (AKS) fournit un cluster Kubernetes managé que vous pouvez utiliser dans Azure. Voici quelques options à prendre en compte lors de l’utilisation d’AKS et d’Azure Database pour PostgreSQL pour créer une application.


## <a name="accelerated-networking"></a>Mise en réseau accélérée
Utilisez des machines virtuelles sous-jacentes dotées de la mise en réseau accélérée dans votre cluster AKS. Lorsque la mise en réseau accélérée est activée sur une machine virtuelle, celle-ci bénéficie d’une latence plus faible, d’une diminution de l’instabilité et d’une utilisation moins importante du processeur. Apprenez-en davantage sur le fonctionnement de la mise en réseau accélérée, les versions de système d’exploitation prises en charge et les instances de machine virtuelle compatibles pour [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

À partir de novembre 2018, AKS prend en charge la mise en réseau accélérée sur ces instances de machine virtuelle prises en charge. La mise en réseau accélérée est activée par défaut sur les nouveaux clusters AKS qui utilisent ces machines virtuelles.

Vous pouvez vérifier si votre cluster AKS dispose de la mise en réseau accélérée de la façon suivante :
1. Accédez au portail Azure et sélectionnez votre cluster AKS.
2. Sélectionnez l’onglet Propriétés.
3. Copiez le nom du **Groupe de ressources d’infrastructure**.
4. Utilisez la barre de recherche du portail pour localiser et ouvrir le groupe de ressources d’infrastructure.
5. Sélectionnez une machine virtuelle dans ce groupe de ressources.
6. Accédez à l’onglet **Mise en réseau** de la machine virtuelle.
7. Vérifiez que la **Mise en réseau accélérée** est activée.

Ou via Azure CLI, en utilisant les deux commandes suivantes :
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
La sortie correspond au groupe de ressources que crée AKS et contenant l'interface réseau. Prenez le nom « nodeResourceGroup » et utilisez-le dans la commande suivante. **EnableAcceleratedNetworking** sera true ou false :
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker pour Azure 
[Open Service Broker pour Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) vous permet de provisionner des services Azure directement à partir de Kubernetes ou de Cloud Foundry. C’est une implémentation de [l’API Open Service Broker](https://www.openservicebrokerapi.org/) pour Azure.

Avec OSBA, vous pouvez créer un serveur Azure Database pour PostgreSQL et liez-le à votre cluster AKS à l’aide du langage natif Kubernetes. En savoir plus sur l’utilisation d’OSBA et d’Azure Database pour PostgreSQL sur la page [GitHub OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Regroupement de connexions
Un dispositif de regroupement de connexions réduit le coût et le temps associés à la création et la fermeture de nouvelles connexions à la base de données. Le pool est une collection de connexions qui peuvent être réutilisées. 

Il existe plusieurs dispositifs de regroupement de connexions que vous pouvez utiliser avec PostgreSQL. L’un d’eux est [PgBouncer](https://pgbouncer.github.io/). Dans le Registre de conteneurs Microsoft, nous fournissons un PgBouncer en conteneur léger qui peut être utilisé dans un side-car pour regrouper les connexions à partir d’AKS vers Azure Database pour PostgreSQL. Visitez la [page du hub docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) pour savoir comment accéder à cette image et l’utiliser. 


## <a name="next-steps"></a>Étapes suivantes
-  [Créer un cluster Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
