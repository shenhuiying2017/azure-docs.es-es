<properties title="Introducci&oacute;n a Almacenamiento de Azure" pageTitle="Introducci&oacute;n a Almacenamiento de Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

## Introducción a Almacenamiento de Azure (proyectos ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [Blobs][Blobs]
> -   [Colas][Colas]
> -   [Tablas][Tareas iniciales]

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales. Consulte [Uso del almacenamiento de tablas en .NET][Uso del almacenamiento de tablas en .NET] para obtener más información.

Para tener acceso mediante programación a tablas en proyectos ASP.NET vNext, tendrá que realizar las tareas siguientes.

1.  Agregue la siguiente declaración de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

        using Microsoft.Framework.ConfigurationModel;

2.  Utilice el comando siguiente para obtener los valores de configuración.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Obtención de la cadena de conexión de almacenamiento

Antes de realizar cualquier acción con una tabla, tendrá que obtener la cadena de conexión para la cuenta de almacenamiento en la que residen las tablas. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está utilizando un proyecto ASP.NET vNext, puede llamar al método get del objeto Configuration para obtener la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

Para obtener más información, consulte [ASP.NET vNext][ASP.NET vNext].

  [Tareas iniciales]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Blobs]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Colas]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Uso del almacenamiento de tablas en .NET]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Uso del almacenamiento de tablas en .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
