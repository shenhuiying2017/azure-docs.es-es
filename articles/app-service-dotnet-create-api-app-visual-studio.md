<properties 
	pageTitle="Conversión de una API existente a una aplicación de API" 
	description="Obtenga información acerca de cómo configurar un proyecto de Web API en Visual Studio para implementarlo como una aplicación de API en el Servicio de aplicaciones de Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Conversión de una API existente a una aplicación de API

## Información general

En este tutorial creará un proyecto ASP.NET Web API en Visual Studio y, a continuación, agregará paquetes de NuGet y metadatos de proyecto que le permitirán publicar e implementar el proyecto como una aplicación de API en el Servicio de aplicaciones de Azure. En el tutorial también se explica cómo personalizar los metadatos de la aplicación de API.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creación de un proyecto de API web

1.  Abra el cuadro de diálogo **nuevo proyecto** en Visual Studio.

2.  Seleccione el nodo **Nube** en el panel **Plantillas instaladas** y, a continuación, seleccione la plantilla **Aplicación web ASP.NET**.

3.  Asigne el nombre  *ContactsList* al proyecto y haga clic en **Aceptar**.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **Vacío**, haga clic en la casilla **API web**, desactive la casilla **Hospedar en la nube** y, a continuación, haga clic en **Aceptar**.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio crea archivos de proyecto para un proyecto de API web vacío.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta **Modelos** y, a continuación, en el menú contextual, haga clic en **Agregar > Clase**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. Asigne el nombre *Contact.cs* al nuevo archivo y haga clic en **Agregar**.

5. Reemplace todo el contenido del archivo con el código siguiente. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. Haga clic con el botón secundario en la carpeta **Controladores** y, a continuación, en el menú contextual, haga clic en **Agregar > Controlador**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. En el cuadro de diálogo **Agregar scaffold**, seleccione la opción **Controlador de Web API 2: Vacío** y haga clic en **Agregar**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. Asigne el nombre **ContactsController** al controlador y haga clic en **Agregar**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. Reemplace el código de este archivo con el código siguiente. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


Ahora tiene un proyecto de API web en funcionamiento. La manera más sencilla de comprobar que funciona es llamar al método Get desde un explorador.

6. Presione CTRL+F5 para ejecutar el proyecto.

	El explorador muestra un error HTTP 403 porque se abre sin una dirección URL completa que señala al método Get.

7. En la barra de direcciones del explorador, agregue "api/contacts/get/" a la URL y presione Entrar.  El aspecto de la dirección URL final será como se muestra en el siguiente ejemplo:

		http://localhost:25735/api/contacts/get/

	Cada explorador responde de manera diferente a una llamada de API; si usa Internet Explorer, verá un mensaje de descarga en la parte inferior de la ventana del explorador:

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## Configuración de la aplicación para implementarla como una aplicación de API

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto (no la solución) y, a continuación, haga clic en **Agregar > SDK de aplicación de API de Azure**.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. En el cuadro de diálogo **Elegir origen de metadatos de la aplicación de API**, haga clic en **Generación automática de metadatos**.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Esta opción habilita la IU dinámica de Swagger, que verá más adelante en el tutorial. Como alternativa, puede proporcionar un archivo de definición de API de Swagger estático. Para ello, seleccione **Especificar un archivo JSON estático que contiene metadatos de Swagger** y Visual Studio le indicará que cargue un archivo. El archivo que cargue se guarda como *apiDefinition.Swagger.json* en la carpeta *Metadata*.

3. Haga clic en **Aceptar**.

	Visual Studio agrega un archivo *apiapp.json* y una carpeta *Metadata*.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Visual Studio también agrega paquetes de NuGet de Swashbuckle, y en los siguientes pasos, probará la IU de definición de API dinámica que proporciona Swagger. 

6. Presione CTRL+F5 para ejecutar el proyecto.

	Al igual que antes, el explorador muestra un error HTTP 403.

7. En la barra de direcciones del explorador, agregue "swagger/" a la URL y presione Entrar.  El aspecto de la dirección URL final será como se muestra en el siguiente ejemplo:

		http://localhost:25735/swagger/

8. En la página Swagger, haga clic en **Contacts** (Contactos) para ver los métodos disponibles.
 
	Solo se muestra `Get` porque es el único método que creó en el controlador de contactos. La página muestra la respuesta JSON de ejemplo.

9. Haga clic en el método Get para ver una respuesta JSON de ejemplo y un botón **Probarlo**.

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. Haga clic en **Probarlo**.

	Se devuelve la respuesta que codificó en ContactsController.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

El proyecto de API web ya está listo para implementarse como una aplicación de API en el Servicio de aplicaciones de Azure. En las secciones siguientes de este tutorial se proporciona información sobre los metadatos que se pueden modificar para personalizar la aplicación de API. 

## Revisión de apiapp.json

