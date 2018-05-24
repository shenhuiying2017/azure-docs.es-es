---
title: 'Solución de problemas de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs'
description: En este tutorial se muestra cómo solucionar los problemas con los dispositivos en la solución de supervisión remota.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 99cf9e341edf0acee434e2d01f6346291cbcea5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Solución de problemas de los dispositivos

En este tutorial se muestra cómo usar la página **Mantenimiento** en la solución para solucionar los problemas de los dispositivos. Para introducir estas funcionalidades, en el tutorial se usa un escenario de la aplicación IoT de Contoso.

Contoso está probando un tipo nuevo de **prototipo** en el campo. Como operador de Contoso, durante las pruebas observa que el dispositivo **Prototipo** desencadena inesperadamente una alerta en el panel. Ahora debe investigar el comportamiento de este dispositivo **Prototipo** defectuoso.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Usar la página **Mantenimiento** para investigar la alerta
> * Llamar a un método de dispositivo para solucionar el problema

## <a name="prerequisites"></a>requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación del acelerador de la solución de supervisión remota](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Uso del panel de mantenimiento

En la página **Panel** observará que hay alertas de temperatura inesperadas que provienen de la regla asociada con los dispositivos **Prototipo**:

![Alertas que aparecen en el panel](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Para investigar aún más el problema, elija la opción **Explorar alerta** junto a la alerta:

![Exploración de la alerta desde el panel](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Aparece la vista de detalles de la alerta:

* Cuándo se desencadenó la alerta
* La información de estado sobre los dispositivos asociados con la alerta
* Telemetría desde los dispositivos asociados con la alerta

![Detalles de alertas](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Para confirmar la alerta, seleccione **Alert occurrences** (Repeticiones de alertas) y elija **Acknowledge** (Confirmación). Esta acción permite que otros operadores vean que ha visto la alerta y que está trabajando en ella.

![Confirme las alertas.](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

Al confirmar la alerta, el estado de la repetición cambia a **Confirmado**.

En la lista, puede ver el dispositivo **Prototipo** responsable de activar la alerta de temperatura:

![Lista de los dispositivos que causan la alerta](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Solución del problema

Para solucionar el problema con el dispositivo **Prototipo**, debe llamar al método **DecreaseTemperature** en el dispositivo.

Para realizar alguna acción en un dispositivo, selecciónelo en la lista de dispositivos y, luego, elija **Trabajos**. El modelo de dispositivo **Prototipo** especifica los seis métodos que debe admitir un dispositivo:

![Vista de los métodos que admite el dispositivo](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Elija **DecreaseTemperature** y establezca el nombre del trabajo en **DecreaseTemperature**. Luego elija **Aplicar**:

![Creación del trabajo para disminuir la temperatura](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Para realizar un seguimiento del estado del trabajo en la página **Mantenimiento**, elija **Trabajos**. Use la vista **Trabajos** para realizar un seguimiento de todos los trabajos y las llamadas de método en la solución:

![Supervisión del trabajo para disminuir la temperatura](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Para ver los detalles de una llamada de método o un trabajo específico, elíjalo en la lista de la vista **Trabajos**:

![Ver detalles del trabajo](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprenderá a:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usar la página **Mantenimiento** para investigar la alerta
> * Llamar a un método de dispositivo para solucionar el problema

Ahora que aprendió a administrar los problemas del dispositivo, el paso siguiente que se sugiere es aprender a [probar la solución con los dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->