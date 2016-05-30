<properties
 pageTitle="Introducción a la biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure | Microsoft Azure"
 description="Biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure"
 services="iot-hub"
 documentationCenter=""
 authors="CarlosAlayo"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="carlosa"/>

# Introducción a la biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure

## Información general

La biblioteca de cliente de administración de dispositivos del Centro de IoT de Azure permite administrar dispositivos IoT con el Centro de IoT de Azure. "Administrar" incluye acciones tales como el reinicio, el restablecimiento de fábrica y la actualización del firmware. Actualmente se ofrece una biblioteca de C independiente de la plataforma, pero pronto se agregará compatibilidad con otros idiomas. Como se describe en el artículo [Azure IoT Hub device management overview][lnk-dm-overview] (Información general sobre la administración de dispositivos de Centro de IoT de Azure), hay tres conceptos clave en la administración de dispositivos de Centro de IoT:

- Dispositivos gemelos
- Trabajos del dispositivo
- Consultas del dispositivo

Si no está familiarizado con estos términos, consulte la información general antes de continuar. Todos ellos están estrechamente relacionados con la biblioteca de cliente.

La biblioteca de cliente de administración de dispositivos tiene dos responsabilidades principales:

- Sincronizar las propiedades del dispositivo físico con su correspondiente dispositivo gemelo en Centro de IoT
- Organizar los trabajos de dispositivo enviados por Centro de IoT al dispositivo

Las propiedades de dispositivo del dispositivo físico (por ejemplo, el nivel de batería y el número de serie) se sincronizan periódicamente con el dispositivo gemelo basado en la nube, de tal forma que Centro de IoT tenga una vista actualizada de cada uno de los dispositivos físicos en todo momento (siempre y cuando el dispositivo esté conectado).

La forma principal de que el servicio interactúe con el dispositivo físico es a través de trabajos de dispositivo y del dispositivo gemelo. Los siguientes tipos de trabajo se muestran en Centro de IoT. La biblioteca de cliente de administración de dispositivos se encarga de gran parte de la orquestación para los trabajos de dispositivo, pero depende del desarrollador implementar las devoluciones de llamada necesarias en el dispositivo para admitir cada tipo de trabajo.

1.	**Actualización de firmware**: actualiza el firmware (o imagen del sistema operativo) en el dispositivo físico.
2.	**Reinicio**: reinicia el dispositivo físico.
3.	**Restablecimiento de fábrica**: revierte el firmware (o imagen del sistema operativo) del dispositivo físico a una imagen de copia de seguridad de fábrica almacenada en el dispositivo.
4.	**Actualización de la configuración**: configura el agente de cliente de Centro de IoT que se ejecuta en el dispositivo físico.
5.	**Lectura de propiedad de dispositivo**: obtiene el valor más reciente de una propiedad de dispositivo en el dispositivo físico.
6.	**Escritura de propiedad de dispositivo**: cambia una propiedad de dispositivo en el dispositivo físico.

Las secciones siguientes le guían por la arquitectura de la biblioteca de cliente, así como proporcionan instrucciones sobre cómo implementar los diferentes objetos de dispositivo en el dispositivo.

## Conceptos funcionales y principios de diseño de la biblioteca de cliente de administración de dispositivos
La biblioteca de cliente de administración de dispositivos se ha diseñado pensando en la integración multiplataforma y en la portabilidad. Esto se ha logrado por las decisiones de diseño siguientes:

1.	Se ha creado en LWM2M sobre el protocolo estándar COAP para dar cabida a la extensibilidad para una variedad de distintos dispositivos.
2.	Se ha escrito en ANSI C99 para facilitar la portabilidad a una amplia variedad de plataformas.
3.	Se ha protegido por TCP/TLS y la autenticación de Centro de IoT de Azure (tokens SAS) para que se pueda utilizar en escenarios de alta seguridad.
4.	Está basado en el proyecto OSS de [Eclipse Wakaama][lnk-Wakaama] para aprovechar el código existente y contribuir a la comunidad.

### Conceptos pertinentes de LWM2M
Se ha elegido el estándar LWM2M para dar cabida a la extensibilidad para una variedad de distintos dispositivos. Para simplificar la experiencia de desarrollo, se ha resumido la mayor parte del protocolo. Sin embargo, es importante comprender los principios fundamentales de la biblioteca, principalmente en su modelo de datos y en cómo se transmiten los datos.

#### Objetos y recursos: modelo de datos en LWM2M
El modelo de datos LWM2M introduce el concepto de objetos y recursos:

