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
	ms.date="03/31/2016"
	ms.author="cephalin"/>

# Implementación de una aplicación web Sails.js en el Servicio de aplicaciones de Azure

En este tutorial se muestra cómo implementar una aplicación Sails.js en el Servicio de aplicaciones de Azure. En el proceso, obtendrá conocimientos generales sobre cómo configurar la aplicación Node.js de modo que se ejecute en el Servicio de aplicaciones.

## Requisitos previos

- Node.js. Encontrará [aquí](https://nodejs.org/) los archivos binarios de instalación.
- Sails.js. Encontrará [aquí](http://sailsjs.org/get-started) las instrucciones de instalación.
- Conocimientos prácticos de Sails.js. Este tutorial no tiene como objetivo ayudarle con los problemas relacionados con la ejecución de Sail.js en general.
- Git. Encontrará [aquí](http://www.git-scm.com/downloads) los archivos binarios de instalación.
- CLI de Azure. Encontrará [aquí](../xplat-cli-install.md) las instrucciones de instalación.
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

    Ahora el registro está habilitado para iisnode. Para obtener más información sobre cómo funciona esto, consulte [Obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-sails.md#iisnodelog).

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

    A continuación, debe asegurarse de que [Grunt](https://www.npmjs.com/package/grunt) es compatible con las unidades de red de Azure. En el momento de redactar este artículo, se puede producir el [error "ENOTSUP: operación no permitida por el socket"](https://github.com/isaacs/node-glob/issues/205) en Grunt porque actualmente usa un paquete de [glob](https://www.npmjs.com/package/glob) obsoleto (v3.1.21) que no es compatible con las unidades de red. Los pasos siguientes muestran cómo hacer que Grunt use [glob v5.0.14 o una versión superior](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7).

3. Dado que ya se ha ejecutado `npm install` al crearse la aplicación, genere npm-shrinkwrap.json en la raíz del proyecto:

        npm shrinkwrap

4. Abra npm-shrinkwrap.json, busque el json de `"grunt":` y agregue la dependencia de la versión de glob que quiera. El json terminado debe tener este aspecto:

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. Busque `"glob":` para encontrar todas las referencias a glob. Si hay alguna referencia que sea v3.1.21 o una versión inferior, cambie el json a lo siguiente:

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. Guarde los cambios y pruébelos para asegurarse de que la aplicación sigue ejecutándose localmente:

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

Si la aplicación Sails.js produce un error por algún motivo en el Servicio de aplicaciones, busque los registros stderr como ayuda para solucionar el problema. Para obtener más información, consulte [Obtener registros stdout y stderr desde iisnode](app-service-web-nodejs-sails.md#iisnodelog). Si se ha iniciado correctamente, el registro stdout debería mostrar este mensaje conocido:

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
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

## Conexión a una base de datos de Azure

Para conectarse a una base de datos de Azure, debe crear una base de datos de su elección en Azure, como una base de datos SQL de Azure, MySQL, MongoDB, Caché en Redis de Azure, etc., y usar el [adaptador de almacén de datos](https://github.com/balderdashy/sails#compatibility) correspondiente para conectarse a ella. Los pasos descritos en esta sección muestran cómo conectarse a una base de datos SQL de Azure.

1. Siga el tutorial que encontrará [aquí](../sql-database/sql-database-get-started.md) para crear una base de datos SQL de Azure en blanco en un nuevo servidor SQL Server. La configuración de firewall predeterminada permite que los servicios de Azure (por ejemplo, el Servicio de aplicaciones) se conecten a ella.

2. Desde el terminal de línea de comandos, instale el adaptador de SQL Server:

        npm install sails-sqlserver --save

    Dado que ha cambiado el archivo package.json, debe volver a generar npm-shrinkwrap.json. Lo hará a continuación.
    
3. Elimine el directorio node\_modules/.

4. Ejecute `npm shrinkwrap`.

5. Vuelva a abrir npm-shrinkwrap.json y actualice las versiones del paquete de `glob` como hizo en la sección anterior.

    Ahora, volvamos a la tarea principal.
        
3. Abra config/connections.js y agregue el siguiente json a la lista de adaptadores:

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
        
4. Abra config/env/production.js para configurar el entorno de producción y establezca `connection` y `migrate` en el objeto JSON `models`:

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. Desde el terminal, [genere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) una [API de proyecto](http://sailsjs.org/documentation/concepts/blueprints) de Sails.js como lo haría normalmente. Por ejemplo:

         sails generate api mywidget
     
5. Guarde todos los cambios, envíelos a Azure y vaya a la aplicación para asegurarse de que sigue funcionando.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Ahora, acceda a la API de proyecto que acaba de crear en el explorador. Por ejemplo:

        http://<appname>.azurewebsites.net/widget/create
    
    La API debería devolverle la entrada creada en la ventana del explorador:
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## Más recursos

- [Introducción a las aplicaciones web Node.js en el Servicio de aplicaciones de Azure](app-service-web-nodejs-get-started.md)
- [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0518_2016-->