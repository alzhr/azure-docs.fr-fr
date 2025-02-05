---
title: Configurer les applications PHP - Azure App Service | Microsoft Docs
description: En savoir plus sur la configuration d'applications PHP dans Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 279660d903b3b0e893c3ccddb89da7c6dc42fa09
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205070"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurer une application PHP Linux pour Azure App Service

Ce guide vous explique comment configurer le runtime PHP intégré pour les applications web, back-ends mobiles et applications API dans Azure App Service.

Ce guide fournit des concepts et des instructions clés aux développeurs PHP qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par suivre le [démarrage rapide PHP](quickstart-php.md) et le [tutoriel PHP avec MySQL](tutorial-php-mysql-app.md).

## <a name="show-php-version"></a>Afficher la version PHP

Pour afficher la version actuelle de PHP, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher toutes les versions prises en charge de PHP, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Définir la version PHP

Exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) pour définir la version 7.2 de PHP :

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Exécuter Composer

Par défaut, Kudu n’exécute pas [Composer](https://getcomposer.org/). Pour activer l’automatisation de Composer lors du déploiement de Kudu, vous devez fournir un [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Dans une fenêtre de terminal local, remplacez le répertoire par la racine du référentiel. Suivez le [étapes d’installation de ligne de commande](https://getcomposer.org/download/) pour télécharger *composer.phar*.

Exécutez les commandes suivantes :

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

La racine du référentiel affiche maintenant deux nouveaux fichiers en plus de *composer.phar* : *.deployment* et *deploy.sh*. Ces fichiers fonctionnent pour les versions Windows et Linux d’App Service.

Ouvrez *deploy.sh* et recherchez la section `Deployment`. Remplacez l'intégralité de cette section par le code suivant :

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Validez toutes vos modifications et redéployez votre code. Composer doit maintenant s’exécuter dans le cadre de l’automatisation du déploiement.

## <a name="customize-start-up"></a>Personnaliser le démarrage

Par défaut, le conteneur PHP intégré exécute le serveur Apache. Au démarrage, il s’exécute `apache2ctl -D FOREGROUND"`. Vous pouvez également exécuter une commande différente au démarrage, en exécutant la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) en dehors de votre code d’application. Vous pouvez ensuite y accéder à l’aide du modèle standard [getenv()](https://secure.php.net/manual/function.getenv.php). Par exemple, pour accéder à un paramètre d’application nommé `DB_HOST`, utilisez le code suivant :

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Modifier la racine du site

L’infrastructure web de votre choix peut utiliser un sous-répertoire en tant que racine du site. Par exemple, [Laravel](https://laravel.com/) utilise le sous-répertoire `public/` en tant que racine du site.

L’image par défaut PHP pour App Service utilise Apache, et ne vous permet pas de personnaliser la racine du site pour votre application. Pour contourner cette limitation, ajoutez un fichier *.htaccess* à la racine du référentiel avec le contenu suivant :

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Si vous préférez ne pas utiliser la réécriture *.htaccess*, vous pouvez à la place déployer votre application Laravel avec une [image Docker personnalisée](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application doit vérifier si les requêtes utilisateur sont chiffrées ou non, inspectez l’en-tête `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Les frameworks web populaires vous permettent d’accéder aux informations `X-Forwarded-*` dans votre modèle d’application standard. Dans [CodeIgniter](https://codeigniter.com/), [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) vérifie la valeur de `X_FORWARDED_PROTO` par défaut.

## <a name="customize-phpini-settings"></a>Personnaliser les paramètres php.ini

S'il vous faut apporter des modifications à votre installation PHP, vous pouvez modifier les [directives php.ini](https://www.php.net/manual/ini.list.php) en suivant ces étapes.

> [!NOTE]
> La meilleure façon de consulter la version de PHP et la configuration actuelle de *php.ini* consiste à appeler [phpinfo()](https://php.net/manual/function.phpinfo.php) dans votre application.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Directives Customize-non-PHP_INI_SYSTEM

Pour personnaliser les directives PHP_INI_USER, PHP_INI_PERDIR et PHP_INI_ALL (consultez les [directives php.ini](https://www.php.net/manual/ini.list.php)), ajoutez un fichier *.htaccess* au répertoire racine de votre application.

Dans le fichier *.htaccess*, ajoutez les directives à l’aide de la syntaxe `php_value <directive-name> <value>`. Par exemple :

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Redéployez votre application avec les modifications et redémarrez-la. Si vous la déployez avec Kudu (à l’aide de [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), par exemple), elle est automatiquement redémarrée au terme du déploiement.

En guise d'alternative à *.htaccess*, vous pouvez utiliser [ini_set()](https://www.php.net/manual/function.ini-set.php) dans votre application pour personnaliser ces directives non-PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a>Personnaliser les directives PHP_INI_SYSTEM

Pour personnaliser les directives PHP_INI_SYSTEM (consultez les [directives php.ini](https://www.php.net/manual/ini.list.php)), vous ne pouvez pas utiliser l'approche *.htaccess*. App Service propose un mécanisme distinct utilisant le paramètre d’application `PHP_INI_SCAN_DIR`.

Commencez par exécuter la commande suivante dans [Cloud Shell](https://shell.azure.com) pour ajouter un paramètre d'application appelé `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` correspond au répertoire par défaut dans lequel se trouve *php.ini*. `/home/site/ini` correspond au répertoire personnalisé dans lequel vous ajouterez un fichier *.ini* personnalisé. Séparez les valeurs par `:`.

Accédez à la session SSH web avec votre conteneur Linux (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Créez un répertoire dans `/home/site` appelé `ini`, puis créez un fichier *.ini* dans le répertoire `/home/site/ini` (par exemple, *settings.ini)* avec les directives que vous souhaitez personnaliser. Utilisez la même syntaxe que pour un fichier *php.ini*. 

> [!TIP]
> Dans les conteneurs Linux intégrés dans App Service, */home* est utilisé en tant que stockage partagé persistant. 
>

Par exemple, pour modifier la valeur de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php), exécutez les commandes suivantes :

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Redémarrez l'application pour que les modifications soient prises en compte.

## <a name="enable-php-extensions"></a>Activer les extensions PHP

Les installations PHP intégrées contiennent les extensions les plus fréquemment utilisées. Vous pouvez activer des extensions supplémentaires de la même manière que vous [personnalisez les directives php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> La meilleure façon de consulter la version de PHP et la configuration actuelle de *php.ini* consiste à appeler [phpinfo()](https://php.net/manual/function.phpinfo.php) dans votre application.
>

Pour activer des extensions supplémentaires, effectuez les étapes suivantes :

Ajoutez un répertoire `bin` au répertoire racine de votre application et placez-y les fichiers d'extension `.so` (par exemple, *mongodb.so*). Assurez-vous que les extensions sont compatibles avec la version de PHP dans Azure, ainsi qu'avec VC9 et NTS (Non-Thread Safe).

Déployez vos modifications.

Suivez les étapes décrites dans [Personnaliser les directives PHP_INI_SYSTEM](#customize-php_ini_system-directives), ajoutez les extensions au fichier *.ini* personnalisé avec l'[extension](https://www.php.net/manual/ini.core.php#ini.extension) ou les directives [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Redémarrez l'application pour que les modifications soient prises en compte.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si une application PHP se comporte différemment dans App Service ou présente des erreurs, essayez ce qui suit :

- [Accéder au flux de journaux](#access-diagnostic-logs).
- Testez l’application localement en mode de production. App Service exécute vos applications Node.js en mode de production et dès lors, vous devez vous assurer que votre projet fonctionne comme prévu en mode de production localement. Par exemple :
    - Selon votre fichier *composer.json*, différents packages peuvent être installés pour le mode de production (`require` et `require-dev`).
    - Certaines infrastructures web peuvent déployer des fichiers statiques différemment en mode de production.
    - Certaines infrastructures web peuvent utiliser des scripts de démarrage personnalisés lorsqu'elles s'exécutent en mode de production.
- Dans App Service, exécutez votre application en mode de débogage. Par exemple, dans [Laravel](https://meanjs.org/), vous pouvez configurer votre application de manière à envoyer les messages de débogage en production en [définissant le paramètre d'application `APP_DEBUG` sur `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

Le message suivant peut s'afficher dans les journaux du conteneur :

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Vous pouvez sans risque ignorer ce message. `/robots933456.txt` est un chemin d’URL factice utilisé par App Service pour vérifier si le conteneur est capable de traiter des requêtes. Une réponse 404 indique que le chemin d’accès n’existe pas, mais informe App Service que le conteneur est intègre et prêt à répondre aux requêtes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application PHP avec MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)
