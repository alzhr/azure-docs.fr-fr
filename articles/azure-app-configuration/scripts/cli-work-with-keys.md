---
title: Exemple de script Azure CLI - Utiliser des clés-valeurs dans un magasin Azure App Configuration | Microsoft Docs
description: Fournit des informations sur l’utilisation de clés-valeurs dans un magasin Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: d89fa4c067e511e6210e8c1473bf1856297fc1de
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904076"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Utiliser des clés-valeurs dans un magasin Azure App Configuration

Cet exemple de script crée une clé-valeur dans un magasin Azure App Configuration liste toutes les clés-valeurs existantes, met à jour la clé-valeur nouvellement créée, et enfin, la supprime.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

Vous devez d’abord installer l’extension CLI d’Azure App Configuration en exécutant la commande suivante :

        az extension add -n appconfig

## <a name="sample-script"></a>Exemple de script

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour effectuer des opérations sur des clés-valeurs dans un magasin de configuration d’application. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig) | Crée ou met à jour une clé-valeur. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig) | Liste les clés-valeurs d’un magasin de configuration d’application. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig) | Supprime une clé-valeur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts d’interface de ligne de commande App Configuration dans la [documentation Azure App Configuration](../cli-samples.md).
