---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177589"
---
Cet article suppose que vous possédez :

1. Un **VPN site à site** ou une connexion **Express Route** déjà établie entre votre réseau local et le réseau virtuel Microsoft Azure.
2. Un compte d’utilisateur avec les autorisations nécessaires pour créer une machine virtuelle dans l’abonnement Azure dans lequel les machines virtuelles ont été basculées.
3. Un abonnement avec un minimum de 4 cœurs disponibles pour faire tourner une nouvelle machine virtuelle serveur de processus.
4. La **phrase secrète du serveur de configuration**.

> [!TIP]
> Vérifiez que vous êtes en mesure de connecter le port 443 du serveur de configuration (qui s’exécute en local) à partir du réseau virtuel Azure dans lequel les machines virtuelles ont été basculées.
