---
title: Configurer une appliance pour l’évaluation de serveurs physiques avec Azure Migrate Server Assessment
description: Explique comment configurer une appliance pour l’évaluation de serveurs physiques à l’aide d’Azure Migrate Server Assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/11/2019
ms.author: raynew
ms.openlocfilehash: a3212e4dac6856a5fd032c731d877453965584ae
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907168"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurer une appliance pour les serveurs physiques

Cet article explique comment configurer l’appliance Azure Migrate si vous évaluez des serveurs physiques avec l’outil Azure Migrate : Server Assessment.

> [!NOTE]
> Si vous ne voyez pas encore dans le portail Azure Migrate des fonctionnalités mentionnées ici, patientez un peu. Elles apparaîtront au cours de la semaine suivante ou un peu plus tard.

L’appliance Azure Migrate est une appliance légère utilisée par Azure Migrate Server Assessment pour effectuer les opérations suivantes :

- Découvrir les serveurs locaux.
- Envoyer des métadonnées et des données de performances concernant les serveurs découverts à Azure Migrate Server Assessment.

[En savoir plus](migrate-appliance.md) sur les appliances d’Azure Migrate.


## <a name="appliance-deployment-steps"></a>Étapes de déploiement d’appliance

Pour configurer l’appliance, vous devez :
- Télécharger un fichier compressé avec le script du programme d’installation Azure Migrate à partir du portail Azure.
- Extraire le contenu du fichier compressé. Lancer la console PowerShell avec des privilèges administratifs.
- Exécuter le script PowerShell pour lancer l’application web de l’appliance.
- Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.

## <a name="download-the-installer-script"></a>Télécharger le script du programme d’installation

Téléchargez le fichier compressé pour l’appliance.

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Non virtualisé/autre**.
3. Cliquez sur **Télécharger** pour télécharger le fichier compressé.

    ![Télécharger la machine virtuelle](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du disque dur virtuel
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Pour l’appliance version 1.19.05.10, le hachage généré doit correspondre à ces valeurs.

  **Algorithme** | **Valeur de hachage**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="run-the-azure-migrate-installer-script"></a>Exécuter le script du programme d’installation Azure Migrate
Le script du programme d’installation effectue les opérations suivantes :

- Installe des agents et une application web pour la découverte et l’évaluation des serveurs physiques.
- Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Plus d’informations](https://www.microsoft.com/download/details.aspx?id=7435)
- Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
- Crée les fichiers suivants sous le chemin :
    - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
    - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Exécutez le script comme suit :

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.
2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).
3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.
4. Exécutez le script à l’aide de la commande suivante :
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
Une fois son exécution terminé, le script lance l’application web de l’appliance.



### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que la machine virtuelle de l’appliance peut se connecter aux [URL Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) nécessaires.

## <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Ouvrez un navigateur sur une machine qui peut se connecter à la machine virtuelle, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du bureau en cliquant sur le raccourci de l’application.
2. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Licence** : Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - **Connectivité** : L’application vérifie que la machine virtuelle a accès à Internet. Si la machine virtuelle utilise un proxy :
        - Cliquez sur **Paramètres du proxy** et spécifiez l’adresse du proxy et le port d’écoute, sous la forme http://ProxyIPAddress ou http://ProxyFQDN.
        - Spécifiez les informations d’identification si le proxy nécessite une authentification.
        - Seuls les proxys HTTP sont pris en charge.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte des machines virtuelles fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate Server Assessment vérifie que les dernières mises à jour sont installées sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Cliquez sur **Se connecter**. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
2. Sous le nouvel onglet, connectez-vous avec vos informations d’identification Azure. 
    - Connectez-vous avec votre nom d’utilisateur et votre mot de passe.
    - La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion effectuée, revenez à l’application web.
4. Sélectionnez l’abonnement où le projet Azure Migrate a été créé. Sélectionnez ensuite le projet.
5. Spécifiez un nom pour l’appliance. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
6. Cliquez sur **S'inscrire**.


## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

Connectez-vous à partir de l’appliance aux serveurs physiques, puis démarrez la découverte.

1. Cliquez sur **Ajouter des informations d’identification** pour spécifier les informations d’identification du compte que l’appliance utilisera pour découvrir les serveurs.  
2. Spécifiez le **système d’exploitation**, un nom convivial pour les informations d’identification, un **nom d’utilisateur** et un **mot de passe**, puis cliquez sur **Ajouter**.
Vous pouvez ajouter un ensemble d’informations d’identification pour les serveurs Windows et Linux.
4. Cliquez sur **Ajouter un serveur**, puis spécifiez les détails du serveur - nom de domaine complet (FQDN)/adresse IP et nom convivial des informations d’identification (une entrée par ligne) - pour vous connecter au serveur.
3. Cliquez sur **Valider**. Après validation, la liste des serveurs qui peuvent être découverts s’affiche.
    - Si la validation échoue pour un serveur, passez en revue l’erreur en pointant sur l’icône dans la colonne **État**. Corrigez les problèmes et recommencez la validation.
    - Pour supprimer un serveur, sélectionnez > **Supprimer**.
4. Après la validation, cliquez sur **Enregistrer et démarrer la découverte** pour démarrer le processus de découverte.

Ceci démarre la découverte. Il faut environ 15 minutes pour que les métadonnées des machines virtuelles découvertes apparaissent dans le portail Azure. 

## <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les serveurs apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**. 


## <a name="next-steps"></a>Étapes suivantes

Essayez l’[évaluation des serveurs physiques](tutorial-assess-physical.md) avec Azure Migrate Server Assessment.
