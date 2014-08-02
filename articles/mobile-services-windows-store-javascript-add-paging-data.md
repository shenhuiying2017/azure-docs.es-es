<properties pageTitle="Add paging to data (JavaScript) - Azure Mobile Services" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Limitación de consultas de Servicios móviles con paginación
===========================================================

> [AZURE.SELECTOR-LIST (Plataforma | Back-end)] 
> - [(C\# para Tienda Windows | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md) 
> - [(C\# para Tienda Windows | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md) 
> - [(JavaScript para Tienda Windows | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md) 
> - [(JavaScript para Tienda Windows | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md) > - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
> - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
> - [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
> - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
> - [(HTML | .NET)](mobile-services-html-add-paging-data) 
> - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
> - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

En este tema se muestra cómo utilizar la paginación para administrar la cantidad de datos devueltos a la aplicación de la Tienda Windows desde Servicios móviles de Azure. En este tutorial se utilizarán los métodos de consulta **Take** y **Skip** en el cliente para solicitar "páginas" específicas de datos.

> [WACOM.NOTE] Para evitar el desbordamiento de datos en clientes de dispositivos móviles, Servicios móviles implementa un límite automático de páginas, con un valor determinado de 50 elementos en una respuesta. Si especifica el tamaño de la página, puede solicitar explícitamente hasta 1.000 elementos en la respuesta.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/). Antes de comenzar este tutorial, debe completar al menos el primer tutorial de la serie de trabajo con datos, [Introducción a los datos](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/).

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

Pasos siguientes
----------------

Así concluye el conjunto de tutoriales que demuestra los aspectos básicos del trabajo con datos en Servicios móviles. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a la autenticación](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Introducción a las notificaciones de inserción](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.


