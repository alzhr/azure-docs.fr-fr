---
title: Migrer Azure Active Directory Domain Services à partir d’un réseau virtuel classique | Microsoft Docs
description: Découvrez comment procéder à la migration d’une instance de domaine managé Azure Active Directory Domain Services existante, depuis le modèle de réseau virtuel classique vers un réseau virtuel basé sur Resource Manager.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: iainfou
ms.openlocfilehash: 8cba2cbf8fcbad1acae8c36892308c3249fc4181
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674916"
---
# <a name="preview---migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Préversion - Migrer Azure Active Directory Domain Services depuis le modèle de réseau virtuel classique vers Resource Manager

Azure Active Directory Domain Services (AD DS) prend en charge un unique déplacement des clients utilisant actuellement le modèle de réseau virtuel classique vers le modèle de réseau virtuel Resource Manager.

Cet article décrit les avantages et les considérations à prendre en compte pour la migration, ainsi que les étapes nécessaires pour réussir la migration d’une instance Azure AD DS existante. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

## <a name="overview-of-the-migration-process"></a>Vue d’ensemble du processus de migration

Le processus de migration prend une instance Azure AD DS existante qui s’exécute dans un réseau virtuel classique et la déplace vers un réseau virtuel Resource Manager existant. La migration est effectuée à l’aide de PowerShell et comporte deux étapes principales d’exécution : la *préparation* et la *migration* .

