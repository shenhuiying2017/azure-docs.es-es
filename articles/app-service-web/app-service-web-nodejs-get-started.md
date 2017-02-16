---
title: "Introducción a las aplicaciones web Node.js en Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo implementar una aplicación de Node.js en una aplicación web en el Servicio de aplicaciones de Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 88405a9e67eb748acc9564022283004b5ebfcf48
ms.openlocfilehash: 63210a5539d1e5e5b7d1f5a60048d507e53038a5


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

En este tutorial se muestra cómo crear una aplicación de [Node.js] sencilla e implementarla en [Azure App Service] a partir de un entorno de línea de comandos, como cmd.exe o bash. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.js.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](app-service-web-nodejs-get-started-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](app-service-web-nodejs-get-started.md): la CLI de última generación para el modelo de implementación de Resource Manager

## <a name="prerequisites"></a>Requisitos previos
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Versión preliminar de la CLI de Azure 2.0](/cli/azure/install-az-cli2)
* Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita] o [activar las ventajas de suscriptor de Visual Studio].

> [!NOTE]
> También puede [probar App Service](http://go.microsoft.com/fwlink/?LinkId=523751) sin una cuenta de Azure. Cree una aplicación de inicio y juegue con ella durante una hora como máximo; no se requiere ninguna tarjeta de crédito ni ningún compromiso.
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>Creación y configuración de una aplicación Node.js sencilla para Azure
1. Abra el terminal de la línea de comandos que prefiera e instale el [generador Express de Yeoman].
   
        npm install -g generator-express

2. `CD` , vaya a un directorio de trabajo y genere una aplicación de Express con la siguiente sintaxis:
   
        yo express
   
    Elija las opciones siguientes cuando se le solicite:  
   
    `? Would you like to create a new directory for your project?`**Yes**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **None**  
    `? Select a database to use:` **None**  
    `? Select a build tool to use:` **Grunt**

3. Mediante `CD`, vaya al directorio raíz de la nueva aplicación e iníciela para asegurarse de que se ejecuta en el entorno de desarrollo:
   
        npm start
   
    En el explorador, vaya a <http://localhost:3000> para asegurarse de que puede ver la página principal de Express. Una vez comprobado que la aplicación se ejecuta correctamente, use `Ctrl-C` para detenerla.

6. Abra el archivo ./config/config.js de la raíz de la aplicación y cambie el puerto de producción a `process.env.port`; la propiedad `production` en el objeto `config` debe ser similar a la del ejemplo siguiente:
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > De forma predeterminada, Azure App Service ejecuta aplicaciones Node.js con las variables de entorno `production` (`process.env.NODE_ENV="production"`.
    > Por tanto, esta configuración le permite que la aplicación de Node.js en Azure responda a solicitudes web en el puerto predeterminado en el que escucha iisnode.
    >
    >

7. Abra./package.json y agregue la propiedad `engines` para [especificar la versión de Node.js deseada](#version).
   
        "engines": {
            "node": "6.9.1"
        }, 

8. Guarde los cambios y, después, inicialice un repositorio Git en la raíz de la aplicación y confirme el código:
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>Implementación de la aplicación Node.js en Azure

1. Inicie sesión en Azure (necesita la [versión preliminar de CLI de Azure 2.0](#prereq)):
   
        az login
   
    Siga las indicaciones para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tenga su suscripción de Azure.

3. Establezca el usuario de implementación de App Service. Posteriormente implementará el código mediante estas credenciales.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Cree un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este tutorial de node.js, no es preciso saber qué es.

        az group create --location "<location>" --name my-nodejs-app-group

    Para ver los posibles valores que puede usar para `<location>`, use el comando de la CLI `az appservice list-locations`.

3. Cree un nuevo [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATIS". Para este tutorial de node.js, solo es preciso saber que en este plan no se cobrarán las aplicaciones web.

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. Cree una nueva aplicación web con un nombre único en `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. Configure la implementación local de Git para la nueva aplicación web con el siguiente comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    Obtendrá una salida JSON como esta, lo que significa que está configurado el repositorio de Git remoto:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Agregue la dirección URL en la salida JSON como un repositorio de Git remoto para el repositorio local (llamado `azure` para mayor claridad).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implemente el código de ejemplo para el repositorio de Git remoto `azure`. Cuando se le pida, use las credenciales de implementación que configuró anteriormente.

        git push azure master
   
    El generador Express ya proporciona un archivo .gitignore, por lo que su comando `git push` no consume ancho de banda al intentar cargar el directorio node_modules/.

9. Por último, inicie la aplicación de Azure activa en el explorador:
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    Ahora debería ver la aplicación web de Node.js que se ejecuta directamente en el Servicio de aplicaciones de Azure.
   
    ![Ejemplo de exploración de la aplicación implementada.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Actualización de la aplicación web Node.js
Para realizar actualizaciones en su aplicación web de Node.js que se ejecuta en el Servicio de aplicaciones, solo tiene que ejecutar `git add`, `git commit` y `git push` como hizo cuando implementó por primera vez su aplicación web.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Modo en que el Servicio de aplicaciones implementa la aplicación de Node.js
El Servicio de aplicaciones de Azure usa [iisnode] para ejecutar aplicaciones de Node.js. La versión preliminar de la CLI de Azure 2.0 y el motor de Kudu (implementación de Git) colaboran para proporcionar una experiencia simplificada al desarrollar e implementar aplicaciones de Node.js desde la línea de comandos. 

* Puede crear un archivo iisnode.yml en el directorio raíz y usarlo para personalizar las propiedades de iisnode. Todos los valores configurables se documentan [aquí](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml).
* En `git push azure master`, Kudu automatiza las tareas de implementación siguientes:
  
  * Si package.json está en la raíz del repositorio, ejecute `npm install --production`.
  * Genere un archivo web.config para iisnode que apunte al script de inicio en package.json (por ejemplo, server.js o app.js).
  * Personalice el archivo web.config para preparar la aplicación para la depuración con Node-Inspector.

## <a name="use-a-nodejs-framework"></a>Uso de una plataforma de Node.js
Si utiliza una plataforma de Node.js popular para desarrollar las aplicaciones, como [Sails.js][SAILSJS] o [MEAN.js][MEANJS], puede implementarla en App Service. Las plataformas de Node.js populares tienen sus peculiaridades y sus dependencias de paquete se actualizan constantemente. Sin embargo, App Service hace que los registros de stdout y stderr estén disponibles, para que pueda saber exactamente lo que sucede con su aplicación y realizar cambios según corresponda. Para más información, consulte [Obtención de registros de stdout y stderr desde iisnode](#iisnodelog).

Los tutoriales siguientes le mostrarán cómo trabajar con una plataforma específica en el Servicio de aplicaciones:

* [Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure]
* [Creación de una aplicación de chat Node.js con Socket.IO en el Servicio de aplicaciones de Azure]
* [Uso de io.js con aplicaciones web del Servicio de aplicaciones de Azure]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Uso de un motor de Node.js específico
En el flujo de trabajo típico, puede indicar a App Service que use un motor de Node.js específico igual que lo haría normalmente en package.json.
Por ejemplo:

    "engines": {
        "node": "6.9.1"
    }, 

El motor de implementación de Kudu determina qué motor de Node.js se usará en el orden siguiente:

* Primero, consulte en iisnode.yml si `nodeProcessCommandLine` está especificado. En caso afirmativo, úselo.
* Después, consulte package.json para ver si `"node": "..."` está especificado en el objeto `engines`. En caso afirmativo, úselo.
* De forma predeterminada, elija una versión predeterminada de Node.js.

Para obtener la lista actualizada de todas las versiones compatibles de Node.js/NPM en Azure App Service, acceda a la siguiente dirección URL para la aplicación:

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> Se recomienda definir explícitamente el motor de Node.js que desea. Puede cambiar la versión predeterminada de Node.js y es posible que obtenga errores en la aplicación web de Azure porque la versión de Node.js predeterminada no es adecuada para su aplicación.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obtención de registros de stdout y stderr desde iisnode
Para leer los registros de iisnode, siga estos pasos.

> [!NOTE]
> Después de completar los pasos, no puede haber archivos de registro hasta que se produzca un error.
> 
> 

1. Abra el archivo iisnode.yml proporcionado por la versión preliminar de la CLI de Azure 2.0.
2. Establezca los dos parámetros siguientes: 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Juntos, indican a iisnode en App Service que coloque el resultado de stdout y stderror en el directorio D:\home\site\wwwroot\**iisnode**.
3. Guarde los cambios e inserte los cambios en Azure con los siguientes comandos de Git:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode ya está configurado. Los pasos siguientes muestran cómo obtener acceso a estos registros.
4. En el explorador, acceda a la consola de depuración de Kudu para su aplicación, que se encuentra en:
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Esta dirección URL se diferencia de la dirección URL de aplicación web porque se le agrega "*.scm.*" al nombre de DNS. Si se omite esta adición a la dirección URL, obtendrá un error 404.
5. Vaya a D:\home\site\wwwroot\iisnode.
   
    ![Desplazamiento a la ubicación de los archivos de registro de iisnode.][iislog-kudu-console-find]
6. Haga clic en el icono **Editar** para el registro que desee leer. También puede hacer clic en **Descargar** o **Eliminar**, si lo desea.
   
    ![Apertura de un archivo de registro de iisnode.][iislog-kudu-console-open]
   
    Ahora puede ver el registro, que le ayuda a depurar la implementación del Servicio de aplicaciones.
   
    ![Examen de un archivo de registro de iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Depuración de la aplicación con Node-Inspector
Si utiliza Node-Inspector para depurar las aplicaciones de Node.js, puede usarlo para su aplicación Servicio de aplicaciones activa. Node-Inspector está preinstalado en la instalación de iisnode para el Servicio de aplicaciones. Si realiza la implementación mediante Git, el archivo web.config generado automáticamente desde Kudu ya contiene toda la configuración que necesita para habilitar Node-Inspector.

Para habilitar Node-Inspector, siga estos pasos:

1. Abra iisnode.yml en la raíz del repositorio y especifique los siguientes parámetros: 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Guarde los cambios e inserte los cambios en Azure con los siguientes comandos de Git:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. Ahora, simplemente vaya al archivo de inicio de la aplicación como se especifica mediante el script de inicio en su archivo package.json con /debug agregado a la dirección URL. Por ejemplo,
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    O bien,
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Más recursos
* [Especificación de una versión de Node.js en una aplicación Azure](../nodejs-specify-node-version-azure-apps.md)
* [Guía de procedimientos recomendados y solución de problemas para aplicaciones Node en Aplicaciones web de Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Cómo depurar una aplicación web de Node.js en el Servicio de aplicaciones de Azure](web-sites-nodejs-debug.md)
* [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js (Aplicaciones web del Servicio de aplicaciones de Azure: Node.js)](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Centro para desarrolladores de Node.js](/develop/nodejs/)
* [Introducción a las aplicaciones web en el Servicio de aplicaciones de Azure](app-service-web-get-started.md)
* [Exploring the Super Secret Kudu Debug Console (Exploración de la consola de depuración súper secreta de Kudu)]

<!-- URL List -->

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[activar las ventajas de suscriptor de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Creación de una aplicación de chat Node.js con Socket.IO en el Servicio de aplicaciones de Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure]: ./app-service-web-nodejs-sails.md
[Exploring the Super Secret Kudu Debug Console (Exploración de la consola de depuración súper secreta de Kudu)]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[generador Express de Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Uso de io.js con aplicaciones web del Servicio de aplicaciones de Azure]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[registrarse para una evaluación gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Jan17_HO3-->


