---
title: "Creación, configuración e implementación de una aplicación web PHP en Azure | Microsoft Docs"
description: "Un tutorial que muestra cómo hacer que una aplicación web PHP (Laravel) se ejecute en el Servicio de aplicaciones de Azure. Obtenga información acerca de cómo configurar el Servicio de aplicaciones de Azure para que cumpla los requisitos del marco de trabajo PHP que elija."
services: app-service\web
documentationcenter: php
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: cb73859d-48aa-470a-b486-d984746d6d26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 487fde71fb28d5ee4666be208d3acfc0de27bed9
ms.lasthandoff: 03/01/2017


---
# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>Creación, configuración e implementación de una aplicación web PHP en Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial explica cómo crear, configurar e implementar una aplicación web PHP de Azure y cómo configurar el Servicio de aplicaciones de Azure para cumplir los requisitos de la aplicación web PHP. Al final del tutorial, se ejecutará una aplicación web [Laravel](https://www.laravel.com/) en vivo en [Azure App Service](../app-service/app-service-value-prop-what-is.md).

Como desarrollador de PHP, puede llevar su marco de trabajo PHP favorito a Azure. Este tutorial usa Laravel simplemente como un ejemplo de aplicación concreta. Aprenderá a: 

* Realizar la implementación mediante Git
* Establecer la versión de PHP
* Usar un archivo de inicio que no está en el directorio raíz de la aplicación
* Acceder a variables de entorno específico
* Actualizar la aplicación en Azure.

Puede aplicar lo que aprenda aquí a otras aplicaciones web PHP que implemente en Azure.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](app-service-web-php-get-started-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0](app-service-web-php-get-started.md): la CLI de última generación para el modelo de implementación de administración de recursos

