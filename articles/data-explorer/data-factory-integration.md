---
title: Intégration d’Azure Data Explorer avec Azure Data Factory
description: Dans cette rubrique, vous allez intégrer Azure Data Explorer avec Azure Data Factory pour utiliser les activités de copie, de recherche et de commande
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: dd2b3bd584bb39810e0a5c9acde1a961330c273d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093227"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Intégrer Azure Data Explorer avec Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) est un service d’intégration de données basé sur le cloud qui vous permet d’intégrer différents magasins de données et d’effectuer des activités sur les données. Azure Data Factory (ADF) vous permet de créer des flux de travail basés sur les données afin d’orchestrer et d’automatiser le déplacement et la transformation des données. Azure Data Explorer est l’un des [magasins de données pris en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) dans Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Activités Azure Data Factory pour Azure Data Explorer

Diverses intégrations à Azure Data Factory sont disponibles pour les utilisateurs d’Azure Data Explorer :

### <a name="copy-activity"></a>Activité de copie
 
L’activité de copie d’Azure Data Factory est utilisée pour transférer des données entre les magasins de données. Azure Data Explorer est pris en charge en tant que source (les données sont copiées depuis Azure Data Explorer vers n’importe quel magasin de données pris en charge) et en tant que récepteur (les données sont copiées depuis n’importe quel magasin de données pris en charge vers Azure Data Explorer). Pour plus d’informations, consultez [Copier des données vers ou depuis Azure Data Explorer à l’aide d’Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). Pour obtenir une procédure détaillée, consultez [Charger des données depuis Azure Data Factory vers Azure Data Explorer](data-factory-load-data.md).
Azure Data Explorer est pris en charge par Azure IR (Integration Runtime), utilisé lorsque les données sont copiées dans Azure et dans le runtime d’intégration auto-hébergé, lors de la copie de données depuis/vers des magasins de données situés localement ou dans un réseau avec contrôle d’accès, tel qu’un réseau virtuel Azure. Pour plus d’informations, consultez [Runtimes d’intégration à utiliser](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Lors de l’utilisation de l’activité de copie et de la création d’un **service lié** ou d’un **jeu de données**, sélectionnez le magasin de données **Azure Data Explorer (Kusto)** et non l’ancien magasin de données **Kusto**.  

### <a name="lookup-activity"></a>Activité de recherche
 
L’activité de recherche est utilisée pour exécuter des requêtes sur Azure Data Explorer. Le résultat de la requête est retourné en tant que sortie de l’activité de recherche et peut être utilisé dans l’activité suivante dans le pipeline, comme décrit dans la [documentation relative à la Recherche ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
En plus de la limite de taille de réponse de 5 000 lignes et 2 Mo, l’activité a également une limite de délai d’expiration de requête de 1 heure.

### <a name="command-activity"></a>Activité de commande

L’activité de commande permet l’exécution de [commandes de contrôle](/azure/kusto/concepts/#control-commands) Azure Data Explorer. Contrairement aux requêtes, les commandes de contrôle peuvent potentiellement modifier des données ou des métadonnées. Certaines commandes de contrôle sont destinées à ingérer des données dans Azure Data Explorer (à l’aide de commandes telles que `.ingest`ou `.set-or-append`) ou à copier des données depuis Azure Data Explorer vers des magasins de données externes (à l’aide de commandes telles que `.export`).
Pour obtenir une procédure détaillée de l’activité de commande, consultez [Utiliser l’activité de commande Azure Data Factory pour exécuter des commandes de contrôle Azure Data Explorer](data-factory-command-activity.md).  L’utilisation d’une commande de contrôle pour copier des données peut parfois représenter une option plus rapide et plus économique que l’activité de copie. Pour déterminer quand utiliser l’activité de commande et l’activité de copie, consultez [Choisir entre les activités de copie et de commande lors de la copie de données](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Copier en bloc à partir d’un modèle de base de données

Le [pipeline Copier en bloc à partir d’une base de données vers Azure Data Explorer à l’aide du modèle Azure Data Factory](data-factory-template.md) est un pipeline Azure Data Factory prédéfini. Le modèle est utilisé pour créer un grand nombre de pipelines par base de données ou par table pour accélérer la copie de données. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Choisir entre les activités de copie et de commande Azure Data Explorer lors de la copie de données 

Cette section vous aidera à sélectionner l’activité appropriée en fonction de vos besoins en matière de copie de données.

Lorsque vous copiez des données depuis ou vers Azure Data Explorer, deux options sont disponibles dans Azure Data Factory :
* Activité de copie.
* L’activité de commande Azure Data Explorer, qui exécute l’une des commandes de contrôle qui transfèrent des données dans Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Copier des données depuis Azure Data Explorer
  
Vous pouvez copier des données depuis Azure Data Explorer à l’aide de l’activité de copie ou de la commande [`.export`](/azure/kusto/management/data-export/). La commande `.export` exécute une requête, puis exporte les résultats de la requête. 

Consultez le tableau suivant pour obtenir une comparaison entre l’activité de copie et la commande `.export` pour la copie de données depuis Azure Data Explorer.

| | Activité de copie | commande .export |
|---|---|---|
| **Description du flux** | ADF exécute une requête sur Kusto, traite le résultat et l’envoie au magasin de données cible. <br>(**ADX > ADF > magasin de données récepteur**) | ADF envoie une commande de contrôle `.export` à Azure Data Explorer, qui exécute la commande et envoie les données directement au magasin de données cible. <br>(**ADX > magasin de données récepteur**) |
| **Magasins de données cibles pris en charge** | Un large éventail de [magasins de données pris en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Objet blob Azure, SQL Database |
| **Performances** | Centralisé | <ul><li>Distribué (par défaut), exportation des données à partir de plusieurs nœuds simultanément</li><li>Plus rapide et coût des marchandises vendues rentable.</li></ul> |
| **Limites du serveur** | [Les limites de requête](/azure/kusto/concepts/querylimits) peuvent être étendues/désactivées. Par défaut, les requêtes ADF comportent : <ul><li>Une limite de taille de 500 000 enregistrements ou 64 Mo.</li><li>Une limite de temps de 10 minutes.</li><li>`noTruncation` défini sur False.</li></ul> | Par défaut, étend ou désactive les limites de requête : <ul><li>Les limites de taille sont désactivées.</li><li>Le délai d’expiration du serveur est étendu à 1 heure.</li><li>`MaxMemoryConsumptionPerIterator` et `MaxMemoryConsumptionPerQueryPerNode` sont étendus au maximum (5 Go, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Si votre destination de copie est l’un des magasins de données pris en charge par la commande `.export`, et si aucune des fonctionnalités de l’activité de copie n’est essentielle à vos besoins, sélectionnez la commande `.export`.

### <a name="copying-data-to-azure-data-explorer"></a>Copier des données dans Azure Data Explorer

Vous pouvez copier des données dans Azure Data Explorer à l’aide de l’activité de copie ou de commandes d’ingestion, telles que [Ingestion à partir d’une requête](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)` et [Ingestion à partir du stockage](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Consultez le tableau suivant pour obtenir une comparaison entre l’activité de copie et les commandes d’ingestion pour la copie de données dans Azure Data Explorer.

| | Activité de copie | Ingestion à partir d’une requête<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Ingestion à partir du stockage <br> `.ingest` |
|---|---|---|---|
| **Description du flux** | ADF récupère les données du magasin de données source, les convertit au format tabulaire et effectue les modifications de mappage de schéma requises. ADF charge ensuite les données dans des objets blob Azure, les divise en blocs, puis télécharge les objets blob pour les ingérer dans la table ADX. <br> (**Magasin de données source > ADF > Objets blob Azure > ADX**) | Ces commandes peuvent exécuter une requête ou une commande `.show`, et ingérer les résultats de la requête dans une table (**ADX > ADX**). | Cette commande ingère les données dans une table en « extrayant » les données d’un ou de plusieurs artefacts de stockage cloud. |
| **Magasins de données sources pris en charge** |  [variété d’options](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Objet blob Azure, SQL (à l’aide du plug-in sql_request), Cosmos (à l’aide du plug-in cosmosdb_sql_request) et tout autre magasin de données qui fournit des API HTTP ou Python. | Filesystem, Stockage Blob Azure, ADLS Gen 1, ADLS Gen 2 |
| **Performances** | Les ingestions sont mises en file d’attente et gérées, ce qui garantit des ingestions de petite taille ainsi qu’une haute disponibilité en fournissant une gestion de l’équilibrage de charge, des nouvelles tentatives et des erreurs. | <ul><li>Ces commandes n’ont pas été conçues pour l’importation de données volumineuses.</li><li>Fonctionne comme prévu et plus économique. Mais utilisez l’activité de copie pour les scénarios de production et lorsque le débit du trafic et les tailles de données sont importants.</li></ul> |
| **Limites du serveur** | <ul><li>Aucune limite de taille.</li><li>Limite maximale du délai d’expiration : 1 heure par objet blob ingéré. |<ul><li>Il n’existe qu’une limite de taille sur la partie de la requête, qui peut être ignorée en spécifiant `noTruncation=true`.</li><li>Limite maximale du délai d’expiration : 1 heure.</li></ul> | <ul><li>Aucune limite de taille.</li><li>Limite maximale du délai d’expiration : 1 heure.</li></ul>|

> [!TIP]
> * Utilisez les commandes `ingest from query` lorsque vous copiez des données depuis ADF vers Azure Data Explorer.  
> * Pour les jeux de données volumineux (> 1 Go), utilisez l’activité de copie.  

## <a name="required-permissions"></a>Autorisations requises

Le tableau suivant répertorie les autorisations requises pour les différentes étapes de l’intégration avec Azure Data Factory.

| Étape | Opération | Niveau minimal d’autorisations | Notes |
|---|---|---|---|
| **Créer un service lié** | Navigation de la base de données | *visionneuse de base de données* <br>L’utilisateur connecté qui utilise ADF doit être autorisé à lire les métadonnées de la base de données. | L’utilisateur peut fournir le nom de la base de données manuellement. |
| | Tester la connexion | *moniteur de base de données* ou *ingéreur de table* <br>Le principal du service doit être autorisé à exécuter des commandes `.show` au niveau de la base de données ou l’ingestion au niveau de la table. | <ul><li>L’opération TestConnection vérifie la connexion au cluster, et non à la base de données. Elle peut s’effectuer correctement même si la base de données n’existe pas.</li><li>Les autorisations d’administrateur de table ne sont pas suffisantes.</li></ul>|
| **Création d’un jeu de données** | Navigation de la table | *moniteur de base de données* <br>L’utilisateur connecté qui utilise ADF doit être autorisé à exécuter des commandes `.show` au niveau de la base de données. | L’utilisateur peut fournir le nom de la table manuellement.|
| **Création d’un jeu de données** ou **Activité de copie** | Aperçu des données | *visionneuse de base de données* <br>Le principal du service doit être autorisé à lire les métadonnées de la base de données. | | 
|   | Importer un schéma | *visionneuse de base de données* <br>Le principal du service doit être autorisé à lire les métadonnées de la base de données. | Quand ADX est la source d’une copie tableau à tableau, ADF importe automatiquement le schéma, même si l’utilisateur n’a pas importé explicitement le schéma. |
| **ADX en tant que récepteur** | Créer un mappage de colonnes par nom | *moniteur de base de données* <br>Le principal du service doit être autorisé à exécuter des commandes `.show` au niveau de la base de données. | <ul><li>Toutes les opérations obligatoires fonctionnent avec l’*ingéreur de table*.</li><li> Certaines opérations facultatives peuvent échouer.</li></ul> |
|   | <ul><li>Créer un mappage CSV sur la table</li><li>Supprimer le mappage</li></ul>| *ingéreur de table* ou *administrateur de base de données* <br>Le principal du service doit être autorisé à apporter des modifications à une table. | |
|   | Réception de données | *ingéreur de table* ou *administrateur de base de données* <br>Le principal du service doit être autorisé à apporter des modifications à une table. | | 
| **ADX en tant que source** | Exécuter la requête | *visionneuse de base de données* <br>Le principal du service doit être autorisé à lire les métadonnées de la base de données. | |
| **Commande Kusto** | | En fonction du niveau d’autorisation de chaque commande. |

## <a name="performance"></a>Performances 

Si Azure Data Explorer est la source et que vous utilisez l’activité de recherche, de copie ou de commande qui contient une requête, consultez les [meilleures pratiques relatives aux requêtes](/azure/kusto/query/best-practices) pour obtenir des informations sur les performances et la [documentation ADF pour l’activité de copie](/azure/data-factory/copy-activity-performance).
  
Cette section aborde l’utilisation de l’activité de copie lorsqu’Azure Data Explorer est le récepteur. Le débit estimé pour le récepteur Azure Data Explorer est de 11 à 13 Mbits/s. Le tableau suivant décrit les paramètres qui affectent les performances du récepteur Azure Data Explorer.

| Paramètre | Notes |
|---|---|
| **Proximité géographique des composants** | Placez tous les composants dans la même région :<ul><li>magasins de données source et récepteur.</li><li>Runtime d’intégration ADF.</li><li>Votre cluster ADX.</li></ul>Assurez-vous qu’au moins le runtime d’intégration se trouve dans la même région que votre cluster ADX. |
| **Nombre d’unités d’intégration de données** | 1 machine virtuelle pour 4 unités d’intégration de données utilisées par ADF. <br>L’augmentation du nombre d’unités d’intégration de données ne vous aidera que si votre source est un magasin basé sur un fichier avec plusieurs fichiers. Chaque machine virtuelle traitera ensuite un fichier différent en parallèle. Par conséquent, la copie d’un seul fichier volumineux aura une latence plus élevée que la copie de plusieurs fichiers plus petits.|
|**Quantité et référence SKU de votre cluster ADX** | Un nombre élevé de nœuds ADX permet de réduire le temps de traitement de l’ingestion.|
| **Parallélisme** | Pour copier une très grande quantité de données depuis une base de données, partitionnez vos données, puis utilisez une boucle ForEach qui copie chaque partition en parallèle ou utilisez la [copie en bloc à partir de la base de données vers un modèle Azure Data Explorer](data-factory-template.md). Remarque : **Paramètres** > **Degré de parallélisme** dans l’activité de copie n’est pas pertinent pour ADX. |
| **Complexité du traitement des données** | La latence varie selon le format du fichier source, le mappage de colonnes et la compression.|
| **La machine virtuelle exécutant votre runtime d’intégration** | <ul><li>Pour la copie Azure, les machines virtuelles ADF et les références SKU de machine virtuelle ne peuvent pas être modifiées.</li><li> Pour la copie locale vers Azure, vérifiez que la machine virtuelle hébergeant votre runtime d’intégration auto-hébergé est suffisamment puissante.</li></ul>|

## <a name="monitor-activity-progress"></a>Surveiller la progression des activités

* Lors de la surveillance de la progression des activités, la propriété *Données écrites* peut être bien plus volumineuse que la propriété *Données lues*, car la propriété *Données lues* est calculée en fonction de la taille du fichier binaire, tandis que la propriété *Données écrites* est calculée en fonction de la taille de la mémoire, après la désérialisation et la décompression des données.

* Lorsque vous surveillez la progression des activités, vous pouvez voir que les données sont écrites dans le récepteur Azure Data Explorer. Lorsque vous interrogez la table Azure Data Explorer, vous constatez que les données ne sont pas arrivées. C’est parce qu’il existe deux étapes lors de la copie vers Azure Data Explorer. 
    * La première étape lit les données sources, les divise en blocs de 900 Mo et charge chaque bloc dans un objet blob Azure. La première étape s’affiche sur la vue de progression de l’activité ADF. 
    * La deuxième étape commence une fois que toutes les données ont été chargées dans des objets blob Azure. Les nœuds du moteur Azure Data Explorer téléchargent les objets blob et ingèrent les données dans la table du récepteur. Les données sont ensuite affichées dans votre table Azure Data Explorer.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [copier des données vers Azure Data Explorer à l’aide d’Azure Data Factory](data-factory-load-data.md).
* Découvrez comment [utiliser un modèle Azure Data Factory pour la copie en bloc à partir d’une base de données vers Azure Data Explorer](data-factory-template.md).
* Découvrez comment utiliser [l’activité de commande Azure Data Factory pour exécuter des commandes de contrôle Azure Data Explorer](data-factory-command-activity.md).
* Découvrez les [requêtes Azure Data Explorer](/azure/data-explorer/web-query-data) pour l’interrogation de données.



