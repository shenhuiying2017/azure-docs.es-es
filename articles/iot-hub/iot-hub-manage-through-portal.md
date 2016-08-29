<properties
	 pageTitle="Usar el portal de Azure para administrar el centro de IoT | Microsoft Azure"
	 description="Información general sobre cómo crear y administrar los centros de IoT de Azure a través del portal de Azure"
	 services="iot-hub"
	 documentationCenter=""
	 authors="nasing"
	 manager="timlt"
	 editor=""/>

<tags
	 ms.service="iot-hub"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.tgt_pltfrm="na"
	 ms.workload="na"
	 ms.date="08/11/2016"
	 ms.author="nasing"/>

# Administración de Centros de IoT a través del portal de Azure

## Introducción

En este artículo se describe cómo empezar a trabajar con Centros de IoT a través del Portal de Azure, cómo buscar Centros de IoT, y cómo crearlos y administrarlos.

## Dónde encontrar centros de IoT

Existen varios lugares donde encontrar los Centros de IoT.

1. **+ Nuevo**: el **Centro de IoT de Azure** es un servicio de IoT que se puede encontrar en la categoría **Internet de las cosas** en **+ Nuevo**, al igual que otros servicios.

2. También se puede acceder a los Centros de IoT mediante Marketplace como servicio destacado en **Internet de las cosas**.

## Crear un centro de IoT

Puede crear un centro de IoT con los métodos siguientes.

1. Al crear un centro de IoT mediante la opción **+ Nuevo**, aparece la hoja que se muestra en la siguiente captura de pantalla. Los pasos para crear el Centro de IoT con este método y con Marketplace son idénticos.

2. Para crear un Centro de IoT mediante Marketplace: al hacer clic en **Crear**, se abre una hoja igual a la hoja anterior de la experiencia **+ Nuevo**. Las secciones siguientes enumeran los diversos pasos para crear un Centro de IoT.

### Elección del nombre del Centro de IoT

Para crear un Centro de IoT, debe asignar un nombre al centro. Tenga en cuenta que este nombre debe ser único entre los centros. La duplicación de centros no está permitida en el back-end, por lo que se recomienda asignar a este centro un nombre lo más único posible.

### Elección del plan de tarifa

Puede elegir entre cuatro niveles: **Gratis**, **Estándar 1 **, **Estándar 2** y **Estándar S3**. El nivel Gratis permite solo la conexión de 500 dispositivos con el Centro de IoT y hasta 8000 mensajes al día.

**Estándar S1**: la edición S1 de Centro de IoT está diseñada para las soluciones de IoT que tienen un gran número de dispositivos que generan cantidades de datos relativamente pequeñas por cada dispositivo. Cada unidad de la edición S1 permite transmitir hasta 400.000 mensajes por día a través de todos los dispositivos conectados.

**Estándar S2**: la edición S2 de Centro de IoT está diseñada para las soluciones de IoT en las que los dispositivos generan grandes cantidades de datos. Cada unidad de la edición S2 permite transmitir hasta 6 millones de mensajes al día entre todos los dispositivos conectados.

**Estándar S3**: la edición S3 del Centro de IoT se ha diseñado para las soluciones de IoT que generan grandes cantidades de datos. Cada unidad de la edición S3 permite transmitir hasta 300 millones de mensajes al día entre todos los dispositivos conectados.

![][4]

> [AZURE.NOTE] El Centro de IoT solo permite un único centro gratuito por cada suscripción.

### Unidades del Centro de IoT

Una unidad del Centro de IoT incluye un número determinado de mensajes por día. La elección del número de unidades de IoT significa que el número total de mensajes compatibles para este centro es el número de unidades multiplicado por el número de mensajes al día para ese nivel. Por ejemplo, si desea que el Centro de IoT admita la entrada de 700 000 mensajes, elija dos unidades del nivel de S1.

### Dispositivo para particiones en la nube y grupo de recursos

Puede cambiar el número de particiones para un centro de IoT. Las particiones predeterminadas están establecidas en cuatro; sin embargo, puede elegir un número diferente de particiones en una lista desplegable.