- **Objetos** describe un conjunto de entidades funcionales coherentes del sistema, como las actualizaciones de firmware y del dispositivo.
- **Recursos** describe los atributos o las acciones incluidos en dichos objetos, como información de nivel de batería y la acción de reinicio.

Observe que hay dos relaciones "1: muchos" en este modelo:

- **Dispositivo y objetos**: cada dispositivo puede tener varios objetos. Por ejemplo, un dispositivo de Contoso debe tener un "objeto de dispositivo" y un "objeto de servidor" (se verá las descripciones detalladas de los objetos en la sección siguiente).
- **Objetos y recursos**: cada objeto puede tener varios recursos. Por ejemplo, un objeto puede contener los recursos de actualización del firmware del dispositivo de Contoso, como el identificador URI del paquete donde se almacena la nueva imagen.

#### Patrón de observación y notificación: cómo se transmiten los datos en LWM2M
Además de estos conceptos, es importante comprender cómo fluyen los datos del dispositivo al servicio. Para ello, LWM2M define el patrón de "observación y notificación". Cuando el dispositivo físico se conecta al servicio, el Centro de IoT inicia “observaciones” en las propiedades seleccionadas del dispositivo. A continuación, el dispositivo físico "notifica" al servicio de los cambios en las propiedades del dispositivo.

En nuestra biblioteca de cliente, hemos implementado el modelo de observación y notificación como la manera de enviar datos de administración del dispositivo desde el dispositivo al Centro de IoT. El patrón se controla mediante dos parámetros:

- **Período mínimo**: el período durante el cual el dispositivo retrasa el envío de una actualización a una propiedad observada. Se establece en 5 minutos. Por tanto, el dispositivo envía una actualización a una propiedad observada a lo sumo cada 5 minutos, incluso si el valor cambia con más frecuencia. Esto significa que si la propiedad cambia cada minuto, el servicio solo verá el último cambio en el minuto 5.

- **Período máximo**: el período en el que el dispositivo, con independencia de si cambia la propiedad observada o no, envía una actualización para el valor de la propiedad. Se establece en 6 horas. Por tanto, el dispositivo envía una actualización a una propiedad observada al menos cada 6 minutos, incluso si el valor no ha cambiado.

> [AZURE.NOTE]  La única excepción a estos parámetros es que las propiedades usadas para el trabajo de actualización de firmware tengan un período mínimo establecido en 30 segundos. Esto es porque estas propiedades cambian muy a menudo durante la ejecución del trabajo, por lo que hemos reducido el período mínimo para acelerar el proceso de actualización.

## Arquitectura y funciones de la biblioteca de cliente
Como se ha visto en la información general, hay dos funciones principales de las que se ocupan las bibliotecas de cliente:

- Sincronizar el dispositivo físico con su dispositivo gemelo correspondiente en Centro de IoT
- Organizar los trabajos de dispositivo enviados por Centro de IoT al dispositivo.

En esta sección, se profundizará en cada una de ellas.

### Sincronización del dispositivo físico y su dispositivo gemelo
La biblioteca cliente usa el patrón de notificación y observación para mantener actualizado el dispositivo gemelo. Hay que recordar que el dispositivo gemelo es la representación del dispositivo físico en el servicio. Para realizar esta sincronización, se produce el siguiente proceso:

1. El dispositivo se registra con el servicio, normalmente durante la inicialización. Por ejemplo: "Soy un dispositivo, tengo el identificador de dispositivo Contoso con el token de acceso Y".
2. El servicio observa los recursos en los objetos. Por ejemplo: "manténganme actualizado sobre el nivel de batería de mi dispositivo Contoso".
3. El dispositivo notifica al servicio del valor del recurso. La frecuencia de las notificaciones se basa en el período mínimo y máximo. Por ejemplo: "5:00 p.m. nivel de batería al 99 %, 5:05 p.m. nivel de batería al 90 %, etc."

### Organización de los trabajos de dispositivo: cómo funcionan conjuntamente el dispositivo gemelo y los trabajos de dispositivo
Para actuar en un dispositivo físico, el servicio debe encontrar el dispositivo gemelo asociado. Esto puede realizarse mediante la consulta en propiedades o por la búsqueda de un identificador concreto. Cuando conoce el identificador gemelo (y por tanto, la coincidencia con el dispositivo físico), el servicio está listo para iniciar un trabajo de dispositivo en el dispositivo físico.

El trabajo de dispositivo representa un conjunto de comandos diferentes que representan el proceso específico que se quiere realizar (por ejemplo, los pasos de actualización de firmware). Un trabajo de dispositivo puede estar compuesto de varios pasos:

