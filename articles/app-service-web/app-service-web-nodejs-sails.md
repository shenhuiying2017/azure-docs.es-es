---
title: "Implementación de una aplicación web Sails.js en Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo implementar una aplicación Node.js en el Servicio de aplicaciones de Azure. En este tutorial se muestra cómo implementar una aplicación web Sails.js."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 2bc84ce01918878abbef1faf539561a4fec4c2e9


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure
En este tutorial se muestra cómo implementar una aplicación Sails.js en el Servicio de aplicaciones de Azure. En el proceso, obtendrá conocimientos generales sobre cómo configurar la aplicación Node.js de modo que se ejecute en el Servicio de aplicaciones.

En este artículo, adquirirá destrezas útiles, como las siguientes:

* Configurar una aplicación Sails.js que se ejecute en App Service.
* Implementar una aplicación en App Service desde la línea de comandos.
* Lectura de los registros stderr y stdout para solucionar cualquier problema de implementación.
* Almacenar las variables de entorno fuera del control de código fuente.
* Acceder a las variables de entorno de Azure desde su aplicación.
* Conectarse a una base de datos (MongoDB).

Debe tener conocimientos prácticos de Sails.js. Este tutorial no tiene como objetivo ayudarle con los problemas relacionados con la ejecución de Sail.js en general.

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](app-service-web-nodejs-sails-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](app-service-web-nodejs-sails.md): la CLI de última generación para el modelo de implementación de Resource Manager

