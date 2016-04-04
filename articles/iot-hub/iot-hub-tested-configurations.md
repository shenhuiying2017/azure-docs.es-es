<properties
	pageTitle="Compatibilidad de plataformas de sistema operativo | Microsoft Azure"
	description="Resume la compatibilidad del SDK de dispositivo IoT con plataformas de sistema operativo."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# Compatibilidad de plataformas de sistema operativo con SDK de dispositivos

En este documento se describe la compatibilidad del SDK con diferentes plataformas de sistema operativo. Si no está seguro del dispositivo que debe usar, consulte la sección de compatibilidad de [bibliotecas y plataformas de sistema operativo](#os-platforms) de este artículo.

## Programa Microsoft Azure Certified for IoT

Si ya tiene un dispositivo, consulte la lista de dispositivos incluidos en el programa [Microsoft Azure Certified for IoT][lnk-certified] para encontrar información de compatibilidad específica del dispositivo. Microsoft Azure Certified para IoT es el programa de socios comerciales que conecta el amplio ecosistema de IoT con Microsoft Azure para que los arquitectos y desarrolladores comprendan los distintos escenarios de compatibilidad. En concreto, proporciona una lista de confianza de combinaciones de sistema operativo y dispositivo que le ayudarán a comenzar a trabajar rápidamente con un proyecto de IoT, tanto si se encuentra en fase piloto o de prueba de concepto.

## Plataformas de sistema operativo

Las bibliotecas de Azure IoT se han probado en las siguientes plataformas de sistema operativo:


|Plataformas de sistema operativo Linux/Unix | Versión|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20 ||
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Plataformas de sistema operativo Windows | Versión|
|:---------------|:------------:|
|Escritorio de Windows| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Otras plataformas | Versión|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## Bibliotecas C

El [SDK de dispositivos de Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) se ha probado en las siguientes configuraciones:

|Plataforma de sistema operativo| Versión|Protocolos|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Fedora Linux| 20 | | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Escritorio de Windows| 10 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Bibliotecas de Node.js

El [SDK de dispositivos de Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) se ha probado en las siguientes configuraciones:


|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |



## Bibliotecas Java

El [SDK de dispositivos de Microsoft Azure IoT para Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) se ha probado en las siguientes configuraciones:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|

El SDK de servicios de Microsoft Azure IoT para Java se ha probado en las siguientes configuraciones:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## CSharp

El [SDK de dispositivos de Microsoft Azure IoT para .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) se ha probado en las siguientes configuraciones:

|Plataforma de sistema operativo| Versión|Protocolos|
|:---------|:----------:|:----------:|
|Escritorio de Windows| 10 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Windows IoT Core|10 | HTTPS |

El código de agente administrado requiere Microsoft .NET Framework 4.5


## Pasos siguientes

- Obtenga más información sobre el programa [Microsoft Azure Certified for IoT][lnk-certified].
- Aprenda más sobre el desarrollo de soluciones con [dispositivos certificados para IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->