---
title: 'Tutoriel : Inviter en bloc des utilisateurs B2B Collaboration - Azure Active Directory | Microsoft Docs'
description: Dans ce tutoriel, vous découvrez comment utiliser PowerShell et un fichier CSV pour envoyer des invitations en bloc à des utilisateurs Azure AD B2B Collaboration externes.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 9/19/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1a6ea8f363f2ddd4a9568700d5bff3330443c0
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128722"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Didacticiel : Inviter en bloc des utilisateurs Azure AD B2B Collaboration (préversion)

|     |
| --- |
| Cet article décrit une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |


Si vous utilisez Azure Active Directory (Azure AD) B2B Collaboration pour travailler avec des partenaires externes, vous pouvez inviter en même temps plusieurs utilisateurs invités pour votre organisation. Dans ce tutoriel, vous allez apprendre à utiliser le portail Azure pour envoyer des invitations en bloc à des utilisateurs externes. Vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Utiliser **Inviter des utilisateurs en bloc (préversion)** pour préparer un fichier de valeurs séparées par des virgules (.csv) avec les informations de l’utilisateur et les préférences d’invitation
> * Charger le fichier .csv sur Azure AD
> * Vérifier que les utilisateurs ont été ajoutés à l’annuaire

Si vous n’avez pas Azure Active Directory, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’au moins deux comptes de messagerie de test auxquels vous pouvez envoyer les invitations. Les comptes doivent être externes à votre organisation. Vous pouvez utiliser n’importe quel type de compte, notamment des comptes de réseaux sociaux, comme des adresses gmail.com ou outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Inviter en bloc des utilisateurs invités

1. Connectez-vous au portail Azure en utilisant un compte d’administrateur d’utilisateurs de l’organisation.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs** > **Inviter en bloc**.
4. Dans la page **Inviter des utilisateurs en bloc (préversion)** , sélectionnez **Télécharger** pour obtenir un fichier .csv valide avec les propriétés de l’invitation.

    ![Bouton Télécharger dans Inviter en bloc](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Ouvrez le fichier .csv et ajoutez une ligne pour chaque utilisateur invité. Les valeurs obligatoires sont les suivantes :

   * **Adresse e-mail à inviter** : utilisateur qui recevra une invitation

   * **URL de redirection** : URL vers laquelle l’utilisateur invité est dirigé après avoir accepté l’invitation

    ![Exemple de fichier CSV avec des utilisateurs invités entrés](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > N’utilisez pas de virgules dans le <bpt id="p1">**</bpt>Message d’invitation personnalisé<ept id="p1">**</ept>, car elles empêchent le message d’être correctement analysé.

6. Enregistrez le fichier .
7. Dans la page **Inviter des utilisateurs en bloc (préversion)** , sous **Chargez votre fichier .csv**, accédez au fichier. Quand vous sélectionnez le fichier, la validation du fichier .csv démarre. 
8. Quand le contenu du fichier est validé, un message indique **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
9. Une fois votre fichier validé, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui ajoute les invitations. 
10. Pour voir l’état du travail, sélectionnez **Cliquez ici pour afficher l’état de chaque opération**. Vous pouvez également sélectionner **Résultats de l’opération en bloc (préversion)** dans la section **Activité**. Pour plus d’informations sur chaque élément de ligne au sein de l’opération en bloc, sélectionnez les valeurs sous les colonnes **Nombre de réussites**, **Nombre d’échecs** ou **Nombre total de requêtes**. Si des échecs se sont produits, les raisons sont affichées.

    ![Exemple de résultats de l’opération en bloc](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Une fois le travail terminé, vous recevez une notification indiquant que l’opération en bloc a réussi.

## <a name="verify-guest-users-in-the-directory"></a>Vérifier les utilisateurs invités dans l’annuaire

Vérifiez que les utilisateurs invités que vous avez ajoutés sont bien présents dans l’annuaire dans le portail Azure ou avec PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Voir les utilisateurs invités dans le portail Azure

1. Connectez-vous au portail Azure en utilisant un compte d’administrateur d’utilisateurs de l’organisation.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs**.
4. Sous **Afficher**, sélectionnez **Uniquement les utilisateurs invités** et vérifiez que les utilisateurs que vous avez ajoutés sont listés.

### <a name="view-guest-users-with-powershell"></a>Voir les utilisateurs invités avec PowerShell

Exécutez la commande suivante :

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Vous devez y voir figurer les utilisateurs que vous avez invités, avec un nom d’utilisateur principal (UPN) au format *adresse_e-mail*#EXT#\@*domaine*. Par exemple, *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, où contoso.onmicrosoft.com est l’organisation à partir de laquelle vous avez envoyé les invitations.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer les comptes d’utilisateur de test dans l’annuaire du portail Azure dans la page Utilisateurs. Pour cela, cochez la case en regard de l’utilisateur invité, puis sélectionnez **Supprimer**. 

Vous pouvez également exécuter la commande PowerShell suivante pour supprimer un compte d’utilisateur :

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Par exemple : `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez envoyé des invitations en bloc à des utilisateurs invités en dehors de votre organisation. Découvrez ensuite comment fonctionne le processus d’échange d’invitation.

> [!div class="nextstepaction"]
> [Découvrir plus d’informations sur le processus d’échange d’invitation d’Azure AD B2B Collaboration](redemption-experience.md)
