---
title: Importar y exportación de identidades del dispositivo de Centro de IoT | Microsoft Docs
description: Conceptos y fragmentos de código de .NET para la administración masiva de identidades del dispositivo de Centro de IoT
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2016
ms.author: dobett

---
# Administración de identidades de dispositivos de Centro de IoT de forma masiva
Cada Centro de IoT tiene un registro de identidad del dispositivo que se usa para crear recursos en el servicio para cada dispositivo, como una cola que contiene los mensajes de nube a dispositivo en curso, y permitir el acceso a los puntos de conexión accesibles desde el dispositivo. En este artículo se describe cómo importar y exportar las identidades del dispositivo de forma masiva hacia y desde un registro de identidad del dispositivo.

Las operaciones de importación y exportación tienen lugar en el contexto de *Trabajos* que permiten a los usuarios ejecutar operaciones de servicio de forma masiva en un Centro de IoT.

La clase **RegistryManager** incluye los métodos **ExportDevicesAsync** y **ImportDevicesAsync**, que usan el marco **Trabajo**. Estos métodos le permiten exportar, importar y sincronizar la totalidad de un registro de dispositivos de Centro de IoT.

## ¿Qué son los Trabajos?
Las operaciones de registro de identidad del dispositivo utilizan el sistema **Trabajo** cuando la operación:

* Tiene un tiempo de ejecución potencialmente largo en comparación con las operaciones de tiempo de ejecución estándar, o
* Devuelve una gran cantidad de datos al usuario.

En estos casos, en lugar de tener una única llamada de API en espera o bloqueando el resultado de la operación, la operación crea de forma asincrónica un **Trabajo** para ese Centro de IoT y la operación y después devuelve inmediatamente un objeto **JobProperties**.

El siguiente fragmento de código de C# muestra cómo crear un trabajo de exportación:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Puede usar la clase **RegistryManager** para consultar el estado del **Trabajo** con los metadatos de **JobProperties** devueltos.

