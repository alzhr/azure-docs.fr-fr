---
title: 'Démarrage rapide : Bibliothèque de stockage d’objets Blob Azure v12 - Python'
description: Dans ce guide de démarrage rapide, vous apprenez à utiliser la bibliothèque cliente Stockage Blob Azure version 12 pour Python afin de créer un conteneur et un objet blob dans le stockage (d’objets) blob. Vous apprenez ensuite à télécharger l’objet blob sur votre ordinateur local et à lister tous les objets blob dans un conteneur.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: d589215cf79154bcc8aead1d6695bd4cf870fc0a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423975"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Démarrage rapide : Bibliothèque cliente Stockage Blob Azure v12 pour Python

Bien démarrer avec la bibliothèque de client Stockage Blob Azure v12 pour Python. Le stockage Blob Azure est la solution de stockage d’objet de Microsoft pour le cloud. Suivez les étapes pour installer le package et essayer l’exemple de code pour les tâches de base. Le stockage Blob est optimisé pour stocker de grandes quantités de données non structurées.

> [!NOTE]
> Pour commencer à utiliser la version précédente du kit de développement logiciel (SDK), consultez [Démarrage rapide : Bibliothèque cliente Stockage Blob Azure pour Python](storage-quickstart-blobs-python-legacy.md).

Utilisez la bibliothèque cliente Stockage Blob Azure afin de :

* Créez un conteneur.
* Charger un blob dans le stockage Azure
* Lister tous les objets blob d’un conteneur
* Télécharger l’objet blob sur votre ordinateur local
* Supprimer un conteneur

[Documentation de référence de l’API](/python/api/azure-storage-blob) | [Code source bibliothèqueC](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [Package (Index package Python)](https://pypi.org/project/azure-storage-blob/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
* Compte de stockage Azure : [créez un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) pour votre système d’exploitation - 2.7, 3.5 ou version ultérieure

## <a name="setting-up"></a>Configuration

Cette section vous guide tout au long de la préparation d’un projet à utiliser avec la bibliothèque cliente Stockage Blob Azure v12 pour Python.

### <a name="create-the-project"></a>Créer le projet

Créez une application Python nommée *blob-quickstart-v12*.

1. Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour le projet.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Basculez vers le répertoire *blob-quickstart-v12* nouvellement créé.

    ```console
    cd blob-quickstart-v12
    ```

1. Dans le répertoire *blob-quickstart-v12*, créez un autre répertoire appelé *data*. C’est là que les fichiers de données blob seront créés et stockés.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installer le package

Alors que vous êtes toujours dans le répertoire de l’application, installez le package de la bibliothèque cliente Stockage Blob Azure pour Python à l’aide de la commande `pip install`.

```console
pip install azure-storage-blob
```

Cette commande installe la bibliothèque cliente de stockage d’objets Blob Azure pour le package Python et toutes les bibliothèques dont elle dépend. Dans ce cas, il s’agit simplement de la bibliothèque Azure Core pour Python.

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez un nouveau fichier texte dans votre éditeur de code
1. Ajoutez les instructions `import`
1. Créez la structure du programme, y compris la gestion des exceptions de base

    Voici le code :

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Enregistrez le nouveau fichier sous *blob-quickstart-v12.py* dans le répertoire *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-connection-string-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modèle objet

Le Stockage Blob Azure est optimisé pour stocker de grandes quantités de données non structurées. Les données non structurées sont des données qui n’obéissent pas à un modèle ou une définition de données en particulier, comme des données texte ou binaires. Le stockage Blob offre trois types de ressources :

* Le compte de stockage
* Un conteneur dans le compte de stockage.
* Un blob dans le conteneur

Le diagramme suivant montre la relation entre ces ressources.

![Diagramme de l’architecture du stockage Blob](./media/storage-blob-introduction/blob1.png)

Utilisez les classes Python suivantes pour interagir avec ces ressources :

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) : La classe `BlobServiceClient` vous permet de manipuler les ressources de stockage Azure et les conteneurs blob.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient) : La classe `ContainerClient` vous permet de manipuler des conteneurs de stockage Azure et leurs blobs.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) : La classe `BlobClient` vous permet de manipuler des blobs de stockage Azure.

