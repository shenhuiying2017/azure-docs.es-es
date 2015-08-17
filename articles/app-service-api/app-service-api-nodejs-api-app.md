<properties
	pageTitle="Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure"
	description="Aprenda a crear un paquete de aplicación de API de Node.js y a implementarlo en el Servicio de aplicaciones de Azure."
	services="app-service\api"
	documentationCenter="nodejs"
	authors="pkefal"
  manager="" 
  editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure

Este tutorial muestra la forma de crear una aplicación de [Node.js](http://nodejs.org) e implementarla en aplicaciones web del Servicio de aplicaciones de Azure con [Git](http://git-scm.com). Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.

A continuación se muestra una captura de pantalla de la aplicación completada:

![][sample-api-app-page]

> [AZURE.NOTE]También puede usar Visual Studio Code para compilar e implementar una aplicación de API Node.js en un Servicio de aplicaciones de Azure. Para obtener más información sobre Visual Studio Code y Node.js, consulte [Visual Studio Code](http://code.visualstudio.com/Docs/) y [Desarrollo de aplicaciones de nodo](http://code.visualstudio.com//Docs/nodejs).

## Creación de una aplicación de API en el Portal de vista previa de Azure

> [AZURE.NOTE]Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a MSDN](/pricing/member-offers/msdn-benefits-details/) o bien [registrarse para obtener una evaluación gratuita](/pricing/free-trial/). También puede probar de forma gratuita [ejemplos de aplicaciones del Servicio de aplicaciones](http://tryappservice.azure.com).

1. Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en **NUEVO**, en la parte inferior izquierda del portal.

3. Haga clic en **Web + móvil > Aplicación de API**.

	![][portal-quick-create]

4. Escriba un valor para **Nombre**, como NodejsAPIApp.

5. Seleccione un plan de Servicio de aplicaciones o cree uno nuevo. Si crea un nuevo plan, seleccione el nivel de precios, la ubicación y otras opciones.

	![][portal-create-api]

6. Haga clic en **Crear**.

	![][api-app-blade]

	Si ha dejado activada la casilla de verificación **Agregar al Panel de inicio**, el portal abrirá automáticamente la hoja de la aplicación de API a partir del momento en que se crea. Si ha desactivado la casilla de verificación, haga clic en **Notificaciones** en la página principal del portal para ver el estado de creación de la aplicación de API y en la notificación para ir a la hoja de la nueva aplicación de API.

7. Haga clic en **Configuración > Configuración de la aplicación**.

9. Establezca el nivel de acceso en **Público (anónimo)**.

11. Haga clic en **Guardar**.

	![][set-api-app-access-level]

## Habilitación de la publicación Git para la nueva aplicación de API

[Git](http://git-scm.com/%20target="_blank) es un sistema de control de versión distribuida que puede utilizar para implementar su sitio web de Azure. Almacenará el código que escriba para su aplicación de API en un repositorio Git local y lo implementará en Azure enviando los cambios a un repositorio remoto. Este método de implementación es una característica de aplicaciones web del Servicio de aplicaciones que puede utilizar en una aplicación de API al basarse las aplicaciones de API en aplicaciones web: una aplicación de API del Servicio de aplicaciones de Azure es una aplicación web con características adicionales para hospedar servicios web.

En el portal, administra las características específicas de las aplicaciones de API en la hoja **Aplicación de API**, así como las características que se comparten con aplicaciones web en la hoja **Host de aplicación API**. Por lo tanto, en esta sección, se va a la hoja **Host de aplicación API** para configurar la característica de implementación de Git.

1. En la hoja Aplicación de API, haga clic en **Host de aplicación API**.

	![][api-app-host]

2. Busque la sección **Implementación** de la hoja **Aplicación de API** y haga clic en **Configurar la implementación continua**. Puede que necesite desplazarse para ver esta parte de la hoja.

	![][deployment-part]

3. Haga clic en **Elegir origen > Repositorio de Git local**.

5. Haga clic en **Aceptar**.

	![][setup-git-publishing]

6. Si no ha configurado previamente las credenciales de implementación para publicar una aplicación de API u otra aplicación del Servicio de aplicaciones, configúrelas ahora:

	* Haga clic en **Establecer credenciales de implementación**.

	* Cree un nombre de usuario y una contraseña.

	* Haga clic en **Guardar**.

	![][deployment-credentials]

1. En la hoja **Host de aplicación API** , haga clic en **Configuración > Propiedades**. La URL del repositorio Git remoto en el que implementará se muestra en "DIRECCIÓN URL DE GIT".

2. Copie la URL para utilizarla más tarde en el tutorial.

	![][git-url]

## Descarga e inspección de código para una aplicación de API de Node.js

En esta sección, podrá descargar y echar un vistazo al código proporcionado como parte del ejemplo NodeAPIApp.

1. Descargue el código en [este repositorio de GitHub](http://go.microsoft.com/fwlink/?LinkID=534023&clcid=0x409). Puede clonar el repositorio o hacer clic en **Descargar Zip** para descargarlo como archivo .zip. Si descarga el archivo .zip, descomprímalo en su disco local.

2. Vaya a la carpeta donde descomprimió el ejemplo.

	![][api-app-folder-browse]

3. Abra el archivo **apiapp.json** en un editor de texto e inspeccione el contenido.

	![][apiapp-json]

	El Servicio de aplicaciones de Azure tiene dos requisitos previos para reconocer una aplicación de Node.js como aplicación de API:

	+ Un archivo denominado *apiapp.json* debe estar presente en el directorio raíz de la aplicación.
	+ La aplicación debe exponer un extremo de metadatos Swagger 2.0. La URL de este extremo se especifica en el archivo *apiapp.json*.

	Observe la propiedad **apiDefinition**. La ruta de acceso de esta URL es relativa a la URL de su API y apunta al extremo Swagger 2.0. El Servicio de aplicaciones de Azure usa esta propiedad para detectar la definición de su API y habilitar muchas de las capacidades de la aplicación de API del Servicio de aplicaciones.

	> [AZURE.NOTE]El extremo debe ser de la especificación Swagger 2.0, ya que las versiones anteriores (p. ej., 1.2) no son compatibles con la plataforma. La aplicación de ejemplo usa swaggerize-express para crear un extremo de la especificación Swagger 2.0.

4. Abra el archivo **server.js** e inspeccione el código.

	![][server-js]

	El código usa el módulo swaggerize-express para crear el extremo Swagger 2.0.

		app.use(swaggerize({
		    api: require('./api.json'),
		    docspath: '/swagger',
		    handlers: './handlers/'
		}));

	La propiedad `api` apunta al archivo api.json que contiene la definición de la especificación Swagger 2.0 de nuestra API. Puede crear el archivo en un editor de texto manualmente o usar el [editor de Swagger](http://editor.swagger.io) en línea y descargar el archivo JSON desde allí. (El archivo *api.json* especifica una propiedad `host`, pero el valor de esta se determina y reemplaza dinámicamente en tiempo de ejecución).

	La propiedad `docspath` apunta al extremo Swagger 2.0. Esta URL es relativa a la ruta de acceso base de su API. La ruta de acceso base se declara en el archivo api.json. En nuestro ejemplo, la ruta de acceso base es */api/datos*, por lo que la ruta de acceso relativa al extremo Swagger es */api/data/swagger*.

	> [AZURE.NOTE]Al declararse la ruta de acceso base en el archivo *api.json*, un intento de acceso al extremo */swagger* como ruta de acceso relativa a la URL de su aplicación de API devolverá 404. Se trata de un error habitual cuando se intenta tener acceso al extremo Swagger.

	La propiedad `handlers` apunta a la carpeta local que contiene los controladores de ruta para el módulo Express.js.

## Ejecución del código de aplicación de API localmente

En esta sección, la aplicación se ejecuta localmente para comprobar que funciona antes de la implementación.

1. Vaya a la carpeta donde descargó el ejemplo.

2. Abra un símbolo de la línea de comandos y escriba el siguiente comando:

		npm install

3. Al finalizar el comando *install*, escriba el siguiente comando:

		node server.js

	La salida de ventana de la línea de comandos muestra "Servidor activado..."

5. Dirija el explorador a http://localhost:1337/

	Verá la siguiente página

	![][sample-api-app-page]

6. Para ver el archivo Swagger.json, vaya a http://localhost:1337/api/data/swagger.

## Publicación de su código de aplicación de API en el Servicio de aplicaciones de API

En esta sección, se crea un repositorio Git local e inserta desde ese repositorio en Azure para implementar su aplicación de ejemplo en la aplicación de API que se ejecuta en el Servicio de aplicaciones de Azure.

1. Si Git no está instalado, instálelo desde [la página de descarga de Git](http://git-scm.com/download%20target="_blank").

1. Desde la línea de comandos, cambie los directorios al directorio de aplicaciones de ejemplo y escriba los siguientes comandos para inicializar el repositorio Git local.

		git init


2. Utilice los siguientes comandos para agregar archivos al repositorio:

		git add .
		git commit -m "Initial commit of the API App"

3. Cree una referencia remota para enviar actualizaciones a la aplicación web (host de aplicación API) que creó anteriormente, con la dirección URL de Git que copió de forma previa:

		git remote add azure [URL for remote repository]

4. Envíe los cambios a Azure escribiendo el siguiente comando:

		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

	La salida de este comando finaliza con un mensaje que indica que la implementación se ha realizado correctamente:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Visualización de la definición de la API en el portal de vista previa de Azure

Ahora que ha implementado una API en su aplicación de API, puede ver la definición de la API en el portal de vista previa de Azure. Comenzará reiniciando la *puerta de enlace*, que permite que Azure reconozca que ha cambiado la definición de la API de una aplicación de API. La puerta de enlace es una aplicación web que controla la administración de API y la autorización de las aplicaciones de API en un grupo de recursos.

6. En el portal de vista previa de Azure, vaya a la hoja **Aplicación de API** para la aplicación de API que creó anteriormente y haga clic en el vínculo **Puerta de enlace**.

	![](./media/app-service-api-nodejs-api-app/clickgateway.png)

7. En la hoja **Puerta de enlace**, haga clic en **Reiniciar**. Ahora puede cerrar esta hoja.

	![](./media/app-service-api-nodejs-api-app/gatewayrestart.png)

8. En la hoja **Aplicación de API**, haga clic en **Definición de API**.

	![](./media/app-service-api-nodejs-api-app/apidef.png)

	La hoja **Definición de API** muestra dos métodos Get.

	![](./media/app-service-api-nodejs-api-app/apidefblade.png)

## Ejecución de la aplicación de ejemplo en Azure

En el portal de vista previa de Azure, vaya a la hoja **Host de aplicación API** para su aplicación de API y haga clic en **Examinar** .

![][browse-api-app-page]

El explorador muestra la página principal que vio anteriormente cuando ejecutó localmente la aplicación de ejemplo.

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Pasos siguientes

Ha implementado una aplicación web de Node.js que usa el back-end de una aplicación de API en Azure. Para obtener más información acerca del uso de Node.js en Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

* Puede probar esta aplicación de API de ejemplo en [Servicio de TryApp](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-nodejs-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-nodejs-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-nodejs-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-nodejs-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-nodejs-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-nodejs-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-nodejs-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-nodejs-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-nodejs-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-nodejs-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-nodejs-api-app/apiapp-json.png
[server-js]: ./media/app-service-api-nodejs-api-app/server-js.png
[sample-api-app-page]: ./media/app-service-api-nodejs-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-nodejs-api-app/browse-api-app-page.png
 

<!---HONumber=August15_HO6-->