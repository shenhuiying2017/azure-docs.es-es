<properties title="Introducci&oacute;n a Servicios m&oacute;viles" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

### <span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

##### Se han agregado referencias

El paquete de NuGet de servicios móviles de Azure se agregó a su proyecto. Como resultado, se agregaron las siguientes referencias de .NET a su proyecto:

-   `Microsoft.WindowsAzure.Mobile`
-   `Microsoft.WindowsAzure.Mobile.Ext`
-   `Newtonsoft.Json`
-   `System.Net.Http.Extensions`
-   `System.Net.Http.Primitives`

##### Conexión de valores de cadena para Servicios móviles

En el archivo App.xaml.cs, se ha creado un objeto **MobileServiceClient** con la dirección URL y la clave de aplicación del servicio móvil seleccionado.

[Más información acerca de servicios móviles][Más información acerca de servicios móviles]

  [Tareas iniciales]: /documentation/articles/vs-mobile-services-dotnet-getting-started/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-mobile-services-dotnet-what-happened/
  [Más información acerca de servicios móviles]: http://azure.microsoft.com/documentation/services/mobile-services/
