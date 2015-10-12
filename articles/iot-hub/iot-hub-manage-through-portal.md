<properties
 pageTitle="Administración de Centros de IoT a través del Portal de Azure | Microsoft Azure"
 description="Información general sobre cómo crear y administrar los Centros de IoT de Azure a través del Portal de Azure"
 services="iot-hub"
 documentationCenter=".net"
 authors="nasing"
 manager="timlt"
 editor=""/>

<tags
 ms.service="azure-iot"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="nasing"/>

# Administración de Centros de IoT a través del Portal de Azure

## Introducción:

En este tema se describe cómo empezar a trabajar con Centros de IoT a través del Portal de Azure, cómo buscar Centros de IoT, y cómo crearlos y administrarlos.

## Búsqueda de Centros de IoT:

Se pueden encontrar Centros de IoT en varios sitios.

1. **+ Nuevo**: el Centro de IoT de Azure es un servicio de IoT, que se puede encontrar en la categoría "Internet de las cosas" en **+ Nuevo**, al igual que otros servicios.

2. También se puede acceder a los Centros de IoT mediante Marketplace como servicio destacado en **Internet de las cosas**.

## Creación de un Centro de IoT

Se puede crear un Centro de IoT Azure siguiendo los métodos enumerados en la sección anterior.

1. Al crear un Centro de IoT mediante la opción **+ Nuevo**, aparece la hoja final que se muestra a continuación. Los pasos para crear el Centro de IoT tanto con este método como con Marketplace son idénticos.

2. Para crear un Centro de IoT mediante Marketplace: al hacer clic en **Crear**, se abre una hoja igual a la hoja anterior de la experiencia **+ Nuevo**. Para crear un Centro de IoT, se deben seguir varios pasos que se enumeran en las secciones siguientes:

### Elección del nombre del Centro de IoT

Para crear un Centro de IoT, el usuario debe asignar un nombre al centro. Tenga en cuenta que este nombre debe ser único entre los centros. La duplicación de centros no está permitida en el back-end, por lo que se recomienda asignar a este centro un nombre lo más único posible.

### Elección del plan de tarifa 

El cliente puede elegir entre tres planes: **Gratos**, **Estándar 1 ** y **Estándar 2**. El plan gratis permite solo la conexión de 10 dispositivos con el Centro de IoT.

**S1 (baja frecuencia)**: la edición S1 (baja frecuencia) de Centros de IoT está diseñada para las soluciones de IoT que tienen un gran número de dispositivos que generan cantidades de datos relativamente pequeñas por cada dispositivo. Cada unidad de la edición S1 (baja frecuencia) permite la conectividad de hasta 500 dispositivos o hasta 50.000 mensajes por día en todos los dispositivos conectados.

**S2 (alta frecuencia)**: la edición S2 (alta frecuencia) de Centros de IoT está diseñada para las soluciones de IoT en las que los dispositivos generan grandes cantidades de datos. Cada unidad de la edición S2 (alta frecuencia) permite la conectividad de hasta 500 dispositivos o hasta 1,5 millones de mensajes por día en todos los dispositivos conectados.
 
![][4]

> [AZURE.NOTE]El Centro de IoT solo permite un único centro gratuito por cada suscripción.

### Unidades del Centro de IoT 

Una unidad de IoT incluye 500 dispositivos, por lo que la elección del número de unidades de IoT significa que el número total de los dispositivos compatibles para este centro es el número de unidades multiplicado por 500. Por ejemplo, si desea que el Centro de IoT admita 1000 dispositivos, seleccione dos unidades.

### Dispositivo para particiones en la nube y grupo de recursos 

Puede cambiar el número de particiones para un Centro de IoT. Las particiones predeterminadas están establecidas en cuatro; sin embargo, puede elegir un número diferente de particiones en una lista desplegable.

Para los grupos de recursos, no es necesario crear explícitamente un grupo de recursos vacío. Al crear un nuevo recurso, puede elegir entre crear un nuevo grupo de recursos o usar un grupo de recursos existente.

![][5]

### Selección de suscripciones 

El Centro de IoT de Azure muestra automáticamente la lista de suscripciones al que está vinculada la cuenta de usuario. Puede elegir una de estas opciones para asociar el Centro de IoT a esa suscripción.

### Selección de la ubicación

La opción de ubicación proporciona una lista de las regiones en las que se ofrece Centro de IoT. Para la versión preliminar pública, el centro se ofrece solo en tres ubicaciones: Este de EE. UU., Norte de Europa y Este de Asia.

### Creación del Centro de IoT

Una vez completados todos los pasos enumerados anteriormente, el Centro de IoT ya está listo para crearse. Al hacer clic en **Crear**, se inicia el proceso back-end de creación de este Centro de IoT con las opciones específicas y se implementa en la ubicación especificada.

