<properties
	pageTitle="Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo implementar una aplicación de Node.js en una aplicación web en el Servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="03/31/2016"
	ms.author="cephalin;robmcm"/>

# Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [pestañas](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial muestra cómo crear una aplicación de [Node.js](http://nodejs.org) sencilla e implementarla en una [aplicación web](app-service-web-overview.md) del [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md) a partir de una líneas de comandos como cmd.exe o bash. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.js.

<a name="prereq"/>
## Requisitos previos

- Node.js. Encontrará [aquí](https://nodejs.org/) los archivos binarios de instalación.
- Yoeman. Las instrucciones de instalación se pueden encontrar [aquí](http://yeoman.io/).
- Git. Encontrará [aquí](http://www.git-scm.com/downloads) los archivos binarios de instalación.
- CLI de Azure. Las instrucciones de instalación se pueden encontrar [aquí](../xplat-cli-install.md).
- Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o bien [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## Creación e implementación de una aplicación web de Node.js simple

1. Abra el terminal de la línea de comandos que prefiera e instale el [generador Express de Yeoman](https://github.com/petecoop/generator-express).

        npm install -g generator-express

2. Mediante `CD`, vaya a un directorio de trabajo y genere una aplicación express de este modo:

        yo express
        
    Elija las opciones siguientes cuando se le solicite:

    `? Would you like to create a new directory for your project?` **Sí** `? Enter directory name` **&lt;nombreDeAplicación>** `? Select a version to install:` **MVC** `? Select a view engine to use:` **Jade** `? Select a css preprocessor to use (Sass Requires Ruby):` **Ninguno** `? Select a database to use:` **Ninguno** `? Select a build tool to use:` **Grunt**

3. Mediante `CD`, vaya al directorio raíz de la nueva aplicación e iníciela para asegurarse de que se ejecuta en el entorno de desarrollo:

        npm start

    En el explorador, vaya a [http://localhost:3000](http://localhost:3000) para asegurarse de que puede ver la página principal de Express. Una vez que haya comprobado que la aplicación se ejecuta correctamente, use `Ctrl-C` para detenerla.
    
1. Inicie sesión en Azure de este modo (necesita [CLI de Azure](#prereq) para hacerlo):

        azure login

    Siga las indicaciones para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tenga su suscripción de Azure.

2. Asegúrese de que todavía se encuentra en el directorio raíz de su aplicación. Cree el recurso de aplicación del Servicio de aplicaciones en Azure con un nombre de aplicación único con el comando siguiente. La dirección URL de la aplicación web es http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Siga las indicaciones para seleccionar una región de Azure para la implementación. Si nunca ha configurado credenciales de implementación de Git/FTP para su suscripción de Azure, también se le solicitará que las cree.

3. Abra config/config.js y cambie el puerto de producción a `process.env.port`. El objeto JSON de producción debería ser similar al siguiente:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Esto permite que la aplicación de Node.js responda a solicitudes web en el puerto predeterminado en el que escucha iisnode.
    
4. Guarde los cambios y use git para implementar la aplicación en Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

    El generador Express ya proporciona un archivo .gitignore, por lo que su comando `git push` no consumirá ancho de banda al intentar cargar el directorio node\_modules/.

5. Por último, inicie la aplicación de Azure activa en el explorador:

        azure site browse

    Ahora debería ver la aplicación web de Node.js que se ejecuta directamente en el Servicio de aplicaciones de Azure.
    
    ![](./media/app-service-web-nodejs-get-started/deployed-express-app.png)

## Actualización de la aplicación web Node.js

Para realizar actualizaciones en su aplicación web de Node.js que se ejecuta en el Servicio de aplicaciones, solo tiene que ejecutar `git add`, `git commit` y `git push` como hizo cuando la implementó.
     
## Modo en que el Servicio de aplicaciones implementa la aplicación de Node.js

El Servicio de aplicaciones de Azure usa [iisnode](https://github.com/tjanczuk/iisnode/wiki) para ejecutar aplicaciones de Node.js. La CLI de Azure y el motor de Kudu (implementación de Git) colaboran para proporcionar una experiencia simplificada al desarrollar e implementar aplicaciones de Node.js desde la línea de comandos.

- `azure site create --git` reconoce el patrón común de Node.js de server.js o app.js y crea una instancia de iisnode.yml en el directorio raíz. Puede utilizar este archivo para personalizar iisnode.
- En `git push azure master`, Kudu automatiza las tareas de implementación siguientes:

    - Si package.json está en la raíz del repositorio, ejecute `npm install --production`.
    - Genere un archivo web.config para iisnode que apunte al script de inicio en package.json (por ejemplo, server.js o app.js).
    - Personalice el archivo web.config para preparar la aplicación para la depuración con Node-Inspector.
    
## Uso de una plataforma de Node.js

Si utiliza una plataforma de Node.js popular, como [Sails.js](http://sailsjs.org/) o [MEAN.js](http://meanjs.org/) para desarrollar aplicaciones, puede implementarla en el Servicio de aplicaciones. Las plataformas de Node.js populares tienen sus peculiaridades y sus dependencias de paquete se actualizan constantemente. Sin embargo, el Servicio de aplicaciones hace que los registros de stdout y stderr estén disponibles, para que pueda saber exactamente lo que sucede con su aplicación y realizar cambios según corresponda. Para más información, consulte [Obtención de registros de stdout y stderr desde iisnode](#iisnodelog).

Consulte los tutoriales que muestran cómo trabajar con una plataforma específica en el Servicio de aplicaciones.

- [Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure](app-service-web-nodejs-sails.md)
- [Creación de una aplicación de chat Node.js con Socket.IO en el Servicio de aplicaciones de Azure](web-sites-nodejs-chat-app-socketio.md)
- [Uso de io.js con aplicaciones web del Servicio de aplicaciones de Azure](web-sites-nodejs-iojs.md)

## Uso de un motor de Node.js específico

En el flujo de trabajo típico, puede indicar al Servicio de aplicaciones que use un motor de Node.js específico igual que lo haría normalmente en package.json. Por ejemplo:

    "engines": {
        "node": "5.5.0"
    }, 

El motor de implementación de Kudu determina qué motor de Node.js se usará en el orden siguiente:

- Primero, consulte en iisnode.yml si `nodeProcessCommandLine` está especificado. En caso afirmativo, úselo.
- Después, consulte package.json para ver si `"node": "..."` está especificado en el objeto `engines`. En caso afirmativo, úselo.
- De forma predeterminada, elija una versión predeterminada de Node.js.

<a name="iisnodelog" />
## Obtención de registros de stdout y stderr desde iisnode

Para leer los registros de iisnode, siga estos pasos:

1. Abra el archivo iisnode.yml proporcionado por la CLI de Azure

2. Establezca los dos parámetros siguientes:

        loggingEnabled: true
        logDirectory: iisnode
    
    Juntos, indican a iisnode en el Servicio de aplicaciones que coloque el resultado de stdout y stderror en el directorio D:\\home\\site\\wwwroot**iisnode**.

3. Guarde los cambios e inserte los cambios en Azure con los siguientes comandos de Git:

        git add .
        git commit -m "<your commit message>"
        git push azure master
   
   iisnode ya está configurado. Los pasos siguientes muestran cómo obtener acceso a estos registros.
     
4. En el explorador, acceda a la consola de depuración de Kudu para su aplicación, que se encuentra en:

        https://<appname>.scm.azurewebsites.net/DebugConsole 

5. Vaya a D:\\home\\site\\wwwroot\\iisnode.

    ![](./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png)

6. Haga clic en el icono **Editar** para el registro que desea leer. También puede hacer clic en **Descargar** o **Eliminar** si lo desea.

    ![](./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png)

    Ahora puede ver el registro, que le ayuda a depurar la implementación del Servicio de aplicaciones.
    
    ![](./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png)


## Depuración de la aplicación con Node-Inspector

Si utiliza Node-Inspector para depurar las aplicaciones de Node.js, puede usarlo para su aplicación Servicio de aplicaciones activa. Node-Inspector está preinstalado en la instalación de iisnode para el Servicio de aplicaciones. Si realiza la implementación mediante Git, el archivo web.config generado automáticamente desde Kudu ya contiene toda la configuración que necesita para habilitar Node-Inspector.

Para habilitar Node-Inspector, siga estos pasos:

1. Abra iisnode.yml en la raíz del repositorio y especifique los siguientes parámetros: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Guarde los cambios e inserte los cambios en Azure con los siguientes comandos de Git:

        git add .
        git commit -m "<your commit message>"
        git push azure master
   
4. Ahora, simplemente vaya al archivo de inicio de la aplicación como se especifica mediante el script de inicio en su archivo package.json con /debug agregado a la dirección URL. Por ejemplo,

        http://<appname>.azurewebsites.net/server.js/debug
    
    O bien,
    
        http://<appname>.azurewebsites.net/app.js/debug

## Más recursos

- [Especificación de una versión de Node.js en una aplicación Azure](../nodejs-specify-node-version-azure-apps.md)
- [Cómo depurar una aplicación web de Node.js en el Servicio de aplicaciones de Azure](web-sites-nodejs-debug.md)
- [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)
- [Azure App Service Web Apps: Node.js (Aplicaciones web del Servicio de aplicaciones de Azure: Node.js)](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro para desarrolladores de Node.js](/develop/nodejs/)
- [Introducción a las aplicaciones web en el Servicio de aplicaciones de Azure](app-service-web-get-started.md)

<!---HONumber=AcomDC_0504_2016-->