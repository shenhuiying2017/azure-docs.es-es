<properties
	pageTitle="Incorporación de autenticación a su aplicación universal de Windows Runtime 8.1 | Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar las Aplicaciones móviles del Servicio de aplicaciones de Azure para autenticar a los usuarios de su aplicación de Windows en una variedad de proveedores de identidades, incluidos Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="glenga"/>

# Incorporación de la autenticación a la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicio de aplicaciones. Una vez que la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Introducción a su aplicación móvil]. http://acom-sandbox.azurewebsites.net/documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/?rnd=1

##<a name="create-gateway"></a>Creación de una puerta de enlace de Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. En Visual Studio, abra el archivo de proyecto App.xaml.cs compartido en el proyecto de aplicación cliente y asegúrese de que la instancia **MobileServiceClient** está configurada para usar la dirección URL del back-end de la aplicación móvil y la puerta de enlace.

&nbsp;&nbsp;5. Con uno de los proyectos de aplicación de Windows configurado como proyecto de inicio, presione la tecla F5 para ejecutar la aplicación y compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado).

&nbsp;&nbsp;Esto se produce porque la aplicación intenta obtener acceso a su Código de aplicación móvil como usuario sin autenticar, pero la tabla *TodoItem* ahora requiere autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del Servicio de aplicaciones.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Almacenamiento del token de autorización en el cliente

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##Pasos siguientes


<!-- URLs. -->
[Introducción a su aplicación móvil]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=Oct15_HO4-->