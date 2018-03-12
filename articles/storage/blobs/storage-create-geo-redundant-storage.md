---
title: "Alta disponibilidad en Azure para los datos de aplicación | Microsoft Docs"
description: "Use el almacenamiento con redundancia geográfica con acceso de lectura para lograr alta disponibilidad para los datos de aplicación"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 5b1c443cae8481d98c32a3f4d9e3899621d1dd89
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Logre alta disponibilidad de los datos de aplicación con Azure Storage

Este tutorial forma parte de una serie que muestra cómo conseguir alta disponibilidad de los datos de aplicación en Azure. Cuando haya terminado, tendrá una aplicación de consola que carga un blob a una cuenta de [almacenamiento con redundancia geográfica con acceso de lectura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) y lo recupera. RA-GRS funciona mediante la replicación de transacciones de la región primaria en la región secundaria. Este proceso de replicación garantiza que los datos de la región secundaria tengan coherencia final. La aplicación utiliza el patrón de [disyuntor](/azure/architecture/patterns/circuit-breaker) patrón para determinar a qué punto de conexión conectarse. La aplicación cambia al punto de conexión secundario cuando se simula un error.

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * Descarga del ejemplo
> * Establecimiento de la cadena de conexión
> * Ejecución de la aplicación de consola

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
  - **Desarrollo de Azure**

  ![Desarrollo de Azure (en web y la nube)](media/storage-create-geo-redundant-storage/workloads.png)

