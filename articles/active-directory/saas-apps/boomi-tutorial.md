---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à Boomi | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1044a78d9bf1b52a7b0dd9e47d7db6d98b45299c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à Boomi

Dans ce tutoriel, vous allez apprendre à intégrer Boomi à Azure Active Directory (Azure AD). En intégrant Boomi à Azure AD, vous pouvez :

* Contrôler, dans Azure AD, qui a accès à Boomi
* Permettre aux utilisateurs de se connecter automatiquement à Boomi avec leur compte Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Boomi pour lequel l’authentification unique (SSO) est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Boomi prend en charge l’authentification unique initiée par le **fournisseur d’identité**

## <a name="adding-boomi-from-the-gallery"></a>Ajout de Boomi depuis la galerie

Pour configurer l’intégration de Boomi avec Azure AD, vous devez ajouter Boomi disponible dans la galerie, à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Boomi** dans la zone de recherche.
1. Sélectionnez **Boomi** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configurer et tester l’authentification unique Azure AD pour Boomi

Configurez et testez l’authentification unique Azure AD auprès de Boomi avec un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Boomi associé.

Pour configurer et tester l’authentification unique Azure AD auprès de Boomi, suivez les indications des modules ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    * **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    * **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Boomi](#configure-boomi-sso)** pour configurer les paramètres de l’authentification unique côté application.
    * **[Créer un utilisateur de test Boomi](#create-boomi-test-user)** pour avoir un équivalent de B.Simon dans Boomi lié à la représentation Azure AD associée.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Boomi**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet de **Configuration SAML de base** pour modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la page **Configurer l’authentification unique avec SAML**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://platform.boomi.com/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez l’[équipe du support technique Boomi](https://boomi.com/company/contact/). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. L’application Boomi s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration des attributs du jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut.

    ![image](common/default-attributes.png)

1. En plus de ce qui précède, l’application Boomi s’attend à ce que quelques attributs supplémentaires (présentés ci-dessous) soient repassés dans la réponse SAML. Ces attributs sont également préremplis, mais vous pouvez les examiner pour voir s’ils répondent à vos besoins.

    | Nom |  Attribut source|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (en base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Boomi**, copiez l’URL ou les URL appropriées en fonction de vos besoins.

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Boomi.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Boomi**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-boomi-sso"></a>Configurer l’authentification unique Boomi

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

1. Accédez à **Nom de la société** et allez dans **Configurer**.

1. Cliquez sur l’onglet **Options SSO** et suivez les étapes ci-dessous.

    ![Configurer l’authentification unique côté application](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Cochez la case **Enable SAML Single Sign-On**.

    b. Cliquez sur **Importer** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat du fournisseur d’identité**.

    c. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), copiez la valeur de **URL de connexion** indiquée dans la fenêtre Configuration de l’application Azure AD.

    d. Dans **Federation Id Location** (Emplacement ID de fédération), sélectionnez le bouton radio **Federation Id is in FEDERATION_ID Attribute element** (L’ID de fédération se trouve dans l’élément d’attribut FEDERATION_ID).

    e. Cliquez sur le bouton **Enregistrer** .

### <a name="create-boomi-test-user"></a>Créer un utilisateur de test Boomi

Pour permettre aux utilisateurs Azure AD de se connecter à Boomi, vous devez les provisionner dans Boomi. En l’occurrence, cet approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :

1. Connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

1. Après connexion, accédez à **Gestion des utilisateurs** et allez dans **Utilisateurs**.

    ![Utilisateurs](./media/boomi-tutorial/tutorial_boomi_001.png "Utilisateurs")

1. Cliquez sur l’icône **+** , et la boîte de dialogue **Ajouter/gérer les rôles utilisateur** s’ouvre.

    ![Utilisateurs](./media/boomi-tutorial/tutorial_boomi_002.png "Utilisateurs")

    ![Utilisateurs](./media/boomi-tutorial/tutorial_boomi_003.png "Utilisateurs")

    a. Dans la zone de texte **Adresse e-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple B.Simon@contoso.com.

    b. Dans la zone de texte **First name**, tapez le prénom de l’utilisateur, par exemple B.

    c. Dans la zone de texte **Nom**, tapez le nom de l’utilisateur, par exemple Simon.

    d. Entrez **l’ID de fédération** de l’utilisateur. Chaque utilisateur doit posséder un ID de fédération qui l’identifie de façon unique dans le compte.

    e. Affectez le rôle **Utilisateur standard** à l’utilisateur. Ne lui affectez pas le rôle d’administrateur, car cela lui donnerait l’accès normal à AtomSphere, ainsi que l’accès en authentification unique.

    f. Cliquez sur **OK**.

    > [!NOTE]
    > L’utilisateur ne recevra pas d’e-mail de notification de bienvenue contenant un mot de passe pouvant être utilisé pour se connecter au compte AtomSphere, car son mot de passe est géré par le biais du fournisseur d’identité. Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Boomi pour approvisionner des comptes d’utilisateur Azure Active Directory.

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Boomi dans le panneau d’accès doit vous connecter automatiquement à l’application Boomi pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer Boomi avec Azure AD](https://aad.portal.azure.com/)