1.	El dispositivo gemelo tiene propiedades que representan el estado de un trabajo de dispositivo.
2.	Para avanzar entre los distintos procesos del trabajo de dispositivo, las propiedades del dispositivo gemelo deben ser un valor determinado, por tanto el dispositivo físico debe establecer la propiedad correcta para que se pueda sincronizar con el dispositivo gemelo y el trabajo avance.

Esta secuencia se repite si el proceso se compone de varios pasos (por ejemplo: durante una actualización del firmware, el dispositivo gemelo cambiará sus propiedades varias veces a través de los distintos pasos antes de considerar finalizado el trabajo).

## Directrices para implementar la administración de dispositivos en el cliente
En las secciones anteriores, hemos aprendido sobre las funciones de las bibliotecas de cliente de administración de dispositivos, sus principios de diseño y cómo se relaciona con LWM2M. Ahora nos centraremos en explicar cómo encajan todas las piezas en tiempo de ejecución.

Básicamente, la biblioteca de cliente se encarga de la comunicación entre el dispositivo y el servicio, por lo que todo lo que queda es la implementación de la lógica específica del dispositivo. Consta de dos partes:

1.	**Implementar los detalles específicos del dispositivo**: implica escribir lógica específica del dispositivo para realizar las acciones solicitadas por el servicio (por ejemplo: descargar el paquete de firmware) en las devoluciones de llamada apropiadas. A continuación se muestra un ejemplo de esta devolución de llamada para el paquete de actualización de firmware. [Aquí][lnk-github1] puede encontrar las devoluciones de llamada en los archivos .c de la carpeta.

            object_firmwareupdate *obj = get_firmwareupdate_object(0);
            obj->firmwareupdate_packageuri_write_callback =     start_firmware_download;
            // platform specific code
            obj->firmwareupdate_update_execute_callback = start_firmware_update;
            //platform specific code


2.	**Informar a la biblioteca de cliente cuando cambian las propiedades**: se consigue mediante una llamada [aquí][lnk-github2] a las funciones del conjunto correspondientes en el archivo .h de la carpeta.

        IOTHUB_CLIENT_RESULT set_firmwareupdate_state(uint16_t instanceId, int value);

### Objetos que se deben implementar en la biblioteca de cliente de administración de datos

Se acaba de explicar cómo se podría implementar la lógica específica del dispositivo para realizar trabajos de dispositivo. Ahora se explicará qué objetos están disponibles para su uso.

Algunos de estos objetos son necesarios, lo que significa que hay que implementar la lógica de específica de dispositivo para que pueda formar parte de la administración de dispositivos de Centro de IoT. Otros son opcionales, por lo que puede elegir según sus necesidades de servicio (por ejemplo, puede elegir no realizar las actualizaciones de firmware con Centro de IoT). Esta es una descripción de cada uno:

- **Objeto de dispositivo (necesario)**: proporciona información específica del dispositivo, como información del fabricante, número de modelo, número de serie u hora del dispositivo. El servicio puede leer esta información y, en algunos casos, actualizarla. También define dos acciones que puede realizar el servicio en un dispositivo: reinicio y restablecimiento de fábrica.
- **Objeto de servidor (obligatorio)**: contiene los parámetros y la configuración de conexión que se utilizan para conectarse a Centro de IoT, como la duración del registro y el enlace de transporte. El servicio solo puede leer esta información.
- **Objeto de configuración (opcional)**: contiene información de configuración definida por el usuario que se puede consultar desde el dispositivo o insertarse en un dispositivo desde el servicio para facilitar la configuración del dispositivo. El servicio puede leer y actualizar esta información.
- **Objeto de la actualización de firmware (opcional)**: proporciona una acción de actualización de firmware que puede invocar el servicio. También proporciona información como la ubicación del paquete de firmware y el estado de una operación de actualización de firmware en curso.

Cada uno de estos objetos cuenta con un conjunto de recursos asociados (recuerde la asociación de 1: muchas). La lista de los objetos LWM2M y todos sus recursos asociados que admite la administración de dispositivos de Centro de IoT de Azure se incluyen al final de este artículo.

> [AZURE.NOTE] En la versión actual del sistema no se admiten las propiedades de dispositivo personalizado, varias instancias de recursos y varias instancias de objetos.

### Resumen

