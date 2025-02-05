---
title: Exemple de script Azure PowerShell - Ajouter un certificat d’application à un cluster | Microsoft Docs
description: Exemple de script Azure PowerShell - Ajouter un certificat d’application à un cluster Service Fabric
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 04cd13efd198f0a4875c0ede525d10cf45220989
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901503"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Ajouter un certificat d’application à un cluster Service Fabric

Cet exemple de script explique comment créer un certificat dans Key Vault, puis comment le déployer sur l’un des groupes de machines virtuelles identiques où s’exécute votre cluster. Ce scénario n’utilise pas directement Service Fabric, il dépend plutôt de Key Vault et des groupes de machines virtuelles identiques.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure. 

## <a name="create-a-certificate-in-key-vault"></a>Créer un certificat dans Key Vault

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Mettre à jour le profil des groupes de machines virtuelles identiques avec un certificat

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore
$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>Mettre à jour le groupe de machines virtuelles identiques
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Si vous souhaitez que le certificat soit placé sur plusieurs types de nœud de votre cluster, les deuxième et troisième parties de ce script doivent être répétées pour chaque type de nœud ayant le certificat.

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Crée une stratégie en mémoire représentant le certificat |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Déploie la stratégie sur Key Vault |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Crée une configuration en mémoire représentant le certificat dans une machine virtuelle |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Ajoute le certificat à la définition en mémoire du groupe de machines virtuelles identiques |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Déploie la nouvelle définition du groupe de machines virtuelles identiques |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).
