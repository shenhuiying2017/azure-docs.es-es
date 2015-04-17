<properties 
	pageTitle="Protección de una aplicación API de Azure" 
	description="Obtenga información acerca de cómo proteger una aplicación de API de Azure mediante el uso de Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Protección de una aplicación API: Adición de Azure Active Directory o de autenticación de proveedor social

## Información general

En el tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md), va a implementar una aplicación de API con nivel de acceso **disponible para cualquier persona**. En este tutorial se muestra cómo proteger una aplicación de API para que solo los usuarios autenticados puedan tener acceso a ella.

Realizará los pasos siguientes:

- Llamar a la aplicación de API para comprobar que funciona.
- Aplicar las reglas de autenticación a la aplicación de API.
- Volver a llamar a la aplicación de API para comprobar que rechaza las solicitudes no autenticadas.
- Iniciar sesión en el proveedor configurado.
- Volver a llamar a la aplicación de API para comprobar que el acceso autenticado funciona.

## Requisitos previos

Este tutorial funciona con la aplicación de API que creó en [Creación de una aplicación de API](app-service-dotnet-create-api-app.md) e implementado en [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).

## Uso del explorador para llamar a la aplicación de API 

La manera más sencilla de comprobar que la aplicación de API es accesible públicamente es llamarla desde un explorador.

1. En el explorador, vaya al [Portal de Azure].

3. En la página principal, haga clic en **Examinar > Aplicaciones de API** y, a continuación, haga clic en el nombre de la aplicación de API que desea proteger.

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

