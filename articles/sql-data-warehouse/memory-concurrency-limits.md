---
title: Limites de mémoire et de concurrence
description: Afficher les limites de mémoire et de concurrence allouées aux différents niveaux de performance et classes de ressources dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/04/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3611e61b303997a4291f4436403bb0a95e647e65
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686038"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limites de mémoire et de concurrence pour Azure SQL Data Warehouse
Afficher les limites de mémoire et de concurrence allouées aux différents niveaux de performance et classes de ressources dans Azure SQL Data Warehouse.  

## <a name="data-warehouse-capacity-settings"></a>Paramètres de la capacité de l’entrepôt de données
Les tableaux suivants présentent la capacité maximale pour l’entrepôt de données à différents niveaux de performance. Pour modifier le niveau de performance, consultez [Calcul de mise à l’échelle – portail](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Niveaux de service

La plage des niveaux de service est comprise entre DW100c et DW30000c.

| Niveau de performance | Nœuds de calcul | Distributions par nœud de calcul | Mémoire par entrepôt de données (Go) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1 200                          |
| DW2500c           | 5\.             | 12                             |  1 500                          |
| DW3000c           | 6             | 10                             |  1 800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5\.                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Le niveau de service maximal est DW30000c, qui correspond à 60 nœuds de calcul et une distribution par nœud de calcul. Par exemple, un entrepôt de données de 600 To à DW30000c traite environ 10 To par nœud de calcul.

## <a name="concurrency-maximums-for-workload-groups"></a>Valeurs maximales de concurrence pour les groupes de charge de travail
Avec l’introduction des groupes de charges de travail (lien à déterminer), le concept d’emplacements de concurrence ne s’applique plus.  Les ressources par demande sont allouées selon un pourcentage et spécifiées dans la définition du groupe de charge de travail.  Toutefois, même avec la suppression des emplacements de concurrence, il existe des quantités minimales de ressources nécessaires par requête, en fonction du niveau de service.  Le tableau ci-dessous définit la quantité minimale de ressources nécessaires par requête entre les niveaux de service et la concurrence associée possible. 

|Niveau de service|Nombre maximal de requêtes concurrentes|% min. pris en charge pour REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5 %|
|DW300c|12|8 %|
|DW400c|16|6,25 %|
|DW500c|20|5 %|
|DW1000c|32|3 %|
|DW1500c|32|3 %|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1,5 %|
|DW5000c|64|1,5 %|
|DW6000c|128|0,75 %|
|DW7500c|128|0,75 %|
|DW10000c|128|0,75 %|
|DW15000c|128|0,75 %|
|DW30000c|128|0,75 %|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Valeurs maximales de concurrence pour les classes de ressources
Pour s’assurer que chaque requête dispose de suffisamment de ressources pour s’exécuter efficacement, SQL Data Warehouse suit l’utilisation des ressources en assignant des emplacements de concurrence à chaque requête. Le système place les requêtes dans une file d’attente en fonction de l’importance et des emplacements de concurrence. Les requêtes attendent dans la file d’attente jusqu’à ce que suffisamment d’emplacements de concurrence soient disponibles. L’[importance](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) et les emplacements de concurrence déterminent la hiérarchisation des priorités du processeur. Pour plus d’informations, voir [Analyser votre charge de travail](analyze-your-workload.md).

**Classes de ressources statiques**

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources statique](resource-classes-for-workload-management.md).  

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles | Emplacements utilisés par staticrc10 | Emplacements utilisés par staticrc20 | Emplacements utilisés par staticrc30 | Emplacements utilisés par staticrc40 | Emplacements utilisés par staticrc50 | Emplacements utilisés par staticrc60 | Emplacements utilisés par staticrc70 | Emplacements utilisés par staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1 200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Classes de ressources dynamiques**

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources dynamique](resource-classes-for-workload-management.md). Les classes de ressources dynamiques utilisent une allocation de pourcentage de mémoire 3-10-22-70 pour les classes de ressources small-medium-large-xlarge à tous les niveaux de service.

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles | Emplacements utilisés par smallrc | Emplacements utilisés par mediumrc | Emplacements utilisés par largerc | Emplacements utilisés par xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5\.                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1 200                        | 36                    | 120                    | 264                   | 840                    |


Quand il n’y a pas suffisamment d’emplacements de concurrence libres pour démarrer l’exécution des requêtes, celles-ci sont mises en file d’attente et exécutées en fonction de leur importance.  Si l’importance est équivalente, les requêtes sont exécutées sur la base du « premier entré, premier sorti ».  À mesure que les requêtes se terminent et que le nombre de requêtes et d’emplacements chute sous les limites, SQL Data Warehouse libère des requêtes en file d’attente. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de tirer parti des classes de ressources pour optimiser davantage votre charge de travail, voir les articles suivants :
* [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md)
* [Analyse de votre charge de travail](analyze-your-workload.md)