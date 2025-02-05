---
title: Utiliser le rapport d’activité pour déplacer des applications AD FS vers Azure Active Directory | Microsoft Docs
description: Le rapport d’activité d’applications AD FS (Active Directory Federation Services) permet de migrer rapidement des applications d’AD FS vers Azure Active Directory (Azure AD). Cet outil de migration pour AD FS identifie la compatibilité avec Azure AD et fournit des instructions de migration.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a267811b7e7ac8715b6823a24c2b3a1f0d430c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180109"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Utiliser le rapport d’activité des applications AD FS (préversion) pour migrer des applications vers Azure AD

De nombreuses organisations utilisent les services de fédération Active Directory (AD FS) pour fournir une authentification unique aux applications clou. Il est avantageux de transférer vos applications AD FS vers Azure AD pour l’authentification, notamment en termes de gestion des coûts, de gestion des risques, de productivité, de conformité et de gouvernance. Toutefois, comprendre quelles applications sont compatibles avec Azure AD et identifier des étapes de migration spécifiques peut prendre beaucoup de temps.

Le rapport d’activité des applications AD FS (préversion) dans le portail Azure vous permet d’identifier rapidement les applications capables de migrer vers Azure AD. Il évalue la compatibilité de toutes les applications AD FS avec Azure AD, recherche tout problème éventuel et fournit des instructions sur la préparation d’applications individuelles pour la migration. Le rapport d’activité des applications AD FS vous offre les possibilités suivantes :

* **Découvrir des applications AD FS et définir l’ampleur de votre migration.** Le rapport d’activité des applications AD FS répertorie toutes les applications AD FS disponibles au sein de votre organisation, et indique leur état de préparation pour une migration vers Azure AD.
* **Hiérarchiser les applications pour la migration.** Obtenez le nombre d’utilisateurs uniques qui se sont connectés à l’application au cours des 1, 7 ou 30 derniers jours afin de déterminer la criticité ou le risque de la migration de l’application.
* **Exécuter des tests de migration et résoudre des problèmes.** Le service de génération de rapports exécute automatiquement des tests pour déterminer si une application est prête pour la migration. Les résultats sont affichés dans le rapport d’activité des applications AD FS en tant qu’état de la migration. Si des problèmes de migration potentiels sont identifiés, vous obtenez des instructions spécifiques sur la manière de les résoudre.

Les données d’activité des applications AD FS sont disponibles pour les utilisateurs auxquels est attribué l’un des rôles d’administrateur suivants : administrateur général, lecteur de rapport, lecteur de sécurité, administrateur d’application ou administrateur d’application cloud.

## <a name="prerequisites"></a>Conditions préalables

