---
title: Gérer des zones DNS dans Azure DNS - Portail Azure | Microsoft Docs
description: Vous pouvez gérer des zones DNS à l’aide du portail Azure. Cet article décrit comment mettre à jour, supprimer et créer des zones DNS sur Azure DNS.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: allensu
ms.openlocfilehash: 5d4cc57c4cb5db7f04d604c8ccbc408df1a3e707
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211910"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Gérer des zones DNS à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure Classic CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Interface de ligne de commande Azure](dns-operations-dnszones-cli.md)

Cet article vous montre comment gérer vos zones DNS avec le portail Azure. Vous pouvez également gérer vos zones DNS à l’aide de l’[interface de ligne de commande Azure](dns-operations-dnszones-cli.md) multiplateforme ou d’Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
2. Dans le menu Hub, accédez à **Créer une ressource > Mise en réseau > Zone DNS** pour ouvrir le panneau **Créer une zone DNS**.

    ![Zone DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. Dans le panneau **Créer une zone DNS**, entrez les valeurs suivantes, puis cliquez sur **Créer** :


   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Nom**|contoso.com|Nom de la zone DNS|
   |**Abonnement**|[Votre abonnement]|Sélectionnez un abonnement pour y créer la zone DNS.|
   |**Groupe de ressources**|**Créer :** contosoDNSRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Lieu**|USA Ouest||

> [!NOTE]
> Le groupe de ressources fait référence à l’emplacement du groupe de ressources et n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

## <a name="list-dns-zones"></a>Création de la liste des zones DNS

Dans le portail Azure, accédez à **Plus de services** > **Mise en réseau** > **Zones DNS**. Chaque zone DNS est indépendante, et les informations telles que le nombre de jeux d’enregistrements et les serveurs de noms peuvent être consultés dans cette vue. La colonne **SERVEURS DE NOMS** ne figure pas dans la vue par défaut. Pour l’ajouter, cliquez sur **Colonnes**, sélectionnez **Serveurs de noms**, puis cliquez sur **Terminé**.

![liste des zones DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Suppression d’une zone DNS

Accédez à une zone DNS dans le portail. Sur le panneau **Zone DNS**, cliquez sur **Supprimer la zone**. Vous êtes alors invité à confirmer la suppression de la zone DNS. La suppression d’une zone DNS entraîne la suppression de tous les enregistrements présents dans la zone.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à travailler avec votre zone DNS et vos enregistrements en vous rendant sur [Prise en main d’Azure DNS à l’aide du portail Azure](dns-getstarted-portal.md).