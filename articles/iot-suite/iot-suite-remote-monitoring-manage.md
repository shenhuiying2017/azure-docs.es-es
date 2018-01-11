---
title: "Administración de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs"
description: "En este tutorial se muestra cómo administrar los dispositivos conectados a la solución de supervisión remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: fab3fd4163141aadc06b385f5759c19eece7fd14
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="manage-and-configure-your-devices"></a>Administración y configuración de los dispositivos

En este tutorial se muestran las funcionalidades de administración de dispositivos de la solución de supervisión remota. Para introducir estas funcionalidades, en el tutorial se usa un escenario de la aplicación IoT de Contoso.

Contoso solicitó maquinaria nueva para expandir una de sus instalaciones y aumentar la producción. Mientras espera la entrega de la maquinaria nueva, desea ejecutar una simulación para comprobar el comportamiento de la solución. Como operador, desea administrar y configurar los dispositivos en la solución de supervisión remota.

Para proporcionar una manera extensible de administrar y configurar los dispositivos, la solución de supervisión remota usa características de IoT Hub, como los [trabajos](../iot-hub/iot-hub-devguide-jobs.md) y los [métodos directos](../iot-hub/iot-hub-devguide-direct-methods.md). Para información sobre cómo un desarrollador de dispositivos implementa métodos en un dispositivo físico, consulte [Personalización de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-customize.md).

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Aprovisionar un dispositivo simulado.
> * Probar el dispositivo simulado.
> * Llamar a los métodos de dispositivo desde la solución.
> * Volver a configurar un dispositivo.

## <a name="prerequisites"></a>Requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-deploy.md).

## <a name="add-a-simulated-device"></a>Adición de un dispositivo simulado

Vaya a la página **Dispositivos** en la solución y elija **Nuevo dispositivo**. En el panel **Nuevo dispositivo**, elija **Simulado**:

![Aprovisionamiento de un dispositivo simulado](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

Deje el número de dispositivos que se van a aprovisionar en **1**. Elija **Faulty Engine** (Fallo del motor) como el **Modelo de dispositivo** y, luego, elija **Aplicar** para crear el dispositivo simulado:

![Aprovisionamiento de un dispositivo de motor simulado](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

Para información sobre cómo aprovisionar un dispositivo *físico*, consulte [Conectar el dispositivo a la solución preconfigurada de supervisión remota](iot-suite-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Prueba del dispositivo simulado

Para ver detalles del dispositivo simulado nuevo, selecciónelo en la lista de dispositivos de la página **Dispositivos**. La información sobre el dispositivo se muestra en el panel de **detalles del dispositivo**:

![Vista del nuevo dispositivo de motor simulado](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

En los **detalles del dispositivo**, compruebe que el dispositivo nuevo está enviando la telemetría. Para ver los distintos flujos de telemetría del dispositivo, elija un nombre de telemetría como **vibración**:

![Selección de un flujo de telemetría para observar](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

El panel de **detalles del dispositivo** muestra otra información sobre el dispositivo, como los valores de etiqueta, los métodos que admite y las propiedades que el dispositivo informa.

Para ver los diagnósticos detallados, desplácese hacia abajo para ver **Diagnósticos**.

## <a name="act-on-a-device"></a>Acción en un dispositivo

Para realizar alguna acción en uno o más dispositivos, selecciónelos en la lista de dispositivos y, luego, elija **Programar**. El modelo de dispositivo **Motor** especifica cuatro métodos que debe admitir un dispositivo:

![Métodos de motor](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

Elija **Reiniciar**, establezca el nombre del trabajo en **RestartEngine** y, luego, elija **Aplicar**:

![Programación del método de reinicio](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

Para realizar un seguimiento del estado del trabajo en la página **Mantenimiento**, elija **Trabajos**:

![Supervisión del trabajo de programaciones](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Métodos en otros dispositivos

Cuando explore los distintos tipos de dispositivos simulados, verá que otros tipos de dispositivos admiten métodos distintos. En una implementación con dispositivos físicos, el modelo de dispositivo especifica los métodos que debe admitir el dispositivo. Habitualmente, el desarrollador de dispositivos es responsable de desarrollar el código que hace que el dispositivo actúe en respuesta a una llamada de método.

Para programar un método para ejecutarse en varios dispositivos, puede seleccionar varios dispositivos en la lista de la página **Dispositivos**. El panel **Programar** muestra los tipos de métodos que son comunes a todos los dispositivos seleccionados.

## <a name="reconfigure-a-device"></a>Nueva configuración de un dispositivo

Para cambiar la configuración de un dispositivo, selecciónelo en la lista de dispositivos de la página **Dispositivos** y, luego, elija **Volver a configurar**. El panel de nueva configuración muestra los valores de propiedad del dispositivo seleccionado que puede cambiar:

![Nueva configuración de un dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

Para hacer un cambio, agregue un nombre para el trabajo, actualice los valores de propiedad y elija **Aplicar**:

![Actualización de un valor de propiedad de dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

Para realizar un seguimiento del estado del trabajo en la página **Mantenimiento**, elija **Trabajos**.

## <a name="next-steps"></a>Pasos siguientes

Este tutorial le ha mostrado cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Aprovisionar un dispositivo simulado.
> * Probar el dispositivo simulado.
> * Llamar a los métodos de dispositivo desde la solución.
> * Volver a configurar un dispositivo.

Ahora que aprendió a administrar los dispositivos, los siguientes pasos sugeridos son para aprender a:

* [Solucionar problemas de los dispositivos](iot-suite-remote-monitoring-maintain.md).
* [Probar la solución con dispositivos simulados](iot-suite-remote-monitoring-test.md).
* [Conectar el dispositivo a la solución preconfigurada de supervisión remota](iot-suite-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->