La configuración del archivo  *apiapp.json* determina cómo se identifica la aplicación de API y cómo se presenta en Azure Marketplace. En esta versión preliminar versión, Visual Studio no incluye la capacidad para publicar aplicaciones de API en Marketplace, por lo que muchas de estas opciones no tienen ningún efecto.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

El contenido inicial del archivo que se crea al elegir la entrada del menú **SDK de aplicación de API de Azure** tiene el aspecto que se muestra en el ejemplo siguiente:

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

En la siguiente tabla se explica el formato y el uso de los campos incluidos en el archivo y los campos opcionales adicionales que se pueden agregar a él. 

**Nota:** como se mencionó anteriormente, para esta versión preliminar, muchos de estos campos no tienen ningún efecto porque determinan cómo se presenta la aplicación de API en Azure Marketplace, pero Visual Studio aún no incluye la capacidad para publicar aplicaciones de API en Marketplace.

| Nombre (negrita=obligatorio) | Tipo | Comentarios |
|:-----------|:------------|:------------|
|**id**           |string|ID. de este paquete. Debe ser único dentro de un espacio de nombres y no debe contener puntos, / ni caracteres @. Al implementar el proyecto, Visual Studio valida que el identificador sea único y le ofrece la oportunidad de cambiarlo. 
|**espacio de nombres**    |string|El espacio de nombres que junto con la propiedad **id** identifica de forma única la aplicación de API.  La propiedad es obligatoria pero el valor puede ser una cadena vacía. <br/><br/>Esta propiedad permite especificar un dominio, como contoso.com.  Si ya se ha tomado el identificador del paquete que desea usar, puede agregar un dominio a fin de poder usar ese identificador del paquete. Por ejemplo, si ContactsList ya existe en la Galería de aplicaciones de API sin un espacio de nombres, puede agregar un paquete de ContactsList con el espacio de nombres contoso.com. <br/><br/>Otra razón para especificar un dominio consiste en agregar un paquete a la Galería de aplicaciones de API a que solo tengan acceso los miembros de su organización. <br/><br/>El espacio de nombres se usará como el nombre del publicador en Marketplace, tras convertir puntos en guiones y guiones en dos guiones (-).<br/><br/>El espacio de nombres es "microsoft.com" para aplicaciones de API proporcionadas por Microsoft.  
|**versión**      |string|Formato [Semver](http://docs.nuget.org/Create/Versioning), p. ej., 1.0.1, 1.1.0-alfa.
|**puerta de enlace**      |string|Versión de puerta de enlace, expresada como una fecha, por ejemplo: 14-01-2015. Una *gateway* es una aplicación web especial a través de la cual se enrutan todas las solicitudes a aplicaciones de API de un grupo de recursos. Una de sus funciones principales es controlar la autenticación. En la actualidad, la única versión de puerta de enlace es 14-01-2015. En el futuro cuando se publiquen nuevas versiones de puerta de enlace, esta propiedad le dará la oportunidad de evitar cambios bruscos y seguir usando la API de puerta de enlace anterior. 
|**título**        |string|Nombre de la aplicación de API que se muestra.
|**resumen**      |string|Un resumen breve de la aplicación de API, 100 caracteres como máximo.
|descripción      |string|Descripción completa de la aplicación de API. Puede contener HTML, 1.500 caracteres como máximo.
|**autor**       |string|Los autores de la aplicación de API, 256 caracteres como máximo.
|página principal         |URI|Página principal de la aplicación de API.
|extremos        |objeto[]|Una matriz de un elemento que puede contener un extremo de definición de API. 
|>>endpoints.apiDefinition|string|URI relativo de la IU de definición de API dinámica de Swagger (por ejemplo, "/swagger/docs/v1") o de un archivo de definición de API estático de Swagger. Para ASP.NET Web API, la IU de Swagger generada dinámicamente suele ser la mejor opción, pero si no funciona con su API o si no usa API Web de ASP.NET, puede proporcionar un archivo de definición estático. Para proporcionar un archivo de definición estático, puede especificar el URI relativo que señala al archivo aquí, o puede dejar esta propiedad vacía e incluir el archivo de definición de API estático en la carpeta de metadatos como [apiDefinition.swagger.json](#apidef). 
|>>endpoints.status|URI|Reservado para uso futuro.
|categorías       |string[]|Determina dónde se mostrará el paquete en Azure Marketplace. Los valores válidos son: social, empresa, integración, protocolo, app-datasvc, otros. Valor predeterminado: otros.
|licencia          |objeto|Licencia de la aplicación de API.
|>>**license.type**|string|Identificador de la licencia de SPDX, p. ej., MIT.
|>>license.url    |url|Dirección URL absoluta que señala al texto de licencia completo.
|>>license.requireAcceptance|booleano|Si una licencia debe aprobarse antes de la instalación. Valor predeterminado: false.
|vínculos            |objeto[]|Una matriz de vínculos para agregar a la página de Marketplace.
|>>**links.text** |string|Texto del vínculo.
|>>**links.url**  |url|Dirección URL del vínculo.
|autenticación.|objeto[]|Una matriz que indica el tipo de autenticación que necesita esta aplicación de API para realizar llamadas de API salientes.  Por ejemplo, un conector de DropBox necesita autenticarse en DropBox y un conector de Salesforce necesita autenticarse en Salesforce.
|>>authentication.type|string|Los valores admitidos son los siguientes (no distingue mayúsculas de minúsculas): Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer. Según este valor, el portal sabe qué valores de configuración, como el Id. de cliente y el secreto de cliente, son necesarios. 
|>>authentication.scopes|string[]|Una matriz de ámbitos que son específicas sel tipo de autenticación.
|derechos de autor        |string|El aviso de derechos de autor de la aplicación de API.
|brandColor       |string|Un color de marca opcional para controlar la experiencia de la interfaz de usuario, en cualquier formato compatible con CSS, por ejemplo, #abc, rojo.
|etiquetas             |string[]|Una lista de etiquetas relacionadas con el paquete.

<!--TareaPendiente: agregar cuando esté listo a dependencias de documento
|dependencias    |objeto[]|Una matriz de dependencias del paquete.
|>>dependencies.id|string|Identificador del paquete de dependencias.
|>>dependencies.domain|string|Dominio del paquete de dependencias.
|>>dependencies.version|string|Versión del paquete de dependencias.
-->

<!--TareaPendiente: agregar cuando esté listo al URI de estado del documento
URI del método Get de un servicio web que devuelve un valor que indica el estado actual de la aplicación de API. Si se proporciona este URI, el portal mostrará el estado operativo actual de la aplicación de API junto con otra información sobre la aplicación de API, por ejemplo:  se está ejecutando, llegando a su cuota, expiración de certificado SSL, etc. El formato de JSON que espera recibir el portal se muestra a continuación, después del final de esta tabla. Si no proporciona un URI de endpoints.status, el portal muestra el estado de la plataforma de Azure como estado de la aplicación de API.
Este es un ejemplo que muestra el formato esperado de la respuesta JSON desde el método Get que `endpoints.status` señala a:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
La propiedad `name` es una descripción breve del estado, `message` es una descripción más larga, y `level` puede ser "Error", "Advertencia" o "Información" para el estado normal.
-->

## Revisión de la carpeta Metadatos

La carpeta Metadatos puede contener iconos y capturas de pantalla de la Galería de aplicaciones de API, un archivo Swagger que documenta la API y la configuración de la interfaz de usuario para el portal de Azure. Toda esta información es opcional.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**Nota:** como se indica anteriormente para *apiapps.json*, los metadatos que pertenecen a la presentación de la aplicación de API en Azure Marketplace no tienen ningún efecto en esta versión preliminar, porque Visual Studio no incluye la capacidad para publicar aplicaciones de API en Marketplace.

### La carpeta Metadatos/iconos

Puede proporcionar iconos que se mostrarán en la Galería. 
Si no se proporcionan iconos personalizados, se usarán iconos de aplicación de API genéricos. Los archivos de icono deben estar en formato PNG y seguir estas convenciones de denominación y tamaño:

<!--todo: also used in the workflow designer--> 

|Nombre de archivo|Tamaño
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### Carpeta Metadatos/capturas de pantalla

Puede proporcionar hasta cinco capturas de pantalla para mostrar en la Galería. Los archivos de imagen deben estar en formato PNG, 533 x 324.

### <a id="apidef"></a>Archivo Metadata/apiDefinition.swagger.json

Puede usar un formato [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) para describir la definición de API de la aplicación de API. Esto le permite exponer las definiciones de API estáticamente en el paquete. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- tareapendiente: averiguar si hay algo para reemplazar esto, podría ser la subcarpeta deploymentTemplates.
### Archivo resourceTemplate.delta.json
Puede especificar una plantilla personalizada del Administrador de recursos de Azure que se ejecuta durante la implementación de la aplicación de API. Los recursos especificados en este archivo delta se agregan a los recursos que se crean de forma predeterminada para una aplicación de API. Por ejemplo, si su aplicación de API requiere una instancia de Base de datos SQL, puede usar este archivo para hacer que la base de datos se pueda aprovisionar automáticamente  y la cadena de conexión que se establecerá en valores de configuración cuando se implemente la aplicación de API.
-->  

### Archivo Metadata/UIDefinition.json

Puede proporcionar información de la interfaz de usuario como sugerencias y validación en el formato de [Azure
Formato de Marketplace](https://auxdocs.azurewebsites.net/es-es/documentation/articles/gallery-items).

### Captura Metadatos/implementación

Visual Studio crea esta carpeta cuando elige la entrada de menú **SDK de aplicación de API de Azure**, pero para esta versión de vista previa no se usa.

## Pasos siguientes

El proyecto de API web está listo para implementarse como una aplicación de API y puede seguir el tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md) para hacerlo.

Para obtener más información, consulte [¿Qué son las aplicaciones de API?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->