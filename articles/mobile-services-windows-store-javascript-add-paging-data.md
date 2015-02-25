<properties pageTitle="Incorporación de paginación a datos (JavaScript): Servicios móviles de Azure" description="Obtenga información acerca de cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación de JavaScript de la Tienda Windows desde Servicios móviles." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>


# Limitación de consultas de Servicios móviles con paginación

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación de la Tienda Windows desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

>[AZURE.NOTE]Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos]. Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos]. 

[AZURE.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Pasos siguientes

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. -->

[Pasos siguientes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Portal de administración]: https://manage.windowsazure.com/


<!--HONumber=42-->
