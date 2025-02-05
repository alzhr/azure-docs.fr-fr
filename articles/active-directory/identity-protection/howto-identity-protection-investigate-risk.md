---
title: Comment examiner les risques dans Azure Active Directory Identity Protection
description: Découvrez comment examiner des utilisateurs, des détections et des connexions à risque dans Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e04cadef1f6a4aee7d8f807fc7aaa999ba6da8d7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886740"
---
# <a name="how-to-investigate-risk"></a>Procédure : Examiner les risques

Identity Protection fournit aux organisations trois rapports qu’ils peuvent utiliser pour examiner les risques liés à l’identité dans leur environnement. Ces rapports sont les **utilisateurs à risque**, les **connexions risquées** et les **détections de risques**. L’examen des événements est essentielle pour mieux comprendre et identifier les points faibles de votre stratégie de sécurité.

Les trois rapports permettent de télécharger des événements au format .CSV en vue d’analyses plus poussées en dehors du Portail Azure. Les rapports des utilisateurs à risque et des connexions risquées permettent de télécharger les 2 500 entrées les plus récentes, tandis que le rapport sur les détections de risques permet de télécharger les 5 000 enregistrements les plus récents.

Les organisations peuvent tirer parti des intégrations d’API Microsoft Graph pour agréger des données avec d’autres sources auxquelles elles peuvent accéder en tant qu’organisations.

Les trois rapports se trouvent dans le **Portail Azure** > **Azure Active Directory** > **Sécurité**.

## <a name="navigating-the-reports"></a>Parcourir les rapports

Chaque rapport démarre avec une liste de toutes les détections pour la période indiquée en haut du rapport. Chaque rapport permet l’ajout ou la suppression de colonnes en fonction des préférences de l’administrateur. Les administrateurs peuvent choisir de télécharger les données au format .CSV. Les rapports peuvent être filtrés à l’aide des filtres situés dans la partie supérieure du rapport.

La sélection d’entrées individuelles peut activer des entrées supplémentaires en haut du rapport, telles que la possibilité de confirmer qu’une connexion est compromise ou sécurisée, de confirmer qu’un utilisateur est compromis ou d’ignorer le risque de l’utilisateur.

La sélection d’entrées individuelles développe une fenêtre de détails sous les détections. L’affichage des détails permet aux administrateurs d’investiguer et d’effectuer des actions lors de chaque détection. 

![Exemple de rapport Identity Protection présentant les connexions risquées et les détails](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Utilisateurs à risque

Les informations indiquées dans le rapport Utilisateurs à risque permettent aux administrateurs de trouver :

- Quels sont les utilisateurs à risques ? Pour qui les risques ont-ils été corrigés ou éliminés ?
- Détails sur les détections
- Historique des connexions à risque
- Historique des risques
 
Les administrateurs peuvent ensuite choisir d’agir sur ces événements. Les administrateurs peuvent choisir d’effectuer les opérations suivantes :

- Réinitialiser le mot de passe de l’utilisateur
- Confirmer la compromission de l’utilisateur
- Ignorer le risque lié à l’utilisateur
- Empêcher l’utilisateur de se connecter
- Effectuer d’autres examens au moyen d’Azure ATP

## <a name="risky-sign-ins"></a>Connexions risquées

Le rapport des connexions à risque contient des données filtrables correspondant aux 30 derniers jours (1 mois).

Les informations indiquées dans le rapport des connexions à risque permettent aux administrateurs de trouver :

- Les connexions classées comme étant à risque, celles confirmées comme étant compromises, celles confirmées comme étant sécurisées, celles rejetées ou corrigées.
- Les niveaux de risque en temps réel et agrégés associés aux tentatives de connexion.
- Types de détections déclenchées
- Stratégies d’accès conditionnel appliquées
- Détails MFA
- Informations sur l'appareil
- Informations de l'application
- Informations sur l’emplacement

Les administrateurs peuvent ensuite choisir d’agir sur ces événements. Les administrateurs peuvent choisir d’effectuer les opérations suivantes :

- Confirmer que la connexion est compromise
- Confirmer que la connexion est sécurisée

## <a name="risk-detections"></a>Détections de risques

Le rapport des détections de risques contient des données filtrables correspondant aux 90 derniers jours (3 mois).

Les informations indiquées dans le rapport des détections de risques permettent aux administrateurs de trouver :

- Des informations sur chaque détection de risques, y compris le type
- Les autres risques déclenchés en même temps
- L’emplacement de la tentative de connexion
- Des liens vers plus d’informations sur Microsoft Cloud App Security (MCAS)

Les administrateurs peuvent ensuite choisir de revenir au rapport des risques ou des connexions de l’utilisateur pour effectuer des actions en fonction des informations recueillies.

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies disponibles pour atténuer les risques](concept-identity-protection-policies.md)

- [Activer les stratégies de connexion et de risque utilisateur](howto-identity-protection-configure-risk-policies.md)
