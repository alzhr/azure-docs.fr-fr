---
title: Guide des performances et du réglage du flux de données de mappage dans Azure Data Factory
description: Découvrez des informations sur les facteurs clés ayant une incidence sur les performances des flux de données de mappage dans Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
ms.openlocfilehash: 20a08345d8335b4857ca9777efb55f953ee63e9f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681551"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guide des performances et du réglage du mappage de flux de données

Les flux de données de mappage dans Azure Data Factory fournissent une interface sans code pour concevoir, déployer et orchestrer des transformations de données à grande échelle. Si vous n’êtes pas familiarisé avec les flux de données de mappage, consultez [Vue d’ensemble des flux de données de mappage](concepts-data-flow-overview.md).

Quand vous concevez et testez des flux de données à partir de l’interface utilisateur ADF, veillez à activer le mode de débogage pour exécuter vos flux de données en temps réel sans attendre le préchauffage d’un cluster. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Supervision des performances de flux de données

Lors de la conception de flux de données de mappage, vous pouvez effectuer un test unitaire de chaque transformation en cliquant sur l’onglet de prévisualisation des données dans le panneau de configuration. Une fois que vous avez vérifié votre logique, testez votre flux de données de bout en bout en tant qu’activité dans un pipeline. Ajoutez une activité Exécuter Data Flow et utilisez le bouton Déboguer pour tester les performances de votre flux de données. Pour ouvrir le plan d’exécution et le profil de performances de votre flux de données, cliquez sur l’icône en forme de lunettes sous « Actions » sous l’onglet de sortie de votre pipeline.

![Moniteur Data Flow](media/data-flow/mon002.png "Moniteur Data Flow 2")

 Vous pouvez utiliser ces informations pour évaluer les performances de votre flux de données sur des sources de données de différentes tailles. Pour plus d’informations, consultez [Supervision des flux de données de mappage](concepts-data-flow-monitoring.md).

![Supervision du flux de données](media/data-flow/mon003.png "Moniteur Data Flow 3")

 Pour les exécutions de débogage de pipeline, il faut environ une minute de temps de configuration de cluster dans vos calculs des performances globales pour un cluster à chaud. Si vous initialisez le runtime d’intégration Azure par défaut, la rotation peut prendre environ 5 minutes.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Augmentation de la taille de calcul dans Azure Integration Runtime

Un runtime d’intégration avec davantage de cœurs augmente le nombre de nœuds dans les environnements de calcul Spark et fournit davantage de puissance de traitement pour lire, écrire et transformer vos données.
* Essayez un cluster **optimisé pour le calcul** si vous voulez que votre vitesse de traitement soit supérieure à votre vitesse d’entrée.
* Essayez un cluster **à mémoire optimisée** si vous voulez mettre en cache plus de données en mémoire.