Tenga en cuenta que la creación del Centro de IoT puede tardar unos minutos, ya que se necesita tiempo para que la implementación back-end se produzca en los servidores de ubicación adecuados.

## Cambio de la configuración del Centro de IoT

Puede cambiar la configuración de un Centro de IoT existente después de crearlo. Al hacer clic y seleccionar el Centro de IoT, se abrirá la página de configuración.

![][8]

**Directivas de acceso compartido**: se trata de las directivas que definen los permisos para que los dispositivos y servicios se conecten al Centro de IoT. Puede acceder a estas directivas haciendo clic en **Directivas de acceso compartido** de **Configuración**. En esta hoja puede modificar las directivas existentes o agregar una nueva.

### Creación de una nueva directiva
 
- Haga clic en el botón **Agregar** para abrir una hoja en la que puede escribir el nombre de la nueva directiva y los permisos que desea asociar a esta directiva, tal como se muestra en la ilustración siguiente.
 
	Hay una serie de permisos que se pueden asociar a estas directivas compartidas. Las dos primeras directivas, **Lectura del Registro** y **Escritura del Registro**, son para la concesión de derechos de acceso de lectura y escritura para el almacén de identidades de dispositivo o el registro de identidades. Tenga en cuenta que, si selecciona la opción de escritura, se elegirá automáticamente la opción de lectura.

 	La directiva de conexión de servicios concede permiso al grupo de consumidores para servicios que se conectan al Centro de IoT, mientras que la conexión de dispositivos concede permisos para los dispositivos del Centro de IoT.
     
- Haga clic en la directiva de creación para agregar la directiva recién creada a la lista existente.

![][10]

## Mensajería 

Haga clic en las directivas de **Mensajería** a fin de mostrar una lista de propiedades de mensajería para el Centro de IoT que se está modificando. Hay dos tipos principales de propiedades que se pueden modificar o copiar: **Configuración de nube a dispositivo** y **Configuración de dispositivo a nube**.

- **Configuración de nube a dispositivo**: tiene dos opciones secundarias: **TTL de nube a dispositivo** (período de vida) y **Tiempo de retención** para los mensajes. Cuando se crea el Centro de IoT por primera vez, estas dos opciones se crean con un valor predeterminado de 1 hora. Sin embargo, puede personalizarlas mediante los controles deslizantes o escribiendo simplemente los valores.
 
- **Configuración de dispositivo a nube**: tiene varias opciones secundarias, algunas de las cuales se denominan/asignan al crear el Centro de IoT y solo se pueden copiar en otras opciones secundarias que son personalizables. Todas ellas se muestran en la siguiente sección.

**Particiones**: este valor se establece cuando se crea el Centro de IoT y se puede cambiar en esta configuración.

**Nombre compatible y extremo del Centro de eventos**: cuando se crea el Centro de IoT, se crea internamente un Centro de eventos al que el usuario puede necesitar acceder en determinadas circunstancias. Este nombre y extremo del Centro de eventos no se puede personalizar pero está disponible para su uso mediante el botón **Copiar**.

**Tiempo de retención**: establecido en 1 día de forma predeterminada pero se puede personalizar con otros valores en la lista desplegable. Tenga en cuenta que este valor es en días para Configuración de dispositivo a nube y no en horas, como en el caso de Configuración de nube a dispositivo.

**Grupos de consumidores**: Grupos de consumidores es una configuración similar a otros sistemas de mensajería que se puede usar para extraer datos de modos específicos a fin de conectar otras aplicaciones o servicios a un Centro de IoT. Cada Centro de IoT se crea con un grupo de consumidores predeterminado. Sin embargo, puede agregar o eliminar grupos de consumidores en los Centros de IoT.

> [AZURE.NOTE]El grupo de consumidores predeterminado no se puede modificar ni eliminar.

![][11]

## Precios y escala

El precio de un Centro de IoT existente se puede cambiar mediante la configuración **Precios**, con las siguientes excepciones:

- En la implementación actual, un Centro de IoT con una SKU gratuita no puede cambiar de nivel a una de las SKU de pago o viceversa.
- Solo puede haber un único Centro de IoT de nivel gratuito en la suscripción.

![][12]

Ir de un nivel alto (S2) a un nivel bajo (S1) solo está permitido cuando el número de mensajes enviados durante ese día no está en conflicto. Por ejemplo, si el número de mensajes por día supera los 50.000, no se puede cambiar el nivel para el Centro de IoT de S2 a S1.

## Eliminación del Centro de IoT

Puede examinar el Centro de IoT que desea eliminar haciendo clic en **Examinar** y, a continuación, seleccionar el centro adecuado que desea eliminar. Haga clic en el botón **Eliminar** debajo del nombre del centro para eliminar el centro.

  
  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png

<!---HONumber=Oct15_HO1-->