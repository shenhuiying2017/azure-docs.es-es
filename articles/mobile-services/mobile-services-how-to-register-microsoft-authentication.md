<properties 
	pageTitle="Registro para autenticación de Microsoft | Microsoft Azure" 
	description="Obtenga información acerca de cómo registrarse para la autenticación de Microsoft en la aplicación de Servicios móviles de Azure." 
	authors="ggailey777" 
	services="mobile-services" 
	documentationCenter="Mobile" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

# Registre la aplicación para usar la cuenta Microsoft para realizar la autenticación

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## Información general 

Este tema indica cómo registrar su aplicación móvil para que pueda usar la cuenta de Microsoft como proveedor de identidad con Servicios móviles de Azure. Se aplican los mismos pasos para la autenticación dirigida por el servicio y la dirigida por el cliente con el SDK de Live.

##Registre la aplicación Tienda Windows en el Centro de desarrollo de Windows

Las aplicaciones de la Tienda Windows deben registrarse primero con el Centro de desarrollo de Windows.

>[AZURE.NOTE]Las aplicaciones de Windows Phone 8, Windows Phone 8.1 Silverlight y las que no pertenezcan a Windows podrán omitir esta sección.

1. Si aún no ha registrado la aplicación, vaya a la página [Enviar una aplicación] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-submit-win8-app.png)

2. Seleccione **Crear una nueva aplicación reservando un nombre único**, haga clic en **Continuar** y, a continuación, escriba un nombre para la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación** y, a continuación, en **Guardar**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-name.png)

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3. En Visual Studio, abra el proyecto que ha creado al completar el tutorial [Introducción a Servicios móviles](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md).

4. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de la aplicación para la Tienda Windows, haga clic en **Tienda** y, a continuación, en **Asociar aplicación con la Tienda...**.

  	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

   	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta de Microsoft, seleccione el nombre de la aplicación que se ha reservado en el paso 2, haga clic en **Siguiente** > **Asociar**.

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

6. (Opcional) Para una aplicación Windows universal, repita los pasos 4 y 5 para el proyecto de la Tienda de Windows Phone.

6. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios** > **Notificaciones push**.

7. En la página **Notificaciones push**, haga clic en **Sitio de Servicios Live** en **Servicios de notificaciones de Windows (WNS) y Servicios móviles de Microsoft Azure**.

Esto muestra la página de la cuenta de Microsoft para su aplicación.

## Configurar el registro de la cuenta de Microsoft y conectarse a Servicios móviles

El primer paso de esta sección se aplica solo a Windows Phone 8, Windows Phone 8.1 Silverlight y aplicaciones que no sean de la Tienda Windows. Para estas aplicaciones, también puede omitir el identificador de seguridad (SID) de paquete, que solo está disponible para aplicaciones de la Tienda Windows.

1. Para una aplicación que no sea de la Tienda Windows, navegue hasta la página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Mis aplicaciones</a> del Centro de desarrolladores de la cuenta de Microsoft, inicie sesión con su cuenta de Microsoft (si es necesario), haga clic en **Crear aplicación** y, a continuación, escriba un **Nombre de la aplicación** y haga clic en **Acepto**.

   	Este reserva el nombre de la aplicación con la cuenta de Microsoft y muestra la página de la cuenta de Microsoft para su aplicación.

2. En la página de la cuenta de Microsoft para su aplicación, haga clic en **Configuración de API**, seleccione habilitar la **Aplicación cliente de escritorio o móvil**, establezca la dirección URL de servicio móvil como el **Dominio de destino**, proporcione un valor de `https://<mobile_service>.azure-mobile.net/` en **Dirección URL de redireccionamiento** y, a continuación, haga clic en **Guardar**.

	 >[AZURE.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso _signin-microsoft_ anexada como servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-microsoft`.

    ![Configuración de la API de la cuenta de Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)

	El **Dominio raíz** debería rellenarse automáticamente.

4. Haga clic en **Configuración de aplicaciones** y tome nota de los valores de **Id. de cliente**, **Secreto de cliente** y **SID de paquete**.
	
   	![Configuración de la aplicación de la cuenta de Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)
	
	
    > [AZURE.NOTE]El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación. Solo los registros de aplicaciones de la Tienda Windows verán un campo de SID del paquete.

4. En el [Portal de administración de Azure], haga clic en la pestaña **identidad** del servicio móvil, escriba el identificador de cliente, el secreto de cliente y el SID del paquete obtenido del proveedor de identidades y haga clic en **Guardar**.

 	![Pestaña Identidad de servicios móviles](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-identity-tab.png)
	
	>[AZURE.NOTE]No es necesario proporcionar un valor de SID del paquete para Windows Phone 8, Windows Phone 8.1 Silverlight o una aplicación que no sea de Windows.
	
El servicio móvil y la aplicación están ahora configurados para funcionar con una cuenta de Microsoft.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portal de administración de Azure]: https://manage.windowsazure.com/
 

<!---HONumber=Oct15_HO3-->