![Nouveau runtime d'intégration](media/data-flow/ir-new.png "Nouveau runtime d'intégration")

Pour plus d’informations sur la création d’un runtime d’intégration, consultez [Runtime d’intégration dans Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Augmenter la taille de votre cluster de débogage

Par défaut, l’activation du débogage utilise le runtime d’intégration Azure par défaut qui est créé automatiquement pour chaque fabrique de données. Ce runtime d’intégration Azure par défaut est défini pour huit cœurs, dont quatre pour un nœud pilote et quatre pour un nœud Worker, à l’aide des propriétés de calcul général. Quand vous effectuez un test avec des données plus volumineuses, vous pouvez augmenter la taille de votre cluster de débogage en créant un runtime d’intégration Azure avec des configurations plus grandes et choisir ce dernier quand vous passez au débogage. ADF utilise alors ce runtime d’intégration Azure pour le débogage dans l’aperçu des données ou de pipeline avec des flux de données.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimisation pour Azure SQL Database et Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Partitionnement sur la source

1. Accédez à l’onglet **Optimiser**, puis sélectionnez **Définir le partitionnement**
1. Sélectionnez **Source**.
1. Sous **Nombre de partitions**, définissez le nombre maximal de connexions à votre base de données Azure SQL DB. Vous pouvez essayer d’utiliser un paramètre plus élevé pour obtenir des connexions parallèles à votre base de données. Toutefois, dans certains cas ceci peut entraîner des performances plus rapides avec un nombre limité de connexions.
1. Indiquez s’il faut effectuer le partitionnement en fonction d’une colonne de table spécifique ou d’une requête.
1. Si vous avez sélectionné **Colonne**, choisissez la colonne de partition.
1. Si vous avez sélectionné **Requête**, entrez une requête qui correspond au schéma de partitionnement de votre table de base de données. Cette requête permet au moteur de base de données source de tirer parti de l’élimination de partition. Vos tables de base de données sources n’ont pas besoin d’être partitionnées. Si votre source n’est pas déjà partitionnée, ADF utilise toujours le partitionnement des données dans l’environnement de transformation Spark en fonction de la clé que vous sélectionnez dans la transformation de la source.

![Partie source](media/data-flow/sourcepart3.png "Partie source")

### <a name="source-batch-size-input-and-isolation-level"></a>Taille de lot, entrée et niveau d’isolation de la source

Sous **Options de la source** dans la transformation de la source, les paramètres suivants peuvent avoir une incidence sur les performances :

* L’option Taille du lot indique à ADF que les données doivent être stockées dans des ensembles en mémoire, et non pas ligne par ligne. Il s’agit d’un paramètre facultatif. Il est possible que vous manquiez de ressources sur les nœuds de calcul s’ils sont mal dimensionnés.
* La définition d’une requête peut vous permettre de filtrer les lignes au niveau de la source avant qu’elles n’arrivent dans le flux de données pour être traitées. Cela peut accélérer l’acquisition initiale des données. Si vous utilisez une requête, vous pouvez ajouter des indicateurs de requête facultatifs pour votre instance Azure SQL DB, par exemple READ UNCOMMITTED.
* La lecture non validée fournira des résultats plus rapides suite à une requête concernant la transformation de la source.

![Source](media/data-flow/source4.png "Source")

### <a name="sink-batch-size"></a>Taille de lot du récepteur

Pour éviter un traitement ligne par ligne de vos flux de données, définissez **Taille du lot** sous l’onglet Paramètres des récepteurs Azure SQL DB et Azure SQL DW. Si la taille du lot est définie, ADF traite les écritures dans les bases de données par lots en fonction de la taille fournie.

![Section sink](media/data-flow/sink4.png "Récepteur")

### <a name="partitioning-on-sink"></a>Partitionnement sur un récepteur

Même si vos données ne sont pas partitionnées dans vos tables de destination, il est recommandé de partitionner vos données dans la transformation du récepteur. Quand les données sont partitionnées, leur chargement est souvent beaucoup plus rapide que si toutes les connexions sont forcées d’utiliser un nœud/une partition unique. Accédez à l’onglet Optimiser de votre récepteur, puis sélectionnez le partitionnement *Tourniquet (round robin)* afin de choisir le nombre idéal de partitions pour écrire dans votre récepteur.

### <a name="disable-indexes-on-write"></a>Désactivez les index lors de l’écriture

Dans votre pipeline, ajoutez une [Activité de procédure stockée](transform-data-using-stored-procedure.md) avant votre activité de flux de données qui désactive les index sur vos tables cibles écrites à partir de votre récepteur. Après votre activité de flux de données, ajoutez une autre activité de procédure stockée qui active ces index.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Augmenter la taille de vos bases de données Azure SQL DB et Azure SQL DW

Planifiez un redimensionnement de vos bases de données Azure SQL DB et Azure SQL DW de source et de récepteur avant l’exécution de votre pipeline pour augmenter le débit et réduire la limitation de requêtes Azure une fois que vous atteignez les limites DTU. Une fois l’exécution de votre pipeline terminée, redimensionnez vos bases de données à leur fréquence d’exécution normale.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Azure SQL DW uniquement] Utiliser la préproduction pour charger des données en bloc par le biais de PolyBase

Pour éviter les insertions ligne par ligne dans votre entrepôt de données, cochez **Activer le mode de préproduction** dans vos paramètres de récepteur afin qu’ADF puisse utiliser [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase permet à ADF de charger les données en bloc.
* Quand vous exécutez votre activité de flux de données à partir d’un pipeline, vous devez sélectionner un emplacement de stockage d’objets blob ou ADLS Gen2 pour mettre vos données en préproduction pendant le chargement en bloc.

## <a name="optimizing-for-files"></a>Optimisation des fichiers

À chaque transformation, vous pouvez définir sous l’onglet Optimiser le schéma de partitionnement que vous souhaitez que Data Factory utilise.
* Pour les fichiers plus petits, vous pouvez trouver que sélectionner *Partition unique* fonctionne parfois mieux et plus rapidement que si vous demandez à Spark de partitionner vos fichiers de petite taille.
* Si vous n’avez pas suffisamment d’informations sur vos données sources, choisissez le partitionnement *Tourniquet (round robin)* , puis définissez le nombre de partitions.
* Si vos données comportent des colonnes qui peuvent faire de bonnes clés de hachage, choisissez *Partitionnement de hachage*.

Quand vous effectuez un débogage dans l’aperçu des données et lors du débogage du pipeline, la taille limite et la taille d’échantillonnage des jeux de données sources basés sur des fichiers s’appliquent uniquement au nombre de lignes retournées, et non au nombre de lignes lues. Cela peut avoir une incidence sur les performances de vos exécutions de débogage, voire provoquer l’échec du flux.
* Les clusters de débogage sont de petits clusters à nœud unique par défaut. Nous vous recommandons d’utiliser de petits fichiers exemples pour le débogage. Sous Paramètres de débogage, pointez vers un petit sous-ensemble de données en utilisant un fichier temporaire.

    ![Paramètres de débogage](media/data-flow/debugsettings3.png "Paramètres de débogage")

### <a name="file-naming-options"></a>Options d’attribution de noms de fichiers

La méthode la plus courante pour écrire des données transformées dans les flux de données de mappage est d’écrire un magasin de fichiers blob ou ADLS. Dans votre récepteur, vous devez sélectionner un jeu de données qui pointe vers un conteneur ou un dossier, et non un fichier nommé. Comme le flux de données de mappage utilise Spark pour l’exécution, votre sortie est répartie sur plusieurs fichiers en fonction de votre schéma de partitionnement.

Un schéma de partitionnement courant consiste à choisir _Sortie vers un fichier unique_, qui fusionne tous les fichiers de PARTIE de sortie dans un fichier unique dans votre récepteur. Cette opération nécessite que la sortie soit réduite à une partition unique sur un seul nœud de cluster. Il est possible que vous n’ayez plus de ressources de nœud de cluster si vous combinez de nombreux fichiers sources volumineux dans un seul fichier de sortie.

Pour éviter d’épuiser les ressources de nœud de calcul, conservez le schéma optimisé par défaut dans le flux de données, puis ajoutez une activité de copie dans votre pipeline qui fusionne tous les fichiers de PARTIE du dossier de sortie dans un nouveau fichier unique. Cette technique sépare l’action de transformation de la fusion de fichiers et donne le même résultat que la définition de _Sortie vers un fichier unique_.

### <a name="looping-through-file-lists"></a>Bouclage parmi les listes de fichiers

Un flux de données de mappage s’exécute mieux quand la transformation de la source effectue une itération sur plusieurs fichiers au lieu d’effectuer une boucle à l’aide de l’activité « For Each ». Nous vous recommandons d’utiliser des caractères génériques ou des listes de fichiers dans votre transformation de la source. Le processus de flux de données s’exécutera plus rapidement si la boucle peut se produire dans le cluster Spark. Pour plus d’informations, consultez [Utilisation des caractères génériques dans la transformation de la source](data-flow-source.md#file-based-source-options).

Par exemple, si vous avez une liste de fichiers de données de juillet 2019 que vous voulez traiter dans un dossier du Stockage Blob, voici un caractère générique que vous pouvez utiliser dans votre transformation de la source.

```DateFiles/*_201907*.txt```

Si vous utilisez des caractères génériques, votre pipeline ne contiendra qu’une seule activité de flux de données. Cette opération sera plus performante qu’une recherche sur le magasin d’objets blob qui effectue ensuite une itération sur tous les fichiers correspondants à l’aide d’une instruction ForEach avec une activité d’exécution de flux de données à l’intérieur.

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres articles sur les flux de données consacrés aux performances :

- [Onglet Optimiser le flux de données](concepts-data-flow-overview.md#optimize)
- [Activité Data Flow](control-flow-execute-data-flow-activity.md)
- [Analyser les performances des flux de données](concepts-data-flow-monitoring.md)
