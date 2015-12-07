<properties
	pageTitle="Compatibilidad de hardware y de plataformas de sistema operativo | Microsoft Azure"
	description="Resume la compatibilidad del SDK de dispositivo de IoT con plataformas de sistema operativo y hardware de dispositivo."
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
     ms.date="10/09/2015"
     ms.author="hegate"/>

# Compatibilidad de hardware y de plataformas de sistema operativo con SDK de dispositivos

Este documento describe la compatibilidad del SDK con distintas plataformas de sistema operativo, así como las configuraciones de dispositivos específicos incluidas en el [programa Microsoft Azure Certified para IoT](#microsoft-azure-certified-for-iot). Si ya tiene un dispositivo, consulte la lista de dispositivos incluidos en el programa para encontrar información de compatibilidad específica del dispositivo. Si no está seguro del dispositivo que debe usar, eche un vistazo a las sección de compatibilidad de [bibliotecas y plataformas de sistema operativo](#os-platforms).


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
|Escritorio de Windows| 7,8,10 |
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
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | | HTTPS, AMQP, MQTT |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Escritorio de Windows| 7,8,10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Bibliotecas de Node.js

El [SDK de dispositivos de Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) se ha probado en las siguientes configuraciones:


|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Bibliotecas Java

El [SDK de dispositivos de Microsoft Azure IoT para Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) se ha probado en las siguientes configuraciones:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

El SDK de servicios de Microsoft Azure IoT para Java se ha probado en las siguientes configuraciones:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp

El [SDK de dispositivos de Microsoft Azure IoT para .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) se ha probado en las siguientes configuraciones:

|Plataforma de sistema operativo| Versión|Protocolos|
|:---------|:----------:|:----------:|
|Escritorio de Windows| 7,8,10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

El código de agente administrado requiere Microsoft .NET Framework 4.5


## Microsoft Azure Certified para IoT

**Microsoft Azure Certified para IoT** es el programa de socios comerciales que conecta el amplio ecosistema de IoT con Microsoft Azure para que los arquitectos y desarrolladores comprendan los distintos escenarios de compatibilidad. Específicamente, proporciona una lista de confianza de combinaciones de sistema operativo/dispositivo que le ayudarán a comenzar a trabajar rápidamente con un proyecto de IoT, tanto si se encuentra en fase piloto o de prueba de concepto. Con las combinaciones certificadas de dispositivo y sistemas operativos, podrá comenzar con su proyecto de IoT, con menos trabajo y con la personalización necesaria para asegurarse de que los dispositivos son compatibles con el conjunto de IoT de Azure y el centro de IoT de Azure.

## Certificación para dispositivos de IoT

La **certificación para dispositivos de IoT** ha probado la compatibilidad con el SDK de IoT de Azure y está lista para usarse en su aplicación de IoT. En concreto, identificamos la compatibilidad según la plataforma de sistema operativo y el lenguaje de código.

#### Lista de dispositivos

Se ha certificado el funcionamiento de cada dispositivo con nuestro SDK en el sistema operativo y el lenguaje elegido por el fabricante del dispositivo. Por ejemplo, BeagleBone Black funciona en Debian con nuestro lenguaje C, Javascript y Java. Esto significa que los desarrolladores pueden crear aplicaciones en cualquiera de las combinaciones de lenguajes sistemas operativos en los dispositivos específicos.

|Dispositivo| Sistema operativo probado |Lenguaje|
|:---------|:----------|:----------|
|Raspberry Pi 2| Raspbian | C, Javascript, Java |
|Raspberry Pi 2| Windows 10 IoT Core| C, Javascript, C#|
|BeagleBone Black| Debian |C, Javascript, Java|
|BeagleBone Green|Debian |C, Javascript, Java|
|TI CC3200 | TI-RTOS 2.x|C|
|Intel Edison |Yocto |C, Javascript|
|Minnowboard Max |Windows 7,8, 10 |C#|
|Arrow DragonBoard 410c |Windows 10 IoT Core | C#|
|Freescale FRDM K64 |mbed 2.0 | C|

## Pasos siguientes

Encontrará más información sobre el desarrollo de soluciones con dispositivos **certificados para IoT** [aquí](http://azure.com/iotdev).

<!---HONumber=AcomDC_1125_2015-->