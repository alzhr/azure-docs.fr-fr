---
title: Délégation Kerberos contrainte pour Azure AD Domain Services | Microsoft Docs
description: Découvrez comment activer la délégation Kerberos contrainte (KCD) basée sur les ressources dans un domaine managé Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 89bc690e5a8c8d24d7732dd4e12f70a9f1f368af
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842660"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configurer la délégation Kerberos contrainte (KCD) dans Azure Active Directory Domain Services

Pendant leur exécution, les applications peuvent avoir besoin d’accéder à des ressources dans le contexte d’un autre utilisateur. Active Directory Domain Services (AD DS) prend en charge un mécanisme, appelé *délégation Kerberos*, qui autorise ce cas d’usage. La délégation Kerberos *contrainte* (KCD) s’appuie alors sur ce mécanisme pour définir les ressources accessibles dans le contexte de l’utilisateur. Les domaines managés Azure Active Directory Domain Services (Azure AD DS) étant plus sécurisés que les environnements AD DS locaux classiques, utilisez un mécanisme KCD plus sécurisé *basé sur les ressources*.

Cet article vous montre comment configurer la délégation Kerberos contrainte basée sur les ressources dans un domaine managé Azure AD Domain Services.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des ressources suivantes :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, [créez et configurez une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Une machine virtuelle de gestion Windows Server jointe au domaine managé Azure AD DS.
    * Si nécessaire, suivez les étapes du tutoriel pour [créer et joindre une machine virtuelle Windows Server à un domaine managé][create-join-windows-vm], puis [installez les outils de gestion AD DS][tutorial-create-management-vm].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Vue d’ensemble de la délégation Kerberos contrainte

La délégation Kerberos permet à un compte d’emprunter l’identité d’un autre compte pour accéder à des ressources. Par exemple, une application web qui accède à un composant web back-end peut emprunter sa propre identité comme s’il s’agissait d’un autre compte d’utilisateur au moment d’établir la connexion back-end. La délégation Kerberos n’est pas sécurisée dans le sens où le compte qui emprunte l’identité peut accéder à n’importe quelles ressources sans aucune limitation.

En revanche, la délégation Kerberos contrainte (KCD) restreint les services ou les ressources auxquels une application ou un serveur déterminé peut se connecter en empruntant une autre identité. Le mécanisme KCD classique exige des privilèges d’administrateur de domaine pour configurer le compte de domaine d’un service, et il contraint le compte à s’exécuter sur un seul domaine. Le mécanisme KCD classique présente aussi quelques inconvénients. Par exemple, dans les anciens systèmes d’exploitation, les administrateurs de service n’avaient aucun moyen utile de savoir quels services front-end déléguaient vers les services de ressources qu’ils possédaient. Tout service front-end qui pouvait déléguer vers un service de ressources constituait un point d’attaque potentiel. Si un serveur qui hébergeait un service front-end configuré pour déléguer vers des services de ressources était compromis, les services de ressources pouvaient l’être tout autant.

Dans un domaine managé Azure AD Domain Services, il n’existe pas de privilèges d’administrateur de domaine. De ce fait, le mécanisme KCD classique basé sur les comptes ne peut pas être configuré dans un domaine managé Azure AD DS. À la place, il est possible d’utiliser le mécanisme KCD basé les ressources, qui est aussi plus sécurisé.

### <a name="resource-based-kcd"></a>Mécanisme KCD basé sur les ressources

Windows Server 2012 et les versions ultérieures offrent la possibilité aux administrateurs de service de configurer la délégation contrainte pour leur service. Ce modèle est appelé KCD basé sur les ressources. Avec cette approche, l’administrateur de service back-end peut octroyer ou refuser à des services front-end spécifiques le droit d’utiliser KCD.

Le mécanisme KCD basé sur les ressources est configuré à l’aide de PowerShell. Vous devez utiliser les applets de commande [Set-ADComputer][Set-ADComputer] ou [Set-ADUser][Set-ADUser], selon que le compte d’emprunt est un compte d’ordinateur ou un compte de service/compte d’utilisateur.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configurer le mécanisme KCD basé sur les ressources pour un compte d’ordinateur

Dans ce scénario, supposons que vous avez une application web qui s’exécute sur l’ordinateur nommé *contoso-webapp.contoso.com*. L’application web doit accéder à une API web qui s’exécute sur l’ordinateur nommé *contoso-api.contoso.com* dans le contexte des utilisateurs du domaine. Pour configurer ce scénario, effectuez les étapes suivantes :

1. [Créer une unité d’organisation personnalisée](create-ou.md) Vous pouvez déléguer des autorisations à des utilisateurs du domaine managé Azure AD DS pour gérer cette unité d’organisation personnalisée.
1. [Joignez les machines virtuelles][create-join-windows-vm] au domaine managé Azure AD DS, à la fois celles qui exécutent l’application web et celles qui exécutent l’API web. Créez ces comptes d’ordinateur dans l’unité d’organisation personnalisée de l’étape précédente.

    > [!NOTE]
    > Les comptes d’ordinateur de l’application web et de l’API web doivent se trouver dans une unité d’organisation personnalisée dans laquelle vous êtes autorisé à configurer le mécanisme KCD basé sur les ressources. Vous ne pouvez pas configurer le mécanisme KCD basé sur les ressources pour un compte d’ordinateur situé dans le conteneur intégré *Ordinateurs du contrôleur de domaine AAD*.

1. Enfin, configurez le mécanisme KCD basé sur les ressources à l’aide de l’applet de commande PowerShell [Set-ADComputer][Set-ADComputer]. Sur votre machine virtuelle de gestion jointe au domaine, connectez-vous avec un compte d’utilisateur membre du groupe *Azure AD DC Administrators*, puis exécutez les applets de commande suivantes. Indiquez vos propres noms d’ordinateur, si nécessaire :
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
    Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configurer le mécanisme KCD basé sur les ressources pour un compte d’utilisateur

Dans ce scénario, supposons que vous disposez d’une application web qui s’exécute en tant que compte de service nommé *appsvc*. L’application web doit accéder à une API web qui s’exécute en tant que compte de service nommé *backendsvc* dans le contexte des utilisateurs du domaine. Pour configurer ce scénario, effectuez les étapes suivantes :

1. [Créer une unité d’organisation personnalisée](create-ou.md) Vous pouvez déléguer des autorisations à des utilisateurs du domaine managé Azure AD DS pour gérer cette unité d’organisation personnalisée.
1. [Joignez les machines virtuelles][create-join-windows-vm] qui exécutent la ressource/API web back-end au domaine managé Azure AD DS. Créez son compte d’ordinateur dans l’unité d’organisation personnalisée.
1. Créez le compte de service (par exemple, « appsvc ») utilisé pour exécuter l’application web dans l’unité d’organisation personnalisée.

    > [!NOTE]
    > Là encore, le compte d’ordinateur de la machine virtuelle de l’API web et le compte de service de l’application web doivent se trouver dans une unité d’organisation personnalisée dans laquelle vous êtes autorisé à configurer le mécanisme KCD basé sur les ressources. Vous ne pouvez pas configurer le mécanisme KCD basé sur les ressources pour les comptes situés dans les conteneurs intégrés *AAD DC Computers* ou *AAD DC Users*. Cela signifie aussi que vous ne pouvez pas utiliser de comptes d’utilisateurs synchronisés à partir d’Azure AD pour configurer le mécanisme KCD basé sur les ressources. Vous devez créer et utiliser des comptes de service spécifiquement créés dans Azure AD DS.

1. Enfin, configurez le mécanisme KCD basé sur les ressources à l’aide de l’applet de commande PowerShell [Set-ADUser][Set-ADUser]. Sur votre machine virtuelle de gestion jointe au domaine, connectez-vous avec un compte d’utilisateur membre du groupe *Azure AD DC Administrators*, puis exécutez les applets de commande suivantes. Indiquez vos propres noms de service en fonction des besoins :

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le fonctionnement de la délégation dans Active Directory Domain Services, consultez [Vue d’ensemble de la délégation Kerberos contrainte][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
