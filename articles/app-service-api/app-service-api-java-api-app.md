<properties
	pageTitle="Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure"
	description="Aprenda a crear un paquete de aplicación de API de Java y a implementarlo en el Servicio de aplicaciones de Azure."
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure

En este tutorial se muestra cómo crear una aplicación de Java e implementarla en las aplicaciones de API del Servicio de aplicaciones de Azure mediante [Git](http://git-scm.com). Las instrucciones que se indican en este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Java. En este tutorial también se usa [Gradle](https://gradle.org) para permitir automatizar la compilación y resolver las dependencias de paquete de la aplicación Java. Finalmente, [RESTEasy](http://resteasy.jboss.org/) se usa para crear el servicio RESTful, mediante la implementación completa de la especificación [JaxRS](https://jax-rs-spec.java.net/).

A continuación se muestra una captura de pantalla de la aplicación completada:

![][sample-api-app-page]

## Creación de una aplicación de API en el Portal de Azure

> [AZURE.NOTE]Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a MSDN](/pricing/member-offers/msdn-benefits-details/) o bien [registrarse para obtener una evaluación gratuita](/pricing/free-trial/). También puede probar de forma gratuita [ejemplos de aplicaciones del Servicio de aplicaciones](http://tryappservice.azure.com).

1. Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en **NUEVO**, en la parte inferior izquierda del portal.

3. Haga clic en **Web + móvil > Aplicación de API**.

	![][portal-quick-create]

4. Escriba un valor para **Nombre**, como JavaAPIApp.

5. Seleccione un plan de Servicio de aplicaciones o cree uno nuevo. Si crea un nuevo plan, seleccione el nivel de precios, la ubicación y otras opciones.

	![][portal-create-api]

6. Haga clic en **Crear**.

	![][api-app-blade]

	Si dejó activada la casilla **Agregar al Panel de inicio**, el portal abre automáticamente la hoja de su aplicación de API después de que se crea esta. Si ha desactivado la casilla de verificación, haga clic en **Notificaciones** en la página principal del portal para ver el estado de creación de la aplicación de API y en la notificación para ir a la hoja de la nueva aplicación de API.

7. Haga clic en **Configuración > Configuración de la aplicación**.

9. Establezca el nivel de acceso en **Público (anónimo)**.

11. Haga clic en **Guardar**.

	![][set-api-app-access-level]

## Habilitación de la publicación Git para la nueva aplicación de API

[Git](http://git-scm.com) es un sistema de control de versión distribuida que puede utilizar para implementar su sitio web de Azure. Almacenará el código que escriba para su aplicación de API en un repositorio Git local y lo implementará en Azure enviando los cambios a un repositorio remoto. Este método de implementación es una característica de aplicaciones web del Servicio de aplicaciones que puede utilizar en una aplicación de API al basarse las aplicaciones de API en aplicaciones web: una aplicación de API del Servicio de aplicaciones de Azure es una aplicación web con características adicionales para hospedar servicios web.

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

## Habilitación de Java Runtime en la nueva aplicación de API

Para que la aplicación de API hospede correctamente una aplicación de Java, debemos habilitar Java Runtime y elegir un servidor de aplicaciones. El portal ofrece una manera fácil de llevar a cabo esta tarea. Vamos a habilitar Java 7 y Jetty para hospedar nuestra aplicación.

1. En la hoja Aplicación de API, haga clic en **Host de aplicación API**.

	![][api-app-host]

2. Haga clic en **Configuración > Configuración de la aplicación**. Ahí, habilite Java y seleccione Jetty como el servidor de aplicaciones. Haga clic en **Guardar**

	![][api-app-enable-java]

De esta forma, podrá **habilitar Java Runtime** en su aplicación de API y crear una carpeta **webapps/** en la raíz de su sitio. Esta carpeta contendrá todos los archivos .war de las aplicaciones.

## Descarga e inspección del código para una aplicación de API de Java

En esta sección, podrá descargar y echar un vistazo al código proporcionado como parte del ejemplo JavaAPIApp.

1. Descargue el código en [este repositorio de GitHub](http://go.microsoft.com/fwlink/?LinkId=571009). Puede clonar el repositorio o hacer clic en **Descargar Zip** para descargarlo como archivo .zip. Si descarga el archivo .zip, descomprímalo en su disco local.

2. Vaya a la carpeta donde descomprimió el ejemplo y desplácese a la carpeta `build\libs`.

	![][api-app-folder-browse]

3. Abra el archivo **apiapp.json** en un editor de texto e inspeccione el contenido.

	![][apiapp-json]

	Los requisitos previos del Servicio de aplicaciones de Azure para reconocer una aplicación de Java como aplicación de API son dos:

	+ Un archivo denominado *apiapp.json* debe estar presente en el directorio raíz de la aplicación.
	+ La aplicación debe exponer un extremo de metadatos Swagger 2.0. La URL de este extremo se especifica en el archivo *apiapp.json*.

	Observe la propiedad **apiDefinition**. La ruta de acceso de esta URL es relativa a la URL de su API y apunta al extremo Swagger 2.0. El Servicio de aplicaciones de Azure usa esta propiedad para detectar la definición de su API y habilitar muchas de las capacidades de la aplicación de API del Servicio de aplicaciones.

4. Vaya a `src\main\java\com\microsoft\trysamples\javaapiapp`, abra el archivo **App.java** e inspeccione el código.

	![][app-java]

	El código usa el paquete de Swagger para JaxRS para crear el extremo de Swagger 2.0.

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	El método `setVersion` establece la versión de API de los metadatos servidos por Swagger.

	El método `setBasePath` establece la ruta de acceso base que Swagger usa para generar los metadatos. Esta URL es relativa a la ruta de acceso base de su aplicación de API.

	El método `setHost` establece el host en el que la API escucha. En este caso, usaremos la variable `websitehostname` que hemos asignado unas cuantas líneas antes para establecerla dinámicamente en `localhost` cuando se ejecuta de forma local o el nombre de host de la aplicación de API cuando la aplicación se ejecuta en el Servicio de aplicaciones de Azure.

	El método `setResourcePackage` establece el paquete que Swagger examinará e incluirá en el archivo Swagger.json, que contiene los metadatos de la API.

	El método `setSchemes` define los esquemas admitidos.

	El método `setScan` permite que Swagger genere la documentación de la aplicación.

	Hay más métodos que personalizan la salida de Swagger cuando se usa RESTEasy y se pueden encontrar en la [página de wiki](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig) de Swagger.

	> [AZURE.NOTE]Puede tener acceso el archivo de metadatos de Swagger en `/JavaAPIApp/api/swagger.json`.

## Ejecución del código de aplicación de API localmente

En esta sección, la aplicación se ejecuta localmente para comprobar que funciona antes de la implementación.

1. Vaya a la carpeta donde descargó el ejemplo.

2. Abra un símbolo de la línea de comandos y escriba el siguiente comando:

		gradlew.bat

3. Al finalizar el comando, escriba el siguiente comando:

		gradlew.bat jettyRunWar

	La salida de la ventana de línea de comandos muestra:

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. Dirija el explorador a `http://localhost:8080/JavaAPIApp/`

	Verá la siguiente página

	![][sample-api-app-page]

6. Para ver el archivo Swagger.json, vaya a `http://localhost:8080/JavaAPIApp/api/Swagger.json`.

## Publicación de su código de aplicación de API en el Servicio de aplicaciones de API

En esta sección, se crea un repositorio Git local e inserta desde ese repositorio en Azure para implementar su aplicación de ejemplo en la aplicación de API que se ejecuta en el Servicio de aplicaciones de Azure.

1. Si Git no está instalado, instálelo desde [la página de descarga de Git](http://git-scm.com/download).

1. En la línea de comandos, cambie los directorios por el directorio de aplicaciones de ejemplo, es decir `build\libs`, y escriba los siguientes comandos para inicializar un repositorio Git local.

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

## Visualización de la definición de API en el Portal de Azure

Ahora que ha implementado una API en su aplicación de API, puede ver la definición de API en el Portal de Azure. Comenzará reiniciando la *puerta de enlace*, que permite que Azure reconozca que ha cambiado la definición de la API de una aplicación de API. La puerta de enlace es una aplicación web que controla la administración de API y la autorización de las aplicaciones de API en un grupo de recursos.

6. En el Portal de Azure, vaya a la hoja **Aplicación de API** de la aplicación de API que creó anteriormente y haga clic en el vínculo **Puerta de enlace**.

	![][click-gateway]

7. En la hoja **Puerta de enlace**, haga clic en **Reiniciar**. Ahora puede cerrar esta hoja.

	![][restart-gateway]

8. En la hoja **Aplicación de API**, haga clic en **Definición de API**.

	![][api-definition-click]

	La hoja **Definición de API** muestra un método Get.

	![][api-definition-blade]

## Ejecución de la aplicación de ejemplo en Azure

En el Portal de Azure, vaya a la hoja **Host de aplicación API** de la aplicación de API y haga clic en **Examinar** .

![][browse-api-app-page]

El explorador muestra la página principal que vio anteriormente cuando ejecutó localmente la aplicación de ejemplo.

## Pasos siguientes

Ha implementado una aplicación web de Java que usa un back-end de aplicación de API para Azure. Para obtener más información sobre del uso de Java en Azure, consulte el [Centro para desarrolladores de Java](/develop/java/).

Puede probar esta aplicación de API de ejemplo en [Servicio de TryApp](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=August15_HO6-->