![Vue d’ensemble du processus de migration d’Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Au cours de l’étape de *préparation*, Azure AD DS effectue une sauvegarde du domaine pour obtenir la capture instantanée la plus récente des utilisateurs, groupes et mots de passe synchronisés sur le domaine managé. La synchronisation est ensuite désactivée, et le service cloud qui héberge le domaine managé Azure AD DS est supprimé. Au cours de la phase de préparation, le domaine managé Azure AD DS ne peut pas authentifier les utilisateurs.

![Phase de préparation de la migration d’Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Dans l’étape de *migration*, les disques virtuels sous-jacents des contrôleurs de domaine du domaine managé Azure AD DS classique sont copiés en vue de créer les machines virtuelles à l’aide du modèle de déploiement Resource Manager. Le domaine managé Azure AD DS est ensuite recréé, ce qui comprend la configuration DNS et LDAPS. La synchronisation avec Azure AD est redémarrée, et les certificats LDAP sont restaurés. Il n’est pas nécessaire de rattacher des machines à un domaine managé Azure AD DS ; elles continuent d’être jointes au domaine managé et à s’exécuter sans modification.

![Migration d’Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Avantages de la migration

Lorsque vous déplacez un domaine managé Azure AD DS à l’aide de ce processus de migration, vous n’êtes pas obligé de rattacher les machines au domaine managé, ou de supprimer l’instance Azure AD DS pour en créer une entièrement nouvelle. Les machines virtuelles restent jointes au domaine managé Azure AD DS à la fin du processus de migration.

À l’issue de la migration, Azure AD DS fournit de nombreuses fonctionnalités qui sont uniquement disponibles pour les domaines utilisant des réseaux virtuels Resource Manager, par exemple :

* Prise en charge de la stratégie de mot de passe affinée
* Protection par verrouillage de compte Active Directory
* Notifications par e-mail des alertes sur le domaine managé AD DS
* Journaux d’audit avec Azure Monitor
* Intégration d’Azure Files
* Intégration de HD Insights

Les domaines managés Azure AD DS utilisant un réseau virtuel Resource Manager vous permettent de rester à jour en profitant des fonctionnalités les plus récentes. La dépréciation de la prise en charge d’Azure AD DS à l’aide des réseaux virtuels classiques est programmée.

## <a name="example-scenarios-for-migration"></a>Exemples de scénarios de migration

Voici quelques scénarios de migration courants d’un domaine managé Azure AD DS.

> [!NOTE]
> Ne convertissez pas le réseau virtuel classique tant que vous n’avez pas vérifié le succès de l’opération de migration. La conversion du réseau virtuel supprime l’option qui permet d’annuler l’opération ou de restaurer le domaine managé Azure AD DS en cas de problème lors des phases de migration et de vérification.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrer Azure AD DS vers un réseau virtuel Resource Manager existant (recommandé)

Le déplacement au préalable d’autres ressources classiques existantes vers un réseau virtuel et un modèle de déploiement Resource Manager constitue un scénario habituel. L’appairage est alors utilisé depuis le réseau virtuel Resource Manager vers le réseau virtuel classique qui continue d’exécuter Azure AD DS. Cette approche permet aux applications et services Resource Manager d’utiliser les fonctionnalités d’authentification et de gestion du domaine managé Azure AD DS dans le réseau virtuel classique. Une fois la migration effectuée, toutes les ressources s’exécuteront à l’aide du réseau virtuel et du modèle de déploiement Resource Manager.

![Migrer Azure AD DS vers un réseau virtuel Resource Manager existant](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Les phases principales impliquées dans cet exemple de scénario de migration comprennent les étapes suivantes :

1. Supprimer les passerelles VPN existantes ou l’appairage de réseaux virtuels qui a été configuré sur le réseau virtuel classique.
1. Migrer le domaine managé Azure AD DS en suivant les étapes décrites dans cet article.
1. Tester et confirmer la réussite de la migration, puis supprimer le réseau virtuel classique.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrer plusieurs ressources y compris Azure AD DS

Dans cet exemple de scénario, vous migrez Azure AD DS et celle d’autres ressources associées, depuis le modèle de déploiement classique vers le modèle de déploiement Resource Manager. Si certaines ressources ont continué de s’exécuter dans le réseau virtuel classique en même temps que le domaine managé Azure AD DS, elles peuvent toutes tirer parti de la migration vers le modèle de déploiement Resource Manager.

![Migrer plusieurs ressources vers le modèle de déploiement Resource Manager](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Les phases principales impliquées dans cet exemple de scénario de migration comprennent les étapes suivantes :

1. Supprimer les passerelles VPN existantes ou l’appairage de réseaux virtuels qui a été configuré sur le réseau virtuel classique.
1. Migrer le domaine managé Azure AD DS en suivant les étapes décrites dans cet article.
1. Configurer l’appairage de réseaux virtuels entre le réseau classique et le réseau Resource Manager.
1. Tester et confirmer la réussite de la migration.
1. [Déplacer des ressources classiques supplémentaires, comme des machines virtuelles][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrer Azure AD DS tout en conservant d’autres ressources sur le réseau virtuel classique

Avec cet exemple de scénario, le temps d’arrêt d’une session est réduit au minimum. Vous migrez uniquement Azure AD DS vers un réseau virtuel Resource Manager, et conservez les ressources existantes sur le réseau virtuel et le modèle de déploiement classiques. Au cours d’une prochaine période de maintenance, vous pouvez migrer des ressources supplémentaires depuis le réseau virtuel et le modèle de déploiement classiques, en fonction de vos besoins.

![Opérer la migration d’Azure AD DS uniquement vers le modèle de déploiement Resource Manager](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Les phases principales impliquées dans cet exemple de scénario de migration comprennent les étapes suivantes :

1. Supprimer les passerelles VPN existantes ou l’appairage de réseaux virtuels qui a été configuré sur le réseau virtuel classique.
1. Migrer le domaine managé Azure AD DS en suivant les étapes décrites dans cet article.
1. Configurer l’appairage des réseaux virtuels entre le réseau classique et le nouveau réseau Resource Manager.
1. Plus tard, [migrer les ressources supplémentaires][migrate-iaas] à partir du réseau virtuel classique, si nécessaire.

## <a name="before-you-begin"></a>Avant de commencer

Certaines considérations relatives à la disponibilité des services d’authentification et de gestion sont à prendre en compte lorsque vous préparez, puis procédez à la migration d’un domaine managé Azure AD DS. Le domaine managé Azure AD DS est inaccessible pendant un certain temps lors de la migration. Les applications et services qui s’appuient sur Azure AD subissent un temps d’arrêt durant la migration.

> [!IMPORTANT]
> Lisez l’intégralité de cet article consacré à la migration, et les consignes associées avant de démarrer le processus de migration. Le processus de migration a une incidence sur la disponibilité à certains moments des contrôleurs de domaine Azure AD DS. Les utilisateurs, les services et les applications ne peuvent pas s’authentifier auprès du domaine managé durant le processus de migration.

### <a name="ip-addresses"></a>Adresses IP

Les adresses IP des contrôleurs de domaine d’un domaine managé Azure AD DS changent après une migration. C’est le cas, par exemple, de l’adresse IP publique du point de terminaison LDAP sécurisé. Les nouvelles adresses IP se trouvent dans la plage d’adresses du nouveau sous-réseau du réseau virtuel Resource Manager.

En cas d’annulation, les adresses IP peuvent changer après le retour à un état antérieur à la migration.

Azure AD DS utilise généralement les deux premières adresses IP disponibles dans la plage d’adresses, mais ce n’est pas systématique. Vous ne pouvez pas actuellement spécifier les adresses IP à utiliser après la migration.

### <a name="downtime"></a>Temps d’arrêt

Le processus de migration implique que les contrôleurs de domaine soient hors connexion pendant un certain temps. Les contrôleurs de domaine sont inaccessibles pendant la migration d’Azure AD DS vers le réseau virtuel et le modèle de déploiement Resource Manager. En moyenne, le temps d’arrêt varie entre 1 et 3 heures. Cette période court entre le moment où les contrôleurs de domaine sont mis hors connexion et le moment où le premier contrôleur de domaine est remis en ligne. Cette moyenne n’inclut pas le temps nécessaire au deuxième contrôleur de domaine pour effectuer la réplication, ni le temps qu’il peut prendre pour procéder à la migration des ressources supplémentaires vers le modèle de déploiement Resource Manager.

### <a name="account-lockout"></a>Verrouillage de compte

Les domaines managés Azure AD DS qui s’exécutent sur les réseaux virtuels classiques n’ont pas de stratégies de verrouillage de compte Active Directory en place. Si les machines virtuelles sont exposées à Internet, les attaquants peuvent utiliser des méthodes de pulvérisation de mot de passe pour forcer le passage jusqu’aux comptes. Il n’existe aucune stratégie de verrouillage de compte permettant d’arrêter ces tentatives. En ce qui concerne les domaines managés Azure AD DS qui utilisent les réseaux virtuels et le modèle de déploiement Resource Manager, les stratégies de verrouillage de compte AD protègent contre ces attaques par pulvérisation de mot de passe.

Par défaut, 5 tentatives de mot de passe incorrect en 2 minutes verrouillent un compte pendant 30 minutes.

Il est impossible de se connecter à compte verrouillé, ce qui peut interférer avec la possibilité de gérer le domaine managé Azure AD DS ou les applications administrées par le compte. Après la migration d’un domaine managé Azure AD DS, les comptes peuvent être confrontés à ce qui ressemble à un verrouillage permanent, suite à des tentatives de connexion répétées qui ont échoué. Voici deux scénarios fréquents survenant après la migration :

* Un compte de service qui utilise un mot de passe expiré.
    * Le compte de service tente à plusieurs reprises de se connecter avec un mot de passe arrivé à expiration, ce qui verrouille le compte. Pour résoudre ce problème, localisez l’application ou la machine virtuelle dotée des informations d’identification expirées et mettez à jour le mot de passe.
* Une entité malveillante utilise des tentatives de connexion par force brute sur des comptes.
    * Lorsque des machines virtuelles sont exposées à Internet, les attaquants essaient souvent des combinaisons courantes de nom d’utilisateur et de mot de passe lorsqu’ils tentent de se connecter. Ces tentatives de connexion répétées qui ont échoué peuvent verrouiller les comptes. Il est déconseillé d’utiliser des comptes administrateur avec des noms génériques, tels que *admin* ou *administrateur* par exemple, si l’on veut réduire le risque de verrouillage des comptes d’administration.
    * Réduisez au minimum le nombre de machines virtuelles exposées à Internet. Vous pouvez utiliser [Azure Bastion (actuellement en préversion)][azure-bastion] pour vous connecter en toute sécurité aux machines virtuelles par le biais du portail Azure.

Si vous pensez que certains comptes puissent être verrouillés après la migration, les étapes finales de la migration expliquent en détail comment activer l’audit ou modifier les paramètres de la stratégie de mot de passe affinée.

### <a name="roll-back-and-restore"></a>Annuler et restaurer

Si la migration échoue, vous pouvez procéder à l’annulation ou à la restauration d’un domaine managé Azure AD DS. L’annulation est une option en libre-service qui permet de retourner immédiatement à l’état du domaine managé précédant la tentative de migration. Les techniciens du support Azure peuvent également en dernier recours restaurer un domaine managé à partir d’une sauvegarde. Pour plus d’informations, consultez [Comment annuler ou restaurer à partir d’une migration ayant échoué](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Restrictions sur les réseaux virtuels disponibles

Certaines restrictions s’appliquent aux réseaux virtuels vers lesquels un domaine managé Azure AD DS peut être migré. Le réseau virtuel Resource Manager de destination doit répondre aux conditions suivantes :

* Le réseau virtuel Resource Manager doit se trouver dans le même abonnement Azure que le réseau virtuel classique où Azure AD DS est actuellement déployé.
* Le réseau virtuel Resource Manager doit se trouver dans la même région que le réseau virtuel classique où Azure AD DS est actuellement déployé.
* Le sous-réseau du réseau virtuel Resource Manager doit posséder au moins 3 à 5 adresses IP disponibles.
* Le sous-réseau du réseau virtuel Resource Manager doit être un sous-réseau dédié pour Azure AD DS ; il ne doit héberger aucune autre charge de travail.

Pour plus d’informations sur les conditions du réseau virtuel, consultez [Considérations relatives à la conception du réseau virtuel et options de configuration][network-considerations].

## <a name="migration-steps"></a>Étapes de la migration

La migration vers le réseau virtuel et le modèle de déploiement Resource Manager se décompose en 5 étapes principales :

| Étape    | Effectuée via  | Durée estimée  | Temps d’arrêt  | Annuler/Restaurer ? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Étape 1 - Mettre à jour et localiser le nouveau réseau virtuel](#update-and-verify-virtual-network-settings) | Portail Azure | 15 minutes | Aucun temps d’arrêt nécessaire | N/A |
| [Étape 2 - Préparer le domaine managé Azure AD DS pour la migration](#prepare-the-managed-domain-for-migration) | PowerShell | 15 à 30 minutes en moyenne | Le temps d’arrêt d’Azure AD DS débute à l’issue de cette commande. | Annulation et restauration disponibles. |
| [Étape 3 - Déplacer le domaine managé Azure AD DS vers un réseau virtuel existant](#migrate-the-managed-domain) | PowerShell | 1 à 3 heures en moyenne | Un contrôleur de domaine est disponible à l’issue de cette commande, le temps d’arrêt s’achève. | En cas d’échec, l’annulation (libre-service) et la restauration sont disponibles. |
| [Étape 4 - Tester et attendre le contrôleur de domaine réplica](#test-and-verify-connectivity-after-the-migration)| PowerShell et portail Azure | 1 heure ou plus, en fonction du nombre de tests | Les deux contrôleurs de domaine sont disponibles et doivent fonctionner normalement. | N/A. Lorsque la migration de la première machine virtuelle s’est correctement déroulée, il n’est plus possible d’annuler ou de restaurer. |
| [Étape 5 - Étapes de configuration facultatives](#optional-post-migration-configuration-steps) | Portail Azure et machines virtuelles | N/A | Aucun temps d’arrêt nécessaire | N/A |

> [!IMPORTANT]
> Pour éviter des temps d’arrêt supplémentaires, lisez l’intégralité de cet article consacré à la migration, et les consignes associées avant de démarrer le processus de migration. Le processus de migration a une incidence pendant un certain temps sur la disponibilité des contrôleurs de domaine Azure AD DS. Les utilisateurs, les services et les applications ne peuvent pas s’authentifier auprès du domaine managé durant le processus de migration.

## <a name="update-and-verify-virtual-network-settings"></a>Mettre à jour et vérifier les paramètres de réseau virtuel

Avant de commencer la migration, procédez aux vérifications et mises à jour initiales suivantes. Ces étapes peuvent s’effectuer à tout moment avant la migration, elles n’affectent pas le fonctionnement du domaine managé Azure AD DS.

1. Mettez à jour votre environnement Azure PowerShell local avec la toute dernière version. Pour réaliser les étapes de migration, vous avez besoin de la version *2.3.2*, au minimum.

    Pour plus d’informations sur la vérification et la mise à jour, consultez [Vue d’ensemble d’Azure PowerShell][azure-powershell].

1. Créez ou choisissez un réseau virtuel Resource Manager existant.

    Assurez-vous que les paramètres réseau ne bloquent pas les ports nécessaires à Azure AD DS. Les ports doivent être ouverts à la fois sur le réseau virtuel classique et le réseau virtuel Resource Manager. Ces paramètres incluent des tables de routage (même si leur utilisation est déconseillée) et des groupes de sécurité réseau.

    Pour afficher les ports obligatoires, consultez [Groupes de sécurité réseau et ports nécessaires][network-ports]. Pour réduire au minimum les problèmes de communication réseau, il est recommandé d’attendre la fin de la migration avant d’appliquer un groupe de sécurité réseau ou une table de routage au réseau virtuel Resource Manager.

    Prenez note du groupe de ressources cible, du réseau virtuel cible et du sous-réseau du réseau virtuel cible. Ces noms de ressources sont utilisés au cours du processus de migration.

1. Vérifiez l’intégrité de votre domaine managé Azure AD DS dans le portail Azure. Si vous remarquez des alertes concernant le domaine managé, résolvez-les avant de démarrer le processus de migration.
1. Éventuellement, si vous envisagez de déplacer d’autres ressources vers le réseau virtuel et le modèle de déploiement Resource Manager, vérifiez que ces ressources peuvent être migrées. Pour plus d’informations, consultez [Migration de ressources IaaS prise en charge par plateforme depuis l’environnement classique vers l’environnement Resource Manager][migrate-iaas].

    > [!NOTE]
    > Ne convertissez pas le réseau virtuel classique en réseau virtuel Resource Manager. Si vous le faites, il n’est plus possible de revenir en arrière ni de restaurer le domaine managé Azure AD DS.

## <a name="prepare-the-managed-domain-for-migration"></a>Préparer le domaine managé pour la migration

Azure PowerShell est utilisé pour préparer le domaine managé Azure AD DS en vue de la migration. Ces étapes incluent la réalisation d’une sauvegarde, l’interruption de la synchronisation et la suppression du service cloud qui héberge Azure AD DS. À la fin de ces préparatifs, Azure AD DS est mis hors connexion pendant un certain temps. Si l’étape de préparation échoue, vous pouvez [revenir à l’état précédent](#roll-back).

Pour préparer le domaine managé Azure AD DS à la migration, effectuez les étapes suivantes :

1. Installez le script `Migrate-Aaads` à partir de [PowerShell Gallery][powershell-script]. Ce script de migration PowerShell est signé numériquement par l’équipe d’ingénierie Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Créez une variable afin de contenir les informations d’identification pour le script de migration à l’aide de l’applet de commande [Get-Credential][get-credential].

    Le compte d’utilisateur que vous spécifiez a besoin des privilèges *d’administrateur général* dans votre locataire Azure AD pour activer Azure AD DS, puis des privilèges de *contributeur* dans votre abonnement Azure pour créer les ressources Azure AD DS voulues.

    Lorsque vous y êtes invité, entrez un compte d’utilisateur et un mot de passe appropriés :

    ```powershell
    $creds = Get-Credential
    ```

1. Exécutez maintenant l’applet de commande `Migrate-Aadds` à l’aide du paramètre *-Prepare*. Fournissez le *-ManagedDomainFqdn* pour votre propre domaine managé Azure AD DS, par exemple *contoso.com* :

    ```powershell
    Migrate-Aadds `
        -Prepare -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrer le domaine managé

Une fois le domaine managé Azure AD DS préparé et sauvegardé, il peut être migré. Cette étape recrée les machines virtuelles du contrôleur de domaine Azure AD Domain Services au moyen du modèle de déploiement Resource Manager. Cette étape peut prendre 1 à 3 heures.

Exécutez l’applet de commande `Migrate-Aadds` à l’aide du paramètre *-Commit*. Indiquez le *-ManagedDomainFqdn* de votre propre domaine managé Azure AD DS préparé à la section précédente, par exemple *contoso.com* :

Spécifiez le groupe de ressources cible qui contient le réseau virtuel vers lequel vous souhaitez faire migrer Azure AD DS, par exemple *myResourceGroup*. Indiquez le réseau virtuel cible, par exemple *myVnet*, et le sous-réseau, par exemple *DomainServices*.

À l’issue de l’exécution de cette commande, vous ne pouvez plus revenir en arrière :

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Après la validation du script, le domaine managé est préparé pour la migration ; entrez *Y* pour démarrer le processus de migration.

> [!IMPORTANT]
> Ne convertissez pas le réseau virtuel classique en réseau virtuel Resource Manager pendant le processus de migration. Si vous convertissez le réseau virtuel, vous ne pouvez plus annuler l’opération ni restaurer le domaine managé Azure AD DS, car le réseau virtuel d’origine n’existe plus.

Toutes les deux minutes pendant le processus de migration, un indicateur de progression signale l’état actuel, comme illustré dans l’exemple de sortie suivant :

![Indicateur de progression de la migration d’Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Le processus de migration continue de s’exécuter, même si vous fermez la fenêtre du script PowerShell. Dans le portail Azure, l’état du domaine managé est indiqué comme étant en *Migration*.

Une fois la migration terminée, vous pouvez afficher l’adresse IP de votre premier contrôleur de domaine dans le portail Azure ou via Azure PowerShell. Une durée estimée pour le second contrôleur de domaine disponible s’affiche également.

Si vous le souhaitez, à ce stade vous pouvez déplacer d’autres ressources existantes, depuis le réseau virtuel classique et le modèle de déploiement associé. Autrement, vous pouvez conserver les ressources sur le modèle de déploiement classique et appairer les réseaux virtuels entre eux à l’issue de la migration Azure AD DS.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Tester et vérifier la connectivité après la migration

Le déploiement du deuxième contrôleur de domaine et sa disponibilité opérationnelle dans le domaine managé Azure AD DS peut prendre un certain temps.

Avec le modèle de déploiement Resource Manager, les ressources réseau du domaine managé Azure AD DS sont affichées dans le portail Azure ou dans Azure PowerShell. Pour en savoir plus sur les ressources réseau et leur fonction, consultez [Ressources réseau utilisées par Azure AD DS][network-resources].

Lorsqu’au moins un contrôleur de domaine est disponible, procédez aux étapes de configuration suivantes pour établir la connectivité réseau avec les machines virtuelles :

* **Mettre à jour les paramètres de serveur DNS** - Pour permettre à d’autres ressources sur le réseau virtuel Resource Manager de résoudre et d’utiliser le domaine managé Azure AD DS, mettez à jour les paramètres DNS avec les adresses IP des nouveaux contrôleurs de domaine. La portail Azure peut configurer automatiquement ces paramètres pour vous. Pour en savoir plus sur la configuration du réseau virtuel Resource Manager, consultez [Mettre à jour les paramètres DNS pour le réseau virtuel Azure][update-dns].
* **Redémarrer les machines virtuelles jointes à un domaine** - Comme les adresses IP de serveur DNS pour les contrôleurs de domaine Azure AD DS changent, redémarrez toutes les machines virtuelles jointes au domaine afin qu’elles utilisent ensuite les nouveaux paramètres de serveur DNS. Si des applications ou des machines virtuelles disposent de paramètres DNS configurés manuellement, mettez-les à jour manuellement en utilisant les nouvelles adresses IP de serveur DNS des contrôleurs de domaine, qui sont affichées dans le portail Azure.

À présent, testez la connexion réseau virtuel et la résolution de noms. Sur une machine virtuelle connectée au réseau virtuel Resource Manager, ou appairée à celui-ci, essayez les tests de communication réseau suivants :

1. Vérifiez si vous pouvez exécuter une commande ping sur l’adresse IP de l’un des contrôleurs de domaine, par exemple `ping 10.1.0.4`.
    * Les adresses IP des contrôleurs de domaine sont affichées sur la page **Propriétés** du domaine managé Azure AD DS, dans le portail Azure.
1. Vérifiez la résolution de noms du domaine managé, par exemple `nslookup contoso.com`.
    * Spécifiez le nom DNS de votre propre domaine managé Azure AD DS pour vérifier que les paramètres DNS sont corrects et résolus.

Le deuxième contrôleur de domaine doit normalement être disponible 1 à 2 heures après l’exécution de l’applet de commande de migration. Pour vérifier si le second contrôleur de domaine est disponible, consultez la page **Propriétés** du domaine managé Azure AD DS dans le portail Azure. Si deux adresses IP sont affichées, le deuxième contrôleur de domaine est prêt.

## <a name="optional-post-migration-configuration-steps"></a>Étapes facultatives de la configuration postérieure à la migration

Une fois le processus de migration terminé, il reste quelques étapes facultatives de configuration, dont l’activation des journaux d’audit ou des notifications par e-mail, et la mise à jour de la stratégie de mot de passe affinée.

#### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>S’abonner aux journaux d’audit avec Azure Monitor

Azure AD DS expose les journaux d’audit pour vous aider à résoudre des problèmes et à afficher les événements sur les contrôleurs de domaine. Pour plus d’informations, consultez [Activer et utiliser les journaux d’audit][security-audits].

Vous pouvez utiliser des modèles pour superviser les informations importantes qui sont exposées dans les journaux. Par exemple, le modèle de classeur du journal d’audit peut superviser les éventuels verrouillages de compte sur le domaine managé Azure AD DS.

#### <a name="configure-azure-ad-domain-services-email-notifications"></a>Configurer les notifications par e-mail pour Azure Active Directory Domain Services

Afin d’être averti lorsqu’un problème est détecté sur le domaine managé Azure AD DS, mettez à jour les paramètres de notification par e-mail dans le portail Azure. Pour plus d’informations, consultez [Configurer les paramètres de notification][notifications].

#### <a name="update-fine-grained-password-policy"></a>Mettre à jour la stratégie de mot de passe affinée

Au besoin, vous pouvez mettre à jour la stratégie de mot de passe affinée pour qu’elle soit moins restrictive que la configuration par défaut. Vous pouvez utiliser les journaux d’audit pour déterminer si un paramètre moins restrictif est pertinent, et configurer ensuite la stratégie si nécessaire. Utilisez les étapes principales suivantes pour examiner et mettre à jour les paramètres de stratégie des comptes qui sont verrouillés de façon répétée après la migration :

1. [Configurez la stratégie de mot de passe][password-policy] pour imposer moins de restrictions sur le domaine managé Azure AD DS, puis observez les événements dans les journaux d’audit.
1. Si des comptes de service utilisent des mots de passe arrivés à expiration, tels qu’ils sont identifiés dans les journaux d’audit, mettez à jour ces comptes avec le mot de passe correct.
1. Si la machine virtuelle est exposée à Internet, passez en revue les noms de comptes génériques, comme *administrateur*, *utilisateur* ou *invité*, qui présentent des tentatives de connexion à risque élevé. Dans la mesure du possible, mettez à jour ces machines virtuelles afin d’utiliser des noms de compte moins génériques.
1. Utilisez un suivi réseau sur la machine virtuelle pour localiser la source des attaques et bloquer ces adresses IP capables de tenter des connexions.
1. En cas de problème de verrouillage minime, mettez à jour la stratégie de mot de passe affinée pour qu’elle soit aussi restrictive que nécessaire.

#### <a name="creating-a-network-security-group"></a>Création d’un groupe de sécurité réseau

Azure AD DS a besoin d’un groupe de sécurité réseau pour sécuriser les ports nécessaires au domaine managé et bloquer tout autre trafic entrant. Ce groupe de sécurité réseau agit comme une couche de protection supplémentaire pour verrouiller l’accès au domaine managé ; il n’est pas créé automatiquement. Pour créer le groupe de sécurité réseau et ouvrir les ports nécessaires, conformez-vous aux étapes suivantes :

1. Dans le portail Azure, sélectionnez votre ressource Azure AD DS. Dans la page de la vue d’ensemble, un bouton est affiché pour créer un groupe de sécurité réseau si aucun n’est associé à Azure Active Directory Domain Services.
1. Si vous utilisez le protocole LDAP sécurisé, ajoutez une règle au groupe de sécurité réseau afin d’autoriser le trafic entrant pour le port *TCP* *636*. Pour plus d’informations, consultez [Configurer le protocole LDAP sécurisé][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Annuler et restaurer à partir de la migration

### <a name="roll-back"></a>Annuler l’opération

En cas d’erreur survenant lors de l’exécution de l’applet de commande PowerShell pour préparer la migration à l’étape 2, ou pour la migration elle-même à l’étape 3, le domaine managé Azure AD DS peut revenir à la configuration d’origine. Cette opération d’annulation nécessite l’existence du réseau virtuel classique d’origine. Notez que les adresses IP peuvent toujours changer après la restauration.

Exécutez l’applet de commande `Migrate-Aadds` à l’aide du paramètre *-Abort*. Fournissez le *-ManagedDomainFqdn* de votre propre domaine managé Azure AD DS préparé dans une des sections précédentes, par exemple *contoso.com* :

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -Credentials $creds
```

### <a name="restore"></a>Restore

En dernier recours, Azure Active Directory Domain Services peut être restauré à partir de la dernière sauvegarde disponible. Une sauvegarde est effectuée à l’étape 1 de la migration, pour s’assurer que la sauvegarde la plus récente est disponible. Cette sauvegarde est stockée pendant 30 jours.

Pour restaurer le domaine managé Azure AD DS à partir d’une sauvegarde, [ouvrez un ticket de cas de support par l’intermédiaire du portail Azure][azure-support]. Indiquez votre ID d’annuaire, le nom de domaine et la raison de la restauration. Le processus de prise en charge et de restauration peut demander plusieurs jours pour s’accomplir.

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes après avoir effectué la migration vers le modèle de déploiement Resource Manager, consultez ces quelques rubriques de la résolution des problèmes courants :

* [Résoudre les problèmes de jonction à un domaine][troubleshoot-domain-join]
* [Résoudre les problèmes de verrouillage de compte][troubleshoot-account-lockout]
* [Résoudre les problèmes de connexion de compte][troubleshoot-sign-in]
* [Résoudre les problèmes de connectivité LDAP sécurisé][tshoot-ldaps]

## <a name="next-steps"></a>Étapes suivantes

Une fois la migration de votre domaine managé Azure AD DS effectuée vers le modèle de déploiement Resource Manager, [créez et joignez une machine virtuelle Windows au domaine][join-windows], puis [installez des outils de gestion][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/1.0
