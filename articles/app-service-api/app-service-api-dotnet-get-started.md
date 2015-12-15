<properties
	pageTitle="Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo crear, implementar y consumir una aplicación de API de ASP.NET en el Servicio de aplicaciones de Azure con Visual Studio 2015."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Información general

En este tutorial, creará una [aplicación de API del Servicio de aplicaciones](app-service-api-apps-why-best-platform.md), implementará ASP.NET Web API para la aplicación de API y usará la aplicación de API desde un cliente MVC de ASP.NET. En el tutorial se da por hecho que está familiarizado con ASP.NET pero no tiene ninguna experiencia previa con Microsoft Azure. Cuando termine el tutorial, tendrá una API web y una aplicación cliente que se ejecuta en la nube.

La aplicación de ejemplo es una sencilla lista de contactos. La ilustración siguiente muestra cómo presenta la aplicación MVC los datos recibidos de la API.

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## Temas que se abordarán

Tres características del Servicio de aplicaciones de Azure son especialmente útiles para desarrollar y hospedar API:

* Compatibilidad integrado con metadatos de API
* Compatibilidad con CORS
* Compatibilidad con autenticación y autorización
 
Este es el primer tutorial de una serie en el que se presentan estas características. Este tutorial se centra en los metadatos de API, el segundo en CORS y el tercero y cuarto en la autenticación y autorización.

En estos tutoriales, aprenderá lo siguiente:

* Instalar el SDK de Azure para .NET para preparar la máquina para el desarrollo de Azure.
* Trabajar con Aplicaciones de API y aplicaciones web en el Servicio de aplicaciones de Azure usando las herramientas integradas en Visual Studio 2015.
* Automatizar la detección de la API mediante el paquete de NuGet de Swashbuckle para generar dinámicamente la definición JSON de la API de Swagger.
* Usar código de cliente generado automáticamente para consumir una aplicación de API desde un cliente .NET.
* Usar el portal de Azure para configurar el punto de conexión de los metadatos de la aplicación de API.
* Usar CORS para llamar a una aplicación de API desde un cliente de JavaScript cuando el cliente está en un dominio distinto de la API.
* Usar Azure Active Directory para proteger una API de accesos no autenticados.
* Consumir una API protegida para usuarios que iniciaron sesión en Azure Active Directory.
* Usar una API protegida mediante una entidad de servicio.

## Requisitos previos

### ASP.NET Web API