Para los grupos de recursos, no es necesario crear explícitamente un grupo de recursos vacío. Al crear un nuevo recurso, puede elegir entre crear un nuevo grupo de recursos o usar un grupo de recursos existente.

![][5]

### Selección de suscripciones

El Centro de IoT de Azure muestra automáticamente la lista de suscripciones al que está vinculada la cuenta de usuario. Puede elegir una de estas opciones para asociar el Centro de IoT a esa suscripción.

### Selección de la ubicación

La opción de ubicación proporciona una lista de las regiones en las que se ofrece Centro de IoT. Centro de IoT está disponible para su implementación en las siguientes ubicaciones: este de Australia, sudeste de Australia, este de Asia, sudeste de Asia, Norte de Europa, Oeste de Europa, Japón Oriental, Japón Occidental, Este de EE. UU., Oeste de EE. UU.

### Creación del Centro de IoT

Cuando se completen todos los pasos anteriores, el centro de IoT estará listo para crearse. Haga clic en **Crear** para iniciar el proceso back-end de creación de este centro de IoT con las opciones específicas e implementarlo en la ubicación especificada.

Tenga en cuenta que la creación del Centro de IoT puede tardar unos minutos, ya que se necesita tiempo para que se produzca la implementación back-end en los servidores de ubicación adecuados.

## Cambio de la configuración del Centro de IoT

Puede cambiar la configuración de un Centro de IoT después de crearlo desde la hoja Centro de IoT.

![][8]

**Directivas de acceso compartido**: estas directivas definen los permisos para que los dispositivos y servicios se conecten al Centro de IoT. Puede acceder a estas directivas haciendo clic en **Directivas de acceso compartido** en **General**. En esta hoja puede modificar las directivas existentes o agregar una nueva.

### Creación de una nueva directiva

- Haga clic en **Agregar** para abrir una hoja en la que puede escribir el nombre de la nueva directiva y los permisos que quiere asociar a esta directiva, tal como se muestra en la siguiente ilustración.

	Hay varios permisos que se pueden asociar a estas directivas compartidas. Las dos primeras directivas, **Lectura del Registro** y **Escritura del Registro**, conceden derechos de acceso de lectura y escritura para el almacén de identidades de dispositivo o el registro de identidades. Tenga en cuenta que, si selecciona la opción de escritura, se elegirá automáticamente también la opción de lectura.

 	La directiva **Conexión del servicio** concede permiso para tener acceso a los puntos de conexión en la nube, como el grupo de consumidores de servicios que se conectan al Centro de IoT. La directiva **Conexión del dispositivo** concede permisos para enviar y recibir mensajes en los puntos de conexión del dispositivo del Centro de IoT.

- Haga clic en **Crear** para agregar la directiva recién creada a la lista existente.

![][10]

## Mensajería

Haga clic en **Mensajería** para mostrar una lista de propiedades de mensajería para el Centro de IoT que se está modificando. Hay dos tipos principales de propiedades que puede modificar o copiar: **De nube a dispositivo** y **De dispositivo a nube**.

- **Configuración de la nube al dispositivo**: esta configuración tiene dos opciones secundarias: **TTL de nube a dispositivo** (periodo de vida) y **Tiempo de retención** para los mensajes. Cuando se crea el Centro de IoT por primera vez, estas dos opciones se crean con un valor predeterminado de una hora. Para ajustar los valores, use los controles deslizantes o escriba los valores.

- **Configuración de dispositivo a nube**: esta configuración tiene varias opciones secundarias, algunas de las cuales se denominan/asignan al crear el Centro de IoT y solo se pueden copiar en otras opciones secundarias personalizables. Esta configuración se muestra en la siguiente sección.

**Particiones**: este valor se establece cuando se crea el Centro de IoT y se puede cambiar en esta configuración.

**Punto de conexión y nombre compatible del Centro de eventos**: cuando se crea el Centro de IoT, se crea internamente un Centro de eventos al que el usuario podría necesitar acceder en determinadas circunstancias. Este nombre y extremo del Centro de eventos no se puede personalizar pero está disponible para su uso mediante el botón **Copiar**.

