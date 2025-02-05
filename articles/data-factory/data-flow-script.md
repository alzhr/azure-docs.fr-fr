---
title: Script de mappage de flux de données Azure Data Factory
description: Vue d’ensemble du langage code-behind du script de flux de données Azure Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010164"
---
# <a name="data-flow-script-dfs"></a>Script de flux de données (DFS)

Le script de flux de données (DFS) est constitué des métadonnées sous-jacentes, similaires à un langage de codage, qui sont utilisées pour exécuter les transformations incluses dans un flux de données de mappage. Chaque transformation est représentée par une série de propriétés qui fournissent les informations nécessaires à l’exécution correcte du travail. Le script est visible et modifiable à partir de ADF en cliquant sur le bouton « script » sur le ruban supérieur de l’interface utilisateur du navigateur.

![Bouton script](media/data-flow/scriptbutton.png "Bouton Script")

Par exemple, dans une transformation source `allowSchemaDrift: true,` indique au service d’inclure toutes les colonnes du jeu de données source dans le flux de données, même s’ils ne sont pas inclus dans la projection de schéma.

## <a name="use-cases"></a>Cas d'utilisation
Le DFS est généré automatiquement par l’interface utilisateur. Vous pouvez cliquer sur le bouton Script pour afficher et personnaliser le script. Vous pouvez également générer des scripts en dehors de l’interface utilisateur ADF, puis les transmettre à la cmdlet PowerShell. Lors du débogage de flux de données complexes, il peut s’avérer plus facile d’analyser le code-behind de script au lieu d’analyser la représentation graphique d’interface utilisateur de vos flux.

Voici quelques exemples de cas d’usage :
- Générer par programme de nombreux flux de données qui sont assez similaires, par exemple des flux de données d’horodatage.
- Les expressions complexes qui sont difficiles à gérer dans l’interface utilisateur ou qui entraînent des problèmes de validation.
- Déboguer et mieux comprendre les différentes erreurs renvoyées lors de l’exécution.

Quand vous générez un script de flux de données à utiliser avec PowerShell ou une API, vous devez réduire le texte mis en forme pour ne former qu’une seule ligne. Vous pouvez conserver les tabulations et les nouvelles lignes comme des caractères d’échappement. Toutefois, le texte doit être mis en forme pour être intégré à une propriété JSON. Un bouton de l’interface utilisateur de l’éditeur de script situé en bas permet de mettre en forme le script sur une seule ligne.

![Bouton Copier](media/data-flow/copybutton.png "bouton Copier")

## <a name="how-to-add-transforms"></a>Comment ajouter des transformations
L’ajout de transformations requiert trois étapes de base : l’ajout des données de transformation principales, la redirection du flux d’entrée, puis celle du flux de sortie. Un exemple sera plus explicite.
Supposons que nous commençons avec une source simple pour recevoir le flux de données comme suit :

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Si nous décidons d’ajouter une transformation de dérivation, nous devons tout d’abord créer le texte de transformation principal comportant une expression simple permettant d’ajouter une nouvelle colonne en majuscules appelée `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Ensuite, nous prenons le DFS existant et ajoutons la transformation :
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Nous allons à présent rediriger le flux entrant en identifiant la transformation à la suite de laquelle nous voulons intégrer la nouvelle transformation (dans ce cas, `source1`) et en copiant le nom du flux dans la nouvelle transformation :
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Enfin, nous identifions la transformation à intégrer à la suite de cette nouvelle transformation et remplaçons son flux d’entrée (dans ce cas, `sink1`) par le nom du flux de sortie de notre nouvelle transformation :
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Notions de base du DFS
Le DFS est constitué d’une série de transformations connectées, y compris de sources, de récepteurs et d’autres éléments permettant d’ajouter de nouvelles colonnes, de filtrer des données, de joindre des données, et plus encore. En règle générale, le script commence parc une ou plusieurs sources suivies par de nombreuses transformations et se termine par un ou plusieurs récepteurs.

Les sources ont toutes la même construction de base :
```
source(
  source properties
) ~> source_name
```

Par exemple, une source simple dotée de trois colonnes (movieId, title, genres) serait la suivante :
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Toutes les transformations autres que les sources ont la même construction de base :
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Par exemple, une transformation de dérivation simple qui prend une colonne (titre) et la remplace par une version en majuscules serait la suivante :
```
source1 derive(
  title = upper(title)
) ~> derive1
```

Et un récepteur sans schéma ressemblerait simplement à ce qui suit :
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les flux de données en commençant par l’[article de présentation des flux de données](concepts-data-flow-overview.md)