## <a name="prerequisites"></a>Requisitos previos
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Versión preliminar de la CLI de Azure 2.0](/cli/azure/install-az-cli2)
* Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> También puede [probar App Service](https://azure.microsoft.com/try/app-service/) sin una cuenta de Azure. Cree una aplicación de inicio y juegue con ella durante una hora como máximo; no se requiere ninguna tarjeta de crédito ni ningún compromiso.
> 
> 

## <a name="step-1-create-and-configure-a-sailsjs-app-locally"></a>Paso 1: Creación y configuración de una aplicación de Sails.js localmente
En primer lugar, cree rápidamente una aplicación Sails.js en su entorno de desarrollo con estos pasos:

1. Abra el terminal de línea de comandos de su elección y `CD` en un directorio de trabajo.
2. Cree una nueva Sails.js y ejecútela:

        sails new <app_name>
        cd <app_name>
        sails lift

    Asegúrese de que puede navegar a la página principal predeterminada en http://localhost:1377.

1. Después, habilite el registro para Azure. En el directorio raíz, cree un archivo denominado `iisnode.yml` y agregue las dos líneas siguientes:

        loggingEnabled: true
        logDirectory: iisnode

    Ya se ha habilitado el registro para el servidor [iisnode](https://github.com/tjanczuk/iisnode) que Azure App Service utiliza para ejecutar aplicaciones Node.js. 
    Para más información sobre cómo su funcionamiento, consulte  [Obtención de registros de stdout y stderr desde iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. A continuación, configure la aplicación Sails.js para usar las variables de entorno de Azure. Abra config/env/production.js para configurar el entorno de producción y establezca `port` y `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Encontrará documentación sobre estas opciones de configuración en la  [documentación de Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

4. A continuación, codifique la versión de Node.js que desea utilizar. En package.json, agregue la siguiente propiedad `engines` para establecer la versión de Node.js que quiera.

        "engines": {
            "node": "6.9.1"
        },

5. Por último, inicialice un repositorio Git y valide los archivos. En la raíz de la aplicación (donde está package.json), ejecute los siguientes comandos de Git:

        git init
        git add .
        git commit -m "<your commit message>"

El código está listo para implementarse. 

## <a name="step-2-create-an-azure-app-and-deploy-sailsjs"></a>Paso 2: Creación de una aplicación de Azure e implementación de Sails.js

A continuación, cree el recurso de App Service en Azure e implemente en él la aplicación de Sails.js.

1. Inicie sesión en Azure de la manera siguiente:

        az login

    Siga las indicaciones para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tenga su suscripción de Azure.

3. Establezca el usuario de implementación de App Service. Posteriormente implementará el código mediante estas credenciales.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con un nombre. Para este tutorial de PHP, no necesita realmente saber qué es.

        az group create --location "<location>" --name my-sailsjs-app-group

    Para ver los posibles valores que puede usar para `<location>`, use el comando de la CLI `az appservice list-locations`.

3. Cree "GRATIS" un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) con un nombre. Para este tutorial de PHP, sepa que no se le cobrará por las aplicaciones web de este plan.

        az appservice plan create --name my-sailsjs-appservice-plan --resource-group my-sailsjs-app-group --sku FREE

4. Cree una nueva aplicación web con un nombre único en `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-sailsjs-app-group --plan my-sailsjs-appservice-plan

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Paso 3: Configuración e implementación de la aplicación Sails.js

1. Configure la implementación local de Git para la nueva aplicación web con el siguiente comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-sailsjs-app-group

    Obtendrá una salida JSON como esta, lo que significa que está configurado el repositorio de Git remoto:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Agregue la dirección URL en la salida JSON como un repositorio de Git remoto para el repositorio local (llamado `azure` para mayor claridad).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implemente el código de ejemplo para el repositorio de Git remoto `azure`. Cuando se le pida, use las credenciales de implementación que configuró anteriormente.

        git push azure master

7. Por último, inicie la aplicación de Azure activa en el explorador:

        az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

    Ahora debería ver la misma página principal de Sails.js.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Solución de problemas de la implementación
Si la aplicación Sails.js produce un error por algún motivo en el Servicio de aplicaciones, busque los registros stderr como ayuda para solucionar el problema.
Para más información, consulte [Obtención de registros de stdout y stderr desde iisnode](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode).
Si la aplicación se ha iniciado correctamente, el registro stdout debería mostrar este mensaje conocido:

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
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
Para conectarse a una base de datos de Azure, debe crear una base de datos de su elección en Azure, como Azure SQL Database, MySQL, MongoDB, Azure Redis Cache, etc., y usar el [adaptador de almacén de datos](https://github.com/balderdashy/sails#compatibility) correspondiente para conectarse a ella. Los pasos de esta sección le muestran cómo conectarse a MongoDB mediante una base de datos de [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md), que puede admitir conexiones de cliente de MongoDB.

1. [Cree una cuenta de DocumentDB que sea compartible con el protocolo MongoDB](../documentdb/documentdb-create-mongodb-account.md).
2. [Cree una colección y una base de datos de DocumentDB](../documentdb/documentdb-create-collection.md). El nombre de la colección es irrelevante, pero necesita el nombre de la base de datos al conectase desde Sails.js.
3. [Busque la información de conexión para la base de datos de DocumentDB](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize).
2. Desde el terminal de línea de comandos, instale el adaptador de MongoDB:

        npm install sails-mongo --save

3. Abra config/connections.js y agregue el siguiente objeto de conexión a la lista:

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > La opción `ssl: true` es importante porque [Azure DocumentDB la requiere](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
    >
    >

4. Para cada variable de entorno (`process.env.*`), debe establecerlo en App Service. Para ello, ejecute los comandos siguientes desde su terminal: Utilice la información de conexión para la base de datos de DocumentDB.

        az appservice web config appsettings update --settings dbuser="<database user>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbpassword="<database password>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbhost="<database hostname>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbport="<database port>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbname="<database name>" --name <app_name> --resource-group my-sailsjs-app-group

    Si coloca la configuración en la configuración de la aplicación de Azure mantendrá los datos confidenciales fuera del control de código fuente (Git). A continuación, configurará el entorno de desarrollo para que utilice la misma información de conexión.
5. Abra config/local.js y agregue el siguiente objeto de conexiones:

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    Esta configuración invalida la configuración del archivo config/connections.js para el entorno local. El archivo .gitignore predeterminado del proyecto excluirá este archivo, por lo que no se almacenará en Git. Ahora, ya puede conectarse a la base de datos de DocumentDB (MongoDB) desde su aplicación web de Azure y su entorno de desarrollo local.
6. Abra config/env/production.js para configurar el entorno de producción y agregue el siguiente objeto `models` :

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. Abra config/env/development.js para configurar el entorno de desarrollo y agregue el siguiente objeto `models` :

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'` permite usar las características de migración de base de datos para crear y actualizar fácilmente las tablas o colecciones de base de datos. Sin embargo, `migrate: 'safe'` se usa para el entorno de Azure (producción) porque Sails.js no permite utilizar `migrate: 'alter'` en un entorno de producción (consulte la  [ documentación de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. Desde el terminal, [genere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) una [API de proyecto](http://sailsjs.org/documentation/concepts/blueprints) de Sails.js como lo haría normalmente y, después, ejecute `sails lift` para crear la base de datos con la migración de la base de datos de Sails.js. Por ejemplo:

         sails generate api mywidget
         sails lift

    El modelo `mywidget` generado por este comando está vacío, pero se puede usar para mostrar que tenemos conectividad de base de datos.
    Al ejecutar `sails lift`, crea las tablas y colecciones que faltan para los modelos que usa la aplicación.
9. Acceda a la API de proyecto que acaba de crear en el explorador. Por ejemplo:

        http://localhost:1337/mywidget/create

    La API debe devolver la entrada creada en la ventana del explorador, lo que significa que la colección se ha creado correctamente.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Ahora, inserte los cambios en Azure y vaya a la aplicación para asegurarse de que sigue funcionando.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

11. Acceda a la API de proyecto de la aplicación web de Azure. Por ejemplo:

         http://<appname>.azurewebsites.net/mywidget/create

     Si la API devuelve otra entrada nueva, significa que la aplicación web de Azure está hablando con la base de datos de DocumentDB (MongoDB).

## <a name="more-resources"></a>Más recursos
* [Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure](app-service-web-nodejs-get-started.md)
* [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)



<!--HONumber=Jan17_HO3-->


