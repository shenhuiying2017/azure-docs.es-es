<properties 
	pageTitle="Autorización del servicio (Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo autorizar a los usuarios en el back-end de JavaScript de Servicios móviles de Azure." 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Autorización en el servicio de usuarios de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]


Este tema le muestra cómo realizar la autorización de usuarios autenticados para obtener acceso a los datos en Servicios móviles de Azure desde una aplicación de la Tienda Windows. En este tutorial, agregará código a los métodos de acceso a datos de su controlador para filtrar las consultas por el userId de los usuarios autenticados, lo que garantiza que cada usuario solo pueda ver sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación]. Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación].  

## <a name="register-scripts"></a>Modificación de los métodos de acceso a datos

[AZURE.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## Prueba de la aplicación

1. En Visual Studio, abra el proyecto que modificó al completar el tutorial [Introducción a la autenticación].

2. Presione la tecla F5 para ejecutar la aplicación y, a continuación, inicie sesión con el proveedor de identidades seleccionado. 

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userId y ahora cuentan con valores nulos.

3. En la aplicación, escriba texto en **Insertar TodoItem** y luego haga clic en **Guardar**.

   	![][3]

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userId correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

6. (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación (Alt+F4) y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior. 

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda cómo enviar una notificación de inserción muy básica a la aplicación.
  
* [Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]
  <br/>Obtenga más información acerca de cómo utilizar Servicios móviles con JavaScript y HTML.

<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push

[Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library



<!--HONumber=42-->
