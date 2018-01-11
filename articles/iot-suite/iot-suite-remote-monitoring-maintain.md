---
title: "Solución de problemas de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs"
description: "En este tutorial se muestra cómo solucionar los problemas con los dispositivos en la solución de supervisión remota."
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
ms.openlocfilehash: d26275b6b03115b775990c9efb5d4706fcb829d1
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Solución de problemas de los dispositivos

En este tutorial se muestra cómo usar la página **Mantenimiento** en la solución para solucionar los problemas de los dispositivos. Para introducir estas funcionalidades, en el tutorial se usa un escenario de la aplicación IoT de Contoso.

Contoso está probando un tipo nuevo de **prototipo** en el campo. Como operador de Contoso, durante las pruebas observa que el dispositivo **Prototipo** desencadena inesperadamente una alarma en el panel. Ahora debe investigar el comportamiento de este dispositivo **Prototipo** defectuoso.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Usar la página **Mantenimiento** para investigar la alarma
> * Llamar a un método de dispositivo para solucionar el problema

## <a name="prerequisites"></a>Requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Uso del panel de mantenimiento

En la página **Panel** observará que hay alarmas de temperatura inesperadas que provienen de la regla asociada con los dispositivos **Prototipo**:

![Alarmas que aparecen en el panel](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Para investigar aún más el problema, elija la opción **Explorar alarma** junto a la alarma:

![Exploración de la alarma desde el panel](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Aparece la vista de talles de la alarma:

* Cuándo se desencadenó la alarma
* La información de estado sobre los dispositivos asociados con la alarma
* Telemetría desde los dispositivos asociados con la alarma

![Detalles de la alarma](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Para confirmar la alarma, seleccione **Alarm occurrences** (Repeticiones de alarmas) y elija **Acknowledge** (Confirmación). Esta acción permite que otros operadores vean que ha visto la alarma y que está trabajando en ella.

![Confirmación de las alarmas](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

En la lista, puede ver el dispositivo **Prototipo** responsable de activar la alarma de temperatura:

![Lista de los dispositivos que causan la alarma](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Solución del problema

Para solucionar el problema con el dispositivo **Prototipo**, debe llamar al método **DecreaseTemperature** en el dispositivo.

Para realizar alguna acción en un dispositivo, selecciónelo en la lista de dispositivos y, luego, elija **Programar**. El modelo de dispositivo **Prototipo** especifica los cuatro métodos que debe admitir un dispositivo:

![Vista de los métodos que admite el dispositivo](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Elija **DecreaseTemperature** y establezca el nombre del trabajo en **DecreaseTemperature**. Luego elija **Aplicar**:

![Creación del trabajo para disminuir la temperatura](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Para realizar un seguimiento del estado del trabajo en la página **Mantenimiento**, elija **Trabajos**. Use la vista **Trabajos** para realizar un seguimiento de todos los trabajos y las llamadas de método en la solución:

![Supervisión del trabajo para disminuir la temperatura](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Para ver los detalles de una llamada de método o un trabajo específico, elíjalo en la lista de la vista **Trabajos**:

![Ver detalles del trabajo](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial le mostramos cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar la página **Mantenimiento** para investigar la alarma
> * Llamar a un método de dispositivo para solucionar el problema

Ahora que aprendió a administrar los problemas del dispositivo, el paso siguiente que se sugiere es aprender a [probar la solución con los dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->