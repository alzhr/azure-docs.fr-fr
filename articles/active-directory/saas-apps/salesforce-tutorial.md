---
title: 'Didacticiel : Intégration de l’authentification unique Azure Active Directory à Salesforce | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63cc4b902c0bd0281228e23076be6e0a18461597
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241427"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à Salesforce

Dans ce tutoriel, vous allez découvrir comment intégrer Salesforce à Azure Active Directory (Azure AD). Quand vous intégrez Salesforce à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Salesforce.
* Permettre à vos utilisateurs de se connecter automatiquement à Salesforce avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Abonnement Salesforce pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Salesforce prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

* Salesforce prend en charge l’attribution d’utilisateurs **juste-à-temps**

* Salesforce prend en charge [l’attribution d’utilisateurs **automatique**](salesforce-provisioning-tutorial.md)

* L’application Salesforce Mobile peut désormais être configurée avec Azure AD pour activer l’authentification unique. Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

## <a name="adding-salesforce-from-the-gallery"></a>Ajout de Salesforce à partir de la galerie

Pour configurer l’intégration de Salesforce avec Azure AD, vous devez ajouter Salesforce à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Salesforce** dans la zone de recherche.
1. Sélectionnez **Salesforce** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configurer et tester l’authentification unique Azure AD pour Salesforce

Configurez et testez l’authentification unique Azure AD avec Salesforce pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Salesforce associé.

Pour configurer et tester l’authentification unique Azure AD avec Salesforce, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique Salesforce](#configure-salesforce-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Salesforce](#create-salesforce-test-user)** pour disposer dans Salesforce d’un équivalent de B.Simon lié à la représentation Azure AD de l’utilisateur.
3. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec Salesforce, effectuez les étapes suivantes :

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Salesforce**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant :

    Compte d’entreprise : `https://<subdomain>.my.salesforce.com`

    Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Dans la zone de texte **Identificateur**, entrez la valeur au format suivant :

    Compte d’entreprise : `https://<subdomain>.my.salesforce.com`

    Compte de développeur : `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Salesforce](https://help.salesforce.com/support).

1. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies en fonction de vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Salesforce**, copiez la ou les URL appropriées, selon vos besoins.

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
   
    > [!NOTE]
    > Les attributs utilisateur Salesforce sont sensibles à la casse pour la validation SAML.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Salesforce.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Salesforce**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-salesforce-sso"></a>Configurer l’authentification unique Salesforce

1. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce.

2. Cliquez sur **Setup** sous **l’icône de paramètres** en haut à droite de la page.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/configure1.png)

3. Dans le volet de navigation, accédez à **SETTINGS** et cliquez sur **Identity** pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-admin-sso.png)

4. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Si vous ne pouvez pas activer les paramètres de l’authentification unique pour votre compte Salesforce, il vous faudra peut-être contacter [l’équipe du support technique de Salesforce](https://help.salesforce.com/support).

5. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

      ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-enable-saml.png)

6. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau à partir d’un fichier de métadonnées**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Cliquez sur **Sélectionner le fichier** pour charger le fichier XML de métadonnées que vous avez téléchargé à partir du Portail Azure, puis cliquez sur **Créer**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/xmlchoose.png)

8. Sur la page **Paramètres d’authentification unique SAML**, les champs se renseignent automatiquement et cliquez sur Enregistrer.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/salesforcexml.png)

9. Dans le volet de navigation de gauche de Salesforce, cliquez sur **Company Settings** pour développer la section associée, puis cliquez sur **My Domain**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-my-domain.png)

10. Faites défiler le contenu de la fenêtre jusqu’à la section **Configuration de l’authentification**, puis cliquez sur le bouton **Modifier**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. Dans la section **Configuration de l’authentification**, cochez **AzureSSO** comme **Service d’authentification** de votre configuration SSO SAML, puis cliquez sur **Enregistrer**.

    ![Configurer l'authentification unique](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Si plusieurs services d’authentification sont sélectionnés, les utilisateurs sont invités à choisir le service d’authentification qu’ils préfèrent utiliser pour se connecter lors de l’initialisation d’une authentification unique sur votre environnement Salesforce. Si vous ne voulez pas que cela se produise, vous devez **décocher toutes les cases en regard des autres services d’authentification**.

### <a name="create-salesforce-test-user"></a>Créer un utilisateur de test Salesforce

Dans cette section, un utilisateur appelé B.Simon est créé dans Salesforce. Salesforce prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Si un utilisateur n’existe pas dans Salesforce, un nouveau est créé lorsque vous tentez d’accéder à Salesforce. Salesforce prend également en charge l’attribution automatique d’utilisateurs. Des informations supplémentaires sur la configuration de l’attribution automatique d’utilisateurs sont disponibles [ici](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>Tester l’authentification unique (SSO)

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Salesforce dans le panneau d’accès doit vous connecter automatiquement à l’application Salesforce pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Tester l’authentification unique pour Salesforce (Mobile)

1. Ouvrez l’application mobile Salesforce. Dans la page de connexion, cliquez sur **Use Custom Domain** (Utiliser un domaine personnalisé).

    ![Application mobile Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Dans la zone de texte **Custom Domain** (Domaine personnalisé), entrez votre nom de domaine personnalisé inscrit, puis cliquez sur **Continue** (Continuer).

    ![Application mobile Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Entrez vos informations d’identification Azure AD pour vous connecter à l’application Salesforce, puis cliquez sur **Next** (Suivant).

    ![Application mobile Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Dans la page **Allow access** (Autoriser l’accès), comme indiqué ci-dessous, cliquez sur **Allow** (Autoriser) pour donner accès à l’application Salesforce.

    ![Application mobile Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Une fois la connexion réussie, la page d’accueil de l’application s’affiche.

    ![Application mobile Salesforce](media/salesforce-tutorial/mobile-app5.png) ![Application mobile Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurer l’approvisionnement de l’utilisateur](salesforce-provisioning-tutorial.md)

- [Essayer Salesforce avec Azure AD](https://aad.portal.azure.com)
