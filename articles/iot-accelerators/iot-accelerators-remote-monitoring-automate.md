---
title: 'Detección de problemas de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs'
description: En este tutorial se muestra como usar reglas y acciones para detectar automáticamente problemas de los dispositivos basados en el umbral en la solución de supervisión remota.
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
ms.openlocfilehash: f3583b27b2fb9959e65a9c66a75c1174ebf3e238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>Detección de problemas mediante reglas basadas en el umbral

En este tutorial se muestran las funcionalidades del motor de reglas de la solución de supervisión remota. Para introducir estas funcionalidades, en el tutorial se usa un escenario de la aplicación IoT de Contoso.

Contoso dispone de una regla que genera una alerta crítica cuando la presión notificada por un dispositivo **refrigerador** supera los 250 PSI. Los operadores identificarán los dispositivos **refrigeradores** con sensores problemáticos mediante la búsqueda de los picos de presión. Para identificar estos dispositivos, creará una regla que genere una advertencia cuando la presión se sitúe por encima de 150 PSI.

También se ha comunicado que se debe activar una alerta crítica cuando la humedad media del dispositivo **refrigerador** durante los últimos cinco minutos es superior al 80 % y la temperatura del dispositivo **refrigerador** durante los últimos cinco minutos es superior a 75 grados fahrenheit.

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Visualización de las reglas de la solución
> * Crear una nueva regla
> * Creación de una regla con varias condiciones
> * Editar una regla existente
> * Eliminar una regla

## <a name="prerequisites"></a>requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación del acelerador de la solución de supervisión remota](iot-accelerators-remote-monitoring-deploy.md).

## <a name="view-the-rules-in-your-solution"></a>Visualización de las reglas de la solución

La página **Rules** (Reglas) de la solución muestra una lista de todas las reglas actuales:

![Página de reglas y acciones](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2.png)

Para ver solo las reglas que se aplican al dispositivo **refrigerador**, utilice un filtro:

![Filtrado de la lista de reglas](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2.png)

Puede ver más información sobre una regla y editarla si la selecciona en la lista:

![Visualización de los detalles de la regla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2.png)

Para deshabilitar, habilitar o eliminar una o varias reglas, seleccione varias reglas en la lista:

![Selección de varias reglas](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Crear una nueva regla

Para agregar una nueva regla que genera una advertencia cuando la presión del dispositivo **refrigerador** se sitúe por encima de 150 PSI, elija **Nueva regla**:

![Creación de una regla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Utilice los valores siguientes para crear la regla:

| Configuración          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nombre de la regla        | Advertencia del refrigerador                       |
| DESCRIPCIÓN      | La presión del refrigerador ha superado 150 PSI |
| Grupo de dispositivos     | Grupo de dispositivos **Refrigeradores**             |
| Cálculo      | Instantánea                               |
| Campo de condición 1| pressure                              |
| Operador de condición 1 | Mayor que                      |
| Valor de condición 1    | 150                               |
| Nivel de gravedad  | Warning (Advertencia)                               |

Para guardar la nueva regla, elija **Aplicar**.

Puede ver cuándo se desencadena la regla en la página **Rules** (Reglas) o en la página **Panel**.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Creación de una regla con varias condiciones

Para crear una regla con varias condiciones que genere una alerta crítica cuando la humedad media del dispositivo **refrigerador** durante los últimos cinco minutos es superior al 80 % y la temperatura del dispositivo **refrigerador** durante los últimos cinco minutos es superior a 75 grados fahrenheit, seleccione **Nueva regla**:

![Creación de reglas mult](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Utilice los valores siguientes para crear la regla:

| Configuración          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nombre de la regla        | Humedad y temperatura críticas del refrigerador    |
| DESCRIPCIÓN      | La temperatura y humedad son críticas |
| Grupo de dispositivos     | Grupo de dispositivos **Refrigeradores**             |
| Cálculo      | Media                               |
| Período de tiempo      | 5                                     |
| Campo de condición 1| humedad                              |
| Operador de condición 1 | Mayor que                      |
| Valor de condición 1    | 80                               |
| Nivel de gravedad  | Crítico                              |

Para agregar la segunda condición, haga clic en "+ Agregar condición".

![Crear condición 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Utilice los siguientes valores en la nueva condición:

| Configuración          | Valor                                 |
| ---------------- | ------------------------------------- |
| Campo de condición 2| temperatura                           |
| Operador de condición 2 | Mayor que                      |
| Valor de condición 2    | 75                                |

Para guardar la nueva regla, elija **Aplicar**.

Puede ver cuándo se desencadena la regla en la página **Rules** (Reglas) o en la página **Panel**.

## <a name="edit-an-existing-rule"></a>Editar una regla existente

Para realizar un cambio en una regla existente, selecciónela en la lista de reglas.

![Editar regla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Pasos siguientes

Este tutorial le ha mostrado cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Visualización de las reglas de la solución
> * Crear una nueva regla
> * Editar una regla existente
> * Eliminar una regla

Ahora que ha aprendido a detectar problemas con las reglas basadas en umbrales, los siguientes pasos sugeridos son para aprender a:

* [Administrar y configurar los dispositivos](iot-accelerators-remote-monitoring-manage.md)
* [Solucionar problemas de los dispositivos](iot-accelerators-remote-monitoring-maintain.md).
* [Probar la solución con dispositivos simulados](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->