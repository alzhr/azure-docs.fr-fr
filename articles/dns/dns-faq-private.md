---
title: FAQ sur Azure Private DNS
description: Questions fréquentes (FAQ) sur Azure Private DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: cb0cc5e99cc07728d475a9f9e54c7eb6a8c7554e
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959929"
---
# <a name="azure-private-dns-faq"></a>FAQ sur Azure Private DNS

Les questions ci-dessous sont des questions fréquentes à propos d’Azure DNS Private Zones.

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS prend-il en charge les domaines privés ?

Les domaines privés sont pris en charge à l’aide de la fonctionnalité Azure DNS Private Zones. Les zones DNS privées peuvent être résolues uniquement à partir de réseaux virtuels spécifiés. Pour plus d’informations, consultez la [Vue d’ensemble](private-dns-overview.md).

Pour plus d’informations sur les autres options DNS internes dans Azure, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>La fonctionnalité Azure DNS Private Zones est-elle disponible dans les différentes régions Azure ?

Oui. La fonctionnalité Private Zones est prise en charge pour la résolution DNS entre les réseaux virtuels de différentes régions Azure. Elle fonctionne même sans le peering explicite des réseaux virtuels. Tous les réseaux virtuels doivent être liés à la zone DNS privée.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>La connectivité à Internet à partir de réseaux virtuels est-elle nécessaire pour les zones privées ?

Non. Les zones privées fonctionnent avec les réseaux virtuels. Vous les utilisez pour gérer les domaines des machines virtuelles et d’autres ressources, à l’intérieur et entre les réseaux virtuels. Une connectivité à Internet n’est pas nécessaire pour la résolution de noms.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>La même zone privée peut-elle être utilisée pour plusieurs réseaux virtuels pour la résolution ?

Oui. Vous pouvez lier une zone DNS privée à des milliers de réseaux virtuels. Pour plus d’informations, consultez [Limites d’Azure DNS](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits).

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Un réseau virtuel qui appartient à un autre abonnement peut-il être lié à une zone privée ?

Oui. Vous devez disposer d'une autorisation d'écriture sur les réseaux virtuels ainsi que sur la zone DNS privée. L’autorisation d’accès en écriture peut être accordée à plusieurs rôles RBAC. Par exemple, le rôle RBAC Contributeur dans un réseau Classic dispose d’autorisations en écriture sur les réseaux virtuels, et le rôle Contributeur dans des zones DNS privées a les autorisations en écriture sur les zones DNS privées. Pour plus d’informations sur les rôles RBAC, consultez [Contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Les enregistrements DNS de machines virtuelles inscrits automatiquement dans une zone privée sont-ils supprimés automatiquement quand vous supprimez la machine virtuelle ?

Oui. Si vous supprimez une machine virtuelle dans un réseau virtuel lié alors que l’inscription automatique est activé, les enregistrements inscrits sont automatiquement supprimés.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Un enregistrement de machine virtuelle inscrit automatiquement dans une zone privée à partir d’un réseau virtuel lié peut-il être supprimé manuellement ?

Oui. Vous pouvez remplacer les enregistrements DNS inscrits automatiquement par un enregistrement DNS créé manuellement dans la zone. La question et la réponse suivantes traitent de ce sujet.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Que se passe-t-il quand nous essayons de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle existante inscrite automatiquement sur un réseau virtuel lié ?

Vous essayez de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle inscrite automatiquement existant sur un réseau virtuel lié. Dans ce cas, le nouvel enregistrement DNS remplace l’enregistrement de machine virtuelle inscrit automatiquement. Si vous essayez de supprimer à nouveau cet enregistrement DNS créé manuellement de la zone, la suppression réussit. L’inscription automatique se produit à nouveau tant que la machine virtuelle existe encore et qu’une adresse IP privée lui est attachée. L’enregistrement DNS est automatiquement recréé dans la zone.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Que se passe-t-il quand nous dissocions un réseau virtuel lié d’une zone privée ? Les enregistrements de machines virtuelles du réseau virtuel inscrits automatiquement sont-ils également supprimés de la zone ?

Oui. Pour dissocier un réseau virtuel lié d’une zone privée, vous mettez à jour la zone DNS pour supprimer le réseau virtuel lié associé. Dans ce processus, les enregistrements de machines virtuelles qui ont été inscrits automatiquement sont supprimés de la zone.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Que se passe-t-il quand nous supprimons un réseau virtuel lié qui est lié à une zone privée ? Devons-nous mettre manuellement à jour la zone privée afin de dissocier le réseau virtuel en tant que réseau virtuel lié de la zone ?

Non. Quand vous supprimez un réseau virtuel lié sans le dissocier au préalable d’une zone privée, le réseau virtuel est supprimé et les liaisons à la zone DNS sont automatiquement supprimées.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>La résolution DNS à l’aide du nom de domaine complet (FQDN) par défaut (internal.cloudapp.net) fonctionne-t-elle même quand une zone privée (par exemple, private.contoso.com) est liée à un réseau virtuel ?

Oui. Les zones privées ne remplacent pas la zone internal.cloudapp.net fournie par Azure. Que vous vous appuyiez sur la zone internal.cloudapp.net fournie par Azure ou sur votre propre zone privée, utilisez le nom de domaine complet de la zone par rapport à laquelle vous souhaitez effectuer la résolution.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Le suffixe DNS sur les machines virtuelles au sein d’un réseau virtuel lié est-il remplacé par celui de la zone privée ?

Non. Le suffixe DNS sur les machines virtuelles de votre réseau virtuel lié reste le suffixe par défaut fourni par Azure (« *.internal.cloudapp.net »). Vous pouvez changer manuellement ce suffixe DNS sur vos machines virtuelles en le remplaçant par celui de la zone privée.
Pour obtenir des conseils sur la façon de modifier ce suffixe, consultez [Utiliser un DNS dynamique pour inscrire les noms d’hôte sur votre propre serveur DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients).

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quelles sont les limites d’utilisation d’Azure DNS Private Zones ?

Pour plus d’informations sur les limites d’utilisation d’Azure DNS Private Zones, consultez [Limites d’Azure DNS](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits).

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Pourquoi mes zones DNS privées existantes n’apparaissent-elles pas dans le nouveau portail ?

Si votre zone DNS privée existante a été créée à l’aide de l’API en préversion, vous devez migrer ces zones vers un nouveau modèle de ressources. Les zones DNS privées créées à l’aide de l’API en préversion ne sont pas affichées dans la nouvelle expérience du portail. Consultez les instructions ci-après sur la migration vers un nouveau modèle de ressources.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Comment migrer mes zones DNS privées vers le nouveau modèle ?

Nous vous recommandons vivement de migrer vers le nouveau modèle de ressource dès que possible. Le modèle de ressources existant continuera à être pris en charge mais ne sera plus enrichi de nouvelles fonctionnalités. Nous prévoyons de déprécier son utilisation ultérieurement, en faveur d’un nouveau modèle de ressources. Pour plus d’informations sur la migration de vos zones DNS privées existantes vers un nouveau modèle de ressources, reportez-vous au [guide de migration des zones privées Azure DNS](private-dns-migration-guide.md).

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur Azure Private DNS](private-dns-overview.md)