## <a name="code-examples"></a>Exemples de code

Ces exemples d’extraits de code vous montrent comment effectuer les opérations suivantes avec la bibliothèque cliente Stockage Blob Azure pour Python :

* [Obtenir la chaîne de connexion](#get-the-connection-string)
* [Créer un conteneur](#create-a-container)
* [Charger des objets blob sur un conteneur](#upload-blobs-to-a-container)
* [Lister les objets blob d’un conteneur](#list-the-blobs-in-a-container)
* [Télécharger des objets blob](#download-blobs)
* [Supprimer un conteneur](#delete-a-container)

### <a name="get-the-connection-string"></a>Obtenir la chaîne de connexion

Le code ci-dessous récupère la chaîne de connexion pour le compte de stockage à partir de la variable d’environnement créée dans la section [Configurer votre chaîne de connexion de stockage](#configure-your-storage-connection-string).

Ajoutez ce code dans le bloc `try` :

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Créez un conteneur.

Choisissez un nom pour le nouveau conteneur. Le code ci-dessous ajoute une valeur UUID au nom du conteneur pour s’assurer qu’il est unique.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur l’affectation de noms aux conteneurs et objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Créez une instance de la classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) en appelant la méthode [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-). Ensuite, appelez la méthode [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) pour effectivement créer le conteneur dans votre compte de stockage.

Ajoutez ce code à la fin du bloc `try` :

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Charger des objets blob sur un conteneur

L’extrait de code suivant :

1. Crée un fichier texte dans le répertoire local.
1. Obtient une référence à un objet [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) en appelant la méthode [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) sur le [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) à partir de la section [Créer un conteneur](#create-a-container).
1. Charge le fichier texte local dans l’objet Blob en appelant la méthode [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-).

Ajoutez ce code à la fin du bloc `try` :

```python
# Create a file in local Documents directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Répertoriez les objets Blob dans le conteneur en appelant la méthode [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-). Dans ce cas, un seul objet blob a été ajouté au conteneur. Il n’y a donc qu’un objet blob répertorié.

Ajoutez ce code à la fin du bloc `try` :

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez l’objet Blob créé précédemment en appelant la méthode [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-). L’exemple de code ajoute le suffixe « DOWNLOAD » au nom de fichier afin que vous puissiez voir les deux fichiers dans votre système de fichiers local.

Ajoutez ce code à la fin du bloc `try` :

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Supprimer un conteneur

Le code suivant nettoie les ressources créées par l’application en supprimant l’ensemble du conteneur avec la méthode [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). Si vous voulez, vous pouvez aussi supprimer les fichiers locaux.

L’application s’arrête pour une entrée d’utilisateur en appelant `input()` avant de supprimer l’objet Blob, le conteneur et les fichiers locaux. C’est une bonne occasion de vérifier que les ressources ont bien été créées avant d’être supprimées.

Ajoutez ce code à la fin du bloc `try` :

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Exécuter le code

Cette application crée un fichier de test dans votre dossier local et le charge sur un stockage blob. L’exemple liste ensuite les objets blob du conteneur et télécharge le fichier avec un nouveau nom pour que vous puissiez comparer les deux fichiers.

Accédez au répertoire contenant le fichier *blob-quickstart-v12.py*, puis exécutez la commande `python` suivante pour exécuter l’application.

```console
python blob-quickstart-v12.py
```

Le résultat de l’application ressemble à l’exemple suivant :

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Avant de commencer le processus de nettoyage, consultez les deux fichiers dans votre dossier *Documents*. Vous pouvez les ouvrir et constater qu’ils sont identiques.

Une fois les fichiers vérifiés, appuyez sur **Entrée** pour supprimer les fichiers de test et terminer la démonstration.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à charger, télécharger et répertorier des objets blob avec Python.

Pour voir des exemples d’applications de stockage d’objets Blob, passez à :

> [!div class="nextstepaction"]
> [Exemples Python du kit de développement logiciel (SDK) du stockage d’objets Blob Azure v12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Pour plus d’informations, consultez le [Kit de développement logiciel (SDK) Azure pour Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Pour obtenir des didacticiels, des exemples, des démarrages rapides et d’autres documents, visitez [Azure pour les développeurs Python](/azure/python/).