A continuación, encontrará un diagrama que reúne todas las piezas diferentes. En el lado derecho, en azul, puede ver los distintos componentes de la biblioteca de cliente de administración de dispositivos: objetos, controladores y métodos de notificación. El lado izquierdo, en verde, representa la lógica que debe escribir en el nivel de aplicación de dispositivo. La biblioteca de cliente conecta a la lógica de aplicación con Centro de IoT, asegurándose de que la comunicación y la organización se controlan apropiadamente.

La siguiente ilustración muestra los componentes de la biblioteca de cliente de administración de datos.

![][img-library-overview]

## Pasos siguientes: obtener experiencia práctica
En este artículo se tratan los aspectos básicos del uso de la biblioteca de cliente de administración de dispositivos de Centro de IoT para C.

Para obtener experiencia práctica, puede tener acceso a los recursos siguientes:

- Ejemplo de actualización de firmware de Intel Edison: ejemplo con características de administración de dispositivos habilitadas con un dispositivo Intel Edison. Consulte [iotdm\_edison\_sample][lnk-edison-sample].
- Ejemplo de dispositivos simulados: un ejemplo de dispositivos independientes de plataforma que se ejecuta en dispositivos con Linux y Windows. Consulte [iotdm\_simple\_sample][lnk-simple-sample]
- Para más información acerca de los objetos LWM2M, consulte [OMA LWM2M object and resource registry][lnk-oma] (Registro de recursos y objetos OMA LWM2M).

## Apéndice: Recursos y objetos LWM2M admitidos

### Objeto de dispositivo

| Nombre de recurso | Operación remota permitida en recurso | Tipo | Intervalo y unidades | Descripción |
|-----------------|--------------------------------------|---------|-----------------|-------------|
| Fabricante | Lectura | String | | Nombre del fabricante |
| ModelNumber | Lectura | String | | Un identificador de modelo (cadena especificada por el fabricante) |
| DeviceType | Lectura | String | | Tipo de dispositivo (cadena especificada por el fabricante)<br/>Nota: se asigna a la API del servidor **SystemPropertyNames.DeviceDescription** |
| SerialNumber | Lectura | String | | Número de serie del dispositivo |
| FirmwareVersion | Lectura | String | | Versión actual del firmware del dispositivo |
| HardwareVersion | Lectura | String | | Versión actual del hardware del dispositivo |
| Reboot | Ejecución | | | Reinicio del dispositivo. |
| FactoryReset | Ejecución | | | Realización del restablecimiento de fábrica del dispositivo para que el dispositivo tenga la misma configuración en la implementación inicial. |
| CurrentTime | Lectura<br/>Escritura | Hora | | Hora UNIX actual del dispositivo. El cliente debe responsabilizarse en aumentar este valor de tiempo cuando transcurre cada segundo.<br/>El servidor de administración de dispositivos es capaz de escribir a este recurso para que el cliente se sincronice con la hora en el servidor. |
| UTCOffset | Lectura<br/>Escritura | String | | Diferencia horaria con UTC en vigor. |
| Zona horaria | Lectura<br/>Escritura | String | | Indica en qué zona horaria se encuentra el dispositivo. |
| MemoryFree | Lectura | Entero | KB | Memoria disponible actual estimada de espacio de almacenamiento que puede almacenar datos y software en el dispositivo |
| MemoryTotal | Lectura | Entero | KB | Cantidad total de espacio de almacenamiento que puede almacenar datos y software en el dispositivo |
| BatteryLevel | Lectura | Entero | 0-100 % | Nivel de batería actual como porcentaje (de 0 a 100) |
| BatteryStatus | Lectura | Entero | 0-6 | **0**: la batería está funcionando correctamente y no se está cargando.<br/>**1**: la batería se está cargando.<br/>**2**: la batería está totalmente cargada y conectada a la red.<br/>**3**: la batería está dañada.<br/>**4**: la batería tiene poca carga.<br/>**5**: la batería no está presente.<br/> **6**: la información sobre la batería no está disponible. |

### Objeto de actualización de firmware

