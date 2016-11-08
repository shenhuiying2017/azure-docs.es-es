---
title: Trabajos de dispositivo de la administración de dispositivos del Centro de IoT | Microsoft Docs
description: Tutorial del Centro de IoT de Azure para administración de dispositivos que describe cómo usar trabajos de dispositivo para realizar operaciones como la actualización de firmware remota.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# Tutorial: Uso de trabajos de dispositivo para actualizar el firmware de dispositivo (vista previa)
[!INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## Introducción
La administración de dispositivos IoT de Azure le permite interactuar con dispositivos físicos mediante los trabajos de dispositivo. Una vez identificado el dispositivo gemelo (la representación de servicio de un dispositivo físico), puede interactuar con el dispositivo físico correspondiente mediante los trabajos de dispositivo. Los trabajos de dispositivo permiten la coordinación de procesos complejos en varios dispositivos. Este proceso puede incluir varios pasos y operaciones de ejecución prolongada.

La administración de dispositivos de Centro de IoT de Azure proporciona en la actualidad seis tipos de trabajos de dispositivo (agregaremos mas trabajos a medida que los clientes los necesiten):

* **Actualización de firmware:** actualiza el firmware (o la imagen del sistema operativo) en el dispositivo físico.
* **Reinicio:** reinicia el dispositivo físico.
* **Restablecimiento de fábrica:** revierte el firmware (o imagen del sistema operativo) del dispositivo físico a una imagen de copia de seguridad de fábrica almacenada en el dispositivo.
* **Actualización de la configuración:** configura el agente de cliente del Centro de IoT que se ejecuta en el dispositivo físico.
* **Lectura de propiedad de dispositivo:** obtiene el valor más reciente de una propiedad de dispositivo en el dispositivo físico.
* **Escritura de propiedad de dispositivo:** cambia una propiedad de dispositivo en el dispositivo físico.

Para obtener más información sobre cómo usar cada uno de estos trabajos, consulte la [documentación de la API][lnk-apidocs].

Puede consultar el historial de trabajos para comprender el estado de los trabajos que ha iniciado. Para ver algunas consultas de ejemplo, consulte [nuestra biblioteca de expresiones de consulta][lnk-query-samples].

## Uso de trabajos de dispositivo para realizar la actualización del firmware: arquitectura
El diagrama siguiente muestra un trabajo de dispositivo de actualización de firmware que interactúa con un único dispositivo físico.

![][img-architecture]

Pasos del trabajo de dispositivo de actualización de firmware:

1. La solución de IoT basada en la nube inicia el trabajo de dispositivo de actualización de firmware y proporciona el identificador URI de la ubicación del paquete de firmware. Es responsabilidad de la solución de IoT colocar el paquete de firmware en una ubicación de donde puede descargarla el dispositivo.
2. Cuando el dispositivo físico recibe este identificador URI, automáticamente empieza a descargar desde él.
3. El código en el dispositivo recibe una solicitud desde el Centro de IoT y descarga el paquete de firmware desde el identificador URI proporcionado de la ubicación.
4. Después de recibir el mensaje de estado de dispositivo de descarga completada, el trabajo de dispositivo solicita al dispositivo que aplique la imagen de firmware descargada.
5. Después de que el dispositivo aplica la imagen de firmware, se reinicia, se vuelve a conectar al Centro de IoT y le informa de que se aplicó el nuevo firmware, con lo cual, el trabajo de dispositivo se considera finalizado.

## Ejecución del ejemplo de actualización de firmware
En el siguiente ejemplo se amplía la función del tutorial [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-get-started]. Desde el momento en que se tienen distintos dispositivos simulados en ejecución, se inicia un trabajo para actualizar el firmware en todos ellos.

### Requisitos previos
Antes de ejecutar este ejemplo, debe haber completado los pasos de [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-get-started]. Esto significa que los dispositivos simulados deben estar en ejecución. Si ya realizó el proceso anteriormente, reinicie ahora los dispositivos simulados.

### Inicio del ejemplo
Para iniciar el ejemplo, debe ejecutar el proceso **FirmwareUpdate.exe**. Esto iniciará el proceso de actualización de firmware en todos los dispositivos simulados. Siga los pasos siguientes para iniciar el ejemplo:

1. Desde la carpeta raíz donde clonó el repositorio **azure-iot-sdks**, vaya a la carpeta **azure-iot-sdks\\csharp\\service\\samples\\bin**.
2. Ejecute `FirmwareUpdate.exe <IoT Hub Connection String>`

Debería ver el resultado en la ventana de línea de comandos que muestra siete trabajos de dispositivo que comprueban la actualización del firmware en seis dispositivos simulados.

### Inicio de un trabajo de dispositivo
En general, los trabajos de dispositivo se inician mediante varios métodos **Schedule&lt;nombreDelTrabajo&gt;Async** en la instancia **JobClient**. En el ejemplo de actualización de firmware, llamamos al método **ScheduleFirmwareUpdateAsync**. Dado que vamos a trabajar con una matriz con seis identificadores de dispositivo, se iniciarán siete trabajos de dispositivo, uno para cada dispositivo que se va a actualizar y un trabajo de dispositivo principal que se usa para realizar el seguimiento de los otros seis.

En el fragmento de código siguiente, a partir de **Program.cs** en el proyecto **FirmwareUpdate**, se inicia un trabajo de actualización de firmware. La llamada toma una matriz de cadenas de valores **deviceId** como un parámetro que representa los dispositivos que queremos actualizar.

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### Consulta del historial de trabajos
En el ejemplo, se muestra periódicamente una lista de los dispositivos que están ejecutando activamente un trabajo de dispositivo. Cuando se completa un trabajo de dispositivo, el dispositivo asociado desaparece de la lista. La siguiente ilustración es una captura de pantalla de **FirmwareUpdate.exe** en ejecución:

![][img-output1]

La lista anterior se genera mediante la consulta de todos los trabajos activos, como se muestra en el fragmento de código siguiente a partir de **Program.cs** en el proyecto **FirmwareUpdate**:

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

Una vez completado el trabajo de dispositivo primario, el ejemplo genera una lista de todos los trabajos de dispositivo. Esto se puede observar en la siguiente ilustración. El trabajo primario solo se completará cuando se hayan completado todos los trabajos secundarios asociados. En la siguiente captura de pantalla, el trabajo primario es la última tarea de la lista y esto se sabe porque **ParentJobId** es **''**. El trabajo primario también tiene el campo **Motivo** establecido en una cadena que indica el resultado agregado de la consulta. En este caso, muestra que seis dispositivos se actualizaron y todos ellos lo hicieron correctamente.

![][img-output2]

La lista anterior se genera mediante la realización de consultas para todos los trabajos y la ordenación por hora de inicio. Cualquiera de las propiedades del objeto **JobResponse**, mostrado aquí, se puede usar para consultar el historial de trabajos del dispositivo.

![][img-properties]

Para ver más ejemplos de cómo consultar el historial de trabajos de dispositivo, consulte [nuestra biblioteca de expresiones de consulta][lnk-query-samples].

### Detalles de implementación del simulador de dispositivo
En las secciones anteriores, se muestran los detalles de implementación de la actualización de firmware desde el punto de vista del servicio (cómo iniciar un trabajo de dispositivo y consultar su estado). Ahora, se analizará la implementación correspondiente en el lado del dispositivo.

La biblioteca de cliente de la administración de dispositivos del Centro de IoT de Azure se encarga de la comunicación entre el dispositivo y el servicio, por lo que todo lo que queda es la implementación de la lógica específica del dispositivo. Consta de dos partes:

* Implementación del proceso de actualización de firmware específico del dispositivo: esto implica escribir una lógica específica del dispositivo para descargar el paquete de firmware y aplicar la actualización en las devoluciones de llamada apropiadas enumeradas a continuación. En el ejemplo simulado, esto se implementa en [el ejemplo][lnk-github-firmware]\:
  
  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```
* Información al servicio del proceso de actualización de firmware: esto implica modificar el estado de la actualización de firmware y los campos de resultados de la misma. Puede conseguirlo mediante una llamada a las API **set\_firmwareupdate\_state** y **set\_firmwareupdate\_updateresult**. En el ejemplo simulado, esto se implementa en [el ejemplo][lnk-github-firmware].

## Pasos siguientes
Para más información acerca de las características de administración de dispositivos de Centro de IoT de Azure puede realizar los tutoriales:

* [Habilitación de los dispositivos administrados detrás de una puerta de enlace de IoT][lnk-dm-gateway]
* [Introducción a la biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure][lnk-library-c]
* La biblioteca de cliente de administración de dispositivos del Centro de IoT de Azure proporciona un ejemplo de un extremo a otro mediante un [dispositivo Intel Edison][lnk-edison].

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Diseño de la solución][lnk-design]
* [Guía del desarrollador][lnk-devguide]
* [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
* [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->