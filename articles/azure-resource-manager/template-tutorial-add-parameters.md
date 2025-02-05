---
title: Tutoriel - Ajouter des paramètres au modèle Azure Resource Manager
description: Ajoutez des paramètres à votre modèle Azure Resource Manager pour le rendre réutilisable.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f5e631994223d6362512ed0ddc89d1d3c884fbd4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001501"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Didacticiel : Ajouter des paramètres à votre modèle Resource Manager

Dans le [tutoriel précédent](template-tutorial-add-resource.md) vous avez appris à ajouter un compte de stockage au modèle, et à le déployer. Dans ce tutoriel, vous allez découvrir comment améliorer le modèle en ajoutant des paramètres. Ce tutoriel dure environ **14 minutes**.

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les ressources](template-tutorial-add-resource.md), mais il n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Outils Resource Manager, et au choix d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez les [outils de modèle](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examiner votre modèle

À la fin du précédent tutoriel, votre modèle présentait le code JSON suivant :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

Vous avez peut-être remarqué d’ailleurs que ce modèle a un problème. Le nom du compte de stockage est codé en dur. Vous pouvez utiliser ce modèle uniquement pour déployer le même compte de stockage à chaque fois. Pour déployer un compte de stockage avec un nom différent, vous devez créer un nouveau modèle, ce qui n’est évidemment pas un moyen pratique d’automatiser vos déploiements.

## <a name="make-your-template-reusable"></a>Rendre votre modèle réutilisable

Vous pouvez réutiliser votre modèle en ajoutant un paramètre que vous définissez pour transmettre un nom de compte de stockage. Le code JSON mis en évidence dans l’exemple suivant montre ce qui a changé dans votre modèle. Le paramètre **storageName** est identifié en tant que chaîne. La longueur maximale est définie sur 24 caractères pour empêcher les noms trop longs.

Copiez l’intégralité du fichier et remplacez votre modèle par son contenu.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-the-template"></a>Déployer le modèle

Déployons le modèle. L’exemple suivant déploie le modèle avec Azure CLI ou PowerShell. Notez que vous fournissez le nom du compte de stockage en tant que valeur dans la commande de déploiement. En guise de nom de compte de stockage, indiquez le nom que vous avez utilisé dans le tutoriel précédent.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](template-tutorial-create-first-template.md#create-resource-group). L’exemple suppose que vous avez défini la variable **templateFile** sur le chemin du fichier de modèle, comme indiqué dans le [premier tutoriel](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Comprendre les mises à jour des ressources

Dans la section précédente, vous avez déployé un compte de stockage portant le même nom que celui que vous avez créé auparavant. Vous vous demandez peut-être de quelle manière le redéploiement se répercute sur la ressource.

Si la ressource existe déjà et qu’aucune modification n’est détectée dans les propriétés, aucune action n’est effectuée. Si la ressource existe déjà et qu’une propriété a été modifiée, la ressource est mise à jour. Si la ressource n’existe pas, elle est créée.

Cette façon de gérer les mises à jour signifie que votre modèle peut inclure toutes les ressources dont vous avez besoin pour une solution Azure. Vous pouvez redéployer le modèle en toute sécurité, sachant que les ressources seront modifiées ou créées uniquement lorsque cela sera nécessaire. Par exemple, si vous avez ajouté des fichiers à votre compte de stockage, vous pouvez redéployer ce compte sans perdre ces fichiers.

## <a name="customize-by-environment"></a>Personnaliser par environnement

Les paramètres vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier. Par exemple, vous pouvez passer des valeurs différentes selon que vous effectuez un déploiement dans un environnement de développement, de test et de production.

Le modèle précédent a toujours déployé un compte de stockage Standard_LRS. Vous pouvez souhaiter avoir le choix de déployer différentes références SKU en fonction de l’environnement. L’exemple suivant montre les modifications apportées pour ajouter un paramètre de référence (SKU). Copiez l’intégralité du fichier et collez-le à la place de votre modèle.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

Le paramètre **storageSKU** possède une valeur par défaut. Cette valeur est utilisée quand aucune valeur n’est spécifiée pendant le déploiement. Il présente également une liste de valeurs autorisées. Ces valeurs correspondent aux valeurs nécessaires à la création d’un compte de stockage. Vous ne souhaitez pas que les utilisateurs de votre modèle transmettent des références SKU qui ne fonctionnent pas.

## <a name="redeploy-the-template"></a>Redéployer le modèle

Vous êtes prêt à déployer de nouveau. La référence SKU par défaut étant définie sur **Standard_LRS**, vous n’avez pas besoin de fournir de valeur pour ce paramètre.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

Pour voir la flexibilité de votre modèle, procédez de nouveau au redéploiement. Cette fois-ci, définissez le paramètre SKU sur **Standard_GRS**. Vous pouvez soit passer un nouveau nom pour créer un compte de stockage différent, soit utiliser le même nom pour mettre à jour votre compte de stockage existant. Les deux options fonctionnent.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Pour finir, nous allons exécuter un test supplémentaire et voir ce qui se passe lorsque vous transmettez une référence qui ne fait pas partie des valeurs autorisées. Dans ce cas, nous testons le scénario dans lequel un utilisateur de votre modèle pense que **basic** fait partie des références (SKU).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

La commande échoue immédiatement avec un message d’erreur précisant les valeurs autorisées. Resource Manager identifie l’erreur avant le démarrage du déploiement.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez amélioré le modèle créé dans le [premier tutoriel](template-tutorial-create-first-template.md) en ajoutant des paramètres. Dans le tutoriel suivant, vous allez découvrir les fonctions de modèle.

> [!div class="nextstepaction"]
> [Ajouter des fonctions de modèle](template-tutorial-add-functions.md)
