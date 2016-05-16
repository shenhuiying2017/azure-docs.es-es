<properties
 pageTitle="Información general sobre la administración de dispositivos | Microsoft Azure"
 description="Información general de la administración de dispositivos de Centro de IoT de Azure: dispositivos gemelos, consultas de dispositivo, trabajos de dispositivo"
 services="iot-hub"
 documentationCenter=""
 authors="ellenfosborne"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Introducción a la administración de dispositivos desde Centro de IoT de Azure (versión preliminar)

La administración de dispositivos desde Centro de IoT de Azure permite la administración de dispositivos de IoT basada en los estándares para que pueda administrar, configurar y actualizar los dispositivos de forma remota.

Hay tres conceptos principales para la administración de dispositivos de IoT de Azure:

1.  **Dispositivo gemelo:** la representación del dispositivo físico en Centro de IoT.

2.  **Consultas de dispositivo**: permiten buscar dispositivos gemelos y conocerlos globalmente. Por ejemplo, puede buscar todos los dispositivos gemelos cuya versión del firmware sea la 1.0.

3.  **Trabajos del dispositivo**: acción que se realiza en uno o varios dispositivos físicos, como la actualización del firmware, el reinicio y el restablecimiento de fábrica.

## Dispositivo gemelo

El dispositivo gemelo es la representación de un dispositivo físico en IoT de Azure. El objeto **Microsoft.Azure.Devices.Device** se utiliza para representar el dispositivo gemelo.

![][img-twin]

El dispositivo gemelo tiene los siguientes componentes:

1.  **Campos del dispositivo:** los campos del dispositivo son propiedades predefinidas que se usan tanto para la administración de dispositivos como la mensajería de Centro de IoT. Dichos campos ayudan al Centro de IoT a identificar los dispositivos físicos y a y conectar con ellos. Los campos del dispositivo no se sincronizan con el dispositivo y se almacenan exclusivamente en el dispositivo gemelo. Los campos del dispositivo incluyen el identificador del dispositivo y la información de autenticación.

2.  **Propiedades del dispositivo:** Las propiedades del dispositivo son un diccionario predefinido de propiedades que describen el dispositivo físico. El dispositivo físico es el maestro de cada propiedad del dispositivo y es el almacén de autoridad de cada valor correspondiente. Una representación coherente en última instancia de estas propiedades se almacena en el dispositivo gemelo que está en la nube. La coherencia y actualización están sujetos a la configuración de la sincronización, que se describe en [Tutorial: how to use the device twin][lnk-tutorial-twin] \(Tutorial: Uso del dispositivo gemelo). Algunos ejemplos de propiedades del dispositivo incluyen la versión del firmware, el nivel de la batería y el nombre del fabricante.

3.  **Propiedades del servicio:** las propiedades del servicio son pares **& lt; clave, valor &gt;** que el programador agrega al diccionario de propiedades del servicio. Estas propiedades extienden el modelo de datos del dispositivo gemelo, lo que permite caracterizar mejor el dispositivo. Las propiedades del servicio no se sincronizan con el dispositivo y se almacenan exclusivamente en el dispositivo gemelo en la nube. Un ejemplo de propiedad de servicio es **& lt; NextServiceDate, 12/11/2017 &gt;**, que se puede utilizar para buscar dispositivos por su siguiente fecha de servicio.

4.  **Etiquetas:** las etiquetas son un subconjunto de propiedades del servicio que son cadenas arbitrarias, en lugar de propiedades del diccionario. Se puede utilizar para anotar dispositivos gemelos u organizar dispositivos en grupos. Las etiquetas no se sincronizan con el dispositivo y se almacenan exclusivamente en el dispositivo gemelo. Por ejemplo, si un dispositivo gemelo representa un camión físico, puede agregar una etiqueta para cada tipo de carga del camión: **manzanas**, **naranjas** y **plátanos**.

## Consultas del dispositivo

En la sección anterior, conoció los diferentes componentes del dispositivo gemelo. A continuación, explicaremos cómo buscar dispositivos en el registro de dispositivos de Centro de IoT basándose en las propiedades del dispositivo, en las propiedades del servicio o en las etiquetas. Un ejemplo de cuándo podría utilizar una consulta es la búsqueda de los dispositivos que necesitan actualizarse. Puede consultar todos los dispositivos con una versión de firmware especificado e introducir el resultado en una acción específica (que se conoce en Centro de IoT como un trabajo del dispositivo, que se explica en la sección siguiente).

Puede realizar consultas mediante etiquetas y propiedades:

-   Para consultar dispositivos gemelos mediante etiquetas, pase una matriz de cadenas y la consulta devolverá el conjunto de dispositivos etiquetados con todas esas cadenas.

