<properties 
	pageTitle="Autorización en el servicio (Android) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo autorizar a los usuarios en el back-end de .NET de Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="glenga"/>

# Autorización en el servicio de usuarios de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

En este tema se muestra cómo autorizar a los usuarios autenticados el acceso a los datos de Servicios móviles de Azure desde una aplicación Android. En este tutorial, agregará código a los métodos de acceso a datos de su controlador para filtrar las consultas según el identificador de usuario de los usuarios autenticados, lo que garantiza que cada usuario solo pueda ver sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación]. Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación].  

## <a name="register-scripts"></a>Modificación de los métodos de acceso a datos

[AZURE.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## Prueba de la aplicación

1. En Eclipse, abra el proyecto que ha modificado al completar el tutorial [Introducción a la autenticación].

2. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar el proyecto en el emulador de Android.

   	Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3. En la aplicación, escriba texto en **Add a ToDo item** (Agregar un elemento ToDo) y luego haga clic en **Save** (Guardar).

   	![][3]

   	De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

5. De vuelta en la tabla **todoitem** del [Portal de administración][Portal de administración de Azure], haga clic en **Examinar** y compruebe que cada elemento recién agregado tiene ahora un valor de userId asociado.

6. (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda cómo enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con Android]
  <br/>Más información sobre el uso de Servicios móviles con Android.

<!-- Anchors. -->
[Registro de scripts de servidor]: #register-scripts
[Pasos siguientes]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push

[Referencia conceptual de Servicios móviles con Android]: /es-es/documentation/articles/mobile-services-android-how-to-use-client-library/


<!--HONumber=42-->