**Tiempo de retención**: establecido en un día de forma predeterminada, pero se puede personalizar con otros valores en la lista desplegable. Tenga en cuenta que este valor es en días para Configuración de dispositivo a nube y no en horas, como en el caso de Configuración de nube a dispositivo.

**Grupos de consumidores**: Grupos de consumidores es una configuración similar a otros sistemas de mensajería que se puede usar para extraer datos de modos específicos a fin de conectar otras aplicaciones o servicios a un Centro de IoT. Cada Centro de IoT se crea con un grupo de consumidores predeterminado. Sin embargo, puede agregar o eliminar grupos de consumidores en los Centros de IoT.

> [AZURE.NOTE] El grupo de consumidores predeterminado no se puede modificar ni eliminar.

![][11]

## Carga de archivos

Para utilizar la funcionalidad de carga de archivos en el Centro de IoT, primero debe asociar una cuenta de almacenamiento de Azure con su centro. Seleccione la configuración de **carga de archivos** para mostrar una lista de propiedades de carga de archivos para el Centro de IoT que se modifica.

**Cuenta de almacenamiento:** Use el portal para seleccionar un contenedor de blobs en una cuenta de almacenamiento de su suscripción actual con el fin de asociarlo a su Centro de IoT. Si es necesario, puede crear una nueva cuenta de almacenamiento en la hoja **Cuentas de almacenamiento**, y el nuevo contenedor de blobs en la hoja **Contenedores**. El Centro de IoT genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos.

![][14]

**Receive notifications for uploaded files** (Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos mediante el botón de alternancia.

**SAS TTL** (TTL SAS): este valor es el periodo de vida de los URI de SAS que el Centro de IoT devuelve al dispositivo. Se establece en una hora de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

**File notification settings default TTL** (TTL predeterminado de configuración de notificación de archivos): el periodo de vida de una notificación de carga de archivos antes de que caduque. Se establece en un día de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

**File notification maximum delivery count** (Número máximo de entregas de notificaciones de archivo): el número de veces que el Centro de IoT tratará de entregar una notificación de carga de archivos. Se establece en 10 días de forma predeterminada, pero se puede personalizar con otros valores mediante el control deslizante.

![][13]

## Precios y escala

El precio de un Centro de IoT existente se puede cambiar mediante la configuración **Precios**, con las siguientes excepciones:

- En la implementación actual, un Centro de IoT con una SKU gratuita no puede cambiar de nivel a una de las SKU de pago o viceversa.
- Solo puede haber un único Centro de IoT de nivel gratuito en la suscripción.

![][12]

Ir de un nivel alto (S2 o S3) a un nivel bajo (S1 o S2) solo está permitido cuando el número de mensajes enviados durante ese día no está en conflicto. Por ejemplo, si el número de mensajes por día supera la cifra de 400 000, puede cambiar el nivel para Centro de IoT, pero si cambia al nivel de S1, el centro estará limitado durante ese día.

## Eliminación del Centro de IoT

Puede examinar el Centro de IoT que desea eliminar haciendo clic en **Examinar** y, a continuación, seleccionar el centro adecuado que desea eliminar. Haga clic en el botón **Eliminar** debajo del nombre del centro para eliminar el centro.

## Pasos siguientes

Siga estos vínculos para más información sobre la administración del Centro de IoT de Azure:

- [Administración de identidades de dispositivos de Centro de IoT de forma masiva][lnk-bulk]
- [Métricas de uso][lnk-metrics]
- [Supervisión de operaciones][lnk-monitor]
- [Configuración y administración del acceso al Centro de IoT][lnk-itpro]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Diseño de la solución][lnk-design]
- [Guía del desarrollador][lnk-devguide]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Protección total de la solución de IoT][lnk-securing]


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png
  [13]: ./media/iot-hub-manage-through-portal/file-upload-settings.png
  [14]: ./media/iot-hub-manage-through-portal/file-upload-container-selection.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0817_2016-->