-   Para consultar dispositivos gemelos mediante propiedades del servicio o propiedades del dispositivo, se usa una expresión de consulta JSON. El ejemplo siguiente muestra cómo se pueden consultar todos los dispositivos con la propiedad del dispositivo con la clave **FirmwareVersion** y el valor **1.0**. Puede ver que el **tipo** de la propiedad es **device**, lo que indica que la consulta se realiza según las propiedades del dispositivo, no las propiedades del servicio:

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## Trabajos del dispositivo

El siguiente concepto de la administración de dispositivos es los trabajos del dispositivo, que permiten la coordinación de orquestaciones de varios pasos en varios dispositivos.

La administración de dispositivos de Centro de IoT de Azure proporciona en la actualidad seis tipos de trabajos de dispositivo (agregaremos mas trabajos a medida que los clientes los necesiten):

- **Actualización de firmware**: actualiza el firmware (o la imagen del sistema operativo) en el dispositivo físico.
- **Reinicio**: reinicia el dispositivo físico.
- **Restablecimiento de fábrica**: revierte el firmware (o imagen del sistema operativo) del dispositivo físico a una imagen de copia de seguridad de fábrica almacenada en el dispositivo.
- **Actualización de la configuración**: configura el agente de cliente de Centro de IoT que se ejecuta en el dispositivo físico.
- **Lectura de propiedad de dispositivo**: obtiene el valor más reciente de una propiedad de dispositivo en el dispositivo físico.
- **Escritura de propiedad de dispositivo**: cambia una propiedad de dispositivo en el dispositivo físico.

Para más información sobre cómo usar cada uno de estos trabajos, consulte la [documentación de la API para C# y node.js][lnk-apidocs].

Un trabajo puede operar en varios dispositivos. Al iniciar un trabajo, se crea un trabajo secundario asociado para cada uno de los dispositivos. Los trabajos secundarios operan en dispositivos individuales. Cada trabajo secundario tiene un puntero a su trabajo primario. El trabajo primario es solo un contenedor para los trabajos secundarios, no implementa ninguna lógica que distinga los distintos tipos de dispositivos (como la actualización de Edison Intel frente a la actualización de un Raspberry Pi). El diagrama siguiente ilustra la relación entre un trabajo primario, sus elementos secundarios y los dispositivos físicos asociados.

![][img-jobs]

Puede consultar el historial de trabajos para conocer el estado de los trabajos que ha iniciado. Para ver consultas de ejemplo, consulte [nuestra biblioteca de consultas][lnk-query-samples].

## Implementación del dispositivo

Una vez que se han tratado los conceptos del servicio, veamos cómo crear dispositivos con funcionalidades de administración. La biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure del dispositivo proporciona los artefactos necesarios para implementar la comunicación entre un dispositivo físico y Centro de IoT.

La biblioteca de cliente de DM de Centro de IoT de Azure resume el estándar [LWM2M][lnk-lwm2m] y el protocolo de solicitud y respuesta basado en CoAP. Por consiguiente, la biblioteca tiene un modelo de dispositivo de *objetos* y *definiciones de recursos*:

-   Los objetos describen un conjunto de entidades funcionales coherentes del sistema, como las actualizaciones de firmware y de los dispositivos.
-   Los recursos describe los atributos o las acciones incluidos en dichos objetos, como la información del nivel de batería y la acción de reinicio.

Cuando se utiliza la biblioteca de cliente de DM de Centro de IoT de Azure, es preciso implementar las devoluciones de llamada para las operaciones de lectura, escritura y ejecución en todos los recursos del dispositivo físico. La biblioteca controla la actualización asincrónica de Centro de IoT cuando se cambian las propiedades.

El diagrama siguiente muestra los distintos componentes necesarios en el agente de cliente de Centro de IoT.

![][img-client]

Puede obtener más información acerca de la implementación en el dispositivo físico en [Introducing the Azure IoT Hub device management (DM) client library][lnk-library-c] (Presentación de la biblioteca de cliente de la administración de dispositivos [DM] de Centro de IoT de Azure).

## Pasos siguientes

Para más información acerca de las características de administración de dispositivos de Centro de IoT de Azure puede realizar los tutoriales:

- [Get started with Azure IoT Hub device management using C#][lnk-get-started] \(Introducción a la administración de dispositivos desde Centro de IoT de Azure mediante C#).

- [How to use the device twin][lnk-tutorial-twin] \(Uso del dispositivo gemelo)

- [How to find device twins using queries][lnk-tutorial-queries] \(Búsqueda de dispositivos gemelos mediante consultas)

- [How to use device jobs to update device firmware][lnk-tutorial-jobs] \(Uso de trabajos de dispositivos para actualizar el firmware del dispositivo)

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0504_2016-->