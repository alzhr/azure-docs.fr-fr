---
title: Référence d’affichage d’un artefact de définition d’applications managées Azure
description: Fournit un exemple d’artefact de définition de vue pour les applications managées Azure. Le nom de fichier est viewDefinition.json.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 1f56f6c9c519bd29423d92a8dc8b8ce5904b523c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332639"
---
# <a name="reference-view-definition-artifact"></a>Référence : Afficher un artefact de définition

Cet article est une référence pour un artefact *viewDefinition.json* dans des applications managées Azure. Pour plus d’informations sur la configuration des vues de création, consultez [Afficher un artefact de définition](concepts-view-definition.md).

## <a name="view-definition"></a>Afficher une définition

Le fichier JSON suivant montre un exemple de fichier *viewDefinition.json* pour les applications managées Azure :

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer une application managée avec des actions et des ressources personnalisées](tutorial-create-managed-app-with-custom-provider.md)
- [Reference : Artefact d’éléments d’interface utilisateur](reference-createuidefinition-artifact.md)
- [Reference : Artefact de modèle de déploiement](reference-main-template-artifact.md)