---
title: Configurer Live Data avec Azure Monitor pour conteneurs (préversion) | Microsoft Docs
description: Cet article décrit la configuration de la vue en temps réel des événements et des journaux de conteneur (stdout/stderr), sans l’utilisation de kubectl, avec Azure Monitor pour conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2019
ms.author: magoedte
ms.openlocfilehash: 596c5ad378d471c6c98616a48f44e96c365ee0bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510754"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Comment configurer la fonctionnalité Live Data (préversion)

Si vous voulez voir Live Data (préversion) avec Azure Monitor pour conteneurs à partir de clusters Azure Kubernetes Service (AKS), vous devez configurer l’authentification pour accorder l’autorisation d’accéder à vos données Kubernetes. Cette configuration de sécurité autorise l’accès en temps réel à vos données via l’API Kubernetes, directement dans le portail Azure.

Cette fonctionnalité prend en charge trois méthodes différentes de contrôle d’accès aux journaux, événements et métriques :

- AKS sans autorisation Kubernetes RBAC activée
- AKS activé avec autorisation Kubernetes RBAC
- AKS activé avec authentification unique SAML Azure Active Directory (AD)

Ces instructions demandent un accès administrateur à votre cluster Kubernetes et, si vous configurez en vue d’utiliser Azure Active Directory (AD) pour l’authentification utilisateur, l’accès administrateur à Azure AD.  

Cet article explique comment configurer l’authentification pour contrôler l’accès à la fonctionnalité Live Data (préversion) à partir du cluster :

- Cluster AKS prenant en charge le contrôle d’accès en fonction du rôle (RBAC)
- Cluster AKS intégré à Azure Active Directory 

