---
title: Configurer une appliance pour Azure Migrate Server Assessment/Migration des machines virtuelles Hyper-V | Microsoft Docs
description: Décrit comment configurer une appliance pour la découverte, l’évaluation et la migration sans agent des machines virtuelles Hyper-V avec Azure Migrate Server Assessment/Migration.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810208"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurer une appliance pour les machines virtuelles Hyper-V

Cet article explique comment configurer l’appliance Azure Migrate si vous évaluez des machines virtuelles Hyper-V avec l’outil Azure Migrate Server Assessment ou si vous migrez des machines virtuelles VMware vers Azure à l’aide de l’outil de migration de serveur Azure Migrate.

L’appliance de machines virtuelles Hyper-V est une appliance légère utilisée par Azure Migrate Server Assessment/Migration pour effectuer les opérations suivantes :

- Découvrir des machines virtuelles Hyper-V locales.
- Envoyer des métadonnées et des données de performances pour les machines virtuelles découvertes à Azure Migrate Server Assessment/Migration.

[En savoir plus](migrate-appliance.md) sur les appliances d’Azure Migrate.


## <a name="appliance-deployment-steps"></a>Étapes de déploiement d’appliance

Pour configurer l’appliance, vous devez :
- Télécharger un disque dur virtuel Hyper-V compressé à partir du portail Azure.
- Créer l’appliance et vérifier qu’elle peut se connecter à Azure Migrate Server Assessment. 
- Configurer l’appliance pour la première fois, puis l’inscrire auprès du projet Azure Migrate.

## <a name="download-the-vhd"></a>Télécharger le disque dur virtuel

Téléchargez le modèle de disque dur virtuel compressé pour l’appliance.

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Oui, avec Hyper-V**.
3. Cliquez sur **Télécharger** pour télécharger le fichier de disque dur virtuel.

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


  
## <a name="create-the-appliance-vm"></a>Créer la machine virtuelle de l’appliance

Importez le fichier téléchargé, puis créez la machine virtuelle.

1. Extrayez le fichier de disque dur virtuel compressé dans un dossier sur l’hôte Hyper-V qui hébergera la machine virtuelle de l’appliance. Trois dossiers sont extraits.
2. Ouvrez le Gestionnaire Hyper-V. Dans **Actions**, cliquez sur **Importer la machine virtuelle**.

    ![Déployer le disque dur virtuel](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Dans l’Assistant Importation de machine virtuelle > **Avant de commencer**, cliquez sur **Suivant**.
3. Dans **Localiser le dossier**, spécifiez le dossier contenant le disque dur virtuel extrait. Cliquez ensuite sur **Suivant**.
1. Dans **Sélectionner une machine virtuelle**, cliquez sur **Suivant**.
2. Dans **Choisir le type d’importation**, cliquez sur **Copier la machine virtuelle (créer un identifiant unique)** . Cliquez ensuite sur **Suivant**.
3. Dans **Choisir la destination**, laissez la valeur par défaut. Cliquez sur **Suivant**.
4. Dans **Dossiers de stockage**, laissez la valeur par défaut. Cliquez sur **Suivant**.
5. Dans **Choisir un réseau**, spécifiez le commutateur virtuel qui sera utilisé par la machine virtuelle. Le commutateur nécessite une connexion à Internet pour envoyer des données à Azure.
6. Dans **Récapitulatif**, passez en revue les paramètres. Puis, cliquez sur **Terminer**.
7. Dans Gestionnaire Hyper-V > **Machines virtuelles**, démarrez la machine virtuelle.


### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que la machine virtuelle de l’appliance peut se connecter aux [URL Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Dans Gestionnaire Hyper-V> **Machines virtuelles**, cliquez avec le bouton droit sur la machine virtuelle > **Se connecter**.
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur une machine qui peut se connecter à la machine virtuelle, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du Bureau de l’appliance en cliquant sur le raccourci de l’application.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
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


### <a name="delegate-credentials-for-smb-vhds"></a>Déléguer des informations d’identification pour les disques durs virtuels sur SMB

Si vous utilisez des disques durs virtuels sur des SMB, vous devez activer la délégation des informations d’identification de l’appliance aux hôtes Hyper-V. Pour effectuer cette opération à partir de l’appliance :

1. Sur la machine virtuelle de l’appliance, exécutez cette commande. HyperVHost1/HyperVHost2 sont des exemples de noms d’hôte.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Vous pouvez également effectuer cette opération dans l’éditeur d’objets de stratégie de groupe sur l’appliance :
    - Dans **Stratégie de l’ordinateur local** > **Configuration de l’ordinateur**, cliquez sur **Modèles d’administration** > **Système** > **Délégation d’informations d’identification**.
    - Double-cliquez sur **Autoriser la délégation de nouvelles informations d’identification**, puis sélectionnez **Activé**.
    - Dans **Options**, cliquez sur **Afficher**, puis ajoutez chaque hôte Hyper-V que vous voulez découvrir à la liste, avec **wsman/** comme préfixe.
    - Dans **Délégation d’informations d’identification**, double-cliquez sur **Autoriser la délégation de nouvelles informations d’identification avec l’authentification de serveur NTLM uniquement**. À nouveau, ajoutez chaque hôte Hyper-V que vous voulez découvrir à la liste, avec **wsman/** comme préfixe.

## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

Connectez-vous de l’appliance à des hôtes ou des clusters Hyper-V, et démarrez la découverte des machines virtuelles.

1. Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte que l’appliance doit utiliser pour découvrir les machines virtuelles. Spécifiez un nom convivial pour les informations d’identification, puis cliquez sur **Enregistrer les détails**.
2. Cliquez sur **Ajouter un hôte**, puis spécifiez les détails de l’hôte/du cluster Hyper-V.
3. Cliquez sur **Valider**. Après la validation, le nombre de machines virtuelles qui peuvent être découvertes sur chaque hôte/cluster s’affiche.
    - Si la validation échoue pour un hôte, examinez l’erreur en pointant sur l’icône dans la colonne **État**. Corrigez les problèmes et recommencez la validation.
    - Pour supprimer des hôtes ou des clusters, sélectionnez > **Supprimer**.
    - Vous ne pouvez pas supprimer un hôte spécifique d’un cluster. Vous pouvez supprimer seulement la totalité du cluster.
    - Vous pouvez ajouter un cluster, même s’il existe des problèmes avec des hôtes spécifiques dans le cluster.
4. Après la validation, cliquez sur **Enregistrer et démarrer la découverte** pour démarrer le processus de découverte.

Ceci démarre la découverte. Il faut environ 15 minutes pour que les métadonnées des machines virtuelles découvertes apparaissent dans le portail Azure. 

## <a name="verify-vms-in-the-portal"></a>Vérifier les machines virtuelles dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les machines virtuelles apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**. 


## <a name="next-steps"></a>Étapes suivantes

Essayez l’[évaluation d’Hyper-V](tutorial-assess-hyper-v.md) avec Azure Migrate Server Assessment.