En el tutorial se asume que está familiarizado con ASP.NET Web API; si necesita una introducción, consulte [Getting Started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

## Visual Studio 2015

En las instrucciones y capturas de pantalla se asume que usa Visual Studio 2015, pero las mismas instrucciones funcionan con Visual Studio 2013.

## Cuenta de Azure

Necesita una cuenta de Azure para completar el tutorial. Puede:

* [Abrir una cuenta de Azure de manera gratuita](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Incluso después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure, como la característica de Aplicaciones web del Servicio de aplicaciones de Azure.
* [Activar los beneficios de la suscripción a Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago.

Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751). Ahí puede crear de forma inmediata una aplicación de corta duración para iniciarse en el Servicio de aplicaciones. No se requiere tarjeta de crédito y no se establece ningún compromiso.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Este tutorial requiere la versión 2.8.1 o posterior del SDK de Azure para .NET.

## Información general de la aplicación de ejemplo

El código que se va a implementar en una aplicación de API y en una aplicación web en este tutorial se encuentra en el repositorio [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) de GitHub. La solución ContactsList de Visual Studio incluye los siguientes proyectos:

* **ContactsList.API**: proyecto de ASP.NET Web API que devuelve una lista de nombres y direcciones de correo electrónico. La llamada inicial al método Get devuelve 3 contactos codificado de forma rígida y las llamadas posteriores a los métodos Put, Post y Delete guardan los cambios en un archivo JSON local.
* **ContactsList.MVC**: cliente de ASP.NET MVC para la API de ContactsList.
* **ContactsList.Angular**: cliente de interfaz de usuario AngularJS básico para la API de ContactsList. Muestra cómo llamar a una aplicación de API sin protección (sin autenticación).
* **ContactsList.Angular.AAD**: cliente de AngularJS que muestra cómo usar Azure Active Directory para autenticar usuarios.
* **CompanyContacts.API**: proyecto de ASP.NET Web API que devuelve una lista de contactos codificada de forma rígida en respuesta a una solicitud Get. La llama el método Get de **ContactsList.API** para demostrar cómo llamar a una API mediante la autenticación de servicio a servicio (entidad de servicio).
 
## Descarga de la aplicación de ejemplo 

1. Descargue el repositorio [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list).

	Puede [descargar un archivo .zip](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip) o clonar el repositorio en la máquina local.

2. Abra la solución ContactsList en Visual Studio 2015 o 2013.

2. Compile la solución para restaurar los paquetes de NuGet.

## Uso de metadatos e interfaz de usuario de Swagger

La compatibilidad con los metadatos de la API de [Swagger 2.0](http://swagger.io/) está integrada en el Servicio de aplicaciones de Azure. Cada aplicación de API puede definir un punto de conexión de URL que devuelve los metadatos de la API en formato JSON de Swagger. Los metadatos que devuelve dicho punto de conexión pueden utilizarse para generar código de cliente que facilite el consumo de la API.

En esta sección del tutorial, verá cómo generar automáticamente metadatos para un proyecto de ASP.NET Web API y ejecutará una herramienta de prueba de la API. Para estas tareas no va a usar el Servicio de aplicaciones de Azure aún; más verá la forma en que Aplicaciones de API usa los metadatos.

Para proporcionar metadatos de Swagger 2.0 a un proyecto de ASP.NET Web API, puede instalar el paquete NuGet de [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). Swashbuckle usa la reflexión para generar metadatos dinámicamente. El paquete NuGet de Swashbuckle ya está instalado en el proyecto ContactsList.API que descargó y ya está instalado cuando se crea un nuevo proyecto con la plantilla de proyecto **Aplicación de API de Azure** (en Visual Studio: **Archivo > Nuevo > Proyecto > Aplicación web ASP.NET > Aplicación de API de Azure**.)

En esta sección del tutorial verá los metadatos de Swagger 2.0 generados y, después, probará una interfaz de usuario que se basa en los metadatos de Swagger.

2. Establezca el proyecto ContactsList.API como proyecto de inicio. (No el proyecto CompanyContacts.API, ya que ese proyecto se utiliza en uno de los tutoriales posteriores.)
 
4. Presione F5 para ejecutar el proyecto en modo de depuración.

	El explorador se abre y muestra la página 403 Prohibido.

	![](./media/app-service-api-dotnet-get-started/403.png)

12. En la barra de direcciones del explorador, agregue `swagger/docs/v1` al final de la línea y presione Entrar. (La dirección URL será `http://localhost:51864/swagger/docs/v1`.)

	Esta es la dirección URL predeterminada que Swashbuckle usa para devolver los metadatos JSON de Swagger 2.0 para la API. Si usa Internet Explorer, el explorador le pide que descargue un archivo v1.json.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Si usa Chrome o Edge, el explorador mostrará los datos JSON en la ventana del explorador.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	El ejemplo siguiente muestra la primera sección de los metadatos de Swagger para la API, con la definición del método Get. Estos son los metadatos subyacentes a la interfaz de usuario de Swagger que usará en los pasos siguientes, y los usará en una sección posterior del tutorial para generar automáticamente el código de cliente.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Cierre el explorador.

3. En el proyecto ContactsList.API, en el **Explorador de soluciones**, abra el archivo *App\_Start\\SwaggerConfig.cs* y, después, desplácese hacia abajo hasta el siguiente código y quite los comentarios.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	El archivo SwaggerConfig.cs se crea cuando se instala el paquete Swashbuckle en un proyecto. El archivo ofrece varias maneras de configurar Swashbuckle.

	El código para el que quitó los comentarios habilita la interfaz de usuario de Swagger que usará en los pasos siguientes. Al crear un proyecto de API web con la plantilla de proyecto de aplicación de API, este código aparece como comentarios de forma predeterminada como medida de seguridad.

5. Vuelva a ejecutar el proyecto.

3. En la barra de direcciones del explorador, agregue `swagger` al final de la línea y presione Entrar. (La dirección URL será `http://localhost:51864/swagger`.)

4. Cuando aparezca la página de la interfaz de usuario de Swagger, haga clic en **Contacts** (Contactos) para ver los métodos disponibles.

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. Haga clic en **Get > Try it out** (Obtener > Probar).

	La interfaz de usuario de Swagger llama al método Get de ContactsList y muestra los resultados JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Haga clic en **Post** (Publicar) y, a continuación, haga clic en el cuadro situado debajo de **Model Schema** (Esquema de modelo).

	Al hacer clic en el esquema del modelo se rellena el cuadro de entrada donde puede especificar el valor del parámetro para el método Post.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Cambie el valor JSON en el cuadro de entrada del parámetro `contact` para que tenga un aspecto similar al del ejemplo siguiente, o bien sustituya su nombre y su dirección de correo electrónico:

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. Haga clic en **Try it out** (Probar).

	La API de ContactsList devuelve un mensaje HTTP 200 y un cuerpo de respuesta que confirma lo que se agregó.

11. Haga clic en **Get > Try it out** (Obtener > Probar).

	La respuesta del método Get ahora incluye el nuevo contacto.

12. Pruebe también los métodos Put, Delete y Get by ID y, después, cierre el explorador.

Swashbuckle funciona con cualquier proyecto de ASP.NET Web API. Si desea agregar generación de metadatos de Swagger a un proyecto existente, simplemente instale el paquete de Swashbuckle. Si desea crear un nuevo proyecto que se vaya a implementar como una aplicación de API del Servicio de aplicaciones, use la plantilla de proyecto **Aplicación de API de Azure** de ASP.NET, como muestra la siguiente ilustración.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Dicha plantilla crea un proyecto de API web en el que Swashbuckle está instalado.

**Nota:** de manera predeterminada, Swashbuckle puede generar identificadores de operaciones de Swagger duplicados para los métodos del controlador. Esto sucede si el controlador tiene métodos HTTP sobrecargados, por ejemplo: `Get()` y `Get(id)`. Para obtener información sobre cómo controlar las sobrecargas, consulte [Personalización de definiciones de API generadas por Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Si crea un proyecto de API web en Visual Studio con la plantilla Aplicación de API de Azure, el código que genera identificadores de operaciones únicos se agrega automáticamente al archivo *SwaggerConfig.cs*.

## Creación de una aplicación de API en Azure e implementación del proyecto ContactsList.API en ella

En esta sección, se usan las herramientas de Azure integradas en el Asistente para **publicación web** de Visual Studio para crear una nueva aplicación de API en Azure. Después, se implementa el proyecto ContactsList.API en la nueva aplicación de API y se vuelve a ejecutar la interfaz de usuario de Swagger para llamar a la API, esta vez mientras se ejecuta en la nube.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.API y, a continuación, haga clic en **Publicar**.

3.  En el paso **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Inicie sesión en su cuenta de Azure si aún no lo hizo o actualice sus credenciales si expiraron.

4. En el cuadro de diálogo Servicio de aplicaciones, elija la **suscripción** de Azure que desee usar y, a continuación, haga clic en **Nuevo**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Cambiar tipo** y después en **Aplicación de API**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

4. En **Nombre de aplicación de API**, escriba un nombre que sea único en el dominio *azurewebsites.net*.

	Visual Studio propone un nombre único en el que se anexa una cadena de fecha y hora al nombre del proyecto. Si lo prefiere, puede aceptar ese nombre.

	Si escribe un nombre que alguien ya usó, verá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y deberá escribir un nombre diferente.

	Azure usará este nombre como prefijo de la dirección URL de la aplicación. La dirección URL completa constará de dicho nombre más *.azurewebsites.net*. Por ejemplo, si el nombre es `ContactsListAPI`, la dirección URL será `contactslistapi.azurewebsites.net`.

6. En la lista desplegable **Grupo de recursos**, escriba "ContactsListGroup" u otro nombre que prefiera.

	Este cuadro permite seleccionar un [grupo de recursos](../azure-preview-portal-using-resource-groups.md) existente o crear uno nuevo, para lo que se debe escribir un nombre de grupo recursos que no exista en la suscripción.

	Para este tutorial, es mejor crear uno nuevo porque así podrá eliminar fácilmente y en un solo paso todos los recursos de Azure que cree para el tutorial.

4. Haga clic en el botón **Nuevo** situado junto a la lista desplegable **Plan de servicio de aplicaciones**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Para más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En el cuadro de diálogo **Configurar plan del servicio de aplicaciones**, escriba "ContactsListPlan", u otro nombre que prefiera.

5. En la lista desplegable **Ubicación**, elija la ubicación más cercana.

	Esta opción especifica en qué centro de datos de Azure se ejecutará su aplicación. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, pero para una aplicación de producción, se recomienda que el servidor esté lo más próximo posible a los clientes que vayan a acceder a él, con el fin de minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. En la lista desplegable **Tamaño**, haga clic en **Gratis**.

	Para este tutorial, el plan de tarifa Gratis proporcionará un rendimiento suficiente.

6. En el cuadro de diálogo **Configurar plan del servicio de aplicaciones**, haga clic en **Aceptar**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. En el cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Crear**.

	Visual Studio crea la aplicación de API y un perfil de publicación que tiene toda la configuración necesaria para la nueva aplicación de API. En los pasos siguientes se usa el nuevo perfil de publicación para implementar el proyecto.
 
	**Nota:** hay otras maneras de crear aplicaciones de API en el Servicio de aplicaciones de Azure. En Visual Studio están disponibles los mismos cuadros de diálogo mientras se crea un nuevo proyecto. También se pueden crear aplicaciones de API mediante el Portal de Azure, [cmdlets de Azure para Windows PowerShell](../powershell-install-configure.md) o la [interfaz de línea de comandos multiplataforma](../xplat-cli.md).

8. En la pestaña **Conexión** del Asistente para **publicación web**, haga clic en **Publicar**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio implementa el proyecto ContactsList.API en la nueva aplicación de API y abre un explorador con la dirección URL de la aplicación de API. Aparece una página que indica que "se creó correctamente".

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. En la barra de direcciones del explorador, agregue "swagger" a la URL y presione Entrar. (La dirección URL será `http://{apiappname}.azurewebsites.net/swagger`.)

	El explorador muestra la misma interfaz de usuario de Swagger que vio anteriormente, pero ahora se ejecuta en la nube. Pruebe el método Get y verá que volvió a los 3 contactos predeterminados, porque los cambios realizados anteriormente se guardaron en un archivo local; los cambios que realice ahora se guardarán en el sistema de archivos de la aplicación de API de Azure.

12. Abra el [Portal de Azure](https://portal.azure.com/).
 
14. Haga clic en **Examinar > Aplicaciones de API > {su nueva aplicación de API}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Haga clic en **Configuración** y, en la hoja **Configuración**, busque la sección de API y haga clic en **Definición de API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	La hoja Definición de API le permite especificar la dirección URL que devuelve los metadatos de Swagger 2.0 en formato JSON. Cuando Visual Studio crea la aplicación de API, establece la dirección URL de la definición de API en el valor predeterminado que vio anteriormente, que es la URL base de la aplicación de API más `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Cuando se selecciona una aplicación de API para la que se va a generar código de cliente, Visual Studio recupera los metadatos de esta dirección URL.

### Dirección URL de definición de API en las herramientas del Administrador de recursos de Azure

La dirección URL de la definición de API de una aplicación de API también se puede configurar mediante las herramientas del Administrador de recursos de Azure como Azure PowerShell, CLI o el [Explorador de recursos](https://resources.azure.com/).

Establezca la propiedad `apiDefinition` en el tipo de recurso Microsoft.Web/sites/config para el recurso <site name>/web. Por ejemplo, en el **Explorador de recursos**, vaya a **suscripciones > {su suscripción} > resourceGroups > {su grupo de recursos} > proveedores > Microsoft.Web > sitios > {su sitio} > configuración > web** y verá la propiedad cors:

		"apiDefinition": {
		  "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
		}

## <a id="codegen"></a> Consumo desde un cliente .NET mediante el código de cliente generado 

Una de las ventajas de integrar Swagger en aplicaciones de API de Azure es la generación automática de código. Las clases cliente generadas facilitan la escritura de código que llama a una aplicación de API.

En esta sección verá cómo consumir una aplicación de API desde una aplicación web MVC de ASP.NET. Ejecute primero el cliente MVC y la API web localmente y luego implemente el cliente en una nueva aplicación web en el Servicio de aplicaciones de Azure y ejecútelo en la nube.

### Generación de código cliente

Puede generar código cliente para una aplicación de API con Visual Studio o desde la línea de comandos. En este tutorial se usa Visual Studio. Para más información sobre cómo hacerlo desde la línea de comandos, consulte el archivo Léame del repositorio [Azure/autorest](https://github.com/azure/autorest) en GitHub.com.

El proyecto ContactsList.MVC ya tiene el código de cliente generado, pero es preciso eliminarlo y volverlo a generar de modo que la dirección URL de su aplicación de API sea la dirección URL de destino predeterminada.

1. En el **Explorador de soluciones** de Visual Studio, en el proyecto ContactsList.MVC, elimine la carpeta *ContactsList.API*.

	Esta carpeta se creó mediante el proceso de generación de código que está punto de recorrer.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Haga clic con el botón derecho en el proyecto ContactsList.MVC y, a continuación, haga clic en **Agregar > Cliente de API de REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **Descargar desde la aplicación de API de Microsoft Azure** y, a continuación, en **Examinar**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. En el cuadro de diálogo **Servicio de aplicaciones**, expanda el grupo de recursos **ContactsListGroup**, seleccione la aplicación de API y haga clic en **Aceptar**.

	Este cuadro de diálogo ofrece más de una manera de organizar las aplicaciones de API en la lista si tiene demasiadas para desplazarse por ellas. También permite especificar una cadena de búsqueda para filtrar las aplicaciones de API por nombre.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Si no ve la aplicación de API en la lista, es probable que al crear la aplicación de API accidentalmente omitiera el paso que cambiaba el tipo de aplicación web a aplicación de API. En ese caso, puede crear una aplicación de API nueva, para lo que debe repetir los pasos que realizó anteriormente. Tendrá que elegir un nombre diferente para la aplicación de API, a menos que vaya al portal y elimine primero la aplicación web.

	Observe que al volver al cuadro de diálogo **Agregar cliente de API de REST**, el cuadro de texto se ha rellenado con la dirección URL de la definición de API que vio anteriormente en el portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Como alternativa, puede escribir la dirección URL directamente en lugar de usar el cuadro de diálogo Examinar. Por ejemplo, si implementa la API en una aplicación web y no aparece en el cuadro de diálogo Examinar, puede escribir manualmente la dirección URL que devuelve los metadatos de Swagger.

	Observe también la opción **Seleccionar un archivo de metadatos de Swagger existente**. Si desea generar el código antes de implementarlo en Azure, puede ejecutarlo localmente, descargar el archivo JSON de Swagger y seleccionarlo aquí.

9. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **Aceptar**.

	Visual Studio crea una carpeta con el nombre de la aplicación de API y genera clases cliente.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Abra *Controllers\\ContactsController.cs* para ver el código que llama a la API mediante el cliente generado.

	El siguiente fragmento de código muestra cómo crear instancias del objeto cliente y llamar al método Get.

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Este código pasa la dirección URL local de IIS Express del proyecto de API al constructor de la clase cliente para que pueda ejecutar el proyecto web MVC y el proyecto de API localmente. Si se omite el parámetro del constructor, el punto de conexión predeterminado es la dirección URL a partir de la cual se genera el código.

6. La clase cliente se generará con un nombre diferente según el nombre de la aplicación de API; cambie este código para que el nombre de tipo coincida con lo que se generó en el proyecto. Por ejemplo, si asignó el nombre ContactsListAPIContoso a la aplicación de API, el código sería similar al del ejemplo siguiente:

		private ContactsListAPIContoso db = new ContactsListAPIContoso(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

7. Compile la solución.

El controlador y las vistas del proyecto MVC tienen un aspecto similar al del controlador y las vistas con técnica de scaffolding de Entity Framework porque así es como se crearon, aplicando una técnica de scaffolding a un modelo de datos de Entity Framework y, después, realizando pequeños cambios para usar el cliente de la API de REST en lugar de un contexto de base de datos de Entity Framework.

### Ejecución en modo local

1. Establezca los proyectos ContactsList.API y ContactsList.MVC como proyectos de inicio, con ContactsList.API antes que ContactsList.MVC. (Haga clic con el botón derecho en la solución, haga clic en **Propiedades**, **Proyectos de inicio múltiples** y establezca cada proyecto en **Inicio**. Use los iconos de flecha arriba y abajo para asegurarse de que ContactsList.API está primera en la lista). 

2. Presione F5 para iniciar los proyectos.

	Un explorador muestra la página 403 de la API y otro muestra la página principal de la aplicación MVC.

3. Haga clic en **Contactos** en la barra de menús del explorador que muestra la página principal de la aplicación MVC.

	La interfaz de usuario MVC muestra los contactos almacenados localmente y puede usar la interfaz de usuario para agregar y eliminar contactos.

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Creación de una aplicación web en Azure e implementación del proyecto ContactsList.MVC en ella

En esta sección se usa el mismo método para crear una aplicación web que el usado para crear una aplicación de API, y el mismo método para implementar un proyecto web en la aplicación web.

#### Modificación del proyecto MVC para que apunte a la aplicación de API de Azure 

Antes de implementar en Azure, cambie el punto de conexión de la API en el proyecto MVC para que cuando el código se implemente, llame a la aplicación de API de Azure que creó anteriormente en lugar de a localhost.

1. En el proyecto ContactsList.MVC, abra *Controllers\\ContactsController.cs*.

2. Convierta en comentario la línea que establece la dirección URL base de la API a la dirección URL de localhost; quite el comentario de la línea que no tiene ningún parámetro de constructor. El código ahora es similar al del ejemplo siguiente, salvo que en ambas líneas el nombre de clase refleja el nombre de la aplicación de API a partir de la que generó el código.

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	La dirección URL de destino predeterminada es la aplicación de API de Azure porque generó el código a partir de ella; si usó un método diferente para generar el código, puede que tenga que especificar la dirección URL de la aplicación de API de Azure del mismo modo especificó la dirección URL local.

#### Creación de una aplicación web para hospedar el sitio de MVC

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.MVC y, a continuación, haga clic en **Publicar**.

2. En el Asistente para **publicación web**, haga clic en la pestaña **Perfil**.

3.  En la pestaña **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

5. En el cuadro de diálogo **Servicio de aplicaciones**, haga clic en **Nuevo**.

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Cambiar tipo** y asegúrese de que el tipo es **Aplicación web**.

4. En **Nombre de aplicación web**, escriba un nombre que sea único en el dominio *azurewebsites.net*.

5. Elija la **suscripción** de Azure con la que desea trabajar.

6. En la lista desplegable **Grupo de recursos**, elija el grupo de recursos que creó anteriormente.

4. En la lista desplegable **Plan del Servicio de aplicaciones**, elija el plan que creó anteriormente.

7. Haga clic en **Crear**.

	Visual Studio crea la aplicación web, crea un perfil de publicación para ella y muestra el paso **Conexión** del Asistente para **publicación web**.

### Implementación del proyecto ContactsList.Web en la nueva aplicación web

3.  En el paso **Conexión** del Asistente para **publicación web**, haga clic en **Publicar**.

	Visual Studio implementa el proyecto ContactsList.MVC en la nueva aplicación web y abre un explorador con la dirección URL de la aplicación web. Aparece la misma interfaz de usuario de MVC que vio en la ejecución local, excepto que ahora muestra los contactos almacenados en el sistema de archivos de la aplicación de API de Azure.

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## Pasos siguientes

En este tutorial vimos cómo crear aplicaciones de API, implementar código en ellos y consumirlos desde clientes .NET. El siguiente tutorial de la serie de introducción a Aplicaciones de API muestra cómo [consumir aplicaciones de API desde clientes de JavaScript mediante CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_1210_2015-->