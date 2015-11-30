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
	ms.date="11/15/2015" 
	ms.author="glenga"/>

# Registre la aplicación para usar la cuenta Microsoft para realizar la autenticación

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## Información general 

Este tema indica cómo registrar su aplicación móvil para que pueda usar la cuenta de Microsoft como proveedor de identidad con Servicios móviles de Azure. Se aplican los mismos pasos para la autenticación dirigida por el servicio y la dirigida por el cliente con el SDK de Live.

##Registre la aplicación Tienda Windows en el Centro de desarrollo de Windows

Las aplicaciones de la Tienda Windows deben registrarse primero con el Centro de desarrollo de Windows.

>[AZURE.NOTE]Las aplicaciones de Windows Phone 8, Windows Phone 8.1 Silverlight y las que no pertenezcan a Windows podrán omitir esta sección.

1. Si aún no ha registrado la aplicación, vaya al [Centro de desarrollo de Windows](https://dev.windows.com/dashboard/Application/New), inicie sesión en su cuenta Microsoft y, escriba un nombre para su aplicación y luego haga clic en **Reservar nombre de aplicación**.
 
3. Abra su proyecto de aplicación de Windows en Visual Studio y en el Explorador de soluciones, haga clic con el botón secundario en el proyecto de la aplicación de la Tienda Windows, haga clic en **Tienda** > **Asociar aplicación con la Tienda...**.

  	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

5. En el asistente, haga clic en **Iniciar sesión** e inicie sesión con su cuenta de Microsoft, seleccione el nombre de la aplicación que ha reservado y luego haga clic en **Siguiente** > **Asociar**.

6. (Opcional) Para una aplicación Windows universal 8.1, repita los pasos 4 y 5 para el proyecto de la Tienda de Windows Phone.

6. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios** > **Notificaciones push**.

7. En la página **Notificaciones push**, haga clic en **Sitio de Servicios Live** en **Servicios de notificaciones de Windows (WNS) y Servicios móviles de Microsoft Azure**.

Esto muestra la página de la cuenta de Microsoft para su aplicación. A continuación, obtendrá las credenciales de autenticación que Azure necesita para usar la autenticación de Microsoft con la aplicación.

## Configurar el registro de la cuenta de Microsoft y conectarse a Servicios móviles

El primer paso de esta sección se aplica solo a Windows Phone 8, Windows Phone 8.1 Silverlight y aplicaciones que no sean de la Tienda Windows. Para estas aplicaciones, también puede omitir el identificador de seguridad (SID) de paquete, que solo está disponible para aplicaciones de la Tienda Windows.

1. Para una aplicación que no sea de la Tienda Windows, navegue hasta la página [Mis aplicaciones](http://go.microsoft.com/fwlink/p/?LinkId=262039) del Centro de desarrolladores de la cuenta de Microsoft, inicie sesión con su cuenta de Microsoft (si es necesario), haga clic en **Crear aplicación** y luego escriba un **Nombre de la aplicación** y haga clic en **Acepto**.

   	Este reserva el nombre de la aplicación con la cuenta de Microsoft y muestra la página de la cuenta de Microsoft para su aplicación.

2. En la página de la cuenta de Microsoft para su aplicación, haga clic en **Configuración de API**, habilite **Aplicación cliente de escritorio o móvil**, establezca la dirección URL de servicio móvil como el **Dominio de destino**, proporcione uno de los siguientes formatos URL en **Dirección URL de redireccionamiento** y luego haga clic en **Guardar**:

	+ **Back-end de .NET**: `https://<mobile_service>.azure-mobile.net/signin-microsoft`
	+ **Back-end de JavaScript**: `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` 

	 >[AZURE.NOTE]Asegúrese de usar el formato correcto de ruta de acceso a dirección URL de redireccionamiento para su tipo de back-end de Servicios móviles. Si es incorrecto, la autenticación no se realizará correctamente. El **Dominio raíz** debería rellenarse automáticamente. &nbsp;

    ![Configuración de la API de la cuenta de Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)


4. Haga clic en **Configuración de aplicaciones** y tome nota de los valores de **Id. de cliente**, **Secreto de cliente** y **SID de paquete**.
	
   	![Configuración de la aplicación de la cuenta de Microsoft](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)
	
	
    > [AZURE.NOTE]El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación. Solo los registros de aplicaciones de la Tienda Windows verán un campo de SID del paquete.

4. En el [Portal de administración de Azure], haga clic en la pestaña **identidad** del servicio móvil, escriba el identificador de cliente, el secreto de cliente y el SID del paquete obtenido del proveedor de identidades y luego haga clic en **Guardar**.
	
	>[AZURE.NOTE]No es necesario proporcionar un valor de SID del paquete para Windows Phone 8, Windows Phone 8.1 Silverlight o una aplicación que no sea de Windows.
	
El servicio móvil y la aplicación están ahora configurados para funcionar con una cuenta de Microsoft.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portal de administración de Azure]: https://manage.windowsazure.com/
 

<!---HONumber=Nov15_HO4-->