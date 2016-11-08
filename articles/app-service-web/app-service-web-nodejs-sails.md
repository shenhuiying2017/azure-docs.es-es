---
title: Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure
description: Obtenga información sobre cómo implementar una aplicación Node.js en el Servicio de aplicaciones de Azure. En este tutorial se muestra cómo implementar una aplicación web Sails.js.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 09/23/2016
ms.author: cephalin

---
# <a name="deploy-a-sails.js-web-app-to-azure-app-service"></a>Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure
En este tutorial se muestra cómo implementar una aplicación Sails.js en el Servicio de aplicaciones de Azure. En el proceso, obtendrá conocimientos generales sobre cómo configurar la aplicación Node.js de modo que se ejecute en el Servicio de aplicaciones. 

Debe tener conocimientos prácticos de Sails.js. Este tutorial no tiene como objetivo ayudarle con los problemas relacionados con la ejecución de Sail.js en general.

## <a name="prerequisites"></a>Requisitos previos
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [CLI de Azure](../xplat-cli-install.md)
* Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Para ver Azure App Service en acción antes de registrarse en una cuenta de Azure, vaya a [Pruebe Azure App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Ahí podrá crear inmediatamente una aplicación de inicio de corta duración en el Servicio de aplicaciones. No se requiere tarjeta de crédito y no se establece ningún compromiso.
> 
> 

## <a name="step-1:-create-a-sails.js-app-locally"></a>Paso 1: Creación de una aplicación de Sails.js localmente
En primer lugar, cree rápidamente una aplicación Sails.js en su entorno de desarrollo con estos pasos:

1. Abra el terminal de línea de comandos de su elección y `CD` en un directorio de trabajo.
2. Cree una nueva Sails.js y ejecútela:
   
        sails new <appname>
        cd <appname>
        sails lift
   
    Asegúrese de que puede navegar a la página principal predeterminada en http://localhost:1377.

## <a name="step-2:-create-the-azure-app-resource"></a>Paso 2: Creación del recurso de aplicación de Azure
Después, cree el recurso de App Service en Azure. En dicho recurso implementará la aplicación Sails.js más adelante.

1. Inicie sesión en Azure de la manera siguiente:
2. En el mismo terminal, cambie al modo ASM e inicie sesión en Azure:
   
        azure config mode asm
        azure login
   
    Siga las indicaciones para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tenga su suscripción de Azure.
3. Asegúrese de que todavía se encuentra en el directorio raíz del proyecto Sails.js. Cree el recurso de aplicación del Servicio de aplicaciones en Azure con un nombre de aplicación único con el comando siguiente. La dirección URL de la aplicación web es http://&lt;appname>.azurewebsites.net.
   
        azure site create --git <appname>
   
    Siga las indicaciones para seleccionar una región de Azure para la implementación. Si nunca ha configurado credenciales de implementación de Git/FTP para su suscripción de Azure, también se le solicitará que las cree.
   
    Cuando se cree el recurso de aplicación del Servicio de aplicaciones:
   
   * La aplicación de Sails.js se inicializa con Git.
   * El repositorio local inicializado con Git se conecta a la nueva aplicación del Servicio de aplicaciones como un Git remoto, acertadamente denominado "azure".
   * Se crea el archivo iisnode.yml en el directorio raíz. Puede usar este archivo para configurar [iisnode](https://github.com/tjanczuk/iisnode), que App Service usa para ejecutar aplicaciones Node.js.

## <a name="step-3:-configure-and-deploy-your-sails.js-app"></a>Paso 3: Configuración e implementación de la aplicación Sails.js
 El uso de una aplicación Sails.js en el Servicio de aplicaciones consta de tres pasos principales:

* Configuración de la aplicación para se ejecute en el Servicio de aplicaciones
* Implementación de la aplicación en el Servicio de aplicaciones
* Lectura de los registros stderr y stdout lecturas para solucionar los problemas de implementación

Siga estos pasos:

1. Abra el nuevo archivo iisnode.yml en el directorio raíz y agregue las dos líneas siguientes:
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Ahora el registro está habilitado para iisnode. Para más información sobre cómo su funcionamiento, consulte  [Obtención de registros de stdout y stderr desde iisnode](app-service-web-nodejs-get-started.md#iisnodelog).
2. Abra config/env/production.js para configurar el entorno de producción y establezca `port` y `hookTimeout`:
   
        module.exports = {
   
            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,
   
            ...
        };
   
    Encontrará documentación sobre estas opciones de configuración en la  [documentación de Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).
   
    A continuación, debe asegurarse de que [Grunt](https://www.npmjs.com/package/grunt) es compatible con las unidades de red de Azure. Las versiones de Grunt inferiores a 1.0.0 utilizan un paquete [glob](https://www.npmjs.com/package/glob) anticuado (anterior a la versión 5.0.14), que no es compatible con las unidades de red. 
3. Abra package.json y cambie la versión de `grunt` a `1.0.0` y quite todos los paquetes de `grunt-*`. La propiedad `dependencies` debería tener este aspecto:
   
        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },
4. En package.json, agregue la siguiente propiedad `engines` para establecer la versión de Node.js que quiera.
   
        "engines": {
            "node": "6.6.0"
        },
5. Guarde los cambios y pruébelos para asegurarse de que la aplicación sigue ejecutándose localmente. Para ello, elimine la carpeta `node_modules` y, después, ejecute:
   
        npm install
        sails lift
6. A continuación, use Git para implementar la aplicación en Azure:
   
        git add .
        git commit -m "<your commit message>"
        git push azure master
7. Por último, inicie la aplicación de Azure activa en el explorador:
   
        azure site browse
   
    Ahora debería ver la misma página principal de Sails.js.
   
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Solución de problemas de la implementación
Si la aplicación Sails.js produce un error por algún motivo en el Servicio de aplicaciones, busque los registros stderr como ayuda para solucionar el problema.
Para obtener más información, consulte [Obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Si se ha iniciado correctamente, el registro stdout debería mostrar este mensaje conocido:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Puede controlar la granularidad de los registros de stdout en el archivo [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Conexión a una base de datos de Azure
Para conectarse a una base de datos de Azure, debe crear una base de datos de su elección en Azure, como Azure SQL Database, MySQL, MongoDB, Azure Redis Cache, etc., y usar el [adaptador de almacén de datos](https://github.com/balderdashy/sails#compatibility) correspondiente para conectarse a ella. Los pasos descritos en esta sección muestran cómo conectarse a una base de datos MySQL en Azure.

1. Siga el tutorial que encontrará [aquí](../store-php-create-mysql-database.md) para crear una base de datos MySQL.
2. Desde el terminal de línea de comandos, instale el adaptador de MySQL:
   
        npm install sails-mysql --save
3. Abra config/connections.js y agregue el siguiente objeto de conexión a la lista: 
   
        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },
4. Para cada variable de entorno (`process.env.*`), debe establecerlo en App Service. Para ello, ejecute los comandos siguientes desde su terminal: Toda la información de conexión que necesita está en Azure Portal (consulte [Conexión a la base de datos MySQL](../store-php-create-mysql-database.md#connect)).
   
        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
   
    Si coloca la configuración en la configuración de la aplicación de Azure mantendrá los datos confidenciales fuera del control de código fuente (Git). A continuación, configurará el entorno de desarrollo para que utilice la misma información de conexión.
5. Abra config/local.js y agregue el siguiente objeto de conexiones:
   
        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
   
    Esta configuración invalida la configuración del archivo config/connections.js para el entorno local. El archivo .gitignore predeterminado del proyecto excluirá este archivo, por lo que no se almacenará en Git. Ahora, ya puede conectarse a la base de datos MySQL desde su aplicación web de Azure y desde su entorno de desarrollo local.
6. Abra config/env/production.js para configurar el entorno de producción y agregue el siguiente objeto `models` :
   
        models: {
            connection: 'mySql',
            migrate: 'safe'
        },
7. Abra config/env/development.js para configurar el entorno de desarrollo y agregue el siguiente objeto `models` :
   
        models: {
            connection: 'mySql',
            migrate: 'alter'
        },
   
    `migrate: 'alter'` permite usar las características de migración de base de datos para crear y actualizar fácilmente las tablas de base de datos MySQL. Sin embargo, `migrate: 'safe'` se usa para el entorno de Azure (producción) porque Sails.js no permite utilizar `migrate: 'alter'` en un entorno de producción (consulte la  [ documentación de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. Desde el terminal, [genere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) una [API de proyecto](http://sailsjs.org/documentation/concepts/blueprints) de Sails.js como lo haría normalmente y, después, ejecute `sails lift` para crear la base de datos con la migración de la base de datos de Sails.js. Por ejemplo:
   
         sails generate api mywidget
         sails lift
   
    El modelo `mywidget` generado por este comando está vacío, pero se puede usar para mostrar que tenemos conectividad de base de datos.
    Al ejecutar `sails lift`, crea las tablas que faltan para los modelos que usa la aplicación.
9. Acceda a la API de proyecto que acaba de crear en el explorador. Por ejemplo:
   
        http://localhost:1337/mywidget/create
   
    La API debe devolver la entrada creada en la ventana del explorador, lo que significa que la base de datos se ha creado correctamente.
   
        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Ahora, inserte los cambios en Azure y vaya a la aplicación para asegurarse de que sigue funcionando.
    
         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Acceda a la API de proyecto de la aplicación web de Azure. Por ejemplo:
    
         http://<appname>.azurewebsites.net/mywidget/create
    
     Si la API devuelve otra entrada nueva, significa que la aplicación web de Azure está hablando con la base de datos MySQL.

## <a name="more-resources"></a>Más recursos
* [Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure](app-service-web-nodejs-get-started.md)
* [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)

<!--HONumber=Oct16_HO2-->


