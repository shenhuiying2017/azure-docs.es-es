<properties
 pageTitle="Escalado del Centro de IoT de Azure | Microsoft Azure"
 description="Describe cómo escalar el Centro de IoT de Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Escalado del Centro de IoT

El Centro de IoT puede admitir hasta un millón de dispositivos conectados al mismo tiempo; para ello, se aumenta el número de S1 o S2 del Centro de IoT a 2000. Para obtener más información, consulte [Centro de IoT Precios][lnk-pricing].

Cada unidad del Centro de IoT permite un número determinado de identidades de dispositivo en el Registro, todos los cuales se pueden conectar simultáneamente, y un número diario de mensajes.

Para escalar correctamente su solución, debe tener en cuenta el uso particular que haga del Centro de IoT. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

Además de esta información sobre la capacidad de procesamiento, recuerde consultar el tema sobre [Cuotas y limitaciones del Centro de IoT][] y diseñar su solución en consecuencia.

## Capacidad de procesamiento para los mensajes de dispositivo a nube y de nube a dispositivo

La mejor forma de dimensionar una solución de Centro de IoT es evaluar el tráfico en cada dispositivo.

Los mensajes de dispositivo a nube siguen estas directrices de capacidad de procesamiento sostenida.

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| ---- | -------------------- | ------------------- |
| S1 | hasta 8 kb/m por dispositivo | promedio de 4 mensajes por hora por dispositivo |
| S2 | hasta 4 kb/m por dispositivo | promedio de 2 mensajes por minuto por dispositivo |

Al recibir los mensajes de dispositivo a nube, la aplicación back-end puede esperar el rendimiento máximo siguiente (a través de todos los lectores).

| Nivel: | Capacidad de procesamiento sostenida |
| ---- | -------------------- |
| S1 | hasta 120 Kb/m por unidad, con un mínimo de 2 Mb/s |
| S2 | hasta 4 Mb/m por unidad, con un mínimo de 2 Mb/s |

El rendimiento de los mensajes de nube a dispositivo escala en cada dispositivo, y cada dispositivo recibe hasta 5 mensajes por minuto.

## Capacidad de procesamiento para las operaciones de registro de identidad

Las operaciones de registro de identidad del Centro de IoT no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos.

Consulte las cifras de rendimiento de ráfaga en [Cuotas y limitaciones del Centro de IoT][].

## Clave de particionamiento

Aunque un único Centro de IoT puede escalarse a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso, se recomienda particionar los dispositivos en varios Centros de IoT, con el fin de suavizar las ráfagas de tráfico y obtener la capacidad de proceso y las velocidades de funcionamiento necesarias.

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción a los Centros de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cuotas y limitaciones del Centro de IoT]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Nov15_HO3-->