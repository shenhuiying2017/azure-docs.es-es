---
title: "Introducción a la solución Device Simulation: Azure | Microsoft Docs"
description: "La solución de simulación de IoT Suite es una herramienta que se puede usar para ayudar en el desarrollo y las pruebas de una solución de IoT. El servicio de simulación es una oferta independiente que se puede usar junto con otras soluciones preconfiguradas o utilizar con sus propias soluciones personalizadas."
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 25b9d1c7debe0d98a87b7d0b47dea6ab6f36c8af
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="device-simulation-walkthrough"></a>Tutorial sobre Device Simulation

Azure IoT Device Simulation es una herramienta que se puede usar para ayudar en el desarrollo y las pruebas de una solución de IoT. Device Simulation constituye una oferta independiente que se puede usar junto con otras soluciones preconfiguradas o utilizar con sus propias soluciones personalizadas.

Este tutorial le explica algunas de las características de Device Simulation. Le muestra cómo funciona y le permite usarla para probar sus propias soluciones de IoT.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Configurar una simulación
> * Iniciar y detener una simulación
> * Ver métricas de telemetría

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesitará una instancia implementada de Azure IoT Device Simulation en la suscripción de Azure.

Si aún no la ha implementado, debe realizar el tutorial [Deploy Azure IoT Device Simulation](iot-suite-device-simulation-explore.md) (Implementación de Azure IoT Device Simulation).

## <a name="configuring-device-simulation"></a>Configuración de Device Simulation

Puede configurar y ejecutar Device Simulation completamente desde dentro del panel. Abra el panel desde la página [Soluciones aprovisionadas](https://www.azureiotsuite.com/) de IoT Suite. Haga clic en **Iniciar** en la nueva implementación de Device Simulation.

### <a name="target-iot-hub"></a>IoT Hub de destino

Puede utilizar Device Simulation con una instancia de IoT Hub aprovisionada previamente o con cualquier otra instancia de IoT Hub:

![IoT Hub de destino](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> La opción de usar una instancia de IoT Hub aprovisionada previamente solo está disponible si eligió crear una nueva instancia cuando implementó Device Simulation. Si no dispone de ninguna instancia de IoT, siempre puede crear una nueva desde [Azure Portal](https://portal.azure.com).

Para seleccionar como destino una instancia de IoT Hub concreta, proporcione la cadena de conexión **iothubowner**. Puede obtener la cadena de conexión de [Azure Portal](https://portal.azure.com):

1. En la página de configuración de IoT Hub en Azure Portal, haga clic en **Directivas de acceso compartido**.
1. Haga clic en **iothubowner**.
1. Copie la clave principal o la secundaria.
1. Pegue este valor en el cuadro de texto IoT Hub de destino.

![IoT Hub de destino](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Modelo de dispositivo

El modelo de dispositivo le permite elegir el tipo de dispositivo que va a simular. Puede elegir uno de los modelos de dispositivo preconfigurados o definir una lista de sensores para un modelo de dispositivo personalizado:

![Modelo de dispositivo](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Modelos de dispositivo preconfigurados

Device Simulation proporciona tres modelos de dispositivo preconfigurados. Hay modelos de dispositivo disponibles para refrigeradores, ascensores y camiones.

Los modelos de dispositivo preconfigurados incluyen varios sensores con una frecuencia de telemetría predeterminada. No puede personalizar la frecuencia de telemetría para estos dispositivos.

En la tabla siguiente se muestra una lista de las configuraciones para cada modelo de dispositivo preconfigurado:

| Modelo de dispositivo | Sensor | Unidad | Frecuencia de telemetría
| -------------| ------ | -----| --------------------|
| Refrigerador | humedad | % | 5 segundos |
| | pressure | psig | 5 segundos |
| | temperatura | F | 5 segundos |
| Ascensor | Planta | | 5 segundos |
| | Vibración | MM | 5 segundos |
| | Temperatura | F | 5 segundos |
| Camión | Latitud | | 3 segundos |
| | Longitud | | 3 segundos |
| | velocidad | km/h | 5 segundos |
| | cargotemperature | F | 5 segundos |

#### <a name="custom-device-model"></a>Modelo de dispositivo personalizado

Los modelos de dispositivo personalizados le permiten modelar los sensores que mejor representan a sus propios dispositivos. Un dispositivo personalizado puede tener hasta 10 sensores personalizados.

Cuando selecciona el tipo de modelo del dispositivo personalizado puede agregar nuevos sensores haciendo clic en **+ Agregar sensor**.

![Agregar sensores](media/iot-suite-device-simulation-explore/customsensors.png)

Los sensores personalizados tienen las siguientes propiedades:

| Campo | DESCRIPCIÓN |
| ----- | ----------- |
| Nombre del sensor | Un nombre descriptivo para el sensor, como **temperatura** o **velocidad**. |
| Comportamiento | Los comportamientos permiten a los datos de telemetría variar de un mensaje al siguiente para simular datos reales. **Incremento** aumenta el valor en uno en cada mensaje enviado empezando por el valor mínimo. Una vez que se alcanza el valor máximo, se empieza de nuevo por el valor mínimo. **Decremento** se comporta de la misma manera que **Incremento** pero cuenta hacia atrás. El comportamiento **Aleatorio** genera un valor aleatorio entre los valores mínimos y máximos. |
| Valor mínimo | El número más bajo dentro del intervalo aceptable. |
| Valor máximo | El número más alto dentro del intervalo aceptable. |
| Unidad | La unidad de medida del sensor, como °F o km/h. |

### <a name="number-of-devices"></a>Número de dispositivos

En la actualidad, Device Simulation le permite simular hasta 1000 dispositivos.

![Número de dispositivos](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Frecuencia de telemetría

La frecuencia de telemetría le permite especificar la frecuencia con la que los dispositivos simulados deben enviar datos a IoT Hub. Puede enviar datos con una frecuencia de tan solo 10 segundos o con una de 99 horas 59 minutos y 59 segundos.

![Frecuencia de telemetría](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> Si va a usar una instancia de IoT Hub diferente de la instancia aprovisionada previamente, debe tener en cuenta los límites de mensaje para el IoT Hub de destino. Por ejemplo, si tiene 1000 dispositivos simulados que envían datos de telemetría cada 10 segundos a un centro S1, puede alcanzar el límite de telemetría del centro en tan solo una hora.

### <a name="simulation-duration"></a>Duración de la simulación

Puede elegir ejecutar la simulación durante un período de tiempo específico o ejecutarla hasta que la detenga de forma explícita. Si elige un período de tiempo específico, puede elegir cualquier duración desde 10 minutos hasta 99 horas, 59 minutos y 59 segundos.

![Duración de la simulación](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Inicio y detención de la simulación

Cuando haya agregado todos los datos de configuración necesarios en el formulario, se habilitará el botón **Iniciar simulación**. Para iniciar la simulación, haga clic en este botón.

![Iniciar simulación](media/iot-suite-device-simulation-explore/start.png)

Si especificó una duración concreta para la simulación, esta se detendrá automáticamente cuando se haya alcanzado el tiempo. Siempre puede detener la simulación antes haciendo clic en **Detener simulación.**

Si decide ejecutar la simulación indefinidamente, entonces se ejecutará hasta que haga clic en **Detener simulación**. Puede cerrar el explorador y volver a la página de Device Simulation para detener la simulación en cualquier momento.

![Detener simulación](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> Solo se puede ejecutar una simulación cada vez. Debe detener la simulación que esté en ejecución en ese momento antes de iniciar una nueva.
