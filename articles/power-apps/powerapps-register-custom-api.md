<properties
	pageTitle="Cómo usar API personalizadas en flujos de lógica y PowerApps | Microsoft Azure"
	description="Descripción de las API personalizadas, uso de proveedores de OAuth y uso de Swagger para agregar API personalizadas en PowerApps y flujos de lógica"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>

# ¿Qué son las API personalizadas?

Las API personalizadas son cualquier API de RESTful que puede incorporar y usar con PowerApps y flujos de lógica. Estas API se pueden hospedar en cualquier lugar, siempre y cuando esté disponible una especificación bien documentada que cumpla el estándar [OpenAPI][1].

>[AZURE.IMPORTANT] Este tema se ha movido a powerapps.microsoft.com en [What are custom APIs](https://powerapps.microsoft.com/tutorials/register-custom-api/) (Qué son las API personalizadas). Vaya a PowerApps para ver la versión más reciente. Este vínculo de Azure está en proceso de archivo.

## Qué necesita para empezar

- Cualquier persona que use PowerApps puede usar las API personalizadas. No es necesario PowerApps Enterprise.
- Se necesita un archivo de Swagger (.json) y un icono para la API personalizada. En este tema se ofrecen varias opciones para crear el archivo de Swagger. Para el icono puede usar cualquier imagen que quiera.


## Autenticación

Puede usar uno de los siguientes mecanismos de autenticación:

- Autenticación básica
- OAuth 2.0: a continuación se indican todos los proveedores de OAuth 2.0 compatibles que puede usar con la API personalizada (próximamente se admitirán más):

	- Azure Active Directory
	- Box
	- Dropbox
	- Facebook
	- Google
	- Instagram
	- OneDrive
	- SalesForce
	- Slack
	- Yammer

> [AZURE.NOTE] Próximamente se admitirá la autenticación de clave de API.


Puede obtener más información sobre cómo especificar el tipo de autenticación en el documento OpenAPI (Swagger) en [OpenAPI Specification][1] (Especificación de OpenAPI).

Si su punto de conexión de API permite el acceso no autenticado, debe quitar el objeto ```securityDefintions``` del archivo OpenAPI (Swagger). En el ejemplo siguiente, quite todos los objetos ```securityDefintions``` siguientes:

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [AZURE.TIP] En los archivos .json no puede agregar comentarios. Todo el texto se considera parte de los datos.

### Ejemplos de autenticación
* [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md) con autenticación de AAD
* [Azure WebApp](powerapps-web-api-tutorial.md) con autenticación de AAD

## Registrar una API personalizada

### Paso 1: Crear un archivo de Swagger

Puede crear un archivo de Swagger desde **cualquier** punto de conexión de API, lo que incluye:

- Cualquier API que se haya publicado y esté disponible públicamente. Entre los ejemplos se incluyen [Spotify][2], [Uber][3], [Slack][4], [Rackspace][5] y muchos más.
- Una API que haya creado e implementado en cualquier servicio en la nube donde pueda implementar aplicaciones web, como Amazon Web Services (AWS), Heroku, aplicaciones web de Azure, la nube de Google, etc.
- Una API que esté implementada en la red o en el equipo, siempre y cuando dicha API esté disponible públicamente en Internet.

Cuando se crea el archivo de Swagger, se crea un archivo .json que debe tener a mano.

#### Obtener ayuda para crear archivos de Swagger

- Si no tiene práctica en la creación de archivos de Swagger o es la primera vez que crea uno, [Get started with Swagger][6] (Introducción a Swagger) es un buen recurso.
 
- Para crear su propia API, implementarla en Azure y crear un archivo de Swagger basado en esta nueva API, consulte [Web API tutorial](powerapps-web-api-tutorial.md) (Tutorial sobre API web). En este tutorial se proporciona un archivo de trabajo de Swagger. También encontrará [Hello World example][7] (Ejemplo de Hello World) en GitHub.

- Para validar los archivos de Swagger, use el [editor de Swagger][8]. Puede pegar los datos de .json y la validación se producirá automáticamente.

- Si desea personalizar el documento de Swagger para trabajar con PowerApps y flujos de lógica, consulte [Customize your Swagger definition](powerapps-how-to-swagger.md) (Personalización de la definición de Swagger).

### Paso 2: Agregar una conexión a la API personalizada
Ahora que se ha generado el archivo de Swagger (archivo .json) para la API personalizada, agregue la conexión a PowerApps. También necesitará un icono para la API personalizada.

1. Vaya al [portal web][9] de PowerApps e inicie sesión con su cuenta profesional.

	> [AZURE.NOTE] Actualmente, las API personalizadas solo se pueden usar en el portal web de PowerApps. No se pueden usar en el cliente de PowerApps.

2. Seleccione **Connections** (Conexiones) y **Add a connection** (Agregar una conexión):  
	![](./media/powerapps-register-custom-api/createnewconnection.png "Crear API personalizada")  

3. Seleccione **Add a custom API** (Agregar una API personalizada):  
	![](./media/powerapps-register-custom-api/connecttocustomapi.png "Crear API personalizada")  
	Agregue las propiedades de la API, incluidos los archivos .json y del icono. A continuación, seleccione **Next** (Siguiente):  

	|Propiedad|Descripción|
|---|---|
|Nombre|Escriba el nombre de la API personalizada. Si se trata de la API web de ejemplo, puede asignarle el nombre **MySampleWebAPI**.|
|Definición de la API de Swagger|Busque el archivo .json que se creó desde Swagger.|
|Cargar icono de API|Busque el archivo de icono.|
|Descripción|Escriba una descripción de la API personalizada. Si se trata de la API web de ejemplo, puede escribir **Tutorial de API web de ejemplo**.|

4. Especifique las propiedades de autenticación. Si el archivo .json usa la autenticación de OAuth2 en el objeto ```securityDefintions```, se le pedirán los valores siguientes:

	|Propiedad|Descripción|
|---|---|
|Id. de cliente|Si usa uno de los proveedores de identidades de OAuth compatibles (que se enumeran en este tema), se proporciona un identificador de cliente. Escriba este identificador de cliente.
|Secreto del cliente|Escriba el secreto del cliente del proveedor de identidades que haya elegido.|  

	En los **ejemplos de autenticación** de este tema se proporcionan ejemplos de estas propiedades de OAuth.

	Si el archivo .json no usa el objeto ```securityDefintions```, no será necesario ningún valor adicional.

5. Seleccione **Crear**. La API personalizada se muestra ahora en **Available Connections** (Conexiones disponibles(:

	![](./media/powerapps-register-custom-api/mycustomapi.png "Conexiones disponibles")


> [AZURE.TIP] Si no se puede validar el archivo de Swagger, podrían sobrar caracteres. Por ejemplo, prácticamente todos los datos deben ir entre comillas, incluidos los sitios web. Si `https://mywebapi.mywebsite.com` no aparece entre comillas, el archivo no se puede validar.


### Paso 3: Agregar la API personalizada a un flujo de lógica y PowerApp
Ahora está listo para usar la API personalizada con PowerApp o con el flujo de lógica. En esta sección, se usa una API personalizada de meteorología.

#### Agregar la API personalizada al flujo de lógica
En este paso, vamos a crear un flujo de lógica muy sencillo que muestra cómo agregar la API personalizada. Para ver una experiencia más detallada, consulte [Get started with logic flows][10] (Introducción a los flujos de lógica).

1. En el [portal web][9] de PowerApps, seleccione la pestaña **Home** (Inicio).
2. En **Make a logic flow** (Crear un flujo de lógica), seleccione **Get started** (Comenzar).
3. En esta ventana, hay varias plantillas de flujo de lógica ya creadas que usan algunos escenarios comunes. Puede usar cualquiera de estas y agregarle la API personalizada. También puede elegir **Create from blank** (Crear desde cero) para crear un flujo de lógica desde cero.

	La forma más rápida de agregar la API personalizada consiste en seleccionar **Create from blank** (Crear desde cero). Se abrirá el siguiente flujo de lógica:  
	![](./media/powerapps-register-custom-api/createfromblank.png "Inicio del flujo de lógica")  

4. Seleccione **Recurrence** (Periodicidad) y establezca la frecuencia en 1 minuto:  
	![](./media/powerapps-register-custom-api/logicrecurrence.png "Seleccionar periodicidad")  	

5. Seleccione el signo más (![](./media/powerapps-register-custom-api/flowplussign.png)) y seleccione **Add an action** (Agregar una acción). En la lista, se muestra la API personalizada:  
![](./media/powerapps-register-custom-api/logicflow.png "API personalizada")

Los pasos siguientes vendrán determinados por lo que pueda hacer la API. En un ejemplo de meteorología, la API podría obtener la temperatura actual y enviar un correo electrónico mediante Office 365:

![](./media/powerapps-register-custom-api/logicflowexample.png "Ejemplo de meteorología")



#### Agregar la API personalizada a PowerApp
En este paso, vamos a crear una aplicación PowerApp muy sencilla que muestra cómo agregar la API personalizada. Para ver una experiencia más detallada, consulte [Create an app from data][11] (Creación de una aplicación a partir de datos).

> [AZURE.NOTE] Actualmente, las API personalizadas solo se pueden usar en el portal web de PowerApps. No se pueden usar en el cliente de PowerApps.

1. En el [portal web][9] de PowerApps, seleccione **New PowerApp** (Nueva aplicación PowerApp):  
	![](./media/powerapps-register-custom-api/newpowerapp.png "Seleccionar nueva aplicación PowerApp")  
2. Se abre una pestaña nueva en el explorador. En dicha pestaña, se crea automáticamente una aplicación PowerApp en blanco. Seleccione **connect to data** (conectar a datos):  
![](./media/powerapps-register-custom-api/blankpowerapp.png "Seleccionar conectar a datos")  
3. En la pestaña **Content** (Contenido), seleccione **Data sources** (Orígenes de datos):  
![](./media/powerapps-register-custom-api/datasources.png "Seleccionar conectar a datos")  
4. En la pantalla nueva, en **My connections** (Mis conexiones), seleccione la API personalizada:  
![](./media/powerapps-register-custom-api/screencustomapi.png "Seleccionar la API personalizada")  
5. Seleccione **Add data source** (Agregar origen de datos).

Una vez agregado, puede usar la API personalizada en la barra de funciones, un cuadro de texto y mucho más. Por ejemplo, en la barra de funciones, puede empezar a escribir **MySampleWebAPI** para ver las funciones disponibles. [Show data from Office 365 ][12] (Mostrar datos de Office 365) es un ejemplo del uso de la API de Office 365.


## Compartir una API personalizada
Los usuarios también pueden compartir las API personalizadas. Una vez que haya agregado una API personalizada, seleccione la pestaña **Connections** (Conexiones), **Custom APIs** (API personalizadas) y el icono de recurso compartido:

![](./media/powerapps-register-custom-api/sharecustomapi.png "Compartir la API personalizada")

> [AZURE.NOTE] Únicamente puede compartir API personalizadas con otros usuarios de su organización.

## Cuotas y limitación

- Puede crear hasta cinco API personalizadas en una cuenta de PowerApps. Las API personalizadas que hayan compartido con usted no cuentan en esta cuota.
- Para cada conexión creada en una API personalizada, los usuarios pueden realizar hasta 500 solicitudes por minuto.
- Tenga en cuenta que la eliminación de una API personalizada elimina todas las conexiones creadas con la misma.

Si tiene alguna pregunta o comentario sobre las API personalizadas, envíe un correo electrónico a [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!---HONumber=AcomDC_0713_2016-->