* Votre entreprise doit utiliser actuellement AD FS pour accéder aux applications.
* Azure AD Connect Health doit être activé dans votre locataire Azure AD.
   * [Apprenez-en davantage sur l’intégrité d’Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Commencez à configurer Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Découvrir les applications AD FS pouvant être migrées 

Le rapport d’activité des applications AD FS est disponible sur le portail Azure, sous les rapports **Utilisation et insights** d’Azure AD. Le rapport d’activité des applications AD FS analyse chaque application AD FS pour déterminer si elle peut être migrée telle quelle, ou si un examen supplémentaire est nécessaire. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un rôle d’administrateur ayant accès aux données d’activité des applications AD FS (administrateur général, lecteur de rapport, lecteur de sécurité, administrateur d’application ou administrateur d’application cloud).

2. Sélectionnez **Azure Active Directory**, puis **Applications d’entreprise**.

3. Sous **Activité**, sélectionnez **Utilisation et insights (préversion)** , puis **Activité des applications AD FS** pour ouvrir la liste de toutes les applications AD FS au sein de votre organisation.

   ![Activité des applications AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Pour chaque application figurant dans la liste d’activités des applications AD FS, affichez l’**État de la migration** :

   * **Prêt pour la migration** signifie que la configuration de l’application AD FS est entièrement prise en charge dans Azure AD et peut être migrée telle quelle.

   * **Révision nécessaire** signifie que certains paramètres de l’application peuvent être migrés vers Azure AD, mais que vous devez examiner les paramètres qui ne peuvent pas être migrés tels quels.

   * **Étapes supplémentaires nécessaires** signifie qu’Azure AD ne prend pas en charge certains paramètres de l’application, de sorte que l’ application ne peut pas être migrée dans son état actuel.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Évaluer l’état de préparation d’une application pour la migration 

1. Dans la liste d’activités des applications AD FS, cliquez sur l’état dans la colonne **État de la migration** pour ouvrir les détails de la migration. Vous voyez un résumé des tests de configuration réussis, ainsi que tout problème de migration potentiel.

   ![Détails de la migration](media/migrate-adfs-application-activity/migration-details.png)

2. Cliquez sur un message pour ouvrir les détails supplémentaires de la règle de migration. Pour obtenir la liste complète des propriétés testées, voir le tableau [ Tests de configuration d’application AD FS ](#ad-fs-application-configuration-tests) ci-dessous.

   ![Détails de la règle de migration](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Tests de configuration d’application AD FS

Le tableau suivant répertorie tous les tests de configuration effectués sur les applications AD FS.

|Propriété  |Statut  |Description  |
|---------|---------|---------|
|Au moins une règle non migrable a été détectée pour l’authentification supplémentaire.       | Réussite/Avertissement          | La partie de confiance dans AD FS utilise un fournisseur MFA local. Pour migrer vers Azure AD, nous vous recommandons d’adopter Azure MFA ou d’intégrer des contrôles personnalisés avec un fournisseur MFA tiers.  [En savoir plus sur Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|La partie de confiance a la valeur AdditionalWSFedEndpoint définie sur true.       | Réussite/Échec          | La partie de confiance dans AD FS autorise plusieurs points de terminaison d’assertion WS-Fed. Actuellement, Azure AD n’en prend en charge qu’un seul. Si vous êtes dans une situation où cela bloque la migration, [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|La partie de confiance a défini AllowedAuthenticationClassReferences.       | Réussite/Échec          | Ceci est un paramètre dans AD FS, qui vous permet de spécifier si l’application est configurée pour n’autoriser que certains types d’authentification. Azure AD ne prend pas cela en charge aujourd’hui. Si vous êtes dans une situation où cela bloque la migration, [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).          |
|AlwaysRequireAuthentication est configuré.      | Réussite/Échec          | Indique si l’application est configurée pour ignorer les cookies SSO et « Toujours demander l’authentification ». Actuellement non pris en charge par Azure AD.          |
|La partie de confiance a la valeur AutoUpdateEnabled définie sur true       | Réussite/Avertissement          | Ceci est un paramètre dans AD FS, qui vous permet de spécifier si AD FS est configuré pour mettre à jour automatiquement l’application en fonction des modifications apportées aux métadonnées de fédération. Si Azure AD ne prend pas en charge ce paramètre actuellement, il ne devrait pas bloquer la migration de l’application vers Azure AD.           |
|La partie de confiance dispose de plusieurs ClaimsProviders activés       | Réussite/Échec          | La partie de confiance est configurée pour recevoir des revendications d’un fournisseur de revendications autre qu’Active Directory.  Cela n’est pas pris en charge dans Azure AD. Si vous êtes dans une situation où cela bloque la migration, [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|La valeur DelegationAuthorization est valide      | Réussite/Échec          | Des règles d’autorisation de délégation personnalisées sont définies pour l’application. Azure AD ne prend pas cela en charge aujourd’hui. Si vous êtes dans une situation où cela bloque la migration,  [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695747-allow-to-define-delegation-authorization-rules).          |
|La partie de confiance a des ADFSRPImpersonationAuthorizationRules       | Réussite/Avertissement          | Des règles d’autorisation de délégation d’emprunt d’identité personnalisées sont définies pour l’application. Si Azure AD ne prend pas en charge ce paramètre actuellement, il ne devrait pas bloquer la migration de l’application vers Azure AD.          |
|Au moins une règle non migrable a été détectée pour IssuanceAuthorization.       | Réussite/Avertissement          | Des règles d’autorisation d’émission personnalisées sont définies pour l’application dans AD FS. Azure AD prend en charge cette fonctionnalité avec un accès conditionnel Azure AD. [En savoir plus sur l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). En outre, dans Azure AD, vous pouvez limiter l’accès à une application aux utilisateurs ou aux groupes affectés à celle-ci. [En savoir plus sur l’attribution de l’accès aux applications à des utilisateurs et des groupes](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups). Pour des informations sur les règles IssuanceAuthorization brutes configurées dans AD FS, voir le tableau [Tests de règle de revendication](#check-the-results-of-claim-rule-tests) ci-dessous.           |
|Au moins une règle non migrable a été détectée pour IssuanceTransform.       | Réussite/Avertissement          | Des règles de transformation d’émission personnalisées sont définies pour l’application dans AD FS. Azure AD prend en charge la personnalisation des revendications émises dans le jeton. Pour en savoir plus, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).  Pour des informations sur les règles IssuanceTransformation brutes configurées dans AD FS, voir le tableau [Tests de règle de revendication](#check-the-results-of-claim-rule-tests) ci-dessous.          |
|La partie de confiance a la valeur MonitoringEnabled définie sur true.       | Réussite/Avertissement          | Ceci est un paramètre dans AD FS, qui vous permet de spécifier si AD FS est configuré pour surveiller les métadonnées d’une fédération pour cette application. Si Azure AD ne prend pas en charge ce paramètre actuellement, il ne devrait pas bloquer la migration de l’application vers Azure AD.          |
|La partie de confiance a NotBeforeSkew configuré.      | Réussite/Avertissement          | AD FS autorise une asymétrie temporelle basé sur les heures NotBefore et NotOnOrAfter dans le jeton SAML. Si Azure AD ne prend pas en charge ce paramètre actuellement, il ne devrait pas bloquer la migration de l’application vers Azure AD.          |
|La partie de confiance a la valeur RequestMFAFromClaimsProviders définie sur true.       | Réussite/Avertissement          | Ceci est un paramètre dans AD FS, qui vous permet de spécifier si l’application est codée en dur sur un autre fournisseur de revendications et requiert MFA. Pour migrer vers Azure AD, nous vous recommandons d’adopter Azure MFA ou d’intégrer des contrôles personnalisés avec un fournisseur MFA tiers.  [En savoir plus sur Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|La partie de confiance a la valeur SignedSamlRequestsRequired définie sur true       | Réussite/Échec          | L’application est configurée dans AD FS pour vérifier la signature dans la demande SAML. Ce paramètre est facultatif et ne doit pas bloquer votre migration. Azure Active Directory utilise les URL de réponse pour valider le fournisseur de services.  Si vous êtes dans une situation où cela bloque la migration, [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|TokenLifetimeCheckResult        | Réussite/Échec         | L’application est configurée pour une durée de vie de jeton personnalisée. Le paramétrage d’AD FS par défaut est de 1 heure. Azure AD prend en charge cette fonctionnalité avec un accès conditionnel. Pour plus d’informations, voir [Configurer la gestion de session d’authentification avec l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|La partie de confiance est configurée pour chiffrer les revendications. Azure AD prend cela en charge       | Réussite          | Avec Azure AD vous pouvez chiffrer le jeton envoyé à l’application. Pour en savoir plus, voir [Configurer le chiffrement des jetons SAML Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|La partie de confiance est configurée pour chiffrer NameID dans le jeton SAML.      | Réussite/Échec          | L’application est configurée pour chiffrer la revendication nameID dans le jeton SAML. Avec Azure AD vous pouvez chiffrer le jeton entier envoyé à l’application. Le chiffrement de revendications spécifiques n’est pas encore pris en charge. Pour en savoir plus, voir [Configurer le chiffrement des jetons SAML Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Vérifier les résultats des tests de règle de revendication

Si vous avez configuré une règle de revendication pour l’application dans AD FS, l’expérience fournit une analyse précise pour toutes les règles de revendication. Vous verrez les règles de revendication qui peuvent être déplacées vers Azure AD et celles qui nécessitent un examen plus approfondi.

1. Dans la liste d’activités des applications AD FS, cliquez sur l’état dans la colonne **État de la migration** pour ouvrir les détails de la migration. Vous voyez un résumé des tests de configuration réussis, ainsi que tout problème de migration potentiel.

2. Sur la page **Détails de la règle de migration**, développez les résultats pour afficher des informations détaillées sur les problèmes de migration potentiels et obtenir des conseils supplémentaires. Pour obtenir une liste détaillée de toutes les règles de revendication testées, voir le tableau [Vérifier les résultats des tests de règle de revendication](#check-the-results-of-claim-rule-tests) ci-dessous.

   L’exemple ci-dessous montre les détails de la règle de migration IssuanceTransform. Il répertorie les parties spécifiques de la revendication qui doivent être examinées et traitées avant de pouvoir migrer l’application vers Azure AD.

   ![La règle de migration fournit des instructions supplémentaires](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Tests de règle de revendication

Le tableau suivant répertorie tous les tests de règle de revendication effectués sur les applications AD FS.

|Propriété  |Description  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | L’instruction de condition utilise des expressions régulières pour évaluer si la revendication correspond à un modèle donné.  Pour obtenir une fonctionnalité similaire dans Azure AD, vous pouvez utiliser une transformation prédéfinie telle que IfEmpty(), StartWith(), Contains() ou autre. Pour plus d’informations, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | L’instruction de condition comprend plusieurs conditions qui doivent être évaluées avant d’exécuter l’instruction d’émission. Azure AD peut prendre en charge cette fonctionnalité avec les fonctions de transformation de la revendication dans lesquelles vous pouvez évaluer plusieurs valeurs de revendication.  Pour plus d’informations, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | La règle de revendication n’a pas pu être reconnue. Pour plus d’informations sur la configuration des revendications dans Azure AD, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | L’instruction de condition utilise un émetteur qui n’est pas pris en charge dans Azure AD. Actuellement, Azure AD ne reçoit pas de revendications de magasins autres qu’Active Directory ou Azure AD. Si cela vous empêche de migrer des applications vers Azure AD, [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | L’instruction de condition utilise une fonction d’agrégation pour émettre ou ajouter une revendication, quel que soit le nombre de correspondances.  Dans Azure AD, vous pouvez évaluer l’attribut d’un utilisateur pour décider de la valeur à utiliser pour la revendication avec des fonctions telles que IfEmpty(), StartWith(), Contains() et autres. Pour plus d’informations, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | L’instruction de condition utilise une revendication restreinte dans Azure AD. Vous pouvez peut-être émettre une revendication restreinte, mais vous ne pouvez pas en modifier la source ou lui appliquer une transformation. Pour plus d’informations, voir [Personnaliser des revendications émises dans des jetons pour une application spécifique dans Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | La déclaration d’émission utilise un magasin d’attributs différent d’Active Directory. Actuellement, Azure AD ne reçoit pas de revendications de magasins autres qu’Active Directory ou Azure AD. Si cela vous empêche de migrer des applications vers Azure AD, [faites-le nous savoir](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | La déclaration d’émission utilise ADD pour ajouter des revendications à l’ensemble de revendications entrantes. Dans Azure AD, cela peut être configuré en tant que transformations de revendication multiples.  Pour plus d’informations, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | La déclaration d’émission utilise des expressions régulières pour transformer la valeur de la revendication à émettre. Pour obtenir une fonctionnalité similaire dans Azure AD, vous pouvez utiliser une transformation prédéfinie telle que Extract(), Trim(), ToLower ou autre. Pour plus d’informations, voir [Personnaliser des revendications émises dans le jeton SAML pour des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Étapes suivantes

- [Gestion des applications avec Azure Active Directory](what-is-application-management.md)
- [Gérer l’accès aux applications](what-is-access-management.md)
- [Fédération avec Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
