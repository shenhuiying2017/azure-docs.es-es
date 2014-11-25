<properties pageTitle="Refine Mobile Services queries with paging (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Limitación de consultas de Servicios móviles con paginación

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title="Windows Phone">Windows Phone</a>
<!--<a href="/es-es/documentation/articles/mobile-services-ios-add-paging-data" title="iOS">iOS</a><a href="/es-es/documentation/articles/mobile-services-android-add-paging-data" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación de la Tienda Windows desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

> [WACOM.NOTE] Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos][Introducción a los datos]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos][Introducción a los datos].

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.





  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
