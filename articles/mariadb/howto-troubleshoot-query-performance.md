---
title: Résoudre les problèmes de performances des requêtes dans Azure Database for MariaDB
description: Découvrez comment utiliser l’instruction EXPLAIN pour résoudre les problèmes de performances des requêtes dans Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 11/09/2018
ms.openlocfilehash: a2f5e7e7c9ca39c092e13242ecdac2675b09fc0d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973500"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Guide pratique pour utiliser l’instruction EXPLAIN visant à profiler les performances des requêtes dans Azure Database for MariaDB
**EXPLAIN** est un outil pratique pour optimiser les requêtes. L’instruction EXPLAIN permet d’obtenir des informations sur la façon dont les instructions SQL sont exécutées. La sortie suivante présente un exemple d’exécution d’une instruction EXPLAIN.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Comme le montre cet exemple, *key* a la valeur NULL. Cette sortie indique que MariaDB ne trouve pas d’index optimisés pour la requête et effectue une analyse de table complète. Optimisons cette requête en ajoutant un index dans la colonne **ID**.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

La nouvelle instruction EXPLAIN montre que MariaDB utilise à présent un index pour limiter le nombre de lignes à 1, ce qui réduit nettement le temps de recherche.
 
## <a name="covering-index"></a>Index de couverture
Un index de couverture se compose de toutes les colonnes d’une requête dans l’index pour réduire l’extraction de valeurs dans les tables de données. En voici une illustration dans l’instruction **GROUP BY**.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Comme le montre la sortie, MariaDB n’utilise pas d’index, car il n’en existe aucun qui convienne. Elle indique aussi *Using temporary; Using file sort*, ce qui signifie que MariaDB crée une table temporaire pour satisfaire la clause **GROUP BY**.
 
Le simple fait de créer un index dans la colonne **c2** n’apporte aucune différence, et MariaDB doit quand même créer une table temporaire :

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Dans ce cas, il est possible de créer un **index couvert** dans **c1** et **c2**, moyennant quoi la valeur de **c2** est directement ajoutée dans l’index pour éliminer toute recherche de données supplémentaire.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Comme le montre l’exemple d’instruction EXPLAIN ci-dessus, MariaDB utilise maintenant l’index couvert et empêche la création d’une table temporaire. 

## <a name="combined-index"></a>Index combiné
Un index combiné est constitué de plusieurs colonnes et peut être considéré comme un tableau de lignes triées en concaténant les valeurs des colonnes indexées. Cette méthode peut être utile dans une instruction **GROUP BY**.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MariaDB effectue une opération de *tri de fichiers* qui est relativement lente, en particulier quand le tri porte sur un grand nombre de lignes. Pour optimiser cette requête, un index combiné peut être créé dans les deux colonnes qui font l’objet du tri.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

L’instruction EXPLAIN montre à présent que MariaDB peut utiliser l’index combiné pour empêcher un nouveau tri, car l’index est déjà trié.
 
## <a name="conclusion"></a>Conclusion
 
L’utilisation de l’instruction EXPLAIN et de différents types d’index peut améliorer considérablement les performances. Le fait de disposer d’un index dans la table ne signifie pas nécessairement que MariaDB pourra l’utiliser pour vos requêtes. Validez toujours vos hypothèses en utilisant EXPLAIN et optimisez vos requêtes en utilisant des index.

## <a name="next-steps"></a>Étapes suivantes
- Pour consulter les réponses d’homologues aux questions qui vous préoccupent le plus ou pour poster une nouvelle question/réponse, visitez le [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
