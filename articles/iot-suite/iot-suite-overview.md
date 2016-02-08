<properties
	pageTitle="Información general de Microsoft Azure IoT Suite | Microsoft Azure"
	description="Esto proporciona una visión general del Conjunto de aplicaciones de IoT de Azure incluidos el empaquetado y las soluciones preconfiguradas."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Información general de Azure IoT Suite

Los servicios de IoT de Azure ofrecen una amplia gama de capacidades. Estos servicios de nivel empresarial le permiten:

- Recopilar datos de dispositivos
- Analizar flujos de datos en movimiento
- Almacenar y consultar grandes conjuntos de datos
- Visualizar datos tanto históricos como en tiempo real
- Integración con sistemas del área de operaciones

Los paquetes del Conjunto de aplicaciones de IoT de Azure aprovechan de manera conjunta y común los servicios de Azure con las extensiones personalizadas como soluciones preconfiguradas. Estas soluciones preconfiguradas son implementaciones base de patrones comunes de soluciones de IoT que le ayudan a reducir el tiempo que dedica a entregar sus soluciones de IoT. Mediante los [kits de desarrollo de software de IoT][lnk-sdks], puede personalizar con facilidad estas soluciones preconfiguradas para satisfacer sus propios requisitos o aprovecharlos como ejemplos al desarrollar nuevas soluciones.

El vídeo siguiente proporciona una introducción al conjunto de aplicaciones de IoT de Azure:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Servicios IoT de Azure en Azure IoT Suite

El componente esencial del conjunto de aplicaciones de IoT de Azure es el servicio [Centro de IoT de Azure][lnk-iot-hub]. Este servicio ofrece las capacidades de mensajería de dispositivo a la nube y de la nube al dispositivo y actúa como la puerta de enlace para la nube y los demás servicios del Conjunto de aplicaciones de IoT clave.

El [Análisis de transmisiones de Azure][lnk-asa] ofrece análisis de datos en movimiento. El Conjunto de aplicaciones de IoT aprovecha este servicio para procesar la telemetría entrante, realizar la agregación y detectar eventos. Las soluciones preconfiguradas también usan el análisis de transmisiones para procesar los mensajes informativos que contienen datos como los metadatos y las respuestas de comandos de dispositivos.

El [Almacenamiento de Azure][lnk-azure-storage] y [Azure DocumentDB][lnk-document-db] ofrecen las capacidades de almacenamiento de datos. Las soluciones preconfiguradas usan el almacenamiento de blobs para almacenar la telemetría y que esté disponible para análisis. Las soluciones preconfiguradas usan el almacenamiento indizado en la capacidad de datos semiestructurados de DocumentDB para administrar los metadatos del dispositivo. Esto permite que las soluciones administren dispositivos heterogéneos que tienen requisitos de almacenamiento de contenido diferentes.

[Aplicaciones web de Azure][lnk-web-apps] y [Microsoft Power BI][lnk-power-bi] proporcionan capacidades de visualización de datos. La flexibilidad de Power BI le permite compilar rápidamente sus propios paneles interactivos que usan los datos del conjunto de aplicaciones de IoT.

Para información general de la arquitectura de una solución de IoT típica, vea [Microsoft Azure e Internet de las cosas (IoT)][iot-suite-what-is-azure-iot].

## Soluciones preconfiguradas

El Conjunto de aplicaciones de IoT de Azure incluye soluciones preconfiguradas que le permiten empezar a trabajar rápidamente con los escenarios comunes de IoT que el Conjunto de aplicaciones de IoT de Azure hace posibles y explorarlos. Puede implementar las soluciones preconfiguradas en su suscripción de Azure y luego ejecutar una solución de IoT completa e integral.

## Pasos siguientes

Para más información sobre las soluciones preconfiguradas del Conjunto de aplicaciones de IoT, vea [¿Cuáles son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?][lnk-what-are-preconfig]

Para empezar a usar una de las soluciones preconfiguradas, vea [Introducción a las soluciones preconfiguradas de IoT][lnk-preconfig-start].

Para más información sobre el servicio Centro de IoT de Azure, vea la [Documentación del Centro de IoT][lnk-iot-hub].


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0128_2016-->