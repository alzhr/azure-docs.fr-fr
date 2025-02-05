---
title: Comportement d’invite dans des requêtes interactives (Microsoft Authentification Library pour JavaScript)
titleSuffix: Microsoft identity platform
description: En savoir plus sur la personnalisation des comportements d’invite dans des appels interactifs à l’aide de Microsoft Authentification Library pour JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803012"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportement d’invite dans des requêtes interactives MSAL.js

Lorsqu’un utilisateur a établi une session Azure AD active avec plusieurs comptes d’utilisateurs, la page de connexion Azure AD invite par défaut l’utilisateur à sélectionner un compte avant de procéder à l’identification. Les utilisateurs n’ont pas la possibilité de sélectionner de comptes s’il n’y a qu’une seule session authentifiée avec Azure AD.

La bibliothèque MSAL.js (à partir de la version v0.2.4) n’envoie pas de paramètre d’invite pendant les requêtes interactives (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` et `acquireTokenPopup`) et n’applique donc aucun comportement d’invite. Pour les requêtes de jeton silencieux via la méthode `acquireTokenSilent`, MSAL.js envoie un paramètre d’invite défini sur `none`.

En fonction de votre scénario d’application, vous pouvez contrôler le comportement d’invite pour les requêtes interactives en définissant le paramètre d’invite dans les paramètres des requêtes envoyé aux méthodes. Par exemple, si vous souhaitez appeler la sélection de compte :

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Les valeurs d’invites suivantes peuvent être transmises lors de l’authentification auprès d’Azure AD :

**login :** Cette valeur force l’utilisateur à entrer les informations d’identification lors de la demande d’authentification.

**select_account :** Cette valeur fournira à l’utilisateur la sélection de compte répertoriant tous les comptes dans la session.

**consent :** Cette valeur appelle la boîte de dialogue de consentement OAuth qui permet aux utilisateurs d’accorder des autorisations à l’application.

**none :** Cette valeur garantit que l’utilisateur ne voit pas aucune invite interactive. Il est recommandé de ne pas passer cette valeur à des méthodes interactives dans MSAL.js. Des comportements inattendus peuvent se produire. Au lieu de cela, utilisez la méthode `acquireTokenSilent` pour obtenir des appels silencieux.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le paramètre `prompt` dans le protocole d’[octroi implicite OAuth 2.0](v2-oauth2-implicit-grant-flow.md) qu’utilise la bibliothèque MSAL.js.