## <a name="prerequisites"></a>Requisitos previos
* [PHP 7.0](http://php.net/downloads.php)
* [Compositor](https://getcomposer.org/download/)
* [Versión preliminar de la CLI de Azure 2.0](/cli/azure/install-az-cli2)
* [Git](http://www.git-scm.com/downloads)
* Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Vea una aplicación web en acción. [Pruebe el Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/) inmediatamente y cree una aplicación de inicio de corta duración; no se requiere tarjeta de crédito ni se establece ningún compromiso.
> 
> 

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>Creación de una aplicación PHP (Laravel) en el equipo de desarrollo
1. Abra un símbolo del sistema de Windows, una ventana de PowerShell, un shell de Linux o un terminal de OS X. Ejecute los comandos siguientes para comprobar que las herramientas necesarias están instaladas correctamente en el equipo. 
   
        php --version
        composer --version
        az --version
        git --version
   
    Si no ha instalado las herramientas, consulte [Requisitos previos](#Prerequisites) para obtener vínculos de descarga.

2. Instale Laravel de la siguiente manera:
   
        composer global require "laravel/installer"

3. `CD` en un directorio de trabajo y cree una nueva aplicación de Laravel de este modo:
   
        cd <working_directory>
        laravel new <app_name>
4. `CD` en el directorio `<app_name>` recién creado y pruebe la aplicación de esta forma:
   
        cd <app_name>
        php artisan serve
   
    Ahora debería poder navegar a http://localhost:8000 en un explorador y ver la pantalla de presentación de Laravel.
   
    ![Pruebe la aplicación PHP (Laravel) localmente antes de implementarla en Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)

1. Inicie un repositorio Git y confirme todo el código:

        git init
        git add .
        git commit -m "Hurray! My first commit for my Azure web app!"

Hasta ahora, se trata del flujo de trabajo normal de Laravel y Git, y no se encuentra aquí para <a href="https://laravel.com/docs/5.3" rel="nofollow">aprender a manejar Laravel</a>. Así que continuemos.

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Creación de una aplicación web de Azure y configuración de la implementación de Git
> [!NOTE]
> "Espere. ¿Qué ocurre si deseo implementar mediante FTP?" Hay un [tutorial de FTP](web-sites-php-mysql-deploy-use-ftp.md), por si lo necesita. 
> 
> 

Con la CLI de Azure, puede crear una aplicación web en el Servicio de aplicaciones de Azure y configurarla para la implementación de Git con solo una línea de comandos. Hagámoslo.

1. Inicie sesión en Azure como se indica a continuación:
   
        az login
   
    Siga el mensaje de ayuda para continuar con el proceso de inicio de sesión.
   
3. Establezca el usuario de implementación de App Service. Posteriormente implementará el código mediante estas credenciales.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Cree un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este tutorial de PHP, no necesita realmente saber qué es.

        az group create --location "<location>" --name my-php-app-group

    Para ver los posibles valores que puede usar para `<location>`, use el comando de la CLI `az appservice list-locations`.

3. Cree un nuevo [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATIS". Para este tutorial de PHP, sepa que no se le cobrará por las aplicaciones web de este plan.

        az appservice plan create --name my-php-appservice-plan --resource-group my-php-app-group --sku FREE

4. Cree una nueva aplicación web con un nombre único en `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-php-app-group --plan my-php-appservice-plan

5. Configure la implementación local de Git para la nueva aplicación web con el siguiente comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-php-app-group

    Obtendrá una salida JSON como esta, lo que significa que está configurado el repositorio de Git remoto:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Agregue la dirección URL en la salida JSON como un repositorio de Git remoto para el repositorio local (llamado `azure` para mayor claridad).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>Configuración de la aplicación web de Azure
Para que la aplicación Laravel funcione en Azure, debe prestar atención a varias cosas. Llevará a cabo este ejercicio con el marco de trabajo PHP de su elección de forma similar.

* Configure la versión PHP 5.6.4 o superior. Consulte [Latest Laravel 5.3 Server Requirements](https://laravel.com/docs/5.3#server-requirements) (Requisitos más recientes del servidor de Laravel 5.3) para obtener la lista entera de requisitos del servidor. El resto de la lista son extensiones que ya se han habilitado mediante las instalaciones de PHP de Azure. 
* Establezca las variables de entorno que la aplicación necesita. Laravel utiliza el archivo `.env` para facilitar la configuración de las variables de entorno. Sin embargo, dado que se supone que no se ha ejecutado el control de código fuente, consulte [Laravel Environment Configuration](https://laravel.com/docs/5.3/configuration#environment-configuration)(Configuración del entorno de Laravel), para establecer la configuración de la aplicación web de Azure en su lugar.
* Asegúrese de que el punto de entrada de la aplicación Laravel, `public/index.php`, se carga en primer lugar. Consulte [Laravel Lifecycle Overview](https://laravel.com/docs/5.3/lifecycle#lifecycle-overview)(Información general sobre el ciclo de vida de Laravel). En otras palabras, debe establecer la dirección URL raíz de la aplicación web para que apunte al directorio `public` .
* Habilite la extensión de Composer en Azure, ya que tiene un archivo composer.json. De este modo, puede dejar que Composer se preocupe de cómo obtener los paquetes necesarios al implementar con `git push`. Es una cuestión de comodidad. 
  Si no habilita la automatización de Composer, bastará con quitar `/vendor` desde el archivo `.gitignore` para que Git incluya ("no ignore") todo el contenido del directorio `vendor` al ejecutar e implementar el código.

Vamos a configurar estas tareas de forma secuencial.

1. Establezca la versión de PHP que requiere la aplicación Laravel.
   
        az appservice web config update --php-version 7.0 --name <app_name> --resource-group my-php-app-group
   
    Con esto, ya ha terminado la configuración de la versión de PHP. 

2. Genere una nueva `APP_KEY` para la aplicación web de Azure y establézcala como configuración de esta aplicación.
   
        php artisan key:generate --show
        az appservice web config appsettings update --settings APP_KEY="<output_of_php_artisan_key:generate_--show>" --name <app_name> --resource-group my-php-app-group

3. Además, active la depuración de Laravel para reemplazar la típica página críptica de `Whoops, looks like something went wrong.` .
   
        az appservice web config appsettings update --settings APP_DEBUG=true --name <app_name> --resource-group my-php-app-group
   
    Con esto, ya ha terminado la configuración de las variables de entorno.
   
   > [!NOTE]
   > Espere, vamos a detenernos un momento y a explicar lo que hace Laravel y lo que hace Azure aquí. Laravel utiliza el archivo `.env` del directorio raíz para proporcionar variables de entorno a la aplicación, en la que encontrará la línea `APP_DEBUG=true` (y también la línea `APP_KEY=...`). A esta variable se accede en `config/app.php` mediante el código `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.3/helpers#method-env) es un método auxiliar de Laravel que utiliza el comando PHP [getenv()](http://php.net/manual/en/function.getenv.php) en segundo plano.
   > 
   > Sin embargo, Git omite `.env` ya que lo ha llamado el archivo `.gitignore` del directorio raíz. En pocas palabras, el comando `.env` del repositorio local de Git no se inserta en Azure con el resto de los archivos. Por supuesto, puede simplemente quitar esa línea desde `.gitignore`, pero ya hemos visto que no es recomendable ejecutar este archivo en el control de código fuente. No obstante, necesitará una manera de especificar estas variables de entorno en Azure. 
   > 
   > La buena noticia es que la configuración de la aplicación de Servicio de aplicaciones de Azure es compatible con [getenv()](http://php.net/manual/en/function.getenv.php) en PHP. Así, aunque puede usar FTP u otros medios para cargar manualmente un archivo `.env` en Azure, basta con especificar las variables que desea como configuración de la aplicación sin tener que cargar el archivo `.env` en Azure, como acaba de hacer. Es más, si la variable está en un archivo `.env` y en la configuración de la aplicación de Azure, esta última tiene prioridad.     
   > 
   > 
4. Para las dos últimas tareas (configuración del directorio virtual y habilitación de Composer) se requiere [Azure Portal](https://portal.azure.com), así que inicie sesión en el [portal](https://portal.azure.com) con su cuenta de Azure.
5. Desde el menú de la izquierda, haga clic en **App Services** > **&lt;app_name > ** > **Extensiones**.
   
    ![Habilite Composer para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
6. Haga clic en **Agregar** para agregar una extensión.
7. Seleccione **Composer** en la **hoja** [Elegir extensión](../azure-resource-manager/resource-group-portal.md#manage-resources) (*hoja*: una página del portal que se abre horizontalmente).
8. Haga clic en **Aceptar** en la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources) **Aceptar los términos legales**. 
9. Haga clic en **Aceptar** en la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources) **Agregar extensión**.
   
    Cuando Azure haya terminado de agregar la extensión, debería ver un mensaje descriptivo emergente en la esquina, y también aparecerá  **Composer** en la lista de la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources) **Extensiones**.
   
    ![Hoja de extensiones después de habilitar Composer para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    Con esto, ya ha terminado la habilitación de Composer.
10. De nuevo en la [hoja de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de la aplicación web, haga clic en **Configuración de la aplicación**.
    
     ![Acceda a la hoja Configuración para configurar el directorio virtual para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     En la **hoja** [Configuración de la aplicación](../azure-resource-manager/resource-group-portal.md#manage-resources), tenga en cuenta la versión de PHP que estableció anteriormente:
    
     ![Versión de PHP en la hoja Configuración de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a-cli2.png)
    
     y la configuración de la aplicación agregada:
    
     ![Configuración de la aplicación en la hoja Configuración de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. Desplácese hasta la parte inferior de la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources) y cambie el directorio raíz virtual para que apunte a **site\wwwroot\public**, en lugar de a **site\wwwroot**.
    
     ![Configure el directorio virtual para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. Haga clic en **Guardar** en la parte superior de la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources).
    
     Con esto, ya ha terminado la configuración del directorio virtual. 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>Implementación de la aplicación web con Git (y configuración de las variables de entorno)
Ya está listo para implementar el código. Podrá hacerlo cuando vuelva al símbolo del sistema o terminal.

1. Inserte el código en la aplicación web de Azure como lo haría para cualquier repositorio de Git:
   
        git push azure master 
   
    Cuando se le solicite, inicie sesión con las credenciales que ha creado antes.

2. Podemos ver la ejecución en el explorador, ejecutando este comando:
   
        az appservice web browse --name <app_name> --resource-group my-php-app-group
   
    El explorador debe mostrarle la pantalla de presentación de Laravel.
   
    ![Pantalla de presentación de Laravel después de implementar la aplicación web en Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
   
    Enhorabuena, ahora ya está ejecutando una aplicación web de Laravel en Azure.

## <a name="troubleshoot-common-errors"></a>Solución de errores comunes
Aquí puede ver algunos de los errores que pueden aparecer al seguir este tutorial:

* [La CLI de Azure muestra el mensaje "'site' is not an azure command" ('site' no es un comando de Azure)](#clierror)
* [La aplicación web muestra el error HTTP 403](#http403)
* [La aplicación web muestra el mensaje "Vaya, parece que hubo algún error".](#whoops)
* [La aplicación web muestra el mensaje "No supported encryptor found." (No se encontró ningún sistema de cifrado compatible).](#encryptor)

<a name="clierror"></a>

### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>La CLI de Azure muestra el mensaje "'site' is not an azure command" ('site' no es un comando de Azure)
Cuando se ejecuta `azure site *` en el terminal de la línea de comandos, puede ver el error `error:   'site' is not an azure command. See 'azure help'.` 

Por lo general, suele ser el resultado de cambiar al modo "ARM" (Azure Resource Manager). Para resolver este problema, vuelva a cambiar al modo "ASM" (Azure Resource Manager) mediante la ejecución de `azure config mode asm`.

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>La aplicación web muestra el error HTTP 403
Ha implementado correctamente la aplicación web en Azure, pero cuando va a la aplicación, obtiene un error `HTTP 403` o `You do not have permission to view this directory or page.`

Es probable que la aplicación web no pueda encontrar el punto de entrada a la aplicación Laravel. Asegúrese de que ha cambiado el directorio raíz virtual para que apunte a `site\wwwroot\public`, donde está el archivo `index.php` de Laravel (consulte [Configuración de la aplicación web de Azure](#configure)).

<a name="whoops"></a>

### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>La aplicación web muestra el mensaje "Vaya, parece que hubo algún error".
Ha implementado correctamente la aplicación web en Azure, pero cuando va a la aplicación, obtiene el críptico mensaje `Whoops, looks like something went wrong.`

Para obtener un error más descriptivo, habilite la depuración de Laravel, para lo que debe establecer la variable de entorno `APP_DEBUG` en `true` (consulte [Configuración de la aplicación web de Azure](#configure)).

<a name="encryptor"></a>

### <a name="web-app-shows-no-supported-encryptor-found"></a>La aplicación web muestra el mensaje "No supported encryptor found." (No se encontró ningún sistema de cifrado compatible).
Ha implementado correctamente la aplicación web en Azure, pero cuando va a la aplicación, obtiene el mensaje de error siguiente:

![No se encuentra APP_KEY en su aplicación PHP (Laravel) de Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

Es un error desagradable, pero al menos no es tan críptico ya que se activó la depuración de Laravel. Una búsqueda rápida de la cadena de error en los foros de Laravel mostrará que esto se debe a que no se ha establecido la APP_KEY en `.env`, o en su caso, a que no tiene el archivo `.env` en Azure en absoluto. Puede solucionar este problema mediante la adición de `APP_KEY` como configuración de aplicación de Azure (consulte [Configuración de la aplicación web de Azure](#configure)).

## <a name="next-steps"></a>Pasos siguientes
Obtenga información acerca de cómo agregar datos a la aplicación mediante la [creación de una base de datos MySQL en Azure](../store-php-create-mysql-database.md). Además, revise vínculos más útiles para PHP en Azure a continuación:

* [Centro para desarrolladores de PHP](/develop/php/)
* [Creación de una aplicación web desde Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
* [Configuración de PHP en Aplicaciones web de Servicio de aplicaciones de Azure](web-sites-php-configure.md)
* [Conversión de WordPress en multisitios en Servicio de aplicaciones de Azure](web-sites-php-convert-wordpress-multisite.md)
* [WordPress de clase empresarial en el Servicio de aplicaciones de Azure](web-sites-php-enterprise-wordpress.md)