* (Opcional) Descargue e instale [Fiddler](https://www.telerik.com/download/fiddler)
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* Instalación de [Python](https://www.python.org/downloads/)
* Descargue e instale el [SDK de Azure Storage para Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python).
* (Opcional) Descargue e instale [Fiddler](https://www.telerik.com/download/fiddler)

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Una cuenta de almacenamiento proporciona un espacio de nombres único para almacenar y tener acceso a los objetos de datos de almacenamiento de Azure.

Siga estos pasos para crear una cuenta de almacenamiento con redundancia geográfica con acceso de lectura:

1. Seleccione el botón **Crear un recurso** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Storage** desde la página **Nuevo** y **Cuenta de almacenamiento: blob, archivo, tabla, cola** en **Destacadas**.
3. Rellene el formulario de la cuenta de almacenamiento con la siguiente información, como se muestra en la siguiente imagen y seleccione **Crear**:

   | Configuración       | Valor sugerido | Descripción |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | Valor único para la cuenta de almacenamiento |
   | **Modelo de implementación** | Resource Manager  | Resource Manager contiene las características más recientes.|
   | **Tipo de cuenta** | StorageV2 | Para más información sobre los tipos de cuenta, consulte [Tipos de cuentas de almacenamiento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Rendimiento** | Estándar | Suficiente para el escenario de ejemplo. |
   | **Replicación**| Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). | Necesario para el ejemplo funcione. |
   |**Se requiere transferencia segura** | Disabled| No es necesario para este escenario. |
   |**Suscripción** | Su suscripción |Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   |**Ubicación** | Este de EE. UU | Elija una ubicación. |

![creación de cuenta de almacenamiento](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Descarga del ejemplo

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Descargue el proyecto de ejemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) y extraiga (descomprima) el archivo storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. También puede usar [git](https://git-scm.com/) para descargar una copia de la aplicación en el entorno de desarrollo. El proyecto de ejemplo contiene una aplicación de consola.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[Descargue el proyecto de ejemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) y extraiga (descomprima) el archivo storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. También puede usar [git](https://git-scm.com/) para descargar una copia de la aplicación en el entorno de desarrollo. El proyecto de ejemplo contiene una aplicación de básica de Python.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>Establecimiento de la cadena de conexión

En la aplicación, debe proporcionar la cadena de conexión para la cuenta de almacenamiento. Se recomienda almacenar esta cadena de conexión dentro de una variable de entorno en la máquina local que ejecuta la aplicación. Siga uno de los estos ejemplos dependiendo de su sistema operativo para crear la variable de entorno.

En Azure Portal, vaya a la cuenta de almacenamiento. Seleccione **Claves de acceso** en **Configuración**, en su cuenta de almacenamiento. Copie la **cadena de conexión** de la clave principal o secundaria. Reemplace \<yourconnectionstring\> con la cadena de conexión real ejecutando uno de los siguientes comandos en función de su sistema operativo. Este comando guarda una variable de entorno en la máquina local. En Windows, la variable de entorno no está disponible hasta que vuelve a cargar el **símbolo del sistema** o el shell que usa. Reemplace  **\<storageConnectionString\>** en el ejemplo siguiente:

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>Ejecución de la aplicación de consola

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
En Visual Studio, presione **F5** o seleccione **Iniciar** para iniciar la depuración de la aplicación. Visual Studio restaura automáticamente los paquetes de NuGet que falten (si se ha configurado); consulte el artículo sobre la [instalación y la reinstalación de paquetes con la restauración de paquetes](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para más información.

Se inicia una ventana en la consola y la aplicación comienza a ejecutarse. La aplicación carga la imagen **HelloWorld.png** de la solución en la cuenta de almacenamiento. La aplicación se comprueba para garantizar que la imagen se ha replicado en el punto de conexión de RA-GRS secundario. A continuación, comienza la descarga de la imagen hasta 999 veces. Cada lectura se representa con una **P** o una **S**. Donde **P** representa el punto de conexión principal y **S**, el secundario.

![Aplicación de consola en ejecución](media/storage-create-geo-redundant-storage/figure3.png)

En el código de ejemplo, la tarea `RunCircuitBreakerAsync` del archivo `Program.cs` se usa para descargar una imagen de la cuenta de almacenamiento con el método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet). Antes de la descarga, se define [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). El contexto de operación define los controladores de eventos, que se activan cuando una descarga se completa correctamente o si se produce un error de descarga y se vuelve a intentar.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Para ejecutar la aplicación en un terminal o un símbolo del sistema, vaya al directorio **circuitbreaker.py** y escriba `python circuitbreaker.py`. La aplicación carga la imagen **HelloWorld.png** de la solución en la cuenta de almacenamiento. La aplicación se comprueba para garantizar que la imagen se ha replicado en el punto de conexión de RA-GRS secundario. A continuación, comienza la descarga de la imagen hasta 999 veces. Cada lectura se representa con una **P** o una **S**. Donde **P** representa el punto de conexión principal y **S**, el secundario.

![Aplicación de consola en ejecución](media/storage-create-geo-redundant-storage/figure3.png)

En el código de ejemplo, el método `run_circuit_breaker` del archivo `circuitbreaker.py` se usa para descargar una imagen de la cuenta de almacenamiento con el método [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

La función de reintento de objeto de Storage se establece en una directiva de reintento lineal. La función de reintento determina si se debe reintentar una solicitud y especifica el número de segundos que deben transcurrir para reintentar la solicitud. Establezca el valor de **retry\_to\_secondary** en true, si la solicitud debe reintentarse en el elemento secundario en caso de que la solicitud inicial al principal produzca error. En la aplicación de ejemplo, una directiva de reintento personalizada se define en la función `retry_callback` del objeto de almacenamiento.
 
Antes de la descarga, se definen el objeto de servicio [retry_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) y la función [response_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python). Estas funciones definen los controladores de eventos que se activan cuando una descarga se completa correctamente o si se produce un error de descarga y se vuelve a intentar.  

---

### <a name="retry-event-handler"></a>Controlador de eventos de reintento

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

El controlador de eventos `OperationContextRetrying` se llama cuando se produce un error de descarga de la imagen y se ha establecido el reintento. Si se alcanza el máximo de reintentos definido en la aplicación, el valor [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) de la solicitud cambia a `SecondaryOnly`. Esta opción obliga a la aplicación a intentar la descarga de la imagen desde el punto de conexión secundario. Esta configuración reduce el tiempo necesario para solicitar la imagen, ya que no se reintenta siempre con el punto de conexión principal.

En el código de ejemplo, la tarea `RunCircuitBreakerAsync` del archivo `Program.cs` se usa para descargar una imagen de la cuenta de almacenamiento con el método [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet). Antes de la descarga, se define [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). El contexto de operación define los controladores de eventos, que se activan cuando una descarga se completa correctamente o si se produce un error de descarga y se vuelve a intentar.
 
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 
El controlador de eventos `retry_callback` se llama cuando se produce un error de descarga de la imagen y se ha establecido el reintento. Si se alcanza el máximo de reintentos definido en la aplicación, el valor [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) de la solicitud cambia a `SECONDARY`. Esta opción obliga a la aplicación a intentar la descarga de la imagen desde el punto de conexión secundario. Esta configuración reduce el tiempo necesario para solicitar la imagen, ya que no se reintenta siempre con el punto de conexión principal.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

---


### <a name="request-completed-event-handler"></a>Controlador de eventos de solicitud completada
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

El controlador de eventos `OperationContextRequestCompleted` se llama cuando la descarga de la imagen es correcta. Si la aplicación utiliza el punto de conexión secundario, continuará usándolo hasta 20 veces. Después de 20 veces, la aplicación establece [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) en `PrimaryThenSecondary` de nuevo y vuelve a probar con el punto de conexión principal. Si la solicitud se realiza correctamente, la aplicación continúa leyendo desde el punto de conexión principal.
 
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

El controlador de eventos `response_callback` se llama cuando la descarga de la imagen es correcta. Si la aplicación utiliza el punto de conexión secundario, continuará usándolo hasta 20 veces. Después de 20 veces, la aplicación establece [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) en `PRIMARY` de nuevo y vuelve a probar con el punto de conexión principal. Si la solicitud se realiza correctamente, la aplicación continúa leyendo desde el punto de conexión principal.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

---

## <a name="next-steps"></a>Pasos siguientes

En la primera parte de la serie, aprendió a lograr alta disponibilidad para una aplicación con las cuentas de RA-GRS, además de:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * Descarga del ejemplo
> * Establecimiento de la cadena de conexión
> * Ejecución de la aplicación de consola

Vaya a la segunda parte de la serie para aprender a simular un error y obligar a la aplicación a utilizar el punto de conexión de RA-GRS secundario.

> [!div class="nextstepaction"]
> [Simulación de un error de conexión al punto de conexión de almacenamiento principal](storage-simulate-failure-ragrs-account-app.md)
