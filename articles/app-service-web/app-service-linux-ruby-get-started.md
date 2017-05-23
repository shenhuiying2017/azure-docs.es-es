---
title: "Creación de una aplicación de Ruby con Web App on Linux de Azure App Service | Microsoft Docs"
description: Aprenda a crear aplicaciones de Ruby con Web App de Azure App Service en Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Creación de una aplicación de Ruby con Web App on Linux de Azure App Service (versión preliminar)

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Información general

En este tutorial se muestra cómo crear una aplicación de Ruby on Rails básica en la aplicación localmente e implementarla en Web App de Azure en Linux.

## <a name="prerequisites"></a>Requisitos previos

* [Ruby 2.3.3 o una versión posterior](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller) instalado en el equipo de desarrollo
* [Git](https://git-scm.com/downloads) instalado en el equipo de desarrollo
* Una [suscripción de Azure activa](https://azure.microsoft.com/pricing/free-trial/)
* Este tutorial se ha redactado en el contexto de un entorno de Ubuntu. Todos los comandos del sistema son específicos de Bash.


## <a name="create-a-new-local-rails-application"></a>Creación de una nueva aplicación de Rails local

1. Cree un directorio para la nueva aplicación y cambie a ese directorio.

        mkdir ~/workspace
        cd ~/workspace

2. Inicialice Ruby y compruebe la versión mediante el comando `ruby -v`.

    ![Inicialización de Ruby](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. Instale Rails mediante el comando `gem install rails`.

    ![Instalación de Rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. Cree una aplicación de Rails denominada "**hello world**" mediante el siguiente comando:

    Si está usando Rails 5.1.0 o posterior, incluya la opción `--skip-yarn` tal como se muestra en el siguiente comando:

        rails new hello-world --skip-yarn

    Para las versiones anteriores a Rails 5.1.0, use el comando siguiente:

        rails new hello-world 

    ![Nueva aplicación Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![Nueva aplicación Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    Si usas Rails 5.1 o una versión posterior, se crea un package.json si la ,opción `--skip-yarn` no se utiliza. No queremos que se incluya en nuestra implementación. Como alternativa, puede eliminar el archivo package.json o agregarlo al archivo *.git-ignore* del directorio siguiente: 

        # Ignore package.json
        /package.json

5. Cambie al directorio *hello world* e inicie el servidor.

        cd hello-world
        rails server

    ![Inicio de Hello world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. Mediante el explorador web, vaya a `http://localhost:3000` para probar la aplicación localmente.    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>Preparación de la aplicación para Azure

De forma predeterminada, la imagen de Ruby ejecuta el servidor con la marca `-e production`. Este entorno requiere configurar ligeramente Web App de Azure en Linux. El contenedor se encarga de algunos pasos de la configuración (por ejemplo, de establecer una `SECRET_KEY_BASE`). Se debe configurar una ruta predeterminada. De lo contrario, recibirá un error 404 al examinar el sitio.

Para configurar una ruta predeterminada, siga estos pasos:

1. Abra *~/workspace/hello-world/config/routes.rb* para editarla. Agregue la siguiente línea como se muestra en la captura de pantalla. 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. Abra *~/workspace/hello-world/app/controllers/application_controller.rb* para editarla. Agregue las siguientes líneas como se muestra en la captura de pantalla.

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. La aplicación ya está configurada. Mediante el explorador web, vaya a `http://localhost:3000` para confirmar la página de aterrizaje de raíz.

    ![Hello World configurada](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Creación de un sitio web de Ruby en Azure

1. Navegue hasta [Azure Portal](http://portal.azure.com) e inicie sesión con su suscripción. Agregue una **aplicación web en Linux** como se muestra en la siguiente captura de pantalla:

    ![Creación de una aplicación web en Linux](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. Aparecerá la hoja **Crear**, como se muestra en la siguiente captura de pantalla:

    ![Hoja de creación](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. Asigne un nombre a la aplicación web.
    2. Elija un grupo de recursos existente o cree uno. (Consulte las regiones disponibles en la [sección de limitaciones](app-service-linux-intro.md)).
    3. Seleccione un plan de Azure App Service existente o cree uno. (Consulte las notas del plan de App Service en la [sección de limitaciones](app-service-linux-intro.md)).
    4. Elija la pila en tiempo de ejecución integrada de **Ruby 2.3** para la configuración del contenedor.
    5. Haga clic en **Anclar al panel** en la aplicación web.
    6. Haga clic en **Crear**.

3. Una vez creada la aplicación web, se mostrará la hoja **Introducción**. Copie la **URL** de la nueva aplicación web y ábrala en el explorador. Se muestra la siguiente página de presentación.

    ![Página de presentación](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Implementación de aplicación

En este tutorial, usamos Git para implementar la aplicación de Ruby local en Azure.

1. El nuevo sitio web de Azure ya tiene una implementación de Git configurada. Encontrará la URL de implementación de Git accediendo a la siguiente dirección URL después de insertar el nombre de la aplicación web:

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    La URL de Git tiene el formato siguiente en función del nombre de la aplicación web:

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. Ejecute los comandos siguientes para implementar la aplicación local en el sitio web de Azure.

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    Confirme que las operaciones de implementación remota se han realizado correctamente.

    ![Implementación de la aplicación web](./media/app-service-linux-ruby-get-started/deployment-success.png)

    Si ve un error que indica que el servidor remoto se ha colgado, es probable que la implementación aún esté en curso. En este caso, vaya a la siguiente URL en el explorador:

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. Una vez que la implementación haya finalizado, reinicie la aplicación web para que la implementación surta efecto. En [Azure Portal](http://portal.azure.com), navegue a la hoja **Introducción** de la aplicación web.

    Haga clic en **Reiniciar** en la barra de herramientas.

    ![Reinicio de una aplicación web](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. Vaya a su sitio y confirmar que las actualizaciones están activas. 

    Mientras se reinicia la aplicación, al tratar acceder al sitio, se producirá un error de código de estado HTTP 503 (servidor no disponible). Puede tardar un par de minutos en reiniciarse completamente.

        http://{your web app name}.azurewebsites.net

    ![Aplicación web actualizada](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Web App de Azure App Service en Linux, vea los vínculos siguientes. También puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creación de aplicaciones web en App Service en Linux](app-service-linux-how-to-create-web-app.md)
* [How to use a custom Docker image for App Service on Linux](app-service-linux-using-custom-docker-image.md) (Uso de una imagen de Docker personalizada para App Service en Linux)
* [Using PM2 Configuration for Node.js in Web Apps on Linux](app-service-linux-using-nodejs-pm2.md) (Uso de la configuración de PM2 para Node.js en Web Apps en Linux)
* [Uso de .NET Core en Azure App Service Web Apps en Linux](app-service-linux-using-dotnetcore.md)
* [Preguntas más frecuentes sobre Azure App Service Web Apps en Linux](app-service-linux-faq.md)


