---
title: Demander l’accès à un package d’accès dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment utiliser le portail Mon Accès pour demander l’accès à un package d’accès dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddc0a3788075701fb4633895e7b22fff2c15f60b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173704"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Demander l’accès à un package d’accès dans la gestion des droits d’utilisation Azure AD

Avec la gestion des droits d'utilisation d’Azure AD, un package d’accès vous permet d’effectuer une configuration unique des ressources et des stratégies qui gèrent automatiquement l’accès pendant toute la durée de vie du package d’accès. 

Un gestionnaire de package d’accès peut configurer des stratégies pour exiger une approbation pour que les utilisateurs aient accès aux packages d’accès. Un utilisateur qui a besoin d’accéder à un package d’accès peut envoyer une demande d’accès. Cet article explique comment envoyer une demande d’accès.

## <a name="sign-in-to-the-my-access-portal"></a>Se connecter au portail Mon Accès

La première étape consiste à se connecter au portail Mon Accès à partir duquel vous pouvez demander l’accès à un package d’accès.

**Rôle prérequis :** Demandeur

1. Recherchez un e-mail ou un message de la part du chef de projet ou du directeur commercial avec lequel vous travaillez. L’e-mail doit inclure un lien vers le package d’accès auquel vous devrez accéder. Le lien commence par `myaccess`, comprend une indication de répertoire et se termine par un ID de package d’accès.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Ouvrez le lien.

1. Connectez-vous au portail Mon Accès.

    Veillez à utiliser le compte de votre organisation (professionnel ou scolaire). Si vous n’êtes pas sûr, vérifiez auprès de votre chef de projet ou de votre directeur commercial.

## <a name="request-an-access-package"></a>Demander un package d’accès

Une fois que vous avez trouvé le package d’accès dans le portail Mon Accès, vous pouvez soumettre une demande.

**Rôle prérequis :** Demandeur

1. Recherchez le package d’accès dans la liste.  Si nécessaire, vous pouvez effectuer une recherche en tapant une chaîne de recherche, puis en sélectionnant le filtre **Nom**, **Catalogue** ou **Ressources**.

    ![Portail Mon Accès - Recherche de ressource](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Cochez la case pour sélectionner le package d’accès.

1. Cliquez sur **Demander l’accès** pour ouvrir le volet Demander l’accès.

    ![Portail Mon Accès - Packages d’accès](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Si la case **Justification métier** s’affiche, fournissez une justification pour obtenir l’accès.

1. Si la case **Demander pendant une période spécifique ?** est activée, sélectionnez **Oui** ou **Non**.

1. Si nécessaire, spécifiez la date de début et la date de fin.

    ![Portail Mon Accès - Demander l’accès](./media/entitlement-management-shared/my-access-request-access.png)

1. Lorsque vous avez terminé, cliquez sur **Envoyer** pour envoyer votre demande.

1. Cliquez sur **Historique des demandes** pour afficher la liste de vos demandes et leur état.

    Si le package d’accès nécessite une approbation, l’état de la demande est désormais en attente d’approbation.

### <a name="select-a-policy"></a>Sélectionner une stratégie

Si vous demandez l’accès à un package d’accès auquel plusieurs stratégies s’appliquent, il pourra vous être demandé de sélectionner une stratégie. Par exemple, un gestionnaire de package d’accès peut configurer un package d’accès avec deux stratégies pour deux groupes d’employés internes. La première stratégie peut autoriser l’accès pendant 60 jours et nécessiter une approbation. La deuxième stratégie peut autoriser l’accès pendant 2 jours et ne nécessiter aucune approbation. Si vous rencontrez ce scénario, vous devez sélectionner la stratégie que vous souhaitez utiliser.

![Portail Mon Accès – Demander l’accès – plusieurs stratégies](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="cancel-a-request"></a>Annuler une demande

Si vous envoyez une demande d’accès et que l’état de la demande est toujours **En attente d’approbation**, vous pouvez annuler la demande.

**Rôle prérequis :** Demandeur

1. Dans le portail Mon Accès, cliquez sur **Historique des demandes**, sur la gauche, pour afficher la liste de vos demandes et leur état.

1. Cliquez sur le lien **Affichage** pour la demande que vous souhaitez annuler.

1. Si l’état de la demande est toujours **En attente d’approbation**, vous pouvez cliquer sur **Annuler la demande** pour annuler la demande.

    ![Portail Mon Accès - Annuler la demande](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Cliquez sur **Historique des demandes** pour confirmer que la demande a bien été annulée.

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)
- [Processus de demande et notifications par e-mail](entitlement-management-process.md)
