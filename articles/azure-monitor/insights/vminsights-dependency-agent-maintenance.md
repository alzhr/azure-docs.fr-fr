---
title: Comment mettre à niveau l’agent Dependency pour Azure Monitor pour machines virtuelles | Microsoft Docs
description: Cet article décrit la façon de mettre à niveau l’agent Dependency Azure Monitor pour machines virtuelles en utilisant la ligne de commande, l’assistant d’installation et d’autres méthodes.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 09/30/2019
ms.openlocfilehash: f062dead8d479fe4da5de46b76b82cee9207bd83
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933709"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Comment mettre à niveau l’agent Dependency pour Azure Monitor pour machines virtuelles

Après le déploiement initial de l’agent Dependency Azure Monitor pour machines virtuelles, des mises à jour incluant des correctifs de bogues ou la prise en charge de nouvelles fonctionnalités sont publiées.  Cet article vous aide à comprendre les méthodes disponibles et à effectuer la mise à niveau manuellement ou par le biais de l’automatisation.

## <a name="upgrade-options"></a>Options de mise à niveau 

L’agent Dependency pour Windows et Linux peut être mis à niveau vers la dernière version manuellement ou automatiquement en fonction du scénario de son déploiement et de l’environnement dans lequel la machine s’exécute. Les méthodes suivantes peuvent être utilisés pour mettre l’agent à niveau.

|Environnement |Méthode d’installation |Méthode de mise à niveau |
|------------|--------------------|---------------|
|Azure VM | Extension de machine virtuelle d’agent Dependency pour [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) et [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | L’agent est automatiquement mis à niveau par défaut, sauf si vous avez configuré votre modèle Azure Resource Manager pour la désactiver en définissant la valeur de la propriété *autoUpgradeMinorVersion* sur **false**. La mise à niveau pour la version mineure où la mise à niveau automatique est désactivée, et une mise à niveau majeure suivent la même méthode - désinstallez et réinstallez l’extension. |
| Images de machines virtuelles Azure personnalisées | Installation manuelle de l’agent Dependency pour Windows ou Linux | La mise à jour des machines virtuelles vers la dernière version de l’agent doit être effectuée à partir de la ligne de commande avec exécution du package d’installation de Windows ou de Linux auto-extractible et du package de script d’installation.|
| Machines virtuelles non Azure | Installation manuelle de l’agent Dependency pour Windows ou Linux | La mise à jour des machines virtuelles vers la dernière version de l’agent doit être effectuée à partir de la ligne de commande avec exécution du package d’installation de Windows ou de Linux auto-extractible et du package de script d’installation. |

## <a name="upgrade-windows-agent"></a>Mise à niveau de l’agent Windows 

Pour mettre à jour l’agent vers la dernière version sur une machine virtuelle Windows qui n’a pas été installée à l’aide de l’extension de machine virtuelle de l’agent Dependency, vous l’exécutez à partir de l’invite de commandes, d’un script ou d’une autre solution d’automatisation ou bien à l’aide de l’Assistant d’installation InstallDependencyAgent-Windows.exe.  

Vous pouvez télécharger la dernière version de l’agent Windows [ici](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Utilisation de l’Assistant d’installation

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.

2. Exécutez **InstallDependencyAgent-Windows.exe** pour démarrer l’Assistant d’installation.

3. Dans la boîte de dialogue **Installation de Dependency Agent 9.9.1**, cliquez sur **J’accepte** pour accepter le contrat de licence.

5. Dans la boîte de dialogue **Désinstallation de Dependency Agent 9.9.0**, cliquez sur **Suivant**. La page d’état affiche la progression de la désinstallation de la version précédente.

6. Dans la boîte de dialogue **Désinstallation de Dependency Agent 9.9.0**, cliquez sur **Désinstaller** afin de désinstaller la version précédente dans le chemin d’accès indiqué dans la boîte de dialogue. 

7. La progression de la désinstallation s’affiche dans la boîte de dialogue **Désinstallation de Dependency Agent 9.9.0**. Une fois le traitement terminé, la page de confirmation **Completing Dependency Agent Uninstall** apparaît. Cliquez sur **Terminer**.

8. Dans la boîte de dialogue **Configuration de Dependency Agent 9.9.1**, la progression de l’installation s’affiche. Lorsque la page **Completing Dependency Agent Uninstall** (Désinstallation de l’agent Dependency), cliquez sur **Terminer**. 

### <a name="from-the-command-line"></a>Depuis la ligne de commande

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.

2. Exécutez la commande ci-dessous.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Le paramètre `/RebootMode=manual` empêche la mise à niveau de redémarrer automatiquement la machine si certains processus utilisent des fichiers de la version précédente et ont un verrou sur eux. 

3. Pour confirmer que la mise à niveau a réussi, consultez la section `install.log` pour plus d’informations sur la configuration. Le répertoire des journaux d’activité est *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Mise à niveau de l’agent Linux 

La mise à niveau à partir des versions précédentes de l’agent Dependency sous Linux est prise en charge et exécutée selon la même commande qu’une nouvelle installation.

Vous pouvez télécharger la dernière version de l’agent Windows [ici](https://aka.ms/dependencyagentlinux).

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.

2. Exécutez la commande suivante en tant que racine`sh InstallDependencyAgent-Linux64.bin -s`. 

Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux d’activité. Sur les agents Linux, le répertoire des journaux est */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez arrêter la surveillance de vos machines virtuelles pendant un certain temps ou supprimer entièrement Azure Monitor pour machines virtuelles, consultez [Désactiver la surveillance dans Azure Monitor pour machines virtuelles](vminsights-optout.md).