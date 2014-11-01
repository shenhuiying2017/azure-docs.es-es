<properties title="Introducci&oacute;n a Almacenamiento de Azure" pageTitle="Introducci&oacute;n a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

### <span id="whathappened">¿Qué le ha ocurrido a mi proyecto?</span>

##### Se han agregado referencias

Se ha agregado el paquete de NuGet de Almacenamiento de Azure a su proyecto de Visual Studio.
Este paquete agrega las siguientes referencias .NET:

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

Además, se ha agregado el paquete de NuGet **Microsoft.Framework.ConfigurationModel.Json**.

##### Se ha agregado la cadena de conexión para el Almacenamiento de Azure

En el archivo config.json de su proyecto, se ha creado un elemento con la cadena y la clave de conexión de la cuenta de almacenamiento seleccionada.

Para obtener más información, consulte [ASP.NET vNext][ASP.NET vNext].

  [Tareas iniciales]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [ASP.NET vNext]: http://www.asp.net/vnext
