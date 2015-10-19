<properties
	pageTitle="Información general de Microsoft Azure IoT Suite | Microsoft Azure"
	description="Le proporcionará una visión general de Azure IoT Suite."
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Información general de Azure IoT Suite

Microsoft ofrece una amplia gama de capacidades con los servicios IoT de Azure. Estos servicios proporcionan capacidades de nivel empresarial para recopilar datos de dispositivos, analizar transmisiones de datos en movimiento, almacenar y consultar grandes conjuntos de datos, visualizar datos históricos y en tiempo real e integrarse con los sistemas back-end importantes. Azure IoT Suite empaqueta un conjunto de servicios potenciados entre sí y capacidades ampliadas para reducir el tiempo de amortización de la inversión para los clientes. Estas extensiones incluyen soluciones preconfiguradas que ofrecen una implementación base de los patrones de soluciones más comunes. En combinación con los kits de desarrollo de software (SDK) de IoT, los clientes pueden fácilmente personalizar las soluciones preconfiguradas o aprovecharlas como ejemplos para el desarrollo de nuevas soluciones.

## Servicios IoT de Azure en Azure IoT Suite

El componente esencial del conjunto de aplicaciones de IoT de Azure es el servicio Centro de IoT. Este servicio proporciona mensajería de dispositivo a nube y de nube a servicio. Actúa como puerta de enlace entre la nube y los otros servicios principales del conjunto de aplicaciones de IoT.

El Análisis de transmisiones de Azure proporciona análisis de datos en movimiento. El conjunto de aplicaciones de IoT aprovecha este servicio para procesar los mensajes de telemetría entrantes, realizar agregaciones y detectar eventos. También se usa para procesar los mensajes informativos que se pueden emplear para las respuesta de comando o los metadatos del dispositivo.

Las funcionalidades de almacenamiento de datos se habilitan mediante una combinación de Almacenamiento de Azure y Azure DocumentDB. El Almacenamiento de Azure permite el almacenamiento de blobs de telemetría para su conservación y análisis posterior. DocumentDB se usa por su funcionalidad de almacenamiento indexado en datos semiestructurados para administrar los metadatos de dispositivo. Esto hace posible la administración de dispositivos heterogéneos al permitir que dispositivos diferentes tengan contenido diferente.

La visualización de los datos se proporciona mediante una combinación de los Sitios web de Azure y Microsoft Power BI. La flexibilidad de Power BI permite a los clientes crear rápidamente sus propios paneles interactivos a partir de datos del conjunto de aplicaciones de IoT.

Encontrará más detalles sobre la arquitectura y cómo se usan estos servicios en el artículo [Microsoft Azure e Internet de las cosas (IoT)](iot-suite-what-is-azure-iot.md).

## Soluciones preconfiguradas

Las soluciones preconfiguradas están incluidas en el conjunto de aplicaciones de IoT de Azure para permitir a los clientes ponerse en marcha rápidamente y explorar escenarios que son posibles con este conjunto de aplicaciones.

La primera solución preconfigurada disponible es [supervisión remota](iot-suite-what-are-preconfigured-solutions.md).

<!---HONumber=Oct15_HO2-->