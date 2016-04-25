<properties
	pageTitle="Tutorial: creación de una API web de AAD personalizada para PowerApps y flujos de lógica | Microsoft Azure"
	description="Tutorial sobre aplicaciones web de ASP.NET para crear una API personalizada en PowerApps y flujos de lógica"
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
   ms.date="04/12/2016"
   ms.author="mandia"/>

# Tutorial: creación de una API web protegida de AAD personalizada para PowerApps y flujos de lógica

En este tutorial se muestra cómo crear una API web de ASP.NET, hospedarla en las aplicaciones web de Azure, habilitar la autenticación de AAD (mediante Easy Auth) y, después, registrar la API web en PowerApps y flujos de lógica.

## Qué necesita para empezar

* Una suscripción de Azure
* Una cuenta de PowerApps
* Visual Studio 2013 o superior

## Paso 1: creación de una API web y su implementación en Azure
1. Abra Visual Studio y cree un proyecto nuevo de aplicación web ASP.NET en C#: ![](./media/powerapps-web-api-tutorial/newwebapp.png "Nueva aplicación web")

2. En la siguiente pantalla, seleccione la plantilla de API web y, después, **Sin autenticación**: ![](./media/powerapps-web-api-tutorial/noauth.png "Sin autorización")

	>[AZURE.IMPORTANT] Recuerde establecer la autenticación en "Sin autenticación".

3. Una vez que se haya generado el proyecto, tendrá que crear la API web para sus recursos. En este tutorial, no profundizaremos en los detalles de cómo crear una API web.

4. Ahora, genere un archivo de Swagger para la API web. Puede hacerlo fácilmente si abre la __Consola del Administrador de paquetes__ e instala __Swashbuckle__: ![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Consola de Swashbuckle")

5. Una vez que lo haya instalado y habilitado, busque los siguientes documentos de Swagger y puntos de conexión de la interfaz de usuario, respectivamente: **<su-url-raíz>/swagger/docs/v1**

 	**<su-url-raíz>/swagger**

6. Una vez que considere que su API web está lista, publíquela en Azure. Para publicar en Visual Studio, vaya a **Compilar** y seleccione **Publicar**.

7. Extraiga el JSON de Swagger yendo a ***https://\<url-aplicación-web-azure>/swagger/docs/v1***.

	> [AZURE.IMPORTANT] Los documentos de Swagger con valores operationId duplicados no son válidos. Si utiliza la plantilla de C# de ejemplo, el operationId "Values\_Get" se repite dos veces. Cambie una instancia a "Value\_Get".


Puede descargar [aquí][6] el archivo de Swagger utilizado en este tutorial. Recuerde sustituir o eliminar los comentarios antes de usarlo. Estos empiezan por `//`.

## Paso 2: configuración de la autenticación de AAD

En este tutorial se da por hecho que sabe cómo crear aplicaciones de AAD en Azure. Para obtener más información sobre cómo crear una aplicación de AAD, consulte el [tutorial de Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md). Se necesitan dos aplicaciones de AAD para este tutorial.

1. La primera aplicación de AAD se usa para proteger la API web. Asígnele el nombre **webAPI**.
2. La segunda aplicación de AAD se utiliza para proteger el registro de API personalizado y adquirir acceso delegado a la API web protegida por la primera aplicación. Asigne a esta el nombre **webAPI-customAPI**.
3. Para **webAPI**, use la siguiente configuración:  

  1. URL de inicio de sesión:******https://login.windows.net***.
  2. URI de id. de aplicación: ***https://\<su-url-raíz>*** (normalmente, la URL de su sitio web implementado en Azure).
  3. URL de respuesta: ***https://\<su-url-raíz>/.auth/login/aad/callback***.  
  
	>[AZURE.IMPORTANT] Necesitará el id. de cliente de esta aplicación más adelante, así que anótelo.

4. Para **webAPI-customAPI**, utilice la siguiente configuración:
  
  1. URL de inicio de sesión:****https://login.windows.net**.
  2. URI de id. de aplicación: ***puede ser cualquier URL única***.
  3. URL de respuesta: ******https://msmanaged-na.consent.azure-apim.net/redirect***.
  4. Agregue permisos para disfrutar de acceso delegado a webAPI.
  5. También necesitará el id. de cliente de esta aplicación más adelante, así que anótelo.
  6. Genere una clave y guárdela en un lugar seguro. La necesitará más adelante.

>[AZURE.IMPORTANT] Ambas aplicaciones deben estar en el mismo directorio.

## Paso 3: configuración de Easy Auth en su aplicación web

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y vaya a la aplicación web que implementara en el **paso 1** (de este tema).
2. En **Configuración**, seleccione **Autenticación/autorización**.
3. Active **Autenticación del servicio de aplicaciones** y seleccione **Azure Active Directory**. En la siguiente hoja, seleccione **Rápido**.  
4. Haga clic en **Seleccionar aplicación de AD existente** y seleccione la primera aplicación de AAD que creó en el paso 2. En este caso, seleccione **webAPI**.

Con esto, se deberá haber configurado la autenticación de AAD para la aplicación web.

## Paso 4: configuración de la API personalizada 

1. Tenemos que modificar ligeramente nuestro archivo de Swagger a fin de especificar el objeto `securityDefintions` y la autenticación de AAD empleada para la aplicación web. Agregue las siguientes líneas de código: 

	```javascript
  "host": "<your-root-url>",
  "schemes": [
    "https"						//Change scheme to https 
  ],
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
	```

2. Vaya al [portal web][1] de PowerApps y agregue una API personalizada. En el artículo sobre el [uso de API personalizadas en flujos de lógica y PowerApps](powerapps-register-custom-api.md) se enumeran los pasos.

3. Una vez que se haya cargado su archivo de Swagger, el asistente detectará automáticamente que está usando la autenticación de AAD para su aplicación webAPI.

4. Configure la autenticación de AAD para su API personalizada:

  1. Id. de cliente: ***el id. de cliente de webAPI-CustomAPI*** del punto 4.5 del **paso 2** (de este tema)
  2. Secreto: ***la clave de webAPI-CustomAPI*** del punto 4.6 del **paso 2** (de este tema)
  3. Dirección URL de inicio de sesión: ******https://login.windows.net***
  4. URI del recurso: ***el id. de cliente de webAPI*** de la nota del punto 3.4 del **paso 2** (de este tema)

5. Seleccione **Crear** y pruebe a crear una conexión en la API personalizada. Si todo está configurado correctamente, debe poder iniciar sesión sin problemas.

Para obtener información más detallada sobre la creación de PowerApps y flujos de lógica, consulte lo siguiente:

- [Connect to Office 365, Twitter, and Microsoft Translator][5] (Conexión a Office 365, Twitter y Microsoft Translator)
- [Show data from Office 365][4] (Visualización de datos de Office 365)
- [Create an app from data][3] (Creación de una aplicación a partir de datos)
- [Get started with logic flows][2] (Introducción a los flujos de lógica)

Si tiene alguna pregunta o comentario, envíenos un correo a [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0413_2016-->