---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à Freshservice | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ba2f59799629873553fe261abebf59529ad94d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "73570607"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à Freshservice

Dans ce tutoriel, vous allez apprendre à intégrer Freshservice à Azure Active Directory (Azure AD). Lorsque vous intégrez Freshservice à Azure AD, vous pouvez :

* Contrôler qui a accès à Freshservice dans Azure AD.
* Permettre à vos utilisateurs de se connecter automatiquement à Freshservice avec leur compte Azure AD.
* Gérez tous vos comptes dans un même emplacement : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement FreshService pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Freshservice prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-freshservice-from-the-gallery"></a>Ajout de Freshservice à partir de la galerie

Pour configurer l’intégration de Freshservice à Azure AD, vous devez ajouter Freshservice disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Freshservice** dans la zone de recherche.
1. Sélectionnez **Freshservice** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configurer et tester l’authentification unique Azure AD pour Freshservice

Configurez et testez l’authentification unique Azure AD avec Freshservice à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Freshservice associé.

Pour configurer et tester l’authentification unique (SSO) Azure AD avec Freshservice, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Freshservice](#configure-freshservice-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Freshservice](#create-freshservice-test-user)** pour avoir un équivalent de B.Simon dans Freshservice lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Freshservice**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<democompany>.freshservice.com`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe du support client Freshservice](https://support.freshservice.com/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Freshservice nécessite l’empreinte digitale SHA-256 pour que l’authentification unique fonctionne. Pour obtenir l’empreinte digitale SHA-256, procédez comme suit :

    ![FingerPrint](./media/freshservice-tutorial/ic790821.png "FingerPrint")

    1. Ouvrez le [lien](https://www.samltool.com/fingerprint.php) dans un autre navigateur web.

    1. Ouvrez le certificat téléchargé (Base64) dans le Bloc-notes, puis collez son contenu dans la zone de texte **Certificat X.509**.

    1. Pour Algorithme, sélectionnez **sha256** dans la liste déroulante.

    1. Cliquez sur **CALCULER L’EMPREINTE DIGITALE**.

    1. Cliquez sur l’icône de copie pour copier l’empreinte digitale **FingerPrint** générée et l’enregistrer sur votre ordinateur.

1. Dans la section **Configurer Freshservice** du **portail Azure**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Freshservice.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Freshservice**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-freshservice-sso"></a>Configurer l’authentification unique Freshservice

1. Pour automatiser la configuration dans Freshservice, vous devez installer l’**extension de navigateur My Apps Secure Sign-in** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Freshservice** pour être redirigé vers l’application Freshservice. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Freshservice. Cette extension de navigateur configure automatiquement l’application et automatise les étapes 3 à 6.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer manuellement Freshservice, ouvrez une nouvelle fenêtre de navigateur web, connectez-vous à votre site d’entreprise Freshservice en tant qu’administrateur, puis effectuez les étapes suivantes :

4. Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/freshservice-tutorial/ic790814.png "Administrateur")

5. Dans le **Customer Portal**, cliquez sur **Security**.

    ![Sécurité](./media/freshservice-tutorial/ic790815.png "Sécurité")

6. Dans la section **Security** , procédez comme suit :

    ![Single Sign On](./media/freshservice-tutorial/ic790816.png "Single Sign On")

    a. Activez **Single Sign On**.

    b. Sélectionnez **SAML SSO**.

    c. Dans la zone de texte **SAML Login URL** (URL de connexion SAML), collez la valeur **URL de connexion** que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Logout URL** (URL de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    e. Dans la zone de texte **Security Certificate Fingerprint** (Empreinte digitale de certificat de sécurité), collez la valeur **FingerPrint** que vous avez générée précédemment.

    f. Cliquez sur **Enregistrer**.

### <a name="create-freshservice-test-user"></a>Créer un utilisateur de test Freshservice

Pour permettre aux utilisateurs Azure AD de se connecter à FreshService, vous devez les attribuer dans FreshService. Dans le cas de FreshService, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise **FreshService** en tant qu’administrateur.

2. Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/freshservice-tutorial/ic790814.png "Administrateur")

3. Dans la section **User Management**, cliquez sur **Requesters**.

    ![Requesters](./media/freshservice-tutorial/ic790818.png "Requesters")

4. Cliquez sur **New Requester**.

    ![New Requesters](./media/freshservice-tutorial/ic790819.png "New Requesters")

5. Dans la section **New Requester** , procédez comme suit :

    ![New Requester](./media/freshservice-tutorial/ic790820.png "New Requester")  

    a. Entrez le prénom et l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte **Prénom** et **Email**.

    b. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé.
    >  

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par FreshService, pour approvisionner des comptes utilisateur AAD.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Freshservice dans le panneau d’accès doit vous connecter automatiquement à l’application Freshservice pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayez Freshservice avec Azure AD](https://aad.portal.azure.com/)