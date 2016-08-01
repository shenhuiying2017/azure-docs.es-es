<properties
	pageTitle="Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo implementar una aplicación Node.js en el Servicio de aplicaciones de Azure. En este tutorial se muestra cómo implementar una aplicación web Sails.js."
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
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cephalin"/>

# Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure

En este tutorial se muestra cómo implementar una aplicación Sails.js en el Servicio de aplicaciones de Azure. En el proceso, obtendrá conocimientos generales sobre cómo configurar la aplicación Node.js de modo que se ejecute en el Servicio de aplicaciones.

## Requisitos previos

- [Node.js](https://nodejs.org/).
- [Sails.js](http://sailsjs.org/get-started).
- Conocimientos prácticos de Sails.js. Este tutorial no tiene como objetivo ayudarle con los problemas relacionados con la ejecución de Sail.js en general.
- [Git](http://www.git-scm.com/downloads).
- [Azure CLI](../xplat-cli-install.md).
- Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para obtener una prueba gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o bien [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Para ver el Servicio de aplicaciones de Azure en acción antes de registrarse en una cuenta de Azure, vaya a la [prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751). Ahí podrá crear inmediatamente una aplicación de inicio de corta duración en el Servicio de aplicaciones. No se requiere tarjeta de crédito y no se establece ningún compromiso.

## Paso 1: Creación de una aplicación Sails.js en el entorno de desarrollo

En primer lugar, cree rápidamente una aplicación Sails.js predeterminada con estos pasos:

1. Abra el terminal de línea de comandos de su elección y `CD` en un directorio de trabajo.

2. Cree una nueva aplicación Sails.js y ejecútela:

        sails new <appname>
        cd <appname>
        sails lift

    Asegúrese de que puede ir a la página principal predeterminada en http://localhost:1377.

## Paso 2: Creación del recurso de aplicación del Servicio de aplicaciones en Azure

A continuación, cree el recurso de aplicación del Servicio de aplicaciones. En dicho recurso implementará la aplicación Sails.js más adelante.

1. En el mismo terminal, inicie sesión en Azure de la manera siguiente:

        azure login

    Siga las indicaciones para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tenga su suscripción de Azure.

2. Asegúrese de que todavía se encuentra en el directorio raíz del proyecto Sails.js. Cree el recurso de aplicación del Servicio de aplicaciones en Azure con un nombre de aplicación único con el comando siguiente. La dirección URL de la aplicación web es http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Siga las indicaciones para seleccionar una región de Azure para la implementación. Si nunca ha configurado credenciales de implementación Git/FTP para su suscripción de Azure, también se le solicitará que las cree.

    Cuando se cree el recurso de aplicación del Servicio de aplicaciones:

    - La aplicación de Sails.js se inicializa con Git.
    - El repositorio local inicializado con Git se conecta a la nueva aplicación del Servicio de aplicaciones como un Git remoto, acertadamente denominado "azure".
    - Se crea el archivo iisnode.yml en el directorio raíz. Puede usar este archivo para configurar [iisnode](https://github.com/tjanczuk/iisnode), que el Servicio de aplicaciones usa para ejecutar aplicaciones Node.js.

## Paso 3: Configuración e implementación de la aplicación Sails.js

 El uso de una aplicación Sails.js en el Servicio de aplicaciones consta de tres pasos principales:

 - Configuración de la aplicación para se ejecute en el Servicio de aplicaciones
 - Implementación de la aplicación en el Servicio de aplicaciones
 - Lectura de los registros stderr y stdout lecturas para solucionar los problemas de implementación

Siga estos pasos:

1. Abra el nuevo archivo iisnode.yml en el directorio raíz y agregue las dos líneas siguientes:

        loggingEnabled: true
        logDirectory: iisnode

    Ahora el registro está habilitado para iisnode. Para obtener más información sobre cómo funciona esto, consulte [Obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Abra config/env/production.js para configurar el entorno de producción y establezca `port` y `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Encontrará documentación sobre estas opciones de configuración en la [documentación de Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

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

6. Guarde los cambios y pruébelos para asegurarse de que la aplicación sigue ejecutándose localmente. Para ello, elimine la carpeta `node_modules` y, a continuación, ejecute:

        npm install
        sails lift

4. A continuación, use Git para implementar la aplicación en Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Por último, inicie la aplicación de Azure activa en el explorador:

        azure site browse

    Ahora debería ver la misma página principal de Sails.js.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## Solución de problemas de la implementación

Si la aplicación Sails.js produce un error por algún motivo en el Servicio de aplicaciones, busque los registros stderr como ayuda para solucionar el problema. Para más información, consulte [Obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-sails.md#iisnodelog). Si se ha iniciado correctamente, el registro stdout debería mostrar este mensaje conocido:

                .-..-.

    Sails              <|    .-..-.
    v0.12.3             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

Puede controlar la granularidad de los registros de stdout en el archivo [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging).

## Conexión a una base de datos de Azure

Para conectarse a una base de datos de Azure, debe crear una base de datos de su elección en Azure, como una base de datos SQL de Azure, MySQL, MongoDB, Caché en Redis de Azure, etc., y usar el [adaptador de almacén de datos](https://github.com/balderdashy/sails#compatibility) correspondiente para conectarse a ella. Los pasos descritos en esta sección muestran cómo conectarse a una base de datos SQL de Azure.

1. Siga el tutorial que encontrará [aquí](../sql-database/sql-database-get-started.md) para crear una base de datos SQL de Azure en blanco en un nuevo servidor SQL Server. La configuración de firewall predeterminada permite que los servicios de Azure (por ejemplo, el Servicio de aplicaciones) se conecten a ella.

2. Desde el terminal de línea de comandos, instale el adaptador de SQL Server:

        npm install sails-sqlserver --save

3. Abra config/connections.js y agregue el siguiente objeto de conexión a la lista:

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. Para cada variable de entorno (`process.env.*`), debe establecerlo en el Servicio de aplicaciones. Para ello, ejecute los comandos siguientes desde su terminal:

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
    Si coloca la configuración en la configuración de la aplicación de Azure mantendrá los datos confidenciales fuera del control de código fuente (Git). A continuación, configurará el entorno de desarrollo para que utilice la misma información de conexión.

4. Abra config/local.js y agregue el siguiente objeto de conexiones:

        connections: {
            sqlserver: {
                user: "<database server administrator>",
                password: "<database server password>",
                host: "<database server name>.database.windows.net", 
                database: "<database name>",
            },
        },
    
    Esta configuración invalida la configuración del archivo config/connections.js para el entorno local. El archivo .gitignore predeterminado del proyecto excluirá este archivo, por lo que no se almacenará en Git. Ahora, ya puede conectarse a Base de datos SQL de Azure desde su aplicación web de Azure y desde su entorno de desarrollo local.

4. Abra config/env/production.js para configurar el entorno de producción y agregue el siguiente objeto `models`:

        models: {
            connection: 'sqlserver',
            migrate: 'safe'
        },

4. Abra config/env/development.js para configurar el entorno de desarrollo y agregue el siguiente objeto `models`:

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

    `migrate: 'alter'` le permite usar las características de migración de base de datos para crear y actualizar fácilmente las tablas de base de datos en la Base de datos SQL de Azure. Sin embargo, `migrate: 'safe'` se usa para el entorno de Azure (producción) porque Sails.js no permite utilizar `migrate: 'alter'` en un entorno de producción (consulte la [documentación acerca de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. Desde el terminal, [genere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) una [API de proyecto](http://sailsjs.org/documentation/concepts/blueprints) de Sails.js como lo haría normalmente y, a continuación, ejecute `sails lift` para crear la base de datos con la migración de la base de datos de Sails.js. Por ejemplo:

         sails generate api mywidget
         sails lift

    El modelo `mywidget` generado por este comando está vacío, pero se puede usar para mostrar que tenemos conectividad de base de datos. Al ejecutar `sails lift`, crea las tablas que faltan para los modelos que usa la aplicación.

6. Acceda a la API de proyecto que acaba de crear en el explorador. Por ejemplo:

        http://localhost:1337/mywidget/create
    
    La API debe devolverle la entrada creada en la ventana del explorador, lo cual significará que la base de datos se creó correctamente.

        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

5. Ahora, inserte los cambios en Azure y vaya a la aplicación para asegurarse de que sigue funcionando.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Acceda a la API de proyecto de la aplicación web de Azure. Por ejemplo:

        http://<appname>.azurewebsites.net/mywidget/create

    Si la API devuelve otra entrada nueva, significa que la aplicación web de Azure está hablando con la Base de datos SQL de Azure.

## Más recursos

- [Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure](app-service-web-nodejs-get-started.md)
- [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0720_2016-->