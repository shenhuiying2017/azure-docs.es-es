<properties
	pageTitle="Aplicación de API de Node.js en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Aprenda a crear una API de RESTful de Node.js e implementarla en una aplicación de API en el Servicio de aplicaciones de Azure."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="05/06/2016"
	ms.author="bradygaster"/>

# Creación de una API de RESTful de Node.js e implementación en una aplicación de API de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

En este tutorial se muestra como crear una API de [Node.js](http://nodejs.org) sencilla e implementarla en una [aplicación de API](app-service-api-apps-why-best-platform.md) en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). Puede usar cualquier sistema operativo que sea capaz de ejecutar Node.js; realizará todo el trabajo mediante herramientas de línea de comandos como cmd.exe o bash.

## Requisitos previos

1. [Node.js](http://nodejs.org) instalado (en este ejemplo se considera que tiene Node.js versión 4.2.2)
2. [Git](https://git-scm.com/) instalado
1. Cuenta [GitHub](https://github.com/)
1. [Cuenta de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) de Microsoft Azure

## Obtención del código de ejemplo

1. Abra una interfaz de la línea de comandos que puede ejecutar comandos de Node.js y Git.

1. Vaya a una carpeta que pueda usar para un repositorio de Git local y clone el [repositorio de GitHub que contiene el código de ejemplo](https://github.com/Azure-Samples/app-service-api-node-contact-list).

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

	La API de ejemplo proporciona dos puntos de conexión: una solicitud Get a `/contacts` devuelve una lista de nombres y direcciones de correo electrónico en formato JSON, mientras que `/contacts/{id}` devuelve solo el contacto seleccionado.

## Código de Node.js scaffold basado en los metadatos de Swagger

[Swagger](http://swagger.io/) es un formato de archivo JSON para metadatos que describe una API de REST. El Servicio de aplicaciones de Azure tiene [compatibilidad integrada para los metadatos de Swagger](app-service-api-metadata.md). En esta parte del tutorial, se aplica la técnica scaffolding al código del servidor para una API de ejemplo basada en un archivo de metadatos de Swagger.

>[AZURE.NOTE] Si no desea aprender cómo aplicar la técnica scaffolding a un archivo Swagger, puede hacer solo los pasos del tutorial que implementan el código de ejemplo en una aplicación de API. Vaya directamente a la sección [Creación de una nueva aplicación de API en el Portal de Azure](#createapiapp).

1. Ejecute los comandos siguientes para instalar los módulos de NPM **yo** y **sgenerator-swaggerize** globalmente.

		npm install -g yo
		npm install -g generator-swaggerize

	Swaggerize es una herramienta que genera código del servidor para una API descrita por un archivo de metadatos de Swagger. El archivo de Swagger que va a utilizar se denomina *api.json* y se encuentra en la carpeta *start* del repositorio que se clonó.

2. Desplácese a la carpeta *start* y ejecute el comando `yo swaggerize`. Swaggerize realiza una serie de preguntas. En **what to call this project**, escriba "contactlist", en **path to swagger document**, escriba "api.json" y en **Express, Hapi, or Restify**, escriba "express".

		yo swaggerize

	![Línea de comandos de swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
	**Nota**: Si se produce un error en este paso, el paso siguiente explica cómo corregirlo.

	Swaggerize crea una carpeta de aplicaciones, controladores de scaffolding y archivos de configuración, y genera un archivo **package.json**. El motor de vista rápida se utiliza para generar la página de Ayuda de Swagger.

3. Si el comando `swaggerize` genera un error de "unexpected token" o "invalid escape sequence", corrija la causa del error mediante la edición del archivo *package.json*. En la línea `regenerate` bajo `scripts`, cambie la barra diagonal inversa que precede a *api.json* a una barra diagonal, para que la línea sea similar al ejemplo siguiente:

 		"regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Desplácese a la carpeta que contiene el código con scaffold (en este caso, la subcarpeta *ContactList*).

1. Ejecute `npm install`.
	
		npm install
		
2. Instale el módulo de NPM **jsonpath**.

		npm install --save jsonpath
        
    ![Instalación de Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Instale el módulo de NPM **swaggerize-ui**.

		npm install --save swaggerize-ui
        
    ![Instalación de la interfaz de usuario de swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

## Personalización del código con scaffold

1. Copie la carpeta **lib** de la carpeta **start** a la carpeta **ContactList** creada por el procesador de scaffolding. 

1. Reemplace el código del archivo **handlers/contacts.js** por el código siguiente.

	Este código usa los datos JSON almacenados en el archivo **lib/contacts.json** proporcionado por **lib/contactRepository.js**. El nuevo código contacts.js que aparece a continuación responderá a las solicitudes HTTP para obtener todos los contactos y los devolverá como una carga JSON.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Reemplace el código del archivo **handlers/contacts/{id}.js** por el código siguiente.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Reemplace el código de **server.js** por el código siguiente.

	Los cambios realizados en el archivo server.js se indican con comentarios para que pueda ver los cambios realizados.

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

## Prueba con la API ejecutada localmente

1. Active el servidor mediante el archivo ejecutable de línea de comandos de Node.js. 

        node server.js

1. Cuando se desplace a ****http://localhost:8000/contacts**, aparecerá el resultado de JSON de la lista de contactos (o se le solicita que lo descargue, según el navegador).

    ![Todas las llamadas de la API de contactos](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Cuando vaya a ****http://localhost:8000/contacts/2**, verá el contacto representado por ese valor de identificador.

    ![Llamada de la API de contactos específica](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Los datos JSON de Swagger se envían a través del punto de conexión **/swagger**:

    ![Json de Swagger de contactos](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. La interfaz de usuario de Swagger se envían a través del punto de conexión **/docs**. En la interfaz de usuario de Swagger puede usar las características de cliente HTML enriquecidas para probar la API.

    ![Interfaz de usuario de Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Creación de una nueva aplicación de API en el Portal de Azure

En esta sección le guiaremos por el proceso de creación de una aplicación de API nueva y vacía en Azure. En la siguiente sección, conectará la aplicación a un repositorio de Git para poder habilitar la entrega continua de los cambios de código.

1. Vaya al [Portal de Azure](https://portal.azure.com/). 

1. Haga clic en **Nuevo > Web + móvil > Aplicación de API**.

    ![Nueva aplicación de API en el portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Escriba un **Nombre de aplicación** que sea único en el dominio *azurewebsites.net*; por ejemplo, NodejsAPIApp más un número para hacerlo único.

	Si escribe un nombre que ya haya sido usado, verá un signo de exclamación rojo a la derecha, en lugar de una marca de verificación verde, y será preciso que escriba otro nombre.

	Azure usa este nombre como prefijo de la dirección URL de la API. La dirección URL completa consta de este nombre, más *.azurewebsites.net*. Por ejemplo, si el nombre es `NodejsAPIApp`, la dirección URL será `nodejsapiapp.azurewebsites.net`.

6. En la lista desplegable **Grupo de recursos**, haga clic en **Nuevo** y luego en **Nombre del nuevo grupo de recursos** y escriba "NodejsAPIAppGroup" u otro nombre que prefiera.

	Un [grupo de recursos](../azure-portal/resource-group-portal.md) es una colección de recursos de Azure, como aplicaciones de API, bases de datos, máquinas virtuales, etc. Para este tutorial, se recomienda crear un nuevo grupo de recursos, ya que así podrá eliminar fácilmente y en un solo paso todos los recursos de Azure que cree para el tutorial.

4. Haga clic en **Plan de servicio de aplicaciones/Ubicación** y después en **Crear nuevo**.

	![Creación de un plan de Servicio de aplicaciones](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

	En los siguientes pasos se crea un plan del Servicio de aplicaciones para el nuevo grupo de recursos. Un plan del Servicio de aplicaciones especifica los recursos de proceso en los que se ejecuta la aplicación de API. Por ejemplo, si elige el nivel Gratis, la aplicación de API se ejecuta en máquinas virtuales compartidas, mientras que para algunos niveles de pago, se ejecuta en máquinas virtuales dedicadas. Para más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En la hoja **Plan de servicio de aplicaciones**, escriba "NodejsAPIAppPlan" u otro nombre si lo prefiere.

5. En la lista desplegable **Ubicación**, elija la ubicación más cercana.

	Esta opción especifica en qué centro de datos de Azure se ejecutará su aplicación. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, Pero para una aplicación de producción, desea que el servidor esté lo más próximo posible a los clientes que acceden a él con el fin de minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Haga clic en **Plan de tarifa > Ver todo > F1 Free** (F1 gratis).

	Para este tutorial, el plan de tarifa gratis proporcionará un rendimiento suficiente.

	![Selección del plan de tarifa Gratis](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. En la hoja **Plan de servicio de aplicaciones**, haga clic en **Aceptar**.

7. En la hoja **Aplicación de API**, haga clic en **Crear**.

## Configuración de la nueva aplicación de API para la implementación de Git

En esta sección del tutorial, creará las credenciales que va a utilizar para la implementación y un repositorio de Git para la aplicación de API en el Servicio de aplicaciones de Azure. Implementará el código en la aplicación de API insertando confirmaciones en este repositorio en el Servicio de aplicaciones de Azure.

1. Una vez creada la aplicación de API, haga clic en **Servicios de aplicaciones > {su aplicación de API}** en la página principal del portal. 

	El portal muestra las hojas **Aplicación de API** y **configuración**.

    ![Hoja de aplicación de API en el portal](media/app-service-api-nodejs-api-app/portalapiappblade.png)

    ![Hoja Configuración del portal](media/app-service-api-nodejs-api-app/portalsettingsblade.png)

1. En la hoja **Configuración**, desplácese hacia abajo hasta la sección **Publicación** y haga clic en **Credenciales de implementación**.
 
3. En la hoja **Configurar credenciales de implementación**, escriba un nombre de usuario y una contraseña, y haga clic en **Guardar**.

	Usará estas credenciales para publicar el código de Node.js en su aplicación de API. En el siguiente paso se crea el repositorio de Git de Azure asociado a la aplicación de API.

    ![Credenciales de implementación](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. En la hoja **Configuración**, haga clic en **Origen de implementación > Elegir origen > Repositorio de Git local** y en **Aceptar**.

    ![Creación de repositorio de Git](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Una vez creado el repositorio de Git, se cambiará la hoja y mostrará las implementaciones activas. Como el repositorio es nuevo, no tiene implementaciones activas en la lista.

    ![Sin implementaciones activas](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Copie la dirección URL del repositorio de Git. Para ello, vaya a la hoja de la nueva aplicación de API y consulte la sección **Essentials** de la hoja. Verá la **URL de clonación de Git** en la sección **Essentials**. Cuando se mantiene el mouse sobre esta dirección URL, verá un icono a la derecha que copiará la dirección URL en el Portapapeles. Haga clic en este icono para copiar la dirección URL.

    ![Obtención de la dirección URL de Git desde el Portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Nota**: necesitará la dirección URL de clonación de Git en el paso siguiente, por tanto asegúrese de guardarla de momento.

Ahora que dispone de una nueva aplicación de API con una copia de seguridad del repositorio de Git, puede insertar código en el repositorio y usar las características de implementación continua de Azure para implementar automáticamente los cambios.

## Implementación del código de API en Azure

En esta sección realizará los siguientes pasos:

* Cree un repositorio de Git local que contiene el código del servidor de la API.
* En ese repositorio creará un Git remoto que apunta al repositorio que ha creado para su aplicación de API de Azure.
* Inserte su código desde el repositorio local en el repositorio remoto. 

Las características de entrega continua integradas del Servicio de aplicaciones de Azure permiten implementar el código simplemente insertando confirmaciones en un repositorio de Git asociado a la aplicación de API.

1. Si ya realizó la primera parte del tutorial, copie la carpeta `start\ContactList` que ha creado mediante el procesador de scaffolding swaggerize a otra carpeta. De lo contrario, copie la carpeta `end\ContactList` a otra carpeta.

1. En la herramienta de la línea de comandos, vaya a la nueva carpeta y ejecute el siguiente comando para crear un nuevo repositorio de Git local.

        git init

     ![Nuevo repositorio de Git local](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Ejecute el siguiente comando para agregar un Git remoto para el repositorio de la aplicación de API.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Nota**: Reemplace la cadena "YOUR\_GIT\_CLONE\_URL\_HERE" por su propia dirección URL de clonación de Git que copió antes.

1. Ejecute los dos comandos siguientes para crear una confirmación que contiene todo el código.

        git add .
        git commit -m "initial revision"

    ![Salida de confirmación de Git](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Ejecute el comando para insertar el código en Azure. Cuando se le pide una contraseña, escriba la que creó antes en el portal de Azure.

        git push azure master

	Esto desencadenará una implementación de la aplicación de API.

1. En el explorador, vuelva a la hoja **Implementaciones** para la aplicación de API, verá la implementación que se está produciendo.

    ![Implementación que está ocurriendo](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Simultáneamente, la interfaz de la línea de comandos reflejará el estado de la implementación mientras está ocurriendo.

    ![Implementación de Node Js que está ocurriendo](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

	Cuando se haya completado la implementación, la hoja **Implementaciones** reflejará la implementación correcta de los cambios del código en la aplicación de API.

## Prueba con la API ejecutada en Azure
 
3. Copie la **URL** en la sección **Essentials** de la hoja Aplicación de API. 

    ![Implementación completada](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Con un cliente de la API de REST, como Postman o Fiddler (o el explorador web), proporcione la dirección URL de la llamada de API de los contactos, que está en el punto de conexión `/contacts` de la aplicación de API.

    **Nota:** La dirección URL será `https://{your API app name}.azurewebsites.net/contacts`.

    Cuando se envía una solicitud GET a este punto de conexión, se obtiene el resultado JSON de la aplicación de API.

    ![Postman que llega a la API](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. En un explorador, vaya al punto de conexión `/docs` para probar la interfaz de usuario de Swagger cuando se ejecuta en Azure.

Ahora que tiene conectada la entrega continua, puede realizar cambios en el código e implementarlos en Azure enviando confirmaciones al repositorio de Git de Azure.

## Pasos siguientes

Ya ha creado e implementado correctamente su primera aplicación de API con Node.js. El siguiente tutorial de la serie de introducción a Aplicaciones de API muestra cómo [consumir aplicaciones de API desde clientes de JavaScript mediante CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0511_2016-->