<properties 
	pageTitle="Creación de paquetes de aplicaciones de API" 
	description="Aprenda a crear un paquete de aplicaciones de API." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# Creación de paquetes de aplicaciones de API

## Información general

Este artículo muestra cómo crear un paquete de aplicaciones de API para publicarlo en [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Para aprender a crear una aplicación de API, consulte [Creación de una aplicación de API con Visual Studio](app-service-dotnet-create-api-app.md).
- Para obtener información sobre cómo publicar un paquete de aplicaciones de API en Azure Marketplace, consulte [Publicación de un paquete de aplicaciones de API en Azure Marketplace](app-service-api-publish-package).

## Estructura de carpetas

Un paquete de Nuget \(archivo \*.nupkg\*\) para una aplicación de API dispone de los siguientes archivos y carpetas dentro de la carpeta *Contenido*:

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Se crear un archivo *.nupkg* con esta estructura de carpetas cuando se empaqueta un proyecto de Visual Studio que tiene *apiapp.json* y *Metadata* en la carpeta del proyecto, como se muestra en la siguiente ilustración:

![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-create-package/metadatainse.png)

En las siguientes secciones, se describen los archivos y carpetas que se incluyen en la estructura de carpetas de la aplicación de API. Después, se muestra cómo se empaqueta e implementa un proyecto.

## apiapp.json

Este es el archivo de manifiesto de la aplicación de API.

|Nombre \(negrita=obligatorio\)|Tipo|Formato|Descripción|
|:---------------------|:-----|:-------|:------------|
|**id**|cadena|[a-zA-Z0-9\_.]|ID. de este paquete. Debe ser único dentro de un espacio de nombres y solo puede contener caracteres alfanuméricos, "\_" y ".". Debe comenzar con un carácter alfanumérico.|
|**namespace**|cadena|nombre de dominio|El espacio de nombres que, junto con la propiedad **id**, identifica de forma única la aplicación de API. Debe ser uno de los nombres de dominio del inquilino de AAD del anunciante.|
|**version**|cadena|[semver](http://docs.nuget.org/Create/Versioning)|La versión de este paquete. Cuando los usuarios habilitan la actualización automática de este paquete, solo se aplicará a las nuevas versiones dentro de la misma versión principal.|
|**gateway**|cadena|2015-01-14|La versión de la API de puerta de enlace que está utilizando este paquete. Una puerta de enlace es una aplicación web especial a través de la cual se enrutan todas las solicitudes a aplicaciones de API de un grupo de recursos. Una de sus funciones principales es controlar la autenticación. En la actualidad, la única versión de puerta de enlace es 14-01-2015. En el futuro, cuando se publiquen nuevas versiones de puerta de enlace, esta propiedad le dará la oportunidad de evitar cambios bruscos y seguir usando la API de puerta de enlace anterior.| 
|**title**|cadena||Nombre de la aplicación de API que se muestra.|
|**summary**|cadena|100 caracteres como máx.|Un resumen breve de la aplicación de API
|description|cadena|1500 caracteres como máx.|Descripción completa de la aplicación de API. Puede contener código HTML. Los atributos y elementos permitidos son "h1", "h2", "h3", "h4", "h5", "p", "ol", "ul", "li", "a[target\|href]", "br", "strong", "em", "b" e "i".|
|**author**|cadena|256 caracteres como máx.|Los autores de la aplicación de API.|
|homepage|cadena|URL|Página principal de la aplicación de API.|
|endpoints|objeto||Una lista de extremos que puede consultar la plataforma de aplicación de API para obtener información sobre los métodos y el estado de la aplicación de API.|
|endpoints.apiDefinition|cadena|Ruta de acceso URL|Dirección URL relativa de una API expuesta por la aplicación de API que devuelve una definición de API Swagger 2.0 en una solicitud GET \(por ejemplo, "/swagger/docs/v1"\). Si se configura, se utilizará en lugar del archivo estático apiDefinition.swagger.json en el paquete, si lo hay.|
|endpoints.status|cadena|Ruta de acceso URL|Dirección URL relativa de una API expuesta por la aplicación de API que devuelve información de estado de tiempo de ejecución sobre la aplicación API en una solicitud GET.|
|categories|cadena[]|comunidad, social, empresa, integración, protocolo, app-datasvc, otros|La categoría de Azure Marketplace de este paquete de la aplicación de API aparecerá aquí. De forma predeterminada, la aplicación de API siempre aparecerá en la categoría "comunidad". Una vez aprobada la aplicación de API, aparecerá en la categoría especificada.|
|license|objeto||Licencia de la aplicación de API.|
|**License.Type**|cadena||Identificador de la licencia de SPDX, p. ej., MIT.|
|license.url|cadena|URL|Dirección URL absoluta que señala al texto de licencia completo.|
|license.requireAcceptance|booleano|true, false|Si una licencia debe aceptarse antes de la instalación. Valor predeterminado: false.|
|links|objeto[]||Una matriz de vínculos para agregar a la página de Marketplace.|
|**links.text**|cadena||El texto del vínculo.|
|**links.url**|cadena|URL|La dirección URL absoluta del vínculo.|
|authentication|objeto[]||Una matriz que indica el tipo de autenticación que necesita esta aplicación de API para realizar llamadas de API salientes. En este momento, se admite solo una autenticación por paquete de aplicación de API.|
|**authentication.type**|cadena|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter y Yammer|La autenticación que requiere la aplicación de API. Actualmente, se admite el tipo de autenticación 11. En el futuro, se agregarán más opciones.| 
|authentication.scopes|cadena[]||Una matriz de ámbitos que son específicas del tipo de autenticación.|
|copyright|cadena|El aviso de derechos de autor de la aplicación de API.
|brandColor|cadena|Cualquier formato compatible con CSS|Un color de marca opcional para controlar la experiencia de la interfaz de usuario. Por ejemplo, el diseñador de la aplicación lógica utiliza esta propiedad para dibujar el color de fondo del encabezado de tarjeta.|

## metadata/apiDefinition.swagger.json

Si lo desea, puede proporcionar un archivo estático de JSON Swagger 2.0 aquí para exponer la definición de la API de su aplicación de API. La plataforma comprobará primero si la propiedad **endpoints.apiDefinition** está configurada en **apiapp.json**. En caso afirmativo, obtendrá la definición de la API desde la dirección URL especificada en la propiedad. Si no es así, intentará encontrar este archivo.

- Para obtener información acerca del estándar Swagger 2.0, consulte [http://swagger.io/](http://swagger.io/). <!--pendiente de proporcionar direcciones URL
- Para obtener información sobre cómo personalizar la definición de la API para optimizarla para las aplicaciones lógicas, consulte [título del documento]().
- Para obtener información acerca de cómo exponer una definición de API dinámica, consulte [título del documento]().--\>

## metadatos/iconos

Si lo desea, puede proporcionar su propio conjunto de iconos para el paquete de aplicaciones de API. Si no se proporcionan los archivos de iconos necesarios, se utilizará un icono predeterminado, como se muestra a continuación.

![Icono grande predeterminado de la aplicación de API](./media/app-service-api-create-package/api-app-default-large-icon.png)

|Archivo |Ancho|Alto|Descripción|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|Necesario si desea utilizar sus propios iconos.|
|metadata/icons/medium.png|90px|90px|Necesario si desea utilizar sus propios iconos.|
|metadata/icons/large.png|115px|115px|Necesario si desea utilizar sus propios iconos.|
|metadata/icons/wide.png|255px|115px|Necesario si desea utilizar sus propios iconos.|
|metadata/icons/hero.png|815px|290px|Opcional si desea utilizar sus propios iconos.|

## metadata/screenshots

Si lo desea, puede proporcionar hasta 5 capturas de pantalla para el paquete de aplicaciones de API.

|Archivo|Ancho|Alto|Descripción|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/\*.png|533px|324px|Capturas de pantalla del paquete de aplicaciones de API.|

## metadata/deploymentTemplates

A veces, un paquete de aplicación de API requiere cierta configuración personalizada durante la implementación. Por ejemplo, el [conector de blobs de almacenamiento de Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) requiere el URI del contenedor de blobs de almacenamiento de Azure. Si lo desea, también puede configurar una clave de acceso.

Para usar este escenario, puede agregar una lista de los archivos de plantilla JSON del Administrador de recursos de Azure \(ARM\) en esta carpeta para personalizar la implementación de las aplicaciones de API. La plataforma de la aplicación de API combinará las plantillas personalizadas de ARM con nuestra plantilla del sistema para producir una plantilla final para la implementación. Todos los parámetros definidos en las plantillas personalizadas de ARM \(esperados para **$system**\) también se solicitarán automáticamente en la hoja de **creación** del portal de vista previa de Azure para que los usuarios de la aplicación de API puedan especificar los valores.

A continuación, aparece una plantilla de ARM de ejemplo que muestra cómo pedir un URI de contenedor de blob y una clave de acceso durante la implementación de la aplicación de API.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

En la siguiente captura de pantalla, se muestra la hoja de creación del portal de vista previa de Azure correspondiente. \(La captura de pantalla muestra un paquete de aplicaciones de API que usa UIDefinition.json para mejorar la hoja de creación. Para obtener más información, consulte [metadata/UIDefinition.json](#metadata-uidefinition-json).

![Ejemplo de hoja de creación con plantilla personalizada de ARM](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](https://msdn.microsoft.com/library/dn835138).

## metadata/UIDefinition.json

Cuando necesite utilizar plantillas personalizadas de ARM para especificar la configuración personalizada necesaria para implementar una aplicación de API, la hoja de **creación** del portal de vista previa de Azure solicita automáticamente los parámetros de la plantilla de ARM para que los usuarios de la aplicación de API puedan especificar los valores. Puede utilizar el archivo *UIDefinition.json* para mejorar la interfaz de usuario de esta hoja de **creación** proporcionando valores predeterminados, información sobre herramientas, validaciones, etc.

Para proporcionar un *archivo UIDefinition.json*, comience con el siguiente esquema y siga la tabla que aparece a continuación para configurar cada parámetro.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|Nombre |Tipo|Descripción|
|:---------------------|:-------|:------------|
|defaultValue|cadena|Valor predeterminado del control de entrada en la hoja de creación del portal de vista previa de Azure.|
|DisplayName|cadena|Etiqueta del control de entrada en la hoja de creación del portal de vista previa de Azure. Debe ser un valor corto.|
|description|cadena|Descripción del control de entrada.|
|tooltip|cadena|Información sobre herramientas del control de entrada en la hoja de creación del portal de vista previa de Azure. Esto se mostrará cuando los usuarios hagan clic en el globo de información situado en la parte derecha de la etiqueta. Esto puede dar como resultado un proceso largo y exhaustivo.|
|constraints|Objeto|Restricciones para comparar con el parámetro.|
|constraints.required|booleano|Determina si el parámetro es necesario o no. El valor predeterminado es false.|

Por ejemplo, este es el archivo *UIDefinition.json* para el [conector de blobs de almacenamiento de Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/):

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

Esto configura la hoja de **creación** del portal de vista previa de Azure para que muestre la siguiente hoja para **configurar el paquete**.

![Ejemplo de hoja de creación con UIDefinition](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
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
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## Creación de un paquete de aplicaciones de API

Para crear un paquete de aplicaciones de API

1. [Descargue e instale la interfaz de la línea de comandos entre plataformas de Azure](xplat-cli).
2. Cambie al modo de Administrador de recursos de Azure ejecutando el siguiente comando:

        azure config mode arm

3. Ejecute el siguiente comando para crear un paquete de NuGet para la aplicación de API:

        azure apiapp package create -p <package folder> -o <destination folder>

    Por ejemplo:

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

Esto validará el contenido del paquete para asegurarse de que se cumplan los siguientes aspectos:

- Se sigue el formato descrito anteriormente.
- Metadata\\apiDefinition.swagger.json \(si se proporciona\) contiene una definición válida de la API de Swagger 2.0

Si hay algún problema, se mostrarán los detalles para que pueda solucionar el problema y crear un paquete de aplicaciones de API válido.

## Pasos siguientes

El paquete de aplicaciones de API ya está listo para publicarlo en Azure Marketplace. Para conocer el procedimiento, consulte el tutorial [Publicación de un paquete de aplicaciones de API en Azure Marketplace](app-service-api-publish-package).

<!--HONumber=52-->
