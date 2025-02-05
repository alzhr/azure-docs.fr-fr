---
title: Démarrage rapide pour les applications web Python de la plateforme d’identités Microsoft | Azure
description: Découvrir comment implémenter la connexion Microsoft sur une application web Python à l’aide d’OAuth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: d9349391ad9af1a4ec1c84b586f825f3f7632ff8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815747"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web Python

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Dans ce guide de démarrage rapide, vous allez apprendre à intégrer une application web Python à la plateforme d’identités Microsoft. Votre application va connecter un utilisateur, obtenir un jeton d’accès pour appeler l’API Microsoft Graph et envoyer une requête à l’API Microsoft Graph.

À la fin de ce guide, votre application acceptera les connexions de comptes Microsoft personnels (y compris outlook.com, live.com et d’autres) et de comptes professionnels ou scolaires de n’importe quelle entreprise ou organisation utilisant Azure Active Directory.

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Prérequis

Pour exécuter cet exemple, vous avez besoin des éléments suivants :

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) or [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pythonhosted.org/Flask-Session/), [requests](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Inscrire et télécharger votre application de démarrage rapide
>
> Vous avez deux options pour démarrer votre application du guide de démarrage rapide : rapide (Option 1) et manuelle (Option 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au [portail Azure - Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
>      - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `python-webapp`.
>      - Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
>      - Sous la section **URI de redirection**, dans la liste déroulante, sélectionnez la plateforme **Web**, puis définissez la valeur sur `http://localhost:5000/getAToken`.
>      - Sélectionnez **Inscription**. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
> 1. Dans le menu de gauche, choisissez **Certificats et secrets**, puis cliquez sur **Nouveau secret client** dans la section **Secrets client** :
>
>      - Tapez une description de la clé (pour le secret d’application de l’instance).
>      - Sélectionnez la durée de clé **Dans 1 an**.
>      - Quand vous cliquez sur **Ajouter**, la valeur de la clé s’affiche.
>      - Copiez la valeur de la clé. Vous en aurez besoin ultérieurement.
> 1. Sélectionnez la section **Autorisations de l’API** :
>
>      - Cliquez sur le bouton **Ajouter une autorisation**.
>      - Vérifiez ensuite que l’onglet **API Microsoft** est sélectionné.
>      - Dans la section *API Microsoft couramment utilisées*, cliquez sur **Microsoft Graph**
>      - Dans la section **Autorisations déléguées**, vérifiez que les autorisations appropriées sont cochées : **User.ReadBasic.All**. Utilisez la zone de recherche au besoin.
>      - Sélectionnez le bouton **Ajouter des autorisations**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
>
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez :
>
> 1. Ajoutez une URL de réponse sous la forme `http://localhost:5000/getAToken`.
> 1. Créer un secret client.
> 1. Ajoutez la permission déléguée User.ReadBasic.All de l’API Microsoft Graph.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec cet attribut.

#### <a name="step-2-download-your-project"></a>Étape 2 : Télécharger votre projet

[Télécharger l'exemple de code](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Étape 3 : Configurer l’application

1. Extrayez le fichier zip dans un dossier local proche du dossier racine (par exemple, **C:\Azure-Samples**)
1. Si vous utilisez un environnement de développement intégré, ouvrez l’exemple dans votre IDE habituel (facultatif).
1. Ouvrez le fichier **app_config.py** qui se trouve dans le dossier racine, puis remplacez son contenu par l’extrait de code suivant :

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Où :
>
> - `Enter_the_Application_Id_here` - est l’ID de l’application pour l’application que vous avez inscrite.
> - `Enter_the_Client_Secret_Here` - correspond au **secret client** que vous avez créé dans **Certificats et secrets** pour l’application que vous avez inscrite.
> - `Enter_the_Tenant_Name_Here` – est la valeur **ID de l’annuaire (locataire)** de l’application que vous avez inscrite.

#### <a name="step-4-run-the-code-sample"></a>Étape 4 : Exécuter l’exemple de code

1. Vous devez installer la bibliothèque Python MSAL, le framework Flask, des Flask-Sessions pour la gestion des sessions côté serveur et la bibliothèque Requests avec PIP, comme suit :

   ```Shell
   pip install -r requirements.txt
   ```

2. Exécutez app.py à partir de l’interpréteur de commandes ou de la ligne de commande :

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. Le secret client étant ajouté en texte brut à vos fichiers projet, il est recommandé, pour des raisons de sécurité, d’utiliser un certificat au lieu d’un secret client avant de considérer l’application comme application de production. Pour savoir plus en détails comment utiliser un certificat, voir [ces instructions](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Plus d’informations

### <a name="getting-msal"></a>Obtention de MSAL
MSAL est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la Plateforme d’identités Microsoft.
Pour ajouter MSAL Python à votre application, vous pouvez utiliser Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Initialisation MSAL
Pour ajouter la référence à MSAL Python, ajoutez le code suivant au début du fichier où vous allez utiliser MSAL :

```Python
import msal
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les applications web qui connectent les utilisateurs, puis qui appellent des API web :

> [!div class="nextstepaction"]
> [Scénario : Applications web qui connectent les utilisateurs](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
