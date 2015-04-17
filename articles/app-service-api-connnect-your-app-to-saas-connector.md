<properties 
	pageTitle="Conexión de la aplicación a un conector SaaS existente" 
	description="Este artículo muestra cómo conectarse a un conector SaaS existente" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# Conexión de la aplicación a un conector SaaS existente

## Información general

En este tutorial se muestra cómo consumir un conector SaaS que se encuentra en Azure Marketplace. 

Realizará los pasos siguientes:
- Aprovisionar el conector de Dropbox.
- Configurar la aplicación de API de Dropbox.
- Configurar la puerta de enlace.
- Adquirir y almacenar el token en el almacén de tokens. 
- Llamar a la API de Dropbox y comprobar que funciona el acceso autenticado.

## Aprovisionamiento del conector de Dropbox

1. Vaya a la página principal del portal de vista previa y haga clic en Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Busque Dropbox en la galería de Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Haga clic en el icono de Dropbox para aprovisionar el conector Dropbox. Haga clic en el botón 'Crear' para aprovisionar el conector Dropbox. Asegúrese de que rellene el nombre y los valores deseados para todos los campos antes de hacer clic en el botón 'Crear'. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. Examine la hoja del grupo de recursos en el [portal de vista previa](https://portal.azure.com/). Verá el conector Dropbox y la puerta de enlace. Cuando aprovisiona una aplicación de API, lo hace para que forme parte de un grupo de recursos. Cuando el portal crea el grupo de recursos, también crea una puerta de enlace. Una puerta de enlace es una aplicación web especial que controla todas las solicitudes destinadas a las Aplicaciones de API del grupo de recursos.

	Puede agregar aplicaciones web y otras aplicaciones de API para el mismo grupo de recursos y cada aplicación de API dentro del grupo de recursos puede tener una de las tres configuraciones de accesibilidad siguientes:

	* Público (anónimo): cualquier persona puede llamar a la aplicación de API desde fuera del grupo de recursos sin tener que iniciar sesión.
	* Público (autenticado): solo los usuarios autenticados pueden llamar a la aplicación de API desde fuera del grupo de recursos.
	* Interno: solo otras Aplicaciones de API o aplicaciones web del mismo grupo de recursos pueden llamar a la aplicación de API.

## Configuración de la aplicación de API de Dropbox

En primer lugar, configure Dropbox para que acepte solo las solicitudes autenticadas.  Establecerá su accesibilidad en **Público (autenticado)** y configurará la puerta de enlace para requerir la autenticación de un proveedor como Azure Active Directory, Google o Facebook.

1.	En el [portal de vista previa](https://portal.azure.com/) de Azure haga clic en **Examinar > Aplicaciones de API** y, a continuación, haga clic en el nombre de la aplicación de API que desea proteger.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	En la hoja Aplicación de API, haga clic en Configuración y, a continuación, haga clic en Configuración de la aplicación.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	En la hoja **Configuración de la aplicación**, cambie el valor de **Nivel de acceso** a **Público (autenticado)**. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	El conector Dropbox ya está protegido contra el acceso no autenticado. Ahora es el momento de configurar la autenticación de Dropbox. Rellene los detalles de *Id. de cliente* y "Secreto del cliente* en la interfaz de usuario que se muestra a continuación (puede conectar el *Id. de cliente* y el "Secreto del cliente* desde la [cuenta de desarrollador de Dropbox](https://www.dropbox.com/developers/apps)).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

A continuación, tendrá que configurar la puerta de enlace para especificar qué proveedor de autenticación se va a utilizar.

## Configuración de la puerta de enlace

1. Vuelva a la hoja de la aplicación de API de Dropbox y, a continuación, haga clic en el vínculo a la puerta de enlace.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. Elija el proveedor de identidades que desea utilizar y siga los pasos descritos en el artículo correspondiente para configurar la aplicación de API con ese proveedor. Estos artículos se han escrito para aplicaciones móviles, pero los procedimientos son los mismos para las Aplicaciones de API. Algunos de los procedimientos necesitan que use tanto el [portal de administración](https://manage.windowsazure.com/) y el [portal de vista previa](https://portal.azure.com/).
  
	- [Cuenta Microsoft](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Inicio de sesión en Facebook](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Inicio de sesión en Twitter](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Inicio de sesión en Google](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	Consulte el artículo "Protección de una aplicación de API: Adición de Azure Active Directory o de autenticación de proveedor social" para obtener instrucciones paso a paso acerca de cómo realizar la configuración. 

## Adquiera y almacene el token OAuth en el almacén de tokens.

1.	En el explorador, vaya a la dirección URL de inicio de sesión: 

	Puede tomar la dirección URL desde la hoja de configuración de Active Directory de la puerta de enlace.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	La dirección URL tendrá el siguiente formato...
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Por ejemplo, si había denominado el grupo de recursos 'MyResource' y ha configurado la puerta de enlace para la autenticación de Azure Active Directory, la dirección URL será la siguiente:

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	Asegúrese de que inicia las herramientas de desarrollador antes de escribir la dirección URL en el explorador y presione Entrar. Debemos recibir el mensaje completo de inicio de sesión. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	Obtenga el valor y el token de autenticación de zumo en la pestaña Red (Chrome).
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Abra la extensión postman en Chrome. Escriba la dirección URL de consentimiento de la puerta de enlace y el token de autenticación de Zumo (encabezado) y solicite el valor y realice una solicitud POST. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Obtenga la dirección URL de redireccionamiento que procede de la solicitud POST y compruebe que funciona la dirección URL de redireccionamiento. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	Si la dirección URL de redireccionamiento funciona, significa que el token es válido. Las llamadas que se realizan en el conector SaaS usarán el token que hemos establecido y comprobado. Este token debe incluirse con todas las solicitudes desde orígenes externos que pasen por la puerta de enlace. Cuando tenga acceso a una API con un explorador, este normalmente almacena el token en una cookie y lo envía a la API junto con todas las llamadas subsiguientes.

El mismo flujo se aplica a todos los conectores SaaS, como Safesforce, Facebook, etc. 

## Llamar a la API de Dropbox y comprobar que funciona el acceso autenticado.

1. Vuelva a la lista de aplicaciones de API y seleccione el conector Dropbox. 

1. Tenga en cuenta la dirección URL en la parte superior.

2. Haga clic en el conector Dropbox (como se muestra en la imagen) para ver todas las API compatibles.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	Puede ver qué operaciones se admiten en la aplicación de API en la hoja de la definición de la API. Puede descargar Swagger que puede consumir en Visual Studio para generar clientes fuertemente tipados. Además, puede descargar Swaddle, que se puede utilizar en Siena y PowerApp Studio. 

2. En una ventana del explorador, escriba la dirección URL que copió del portal y anéxela a cualquiera de las API admitidas para tener acceso a los archivos o a otros detalles de la cuenta de Dropbox. 

	Formato: `<URL>`/Operation

	Por ejemplo:

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	Dado que ya se ha establecido la autenticación correcta, la llamada anterior se realiza correctamente y la carpeta de las fotos muestra los detalles del explorador. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<Copy the image of the browser>-->

## Pasos siguientes

Ha visto cómo proteger una aplicación de API y configurar la puerta de enlace para tener acceso al conector SaaS con el almacén de tokens. Para obtener más información, vea [¿Qué son Aplicaciones de API?](app-service-api-apps-why-best-platform.md). 

<!--HONumber=49-->