<properties 
	pageTitle="Introducción a la autenticación de las aplicaciones móviles en iOS" 
	description="Obtenga información acerca de cómo utilizar las aplicaciones móviles para autenticar usuarios de su aplicación iOS en una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="mattchenderson,krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# Incorporación de la autenticación a la aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicio de aplicaciones. Una vez que la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Creación de una aplicación iOS]. 

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>En Xcode, abra el proyecto. Presione el botón <b>Ejecutar</b> para iniciar la aplicación. Compruebe que se produce una excepción con el código de estado 401 (No autorizado) después de iniciarse la aplicación.</p>
   
   	<p>Esto se produce porque la aplicación intenta obtener acceso a su Código de aplicación móvil como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del Servicio de aplicaciones.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Almacenamiento de tokens de autenticación en la aplicación

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[Creación de una aplicación iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Portal de administración de Azure]: https://portal.azure.com

<!--HONumber=49-->