3. En la hoja **Aplicación de API**, haga clic en la **Dirección URL** para abrir una ventana del explorador que llama a su aplicación de API.

	![API App blade](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. Agregue `/api/contacts/get/` a la dirección URL en la barra de direcciones del explorador.

	Por ejemplo, si la dirección URL de la aplicación de API es la siguiente:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	La dirección URL completa sería la siguiente:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	Los distintos exploradores controlan las llamadas a API de manera diferente. En la imagen se muestra una llamada realizada correctamente desde un explorador Chrome.

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. Guarde la dirección URL que ha utilizado, pues la volverá a usar más adelante en el tutorial.

## Protección de la aplicación de API

Cuando implementa una aplicación de API, lo hace en un grupo de recursos. Puede agregar aplicaciones web y otras aplicaciones de API para el mismo grupo de recursos y cada aplicación de API dentro del grupo de recursos puede tener una de las tres configuraciones de accesibilidad siguientes:
<!--todo: diagram showing different accessibility settings-->

- **Público (anónimo)**: cualquier persona puede llamar a la aplicación de API desde fuera del grupo de recursos sin tener que iniciar sesión.
- **Público (autenticado)**: solo los usuarios autenticados pueden llamar a la aplicación de API desde fuera del grupo de recursos.
- **Interno**: solo otras Aplicaciones de API o aplicaciones web del mismo grupo de recursos pueden llamar a la aplicación de API.

Cuando Visual Studio crea el grupo de recursos, también crea una *puerta de enlace*.  Una puerta de enlace es una aplicación web especial que controla todas las solicitudes destinadas a las Aplicaciones de API del grupo de recursos.

Cuando vaya a la hoja del grupo de recursos en el [Portal de Azure], podrá ver la aplicación de API y la puerta de enlace en el diagrama.

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

Para configurar la aplicación de API para aceptar únicamente solicitudes autenticadas, establecerá su accesibilidad en **Público (autenticado)** y configurará la puerta de enlace para requerir la autenticación de un proveedor como Azure Active Directory, Google o Facebook.

1. Vuelva a la hoja **Aplicación de API** de la aplicación de API que desea proteger.

2. En la hoja **Aplicación de API**, haga clic en **Configuración** y, a continuación, haga clic en **Configuración de la aplicación**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Application settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. En la hoja **Configuración de la aplicación**, cambie el **Nivel de acceso** a **Público (autenticado)** y, a continuación, haga clic en **Guardar**.

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	La aplicación de API ya está protegida contra el acceso no autenticado. A continuación, tendrá que configurar la puerta de enlace para especificar qué proveedor de autenticación se va a utilizar.

4. Desplácese a la izquierda a la hoja **Aplicación de API** y, a continuación, haga clic en el vínculo a la puerta de enlace.

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. En la hoja **Puerta de enlace**, haga clic en **Configuración** y, a continuación, haga clic en **Identidad**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	En la hoja **Identidad** puede navegar a los distintos módulos para configurar la autenticación mediante Azure Active Directory y otros proveedores.

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. Elija el proveedor de identidades que desea utilizar y siga los pasos descritos en el artículo correspondiente para configurar la aplicación de API con ese proveedor. Estos artículos se han escrito para aplicaciones móviles, pero los procedimientos son los mismos para las Aplicaciones de API. Algunos de los procedimientos requieren que se use el [portal antiguo]. 

 - [Cuenta Microsoft](app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Inicio de sesión en Facebook](app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Inicio de sesión en Twitter](app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Inicio de sesión en Google](app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Por ejemplo, las siguientes capturas de pantalla muestran lo que debe ver en las páginas del [portal antiguo] y en las hojas [Portal de Azure] una vez configurada la autenticación de Azure Active Directory.

En el Portal de Azure, la hoja **Azure Active Directory** tiene un **identificador de cliente** de la aplicación que ha creado en la pestaña de Azure Active Directory del portal antiguo, e **Inquilinos permitidos** tiene el inquilino de Azure Active Directory (por ejemplo, "contoso.onmicrosoft.com").

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

En el portal antiguo, la pestaña **Configurar** de la aplicación que ha creado en la pestaña **Azure Active Directory** tiene las opciones **URL de inicio de sesión**, **URI de id. de aplicación** y **URL de respuesta** en la hoja **Azure Active Directory** del Portal de Azure.

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

(La dirección URL de respuesta de la imagen muestra la misma dirección URL dos veces, una vez con `http:` y otra con `https:`).

## Comprobación de que funciona la autenticación 

1. Abra una ventana del explorador y, en la barra de direcciones, escriba la dirección URL que llama el método 'Get' de la aplicación de API, tal como hizo anteriormente.

	Esta vez el intento de obtener acceso a la aplicación de API da como resultado un mensaje de error.

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. En el explorador, vaya a la dirección URL de inicio de sesión: 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Por ejemplo, si ha denominado el grupo de recursos myfirstrg y ha configurado la puerta de enlace para la autenticación de Azure Active Directory, la dirección URL será la siguiente:

    	http://myfirstrggateway.azurewebsites.net/login/aad

	Observe que a diferencia de la dirección URL anterior, esta no incluye el nombre de aplicación de API:  la puerta de enlace está autenticando al usuario, no a la aplicación de API.  La puerta de enlace controla la autenticación para todas las Aplicaciones de API del grupo de recursos.

	(Si tiene un problema al iniciar sesión, pruebe a abrir una ventana privada o de incógnito).

3. Escriba sus credenciales cuando el explorador muestra una página de inicio de sesión. 
 
	Si configura el inicio de sesión de Azure Active Directory, utilice uno de los usuarios enumerados en la pestaña **Usuarios** para la aplicación que ha creado en la pestaña Azure Active Directory del [portal antiguo], como admin@contoso.onmicrosoft.com.

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. Cuando aparezca el mensaje de "inicio de sesión completa", vuelva a escribir la dirección URL al método Get de la aplicación de API.

	Como ya se ha autenticado, la llamada es correcta. La puerta de enlace reconoce que es un usuario autenticado y pasa la solicitud a la aplicación de API.

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo:replace with image showing fictional names-->

## Uso de Postman para enviar una solicitud Post

Al iniciar sesión la puerta de enlace, esta envía un token de autenticación.  Este token debe incluirse con todas las solicitudes desde orígenes externos que pasen por la puerta de enlace. Cuando tenga acceso a una API con un explorador, este normalmente almacena el token en una cookie y lo envía a la API junto con todas las llamadas subsiguientes.

Para que pueda ver lo que sucede en segundo plano, en esta sección se utilizará una herramienta de explorador para crear y enviar una solicitud Post y obtener el token de la autorización de la cookie e incluirla en un encabezado HTTP.

Estas instrucciones muestran cómo utilizar la herramienta Postman en el explorador Chrome, pero podría hacer lo mismo con cualquier herramienta de cliente REST y herramientas para desarrolladores de explorador.

1. En una ventana del explorador Chrome, siga los pasos mostrados en la sección anterior para autenticarse y después abra Herramientas para desarrolladores (F12).

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. En la pestaña **Recursos** de las herramientas para desarrolladores de Chrome, encontrará las cookies para la puerta de enlace, y haga tres veces clic en el valor de la cookie **x-zumo-auth** para seleccionar todos los casos.

	**Nota:** asegúrese de obtener el valor de las cookies. Si hace doble clic, obtendrá solo la primera parte de él.

5. Haga clic con el botón secundario en el **Valor** de la cookie **x-zumo-auth** y, a continuación, haga clic en **Copiar**.

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Si no lo ha hecho todavía, instale la extensión de Postman en el explorador Chrome.

6. Abra la extensión Postman.

7. En el campo URL de solicitud, escriba la dirección URL al método Get de la aplicación de API que utilizó anteriormente, pero omita `get/` del final.
 
		http://[apiappurl]/api/contacts
    
8. Haga clic en **Encabezados** y, a continuación, agregue un encabezado *x-zumo-auth*. Pegue el valor del token del Portapapeles en el campo **Valor**.

9. Agregue un encabezado *Content-Type* con valor *application/json*.

10. Haga clic en **form-data** y después agregue una clave de *contacto* con el siguiente valor:

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Haga clic en Enviar.

	La aplicación de API devuelve una respuesta *201 - Creado*.

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. Para comprobar que esta solicitud no funcionará sin el token de autenticación, elimine el encabezado de autenticación y haga clic en Enviar de nuevo.

	Obtendrá una respuesta *403 Prohibido* respuesta.

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## Pasos siguientes

Ha visto cómo proteger una aplicación de API de Azure al requerir Azure Active Directory o la autenticación de proveedor social. Para obtener más información, vea [¿Qué son Aplicaciones de API?](app-service-api-apps-why-best-platform.md). 

[portal anterior]: https://manage.windowsazure.com/
[Portal de Azure]: https://portal.azure.com/


<!--HONumber=49-->