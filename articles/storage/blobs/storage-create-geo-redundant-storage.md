---
title: "Alta disponibilidad en Azure para los datos de aplicación | Microsoft Docs"
description: "Use el almacenamiento con redundancia geográfica con acceso de lectura para lograr alta disponibilidad para los datos de aplicación"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 63ca91c2eadf7b003427e9716d99621fca1b1a19
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Logre alta disponibilidad de los datos de aplicación con Azure Storage

Este tutorial es la primera parte de una serie. Este tutorial muestra cómo conseguir alta disponibilidad de los datos de aplicación en Azure. Cuando haya terminado, tendrá una aplicación de consola básica . NET que carga un blob a una cuenta de almacenamiento con [redundancia geográfica y acceso de lectura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) y lo recupera. RA-GRS funciona mediante la replicación de transacciones de la región primaria en la región secundaria. Este proceso de replicación garantiza que los datos de la región secundaria tengan coherencia final. La aplicación utiliza el patrón de [disyuntor](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) patrón para determinar a qué punto de conexión conectarse. La aplicación cambia al punto de conexión secundario cuando se simula un error.

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * Descarga del ejemplo
> * Establecimiento de la cadena de conexión
> * Ejecución de la aplicación de consola

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
  - **Desarrollo de Azure**

  ![Desarrollo de Azure (en web y la nube)](media/storage-create-geo-redundant-storage/workloads.png)

* Descargue e instale [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Una cuenta de almacenamiento proporciona un espacio de nombres único para almacenar y tener acceso a los objetos de datos de almacenamiento de Azure.

Siga estos pasos para crear una cuenta de almacenamiento con redundancia geográfica con acceso de lectura:

1. Seleccione el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Storage** desde la página **Nuevo** y **Cuenta de almacenamiento: blob, archivo, tabla, cola** en **Destacadas**.
3. Rellene el formulario de la cuenta de almacenamiento con la siguiente información, como se muestra en la siguiente imagen y seleccione **Crear**:

   | Configuración       | Valor sugerido | Descripción |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | Valor único para la cuenta de almacenamiento |
   | **Modelo de implementación** | Resource Manager  | Resource Manager contiene las características más recientes.|
   | **Tipo de cuenta** | Uso general | Para más información sobre los tipos de cuenta, consulte [Tipos de cuentas de almacenamiento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Rendimiento** | Estándar | Suficiente para el escenario de ejemplo. |
   | **Replicación**| Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). | Necesario para el ejemplo funcione. |
   |**Se requiere transferencia segura** | Disabled| No es necesario para este escenario. |
   |**Suscripción** | Su suscripción |Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   |**Ubicación** | Este de EE. UU | Elija una ubicación. |

![creación de cuenta de almacenamiento](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>Descarga del ejemplo

[Descarga del proyecto de ejemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)

Extraiga (descomprima) el archivo storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip.
El proyecto de ejemplo contiene una aplicación de consola.

## <a name="set-the-connection-string"></a>Establecimiento de la cadena de conexión

En la aplicación, debe proporcionar la cadena de conexión para la cuenta de almacenamiento. Se recomienda almacenar esta cadena de conexión dentro de una variable de entorno en la máquina local que ejecuta la aplicación. Siga uno de los estos ejemplos dependiendo de su sistema operativo para crear la variable de entorno.

En Azure Portal, vaya a la cuenta de almacenamiento. Seleccione **Claves de acceso** en **Configuración**, en su cuenta de almacenamiento. Copie la **cadena de conexión** de la clave principal o secundaria. Reemplace \<yourconnectionstring\> con la cadena de conexión real ejecutando uno de los siguientes comandos en función de su sistema operativo. Este comando guarda una variable de entorno en la máquina local. En Windows, la variable de entorno no está disponible hasta que vuelve a cargar el **símbolo del sistema** o el shell que usa. Reemplace  **\<storageConnectionString\>** en el ejemplo siguiente:

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

![archivo App.config](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>Ejecución de la aplicación de consola

En Visual Studio, presione **F5** o seleccione **Iniciar** para iniciar la depuración de la aplicación. Visual Studio restaura automáticamente los paquetes de NuGet que falten (si se ha configurado); consulte el artículo sobre la [instalación y la reinstalación de paquetes con la restauración de paquetes](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para más información.

Se inicia una ventana en la consola y la aplicación comienza a ejecutarse. La aplicación carga la imagen **HelloWorld.png** de la solución en la cuenta de almacenamiento. La aplicación se comprueba para garantizar que la imagen se ha replicado en el punto de conexión de RA-GRS secundario. A continuación, comienza la descarga de la imagen hasta 999 veces. Cada lectura se representa con una **P** o una **S**. Donde **P** representa el punto de conexión principal y **S**, el secundario.

![Aplicación de consola en ejecución](media/storage-create-geo-redundant-storage/figure3.png)

En el código de ejemplo, la tarea `RunCircuitBreakerAsync` del archivo `Program.cs` se usa para descargar una imagen de la cuenta de almacenamiento con el método [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet). Antes de la descarga, se define [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). El contexto de operación define los controladores de eventos, que se activan cuando una descarga se completa correctamente o si se produce un error de descarga y se vuelve a intentar.

### <a name="retry-event-handler"></a>Controlador de eventos de reintento

El controlador de eventos `OperationContextRetrying` se llama cuando se produce un error de descarga de la imagen y se ha establecido el reintento. Si se alcanza el máximo de reintentos definido en la aplicación, el valor [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) de la solicitud cambia a `SecondaryOnly`. Esta opción obliga a la aplicación a intentar la descarga de la imagen desde el punto de conexión secundario. Esta configuración reduce el tiempo necesario para solicitar la imagen, ya que no se reintenta siempre con el punto de conexión principal.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Controlador de eventos de solicitud completada

El controlador de eventos `OperationContextRequestCompleted` se llama cuando la descarga de la imagen es correcta. Si la aplicación utiliza el punto de conexión secundario, continuará usándolo hasta 20 veces. Después de 20 veces, la aplicación establece [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) en `PrimaryThenSecondary` de nuevo y vuelve a probar con el punto de conexión principal. Si la solicitud se realiza correctamente, la aplicación continúa leyendo desde el punto de conexión principal.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>pasos siguientes

En la primera parte de la serie, aprendió a lograr alta disponibilidad para una aplicación con las cuentas de RA-GRS, además de:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * Descarga del ejemplo
> * Establecimiento de la cadena de conexión
> * Ejecución de la aplicación de consola

Vaya a la segunda parte de la serie para aprender a simular un error y obligar a la aplicación a utilizar el punto de conexión de RA-GRS secundario.

> [!div class="nextstepaction"]
> [Simulación de un error de conexión al punto de conexión de almacenamiento principal](storage-simulate-failure-ragrs-account-app.md)
