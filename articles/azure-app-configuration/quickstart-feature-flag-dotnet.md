---
title: Démarrage rapide pour l’ajout d’indicateurs de fonctionnalités à des applications .NET Framework | Microsoft Docs | Microsoft Docs
description: Guide de démarrage rapide pour l’ajout d’indicateurs de fonctionnalités à des applications .NET Framework et leur gestion dans Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: cba0a9a003b53eeb102e14c00cb91f344ebbe3d6
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821876"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Démarrage rapide : Ajouter des indicateurs de fonctionnalités à une application .NET Framework

Dans ce guide de démarrage rapide, vous incorporez Azure App Configuration à une application .NET Framework pour créer une implémentation de bout en bout de la gestion des fonctionnalités. Vous pouvez utiliser le service App Configuration pour stocker de manière centralisée tous vos indicateurs de fonctionnalités et contrôler leur état. 

Les bibliothèques de gestion des fonctionnalités .NET étendent le framework avec une prise en charge complète des indicateurs de fonctionnalités. Ces bibliothèques sont basées sur le système de configuration de .NET. Elles s’intègrent facilement à App Configuration via son fournisseur de configuration .NET.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Créer un magasin de configuration d’application

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Créer une application console .NET

1. Démarrez Visual Studio, puis sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Create un nouveau projet**, filtrez sur le type de projet **Console** et cliquez sur **Application console (.NET Framework)** . Cliquez sur **Suivant**.

1. Dans **Configurer votre nouveau projet** , entrez un nom de projet. Sous **Framework**, sélectionnez **.NET Framework 4.7.1** ou version ultérieure. Cliquez sur **Créer**.

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin de configuration d’application

1. Cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, recherchez et ajoutez les packages NuGet suivants à votre projet. Si vous ne les trouvez pas, cochez la case **Inclure les préversions**.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Ouvrez *Program.cs* et ajoutez les instructions suivantes :

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Mettez à jour la méthode `Main` pour vous connecter à App Configuration, en spécifiant l’option `UseFeatureFlags` pour que les indicateurs de fonctionnalité soient récupérés. Affichez ensuite un message si l’indicateur de fonctionnalité `Beta` est activé.

    ```csharp
        static void Main(string[] args)
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options => 
                { 
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags(); 
                }).Build();
            
            IServiceCollection services = new ServiceCollection(); 
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement(); 
            IFeatureManager featureManager = services.BuildServiceProvider().GetRequiredService<IFeatureManager>(); 
            
            if (featureManager.IsEnabled("Beta")) 
            { 
                Console.WriteLine("Welcome to the beta"); 
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la chaîne de connexion de votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Redémarrez Visual Studio pour que la modification soit prise en compte. 

1. Appuyez sur Ctrl + F5 pour générer et exécuter l’application console.

    ![Application avec indicateur de fonctionnalité activé](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un indicateur de fonctionnalité dans App Configuration et vous l’avez utilisé avec une application console .NET Framework. Pour découvrir comment mettre à jour dynamiquement les indicateurs de fonctionnalité et d’autres valeurs de configuration sans redémarrer l’application, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Activer la configuration dynamique](./enable-dynamic-configuration-dotnet.md)