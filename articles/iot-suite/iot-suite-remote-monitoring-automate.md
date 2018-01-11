---
title: "Detección de problemas de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs"
description: "En este tutorial se muestra como usar reglas y acciones para detectar automáticamente problemas de los dispositivos basados en el umbral en la solución de supervisión remota."
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
ms.openlocfilehash: e00c4ab2fc8bb13a765f7c2154555607dddfc651
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>Detección de problemas mediante reglas basadas en el umbral

En este tutorial se muestran las funcionalidades del motor de reglas de la solución de supervisión remota. Para introducir estas funcionalidades, en el tutorial se usa un escenario de la aplicación IoT de Contoso.

Contoso dispone de una regla que genera una alerta crítica cuando la presión notificada por un dispositivo **refrigerador** supera los 250 PSI. Los operadores identificarán los dispositivos **refrigeradores** con sensores problemáticos mediante la búsqueda de los picos de presión. Para identificar estos dispositivos, creará una regla que genere una advertencia cuando la presión se sitúe por encima de 150 PSI.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Ver las reglas de la solución
> * Crear una nueva regla
> * Editar una regla existente
> * Eliminar una regla

## <a name="prerequisites"></a>Requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe realizar el tutorial [Implementación de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-deploy.md).

## <a name="view-the-rules-in-your-solution"></a>Ver las reglas de la solución

La página **Rules and Actions** (Reglas y acciones) de la solución muestra una lista de todas las reglas actuales:

![Página de reglas y acciones](media/iot-suite-remote-monitoring-automate/rulesactions.png)

Para ver solo las reglas que se aplican al dispositivo **refrigerador**, utilice un filtro:

![Filtrado de la lista de reglas](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

Puede ver más información sobre una regla y editarla si la selecciona en la lista:

![Visualización de los detalles de la regla](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

Para deshabilitar, habilitar o eliminar una o varias reglas, seleccione varias reglas en la lista:

![Selección de varias reglas](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>Crear una nueva regla

Para agregar una nueva regla que genera una advertencia cuando la presión del dispositivo **refrigerador** se sitúe por encima de 150 PSI, elija **Nueva regla**:

![Creación de una regla](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

Utilice los valores siguientes para crear la regla:

| Configuración          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nombre             | Advertencia del refrigerador                       |
| Origen           | Grupo de dispositivos **Refrigeradores**             |
| Campo del desencadenador    | pressure                              |
| Operador del desencadenador | Mayor que                          |
| Valor del desencadenador    | 150                                   |
| Nivel de gravedad   | Warning (Advertencia)                               |
| Descripción      | La presión del refrigerador ha superado 150 PSI |

Para guardar la nueva regla, elija **Aplicar**.

Puede ver cuándo se desencadena la regla en la página **Rules and Actions** (Reglas y acciones) o en la página **Panel**.

## <a name="edit-an-existing-rule"></a>Editar una regla existente

Para realizar un cambio en una regla existente, selecciónela en la lista de reglas. A continuación, en el panel **Rule Detail** (Detalles de la regla), elija **Modo de edición**.

![Editar regla](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>Deshabilitar una regla

Para desactivar temporalmente una regla, puede deshabilitarla en la lista de reglas. Elija la regla para deshabilitar y, a continuación, elija **Deshabilitar**. El **estado** de la regla en la lista cambia para indicar que la regla está ahora deshabilitada. Puede volver a habilitar una regla que anteriormente ha deshabilitado siguiendo el mismo procedimiento.

![Deshabilitar regla](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

Puede habilitar y deshabilitar varias reglas al mismo tiempo si selecciona varias en la lista.

## <a name="delete-a-rule"></a>Eliminar una regla

Para eliminar permanentemente una regla, seleccione la regla en la lista de reglas y, a continuación, elija **Eliminar**.

Puede eliminar varias reglas al mismo tiempo si selecciona varias en la lista.

## <a name="next-steps"></a>Pasos siguientes

Este tutorial le ha mostrado cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Ver las reglas de la solución
> * Crear una nueva regla
> * Editar una regla existente
> * Eliminar una regla

Ahora que ha aprendido a detectar problemas con las reglas basadas en umbrales, los siguientes pasos sugeridos son para aprender a:

* [Administrar y configurar los dispositivos](./iot-suite-remote-monitoring-manage.md)
* [Solucionar problemas de los dispositivos](./iot-suite-remote-monitoring-maintain.md).
* [Probar la solución con dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->