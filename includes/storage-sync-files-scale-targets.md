---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391626"
---
| Ressource | Cible | Limite inconditionnelle |
|----------|--------------|------------|
| Services de synchronisation de stockage par région | 20 services de synchronisation de stockage | OUI |
| Groupes de synchronisation par service de synchronisation de stockage | 100 groupes de synchronisation | OUI |
| Serveurs inscrits par le service de synchronisation de stockage | 99 serveurs | OUI |
| Points de terminaison cloud par groupe de synchronisation | 1 point de terminaison cloud | OUI |
| Points de terminaison de serveur par groupe de synchronisation | 50 points de terminaison de serveur | Non |
| Points de terminaison de serveur par serveur | 30 points de terminaison de serveur | OUI |
| Objets du système de fichiers (répertoires et fichiers) par groupe de synchronisation | 50 millions d’objets | Non |
| Nombre maximal d’objets de système de fichiers (répertoires et fichiers) dans un répertoire | 5 millions d’objets | OUI |
| Taille maximale du descripteur de sécurité d’objet (répertoires et fichiers) | 64 Kio | OUI |
| Taille du fichier | 100 Gio | Non |
| Taille minimale d’un fichier à hiérarchiser | 64 Kio | OUI |
| Sessions de synchronisation simultanées | Agent v4 et versions ultérieures : La limitation varie selon les ressources système disponibles. <BR> Agent V3 : Deux sessions de synchronisation actives par processeur ou un maximum de huit sessions de synchronisation actives par serveur. | OUI

> [!Note]  
> Un point de terminaison Azure File Sync peut augmenter la taille d’un partage de fichiers Azure. Si la limite de taille du partage de fichiers Azure est atteinte, la synchronisation ne fonctionne pas.