El siguiente fragmento de código de C# muestra cómo sondear cada cinco segundos para ver si el trabajo ha terminado de ejecutarse:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Exportación de dispositivos
Utilice el método **ExportDevicesAsync** para exportar la totalidad de un registro de dispositivos de un Centro de IoT a un contenedor de blobs de [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) mediante una [firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

Este método le permite crear copias de seguridad confiables de la información del dispositivo en un contenedor de blobs que usted controle.

El método **ExportDevicesAsync** requiere dos parámetros:

* Una *cadena* que contiene un identificador URI de un contenedor de blobs. Este identificador URI debe contener un token SAS que conceda acceso de escritura al contenedor. El trabajo crea un blob en bloques en este contenedor para almacenar los datos serializados de exportación del dispositivo. El token de SAS debe incluir estos permisos:
  
   ```
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```
* Un valor *booleano* que indica si desea excluir las claves de autenticación de los datos de exportación. Si es **false**, las claves de autenticación se incluyen en la exportación; si no, las claves se exportan como **null**.

El siguiente fragmento de código de C# muestra cómo iniciar un trabajo de exportación que incluye las claves de autenticación de dispositivo en los datos de exportación y luego sondea la finalización:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

El trabajo almacena su salida en el contenedor de blobs proporcionado como un blob en bloques con el nombre **devices.txt**. Los datos de salida se componen de datos de dispositivos serializados de JSON, con un dispositivo por línea.

El siguiente es un ejemplo de los datos de salida:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Si necesita acceso a los datos en el código, puede deserializar fácilmente estos datos mediante la clase **ExportImportDevice**. El siguiente fragmento de código de C# muestra cómo leer la información de dispositivo que se ha exportado previamente a un blob en bloques:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
> También puede utilizar el método **GetDevicesAsync** de la clase **RegistryManager** para obtener una lista de dispositivos. Sin embargo, este enfoque tiene un límite máximo de 1000 en cuanto al número de objetos de dispositivo que se devuelven. El caso de uso esperado para el método **GetDevicesAsync** es para facilitar la depuración en escenarios de desarrollo, y no se recomienda para las cargas de trabajo de producción.
> 
> 

## Importación de dispositivos
El método **ImportDevicesAsync** de la clase **RegistryManager** le permite realizar operaciones de sincronización e importación masiva en un registro de dispositivos de Centro de IoT. Al igual que el método **ExportDevicesAsync**, el método **ImportDevicesAsync** usa el marco **Trabajo**.

Debe tener cuidado con el método **ImportDevicesAsync** porque además del aprovisionamiento de nuevos dispositivos en el registro de identidad del dispositivo, también puede actualizar y eliminar dispositivos existentes.

> [!WARNING]
> Una operación de importación no se puede deshacer. Siempre debe realizar una copia de seguridad de los datos existentes mediante el método **ExportDevicesAsync** en otro contenedor de blobs antes de realizar cambios masivos en el registro de identidad del dispositivo.
> 
> 

El método **ImportDevicesAsync** requiere dos parámetros:

* Una *cadena* que contiene un identificador URI de un contenedor de blobs de [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) como *entrada* para el trabajo. Este identificador URI debe contener un token SAS que conceda acceso de lectura al contenedor. Este contenedor debe incluir un blob con el nombre **devices.txt** que contenga los datos del dispositivo serializados para importar en el registro de identidad del dispositivo. Los datos de importación deben contener la información del dispositivo en el mismo formato JSON que usa el trabajo **ExportImportDevice** cuando crea un blob **devices.txt**. El token de SAS debe incluir estos permisos:
  
   ```
   SharedAccessBlobPermissions.Read
   ```
* Una *cadena* que contenga un identificador URI de un contenedor de blobs de [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) como *salida* del trabajo. El trabajo crea un blob en bloques en este contenedor para almacenar cualquier información de error del **Trabajo** de importación finalizado. El token de SAS debe incluir estos permisos:
  
   ```
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Los dos parámetros pueden apuntar al mismo contenedor de blobs. Los parámetros independientes simplemente habilitan más control sobre sus datos, ya que el contenedor de salida requiere permisos adicionales.
> 
> 

El siguiente fragmento de código de C# muestra cómo iniciar un trabajo de importación:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## Comportamiento de la importación
Puede usar el método **ImportDevicesAsync** para realizar las siguientes operaciones de forma masiva en el registro de identidad del dispositivo:

* Registro masivo de nuevos dispositivos
* Eliminación masiva de dispositivos existentes
* Cambios masivos de estado (habilitar o deshabilitar dispositivos)
* Asignación masiva de nuevas claves de autenticación de dispositivos
* Regeneración automática masiva de claves de autenticación de dispositivos

Puede realizar cualquier combinación de las operaciones anteriores en una única llamada **ImportDevicesAsync**. Por ejemplo, puede registrar nuevos dispositivos y eliminar o actualizar los dispositivos existentes al mismo tiempo. Cuando se utiliza junto con el método **ExportDevicesAsync**, puede migrar completamente todos los dispositivos de un Centro de IoT a otro.

Puede controlar el proceso de importación por dispositivo utilizando la propiedad opcional **importMode** en los datos de serialización de importación para cada dispositivo. La propiedad **importMode** tiene las siguientes opciones:

| importMode | Descripción |
| --- | --- |
| **createOrUpdate** |Si no existe un dispositivo con el **id.** especificado, este se registra por primera vez. <br/>Si el dispositivo ya existe, la información existente se sobrescribe con los datos de entrada proporcionados con independencia del valor **ETag**. |
| **create** |Si no existe un dispositivo con el **id.** especificado, este se registra por primera vez. <br/>Si el dispositivo ya existe, se escribe un error en el archivo de registro. |
| **update** |Si ya existe un dispositivo con el **id.** especificado, la información existente se sobrescribe con los datos de entrada proporcionados con independencia del valor **ETag**. <br/>Si el dispositivo no existe, se escribe un error en el archivo de registro. |
| **updateIfMatchETag** |Si ya existe un dispositivo con el **id.** especificado, la información existente se sobrescribe con los datos de entrada proporcionados solo si hay una coincidencia con **ETag**. <br/>Si el dispositivo no existe, se escribe un error en el archivo de registro. <br/>Si no existe la coincidencia con **ETag**, se escribe un error en el archivo de registro. |
| **createOrUpdateIfMatchETag** |Si no existe un dispositivo con el **id.** especificado, este se registra por primera vez. <br/>Si el dispositivo ya existe, la información existente se sobrescribe con los datos de entrada proporcionados solo si hay una coincidencia con **ETag**. <br/>Si no existe la coincidencia con **ETag**, se escribe un error en el archivo de registro. |
| **delete** |Si ya existe un dispositivo con el **id.** especificado, este se elimina con independencia del valor **ETag**. <br/>Si el dispositivo no existe, se escribe un error en el archivo de registro. |
| **deleteIfMatchETag** |Si ya existe un dispositivo con el **id.** especificado, este se elimina solo si hay una coincidencia con **ETag**. Si el dispositivo no existe, se escribe un error en el archivo de registro. <br/>Si no existe una coincidencia con ETag, se escribe un error en el archivo de registro. |

> [!NOTE]
> Si los datos de serialización no definen explícitamente una marca **importMode** para un dispositivo, se establece de manera predeterminada en **createOrUpdate** durante la operación de importación.
> 
> 

## Ejemplo de importación de dispositivos: aprovisionamiento masivo de dispositivos
El siguiente ejemplo de código de C# muestra cómo generar varias identidades de dispositivos que incluyen las claves de autenticación, escribir esa información de dispositivo en un blob en bloques de Almacenamiento de Azure y, a continuación, importar los dispositivos en el registro de identidad del dispositivo:

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Ejemplo de importación de dispositivos: eliminación masiva
El ejemplo de código siguiente muestra cómo eliminar los dispositivos que ha agregado con el ejemplo de código anterior:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same storage blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## Obtención del identificador URI de SAS del contenedor
El ejemplo de código siguiente muestra cómo generar un [identificador URI de SAS](../storage/storage-dotnet-shared-access-signature-part-2.md) con permisos de lectura, escritura y eliminación para un contenedor de blobs:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## Pasos siguientes
En este artículo, aprendió a realizar operaciones de forma masiva en el registro de identidad del dispositivo en un Centro de IoT. Siga estos vínculos para más información sobre la administración del Centro de IoT de Azure:

* [Métricas de uso][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]
* [Configuración y administración del acceso al Centro de IoT][lnk-itpro]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Diseño de la solución][lnk-design]
* [Guía del desarrollador][lnk-devguide]
* [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
* [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0720_2016-->