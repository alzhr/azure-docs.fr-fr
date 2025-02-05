---
title: Forum aux questions sur Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Obtenez des réponses aux questions les plus fréquemment posées sur Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 1f25ccd26aed832c068c04198486e769ec980380
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072207"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Forum aux questions sur Azure Dev Spaces

Cet article répond aux questions fréquemment posées sur Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quelles régions Azure proposent actuellement Azure Dev Spaces ?

Pour obtenir la liste complète des régions disponibles, consultez [Régions et configuration prises en charge][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Puis-je utiliser Azure Dev Spaces sans adresse IP publique ?

Non, vous ne pouvez pas configurer Azure Dev Spaces sur un cluster AKS sans adresse IP publique. Une adresse IP publique est [nécessaire à Azure Dev Spaces pour le routage][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Puis-je utiliser ma propre entrée avec Azure Dev Spaces ?

Oui, vous pouvez configurer votre propre entrée en plus de celle créée par Azure Dev Spaces. Par exemple, vous pouvez utiliser [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Puis-je utiliser HTTPS avec Azure Dev Spaces ?

Oui, vous pouvez configurer votre propre entrée avec HTTPS à l’aide de [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Puis-je utiliser Azure Dev Spaces sur un cluster qui utilise CNI plutôt que kubenet ? 

Oui, vous pouvez utiliser Azure Dev Spaces sur un cluster AKS qui utilise CNI pour la mise en réseau. Par exemple, vous pouvez utiliser Azure Dev Spaces sur un cluster AKS avec des [conteneurs Windows existants][windows-containers], qui utilise CNI pour la mise en réseau.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Puis-je utiliser Azure Dev Spaces avec des conteneurs Windows ?

Actuellement, Azure Dev Spaces est conçu pour être exécuté sur des pods et des nœuds Linux uniquement, mais vous pouvez exécuter Azure Dev Spaces sur un cluster AKS avec des [conteneurs Windows existants][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Puis-je utiliser Azure Dev Spaces sur des clusters AKS avec les plages d’adresses IP autorisées du serveur d’API activées ?

Oui. Vous pouvez utiliser Azure Dev Spaces sur des clusters AKS avec les [plages d’adresses IP autorisées du serveur d’API][aks-auth-range] activées. Lors de la [création][aks-auth-range-create] de votre cluster, vous devez [autoriser des plages supplémentaires en fonction de votre région][aks-auth-range-ranges]. Vous pouvez également [mettre à jour][aks-auth-range-update] et le cluster existant pour autoriser ces plages supplémentaires.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md