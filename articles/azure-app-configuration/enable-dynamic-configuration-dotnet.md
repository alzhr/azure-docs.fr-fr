---
title: Tutoriel sur l’utilisation de la configuration dynamique d’Azure App Configuration dans une application .NET Framework | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7e28cdacce8eac4774683013ae1c30ca34ebfaad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821626"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Didacticiel : Utiliser la configuration dynamique dans une application .NET Framework

La bibliothèque cliente .NET App Configuration permet d’effectuer la mise à jour à la demande d’un ensemble de paramètres de configuration, sans entraîner le redémarrage de l’application. Vous pouvez implémenter cette configuration en obtenant d’abord une instance de `IConfigurationRefresher` parmi les options du fournisseur de configuration, puis en appelant `Refresh` sur cette instance, à n’importe quel endroit de votre code.

Pour maintenir les paramètres à jour et éviter trop d’appels au magasin de configuration, un cache est utilisé pour chaque paramètre. Tant que la valeur mise en cache d’un paramètre n’a pas expiré, l’opération d’actualisation ne met pas à jour la valeur, même si celle-ci a été modifiée dans le magasin de configuration. Pour chaque requête, le délai d’expiration par défaut est de 30 secondes. Toutefois, vous pouvez le modifier selon vos besoins.

Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application mentionnée dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application .NET Framework avec App Configuration](./quickstart-dotnet-app.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer votre application afin de mettre à jour sa configuration à la demande à l’aide d’un magasin de configuration d’application.
> * Injecter la configuration la plus récente dans les contrôleurs de votre application.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 ou ultérieur](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Créer un magasin de configuration d’application

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Sélectionnez **Explorateur de configurations** >  **+ Créer** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

## <a name="create-a-net-console-app"></a>Créer une application console .NET

1. Démarrez Visual Studio, puis sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Create un nouveau projet**, filtrez sur le type de projet **Console** et cliquez sur **Application console (.NET Framework)** . Cliquez sur **Suivant**.

1. Dans **Configurer votre nouveau projet** , entrez un nom de projet. Sous **Framework**, sélectionnez **.NET Framework 4.7.1** ou version ultérieure. Cliquez sur **Créer**.

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration
1. Cliquez avec le bouton droit sur votre projet, puis sélectionnez **Gérer les packages NuGet**. Sous l’onglet **Parcourir**, recherchez le package NuGet *Microsoft.Extensions.Configuration.AzureAppConfiguration* et ajoutez-le à votre projet. Si vous ne le trouvez pas, cochez la case **Inclure la préversion**.

1. Ouvrez *Program.cs*, puis ajoutez une référence au fournisseur App Configuration .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Ajoutez deux variables pour stocker les objets liés à la configuration.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Mettez à jour la méthode `Main` pour vous connecter à App Configuration avec les options d’actualisation spécifiées.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    La méthode `ConfigureRefresh` permet de spécifier les paramètres utilisés pour mettre à jour les données de configuration à l’aide du magasin de configuration d’application, lorsqu’une opération d’actualisation est déclenchée. Vous pouvez récupérer une instance de `IConfigurationRefresher` en appelant la méthode `GetRefresher` dans les options fournies à la méthode `AddAzureAppConfiguration`. Vous pouvez aussi utiliser la méthode `Refresh` de cette instance pour déclencher une opération d’actualisation n’importe où dans votre code.

    > [!NOTE]
    > Pour un paramètre de configuration, le délai d’expiration du cache par défaut est de 30 secondes. Toutefois, vous pouvez le modifier en appelant la méthode `SetCacheExpiration` de l’initialiseur d’options qui est passé en tant qu’argument à la méthode `ConfigureRefresh`.

1. Ajoutez une méthode appelée `PrintMessage()` qui déclenche une actualisation manuelle des données de configuration à partir d’App Configuration.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Redémarrez Visual Studio pour que la modification soit prise en compte. 

1. Appuyez sur Ctrl + F5 pour générer et exécuter l’application console.

    ![Lancement local de l’application](./media/dotnet-app-run.png)

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance de magasin de configuration d’application que vous avez créée dans le démarrage rapide.

1. Sélectionnez **Explorateur de configuration**, puis mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Données issues d’Azure App Configuration - Mise à jour |

1. De retour dans l’application en cours d’exécution, appuyez sur la touche Entrée pour déclencher une actualisation et afficher la valeur mise à jour dans l’invite de commandes ou dans la fenêtre PowerShell.

    ![Actualisation locale de l’application](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Étant donné que le délai d’expiration du cache a été défini sur 10 secondes à l’aide de la méthode `SetCacheExpiration` lors de la spécification de la configuration de l’opération d’actualisation, la valeur du paramètre de configuration ne sera actualisée que si 10 secondes se sont écoulées depuis la dernière actualisation de ce paramètre.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une identité de service managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour savoir comment ajouter une identité de service gérée par Azure qui simplifie l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
