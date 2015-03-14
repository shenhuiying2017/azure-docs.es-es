<properties 
	pageTitle="Incorporación de paginación a datos (Windows Phone) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación de Windows Phone desde Servicios móviles." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/11/2014" 
	ms.author="glenga"/>

# Limitación de consultas de Servicios móviles con paginación

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación de Windows Phone desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

>[AZURE.NOTE]Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Incorporación de Servicios móviles a una aplicación existente](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/). Antes de comenzar este tutorial, debe completar este tutorial. 

##Establecimiento del tamaño de la página de servidor

[AZURE.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda cómo enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. -->

[Pasos siguientes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/


[Portal de administración]: https://manage.windowsazure.com/


<!--HONumber=42-->
