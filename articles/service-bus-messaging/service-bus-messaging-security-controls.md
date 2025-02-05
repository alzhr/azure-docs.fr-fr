---
title: Contrôles de sécurité pour la messagerie Azure Service Bus
description: Check-list des contrôles de sécurité utilisés pour l’évaluation de la messagerie Azure Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: d2b92759384a9a0b63d784a8cb1afb3d18d55aeb
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219311"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Contrôles de sécurité pour la messagerie Azure Service Bus

Cet article décrit les contrôles de sécurité intégrés à la messagerie Azure Service Bus.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Prise en charge du point de terminaison de service| Oui (niveau Premium uniquement) | Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour le [niveau Service Bus Premium](service-bus-premium-messaging.md). |  |
| Prise en charge de l’injection de réseau virtuel| Non | |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui (niveau Premium uniquement) |  |  |
| Prise en charge du tunneling forcé| Non |  |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Prise en charge par le biais de [Azure Monitor and Alerts](service-bus-metrics-azure-monitor.md). |  |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Des journaux d’opérations sont disponibles.  | [Journaux de diagnostic Service Bus](service-bus-diagnostic-logs.md) |
| Journalisation et audit du plan de données| Non |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Authentication| OUI | Géré avec la [fonctionnalité Managed Service Identity d’Azure Active Directory](service-bus-managed-service-identity.md).| [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md) |
| Authorization| OUI | Prend en charge l’autorisation par [contrôle d’accès en fonction du rôle (RBAC)](authenticate-application.md) et jeton SAS. | [Authentification et autorisation Service Bus](service-bus-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft |  Oui pour le chiffrement côté serveur au repos par défaut. | Les clés gérées par le client et du BYOK ne sont pas encore prises en charge. Le chiffrement côté client est sous la responsabilité du client |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non |   |   |
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A | |   |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| OUI | Prend en charge le mécanisme HTTPS/TLS standard. |   |
| Appels d’API chiffrés| OUI | Les appels d’API sont effectués via [Azure Resource Manager](../azure-resource-manager/index.yml) et HTTPS. |   |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation |
|---|---|--|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Prend en charge le contrôle de version du fournisseur de ressources via l’[API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).