| Nombre de recurso | Operación | Tipo | Intervalo y unidades | Descripción |
|----------------|-----------|---------|-----------------|-------------|
| Paquete | Escritura | Opaca | | Paquete de firmware en formato binario.<br/>Se asigna a la API de servicio:<br/>**SystemPropertyNames.FirmwarePackage** |
| PackageUri | Escritura | String | 0-255 bytes | Identificador URI desde el cual el dispositivo puede descargar el paquete de firmware.<br/>Se asigna a la API de servicio: **SystemPropertyNames.FirmwarePackageUri** |
| Actualizar | Ejecución | | | Actualiza el firmware mediante el paquete de firmware almacenado en el paquete, o bien utilizando el firmware descargado desde el identificador URI de paquete.<br/>Se asigna a la API de servicio:<br/>**ScheduleFirmwareUpdateAsync** |
| Estado | Lectura | Entero | 1-3 | Estado del proceso de actualización de firmware:<br/>**1**: inactivo. Esto puede ocurrir antes de descargar el paquete de firmware o después de aplicar el paquete de firmware.<br/>**2**: descargando el paquete de firmware.<br/>**3**: paquete de firmware descargado.<br/> Se asigna a la API de servicio: **SystemPropertyNames.FirmwareUpdateState** |
| UpdateResult | Lectura | Entero | 0-6 | Resultado de la descarga o actualización del firmware<br/>**0**: valor predeterminado.<br/>**1**: actualización de firmware correcta.<br/>**2**: no hay suficiente almacenamiento para el nuevo paquete de firmware.<br/>**3**: memoria agotada durante la descarga del paquete de firmware.<br/>**4**: conexión perdida durante la descarga del paquete de firmware.<br/>**5**: error de comprobación de CRC para el nuevo paquete descargado.<br/>**6**: tipo de paquete de firmware no admitido.<br/>**7**: URI no válido. Se asigna a la API de servicio: **SystemPropertyNames.FirmwareUpdateState** |
| PkgName | Lectura | String | 0-255 bytes | Nombre descriptivo del paquete de firmware al que hace referencia el recurso **Paquete**<br/>Se asigna a la API de servicio:<br/>**SystemPropertyNames.FirmwarePackageName** |
| PackageVersion | Lectura | String | 0-255 bytes | Versión del paquete de firmware al que hace referencia el recurso **Paquete**<br/>Se asigna a la API de servicio:<br/>**SystemPropertyNames.FirmwarePackageVersion** |

### Objeto de servidor LWM2M

| Nombre de recurso | Operación | Tipo | Intervalo y unidades | Descripción |
|------------------------|------------|---------|-----------------|---------------|
| Período mínimo predeterminado | Lectura Escritura | Entero | Segundos | El período durante el cual el dispositivo retrasa el envío de una actualización a una propiedad observada. Por ejemplo, dado un valor **DefaultMinPeriod** de 5 minutos, el dispositivo envía una actualización a una propiedad observada a lo sumo cada 5 minutos, incluso si el valor cambia con más frecuencia. Se asigna a la API de servicio: **SystemPropertyNames.FirmwareUpdateState** |
| Período máximo predeterminado | Lectura Escritura | Entero | Segundos | El período (en segundos) en el que el dispositivo, con independencia de si cambia la propiedad observada, envía una actualización para el valor de la propiedad. Por ejemplo, dado un valor **DefaultMaxPeriod** de 6 horas, una propiedad observada envía una actualización para el valor de dicha propiedad al menos cada 6 horas, con independencia de los cambios de los recursos.<br/>Se asigna a la API de servicio:<br/>**SystemPropertyNames.DefaultMaxPeriod** |
| Vigencia | Lectura Escritura | Entero | Segundos | La duración de registro del dispositivo. Un nuevo registro o una actualización de solicitud debe recibirse desde el dispositivo dentro de esta duración; de lo contrario, se cancela el registro del dispositivo desde el servicio.<br/>Se asigna a la API de servicio:<br/>**SystemPropertyNames.RegistrationLifetime** |

### Objeto de configuración

| Nombre de recurso | Operación | Tipo | Intervalo y unidades | Descripción |
|---------------|------------|--------|-----------------|-------------|
| Nombre | Lectura Escritura | String | | Identifica de manera exclusiva el nombre de la configuración del dispositivo que se va a leer o actualizar. |
| Valor | Lectura Escritura | String | | Identifica de manera exclusiva el valor de configuración que se va a leer o actualizar. |
| Aplicar | Ejecución | | | Se aplica el cambio de configuración en el dispositivo. |

[img-library-overview]: media/iot-hub-device-management-library/library.png
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-simple-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_simple_sample
[lnk-edison-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[Azure IoT Hub device SDK]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c
[Azure IoT Hub]: Link%20to%20DM%20Overview
[Lightweight M2M]: http://openmobilealliance.org/about-oma/work-program/m2m-enablers/
[CoAP]: https://tools.ietf.org/html/rfc7252
[Wakaama]: https://github.com/eclipse/wakaama
[OMA LWM2M Object and resource registry]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-run-linux]: http://TODO
[lnk-Wakaama]: https://github.com/eclipse/wakaama
[lnk-github1]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-github2]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-oma]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

<!---HONumber=AcomDC_0518_2016-->