>[!NOTE]
>Les clusters AKS activés en tant que [clusters privés](https://azure.microsoft.com/updates/aks-private-cluster/) ne sont pas pris en charge par cette fonctionnalité. Cette fonctionnalité repose sur l’accès direct à l’API Kubernetes via un serveur proxy depuis votre navigateur. L’activation de la sécurité réseau pour bloquer l’API Kubernetes depuis ce proxy bloque ce trafic. 

>[!NOTE]
>Cette fonctionnalité est disponible dans toutes les régions Azure, y compris Azure - Chine. Elle n’est actuellement pas disponible dans la région Azure - Gouvernement des États-Unis.

## <a name="authentication-model"></a>Modèle d’authentification

La fonctionnalité Live Data (préversion) utilise l’API Kubernetes, et est identique à l’outil de ligne de commande `kubectl`. Les points de terminaison de l’API Kubernetes se servent d’un certificat auto-signé, que votre navigateur ne peut pas valider. Cette fonctionnalité utilise un proxy interne pour valider le certificat auprès du service AKS, garantissant ainsi que le trafic est approuvé.

Le portail Azure vous invite à valider vos informations d’identification de connexion à un cluster Azure Active Directory, et vous redirige vers la configuration de l’inscription cliente lors de la création du cluster (et reconfigurée dans cet article). Ce comportement est similaire au processus d’authentification exigé par `kubectl`. 

>[!NOTE]
>L’autorisation d’accès à votre cluster est managée par Kubernetes et le modèle de sécurité avec lequel elle est configurée. Les utilisateurs accédant à cette fonctionnalité nécessitent l’autorisation de télécharger la configuration Kubernetes (*kubeconfig*), ce qui équivaut à exécuter `az aks get-credentials -n {your cluster name} -g {your resource group}`. Ce fichier de configuration contient le jeton d’autorisation et d’authentification du **Rôle utilisateur de cluster Azure Kubernetes Service**, dans le cas où RBAC Azure est activé avec des clusters AKS sans autorisation RBAC activée. Il contient des informations sur Azure AD et des détails de l’inscription cliente, lorsque AKS est activé avec l’authentification unique basée sur SAML Azure Active Directory (AD).

>[!IMPORTANT]
>L’utilisateur de cette fonctionnalité nécessite le [Rôle utilisateur de cluster Azure Kubernetes](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) sur le cluster pour télécharger le fichier `kubeconfig` et utiliser cette fonctionnalité. Les utilisateurs n’ont **pas** besoin d’un accès contributeur au cluster pour utiliser cette fonctionnalité. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes sans RBAC activé

Si votre cluster Kubernetes n’est pas configuré avec l’autorisation Kubernetes RBAC ni intégré dans l’authentification unique Azure AD, il est inutile de suivre ces étapes. Cela est dû au fait que vous disposez d’autorisations d’administration par défaut dans une configuration non RBAC.

## <a name="configure-kubernetes-rbac-authentication"></a>Configurer l’authentification RBAC de Kubernetes

Lorsque vous activez l’autorisation RBAC de Kubernetes, deux utilisateurs sont concernés, **clusterUser** et **clusterAdmin**, pour accéder à l’API Kubernetes. Cela revient à exécuter `az aks get-credentials -n {cluster_name} -g {rg_name}` sans l’option d’administration. Autrement dit, **clusterUser** doit être autorisé à accéder aux points de terminaison dans l’API Kubernetes.

Les exemples d’étapes suivants montrent comment configurer la liaison de rôle de cluster à partir de ce modèle de configuration yaml.

1. Copiez et collez le fichier yaml, puis enregistrez-le sous le nom LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. Pour mettre à jour votre configuration, exécutez la commande suivante : `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Si vous avez appliqué une version précédente du fichier `LogReaderRBAC.yaml` à votre cluster, mettez-la à jour en copiant et en collant le nouveau code indiqué à l’étape 1 ci-dessus, puis exécutez la commande mentionnée à l’étape 2 pour l’appliquer à votre cluster.

## <a name="configure-ad-integrated-authentication"></a>Configurer l’authentification intégrée à Azure AD 

Un cluster AKS configuré en vue d’utiliser Azure Active Directory (Azure AD) pour l’authentification utilisateur se sert des informations d’identification de connexion de la personne qui accède à cette fonctionnalité. Dans cette configuration, vous vous connectez à un cluster AKS en utilisant votre jeton d’authentification Azure AD.

L’inscription cliente Azure AD doit être reconfigurée pour permettre au portail Azure de rediriger les pages d’autorisation en tant qu’URL de redirection approuvée. Les utilisateurs provenant d’Azure AD bénéficient ensuite d’un accès direct aux mêmes points de terminaison de l’API Kubernetes via **ClusterRoles** et **ClusterRoleBindings**. 

Pour plus d’informations sur la configuration de la sécurité avancée dans Kubernetes, consultez la [documentation de Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Si vous créez un cluster prenant en charge RBAC, consultez [Intégrer Azure Active Directory à Azure Kubernetes Service](../../aks/azure-ad-integration.md) et suivez les étapes de configuration de l’authentification Azure AD. Pendant les étapes de création de l’application cliente, une remarque dans cette section met en évidence les deux URL de redirection que vous devez créer pour Azure Monitor pour conteneurs. 

### <a name="client-registration-reconfiguration"></a>Reconfiguration de l’inscription cliente

1. Localisez l’inscription cliente de votre cluster Kubernetes dans Azure AD sous **Azure Active Directory > Inscriptions d’applications** du portail Azure.

2. Sélectionnez **Authentification** dans le volet de gauche. 

3. Ajoutez deux URL de redirection à cette liste, en tant que types d’application **web**. La première valeur de l’URL de base doit être `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` et la deuxième valeur de l’URL de base doit être `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Si vous utilisez cette fonctionnalité dans Azure Chine, la première valeur de l’URL de base doit être `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`, et la seconde `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`. 
    
4. Après avoir inscrit les URL de redirection, sous **Paramètres avancés**, sélectionnez les options **Jetons d’accès** et **Jetons d’ID**, puis enregistrez vos modifications.

>[!NOTE]
>La configuration de l’authentification avec Azure Active Directory pour l’authentification unique est possible seulement pendant le déploiement initial d’un nouveau cluster AKS. Vous ne pouvez pas configurer l’authentification unique pour un cluster AKS déjà déployé.
  
>[!IMPORTANT]
>Si vous avez reconfiguré Azure AD pour l’authentification utilisateur à l’aide de l’URI mis à jour, effacez le cache de votre navigateur pour vous assurer que le jeton d’authentification mis à jour est téléchargé et appliqué.

## <a name="grant-permission"></a>Accorder l’autorisation

Chaque compte Azure AD doit disposer de l’autorisation d’accès à la fonctionnalité Live Data (préversion) sur les API appropriées dans Kubernetes. Les étapes de cet octroi au compte Azure Active Directory sont similaires à celles décrites dans la section [Authentification RBAC de Kubernetes](#configure-kubernetes-rbac-authentication). Avant d’appliquer le modèle de configuration yaml à votre cluster, remplacez **clusterUser** sous **ClusterRoleBinding** par l’utilisateur souhaité. 

>[!IMPORTANT]
>Si l’utilisateur pour lequel vous accordez la liaison RBAC figure dans le même locataire Azure AD, attribuez les autorisations en fonction de userPrincipalName. Si l’utilisateur se trouve dans un autre locataire Azure AD, recherchez et utilisez la propriété objectId.

Pour obtenir de l’aide supplémentaire lors de la configuration de **ClusterRoleBinding** de votre cluster AKS, consultez [Créer une liaison RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Étapes suivantes

L’authentification étant configurée, vous pouvez désormais afficher les [métriques](container-insights-livedata-metrics.md), les [déploiements](container-insights-livedata-deployments.md) ainsi que les [événements et journaux](container-insights-livedata-overview.md) en temps réel depuis votre cluster.