---
title: Habilitación de los dispositivos administrados detrás de una puerta de enlace de IoT | Microsoft Docs
description: Tema de referencia donde se utiliza una puerta de enlace de IoT creada mediante el SDK de puerta de enlace junto con dispositivos administrados por el Centro de IoT.
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: cstreet

---
# Habilitación de los dispositivos administrados detrás de una puerta de enlace de IoT
## Aislamiento básico de dispositivos
Las organizaciones utilizan a menudo puertas de enlace de IoT para aumentar la seguridad general de sus soluciones de IoT. Algunos dispositivos necesitan enviar datos a la nube, pero no son capaces de protegerse de las amenazas de Internet. Puede proteger estos dispositivos de subprocesos externos haciendo que se comuniquen con el mundo exterior mediante una puerta de enlace.

La puerta de enlace se sitúa en la frontera entre un entorno seguro y la red Internet abierta. Los dispositivos hablan con la puerta de enlace y la puerta de enlace pasa los mensajes al destino en la nube correcto. La puerta de enlace está protegida frente a subprocesos externos, bloquea las solicitudes no autorizadas, permite el tráfico de entrada autorizado y reenvía dicho tráfico al dispositivo correcto.

![][1]

Otra opción es colocar los dispositivos que se pueden proteger ellos mismos detrás de la puerta de enlace como una capa de seguridad añadida. En este escenario, solo necesitará mantener revisado el SO de la puerta de enlace frente a las vulnerabilidades más recientes, en lugar de actualizar el SO en cada dispositivo.

## Aislamiento más inteligencia
Un enrutador protegido es una puerta de enlace suficiente para aislar dispositivos de forma sencilla. Sin embargo, las soluciones de IoT con frecuencia requieren una puerta de enlace que proporcione inteligencia más aislar simplemente los dispositivos. Por ejemplo, puede que quiera administrar los dispositivos desde la nube. Puede usar LWM2M, un protocolo estándar de administración de dispositivos, en la parte de administración en la nube de la solución. Sin embargo, los dispositivos envían datos de telemetría mediante un protocolo no habilitado para TCP/IP. Además, los dispositivos generan grandes cantidades de datos y solo quiere cargar un subconjunto filtrado de datos de telemetría. Puede crear una solución que incorpore una puerta de enlace de IoT capaz de tratar con dos flujos de datos distintas. La puerta de enlace debe:

* Comprender los datos de **telemetría**, filtrarlos y luego cargarlos en la nube. La puerta de enlace ya no es un simple enrutador que se limita a reenviar datos entre el dispositivo y la nube.
* Intercambie simplemente los **datos de administración de dispositivos de LWM2M** entre los dispositivos y la nube. No es necesario que la puerta de enlace comprenda los datos que llegan, solo tiene que asegurarse de que los datos van y vienen entre los dispositivos y la nube.

En la siguiente ilustración se muestra este escenario:

![][2]

## La solución: administración de dispositivos de IoT de Azure y el SDK de puerta de enlace
La versión preliminar pública de [administración de dispositivos IoT de Azure][lnk-device-management] y al versión beta del [SDK de puerta de enlace de IoT de Azure] hacen posible este escenario. La puerta de enlace administra cada flujo de datos de la manera siguiente:

* **Telemetría**: puede usar el SDK de puerta de enlace para crear una canalización que comprenda, filtre y envíe los datos de telemetría a la nube. El SDK de puerta de enlace proporciona código que implementa partes de esta canalización en nombre del desarrollador. Puede encontrar más información sobre la arquitectura del SDK en el tutorial [SDK de puerta de enlace de IoT: introducción][lnk-gateway-get-started].
* **Administración de dispositivos**: la administración de dispositivos de Azure ofrece un cliente de LWM2M que se ejecuta en el dispositivo, así como una interfaz de nube para la emisión de comandos de administración al dispositivo.
  
  No se necesita ninguna lógica especial en la puerta de enlace ya no es necesario procesar los datos de LWM2M intercambiados entre el dispositivo y el Centro de IoT. Puede habilitar la conexión compartida a Internet, una característica de muchos sistemas operativos modernos, en la puerta de enlace para permitir el intercambio de datos de LWM2M. En este escenario puede elegir un sistema operativo adecuado, ya que el SDK de puerta de enlace admite diversos sistemas operativos. Estas son las instrucciones para habilitar la conexión compartida a Internet en [Windows 10] y [Ubuntu], dos de las muchos sistemas operativos admitidos.

La siguiente ilustración muestra la arquitectura de alto nivel usada para permitir este escenario mediante [administración de dispositivos IoT de Azure][lnk-device-management] y el [SDK de puerta de enlace de IoT de Azure].

![][3]

## Pasos siguientes
Para más información sobre cómo usar el SDK de puerta de enlace, consulte los siguientes tutoriales:

* [SDK de puerta de enlace de IoT: introducción mediante Linux][lnk-gateway-get-started]
* [SDK de puerta de enlace: envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway-simulated].

Para aprender a usar las funciones de administración de dispositivos, consulte [Introducción a la biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure][lnk-library-c].

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Diseño de la solución][lnk-design]
* [Guía del desarrollador][lnk-devguide]
* [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
* [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[SDK de puerta de enlace de IoT de Azure]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/es-ES/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->