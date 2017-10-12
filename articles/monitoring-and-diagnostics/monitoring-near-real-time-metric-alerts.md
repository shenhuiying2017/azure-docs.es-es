---
title: "Alertas de métricas casi en tiempo real en Azure Monitor | Microsoft Docs"
description: "Las alertas de métricas casi en tiempo real le permiten supervisar las métricas de recursos de Azure con una frecuencia de un minuto."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertas de métricas casi en tiempo real (versión preliminar)
Azure Monitor ahora admite un nuevo tipo de alerta de métricas denominada alertas de métricas casi en tiempo real (versión preliminar) Esta característica actualmente está en su versión preliminar pública.
Estas alertas se diferencian de las alertas de métricas normales de varias maneras:

- **Latencia mejorada**: las alertas de métricas casi en tiempo real pueden supervisar los cambios en los valores de las métricas con una frecuencia de tan solo 1 minuto.
- **Más control sobre las condiciones de las métricas**: las alertas de métricas casi en tiempo real permiten a los usuarios definir reglas de alerta más sofisticadas. Las alertas admiten ahora la supervisión de los valores máximos, mínimos, promedio y totales de las métricas.
- **Supervisión combinada de varias métricas**: las alertas de métricas casi en tiempo real pueden supervisar varias métricas (actualmente dos) con una sola regla. La alerta se desencadena si ambas métricas incumplen sus respectivos umbrales durante un período especificado.
- **Sistema de notificaciones modular**: las alertas de métricas casi en tiempo real usan [grupos de acciones](monitoring-action-groups.md). Esta funcionalidad permite a los usuarios crear acciones de forma modular y reutilizarlas para muchas reglas de alertas.

> [!NOTE]
> La característica de alertas de métricas casi en tiempo real se encuentra actualmente en versión preliminar pública. La funcionalidad y la experiencia del usuario están sujetas a cambios.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>¿Para qué recursos pueden crearse alertas de métricas casi en tiempo real?
Lista completa de los tipos de recursos que son compatibles con las alertas de métricas casi en tiempo real:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Creación de una alerta de métricas casi en tiempo real
Actualmente, las alertas de métricas casi en tiempo real solo pueden crearse mediante Azure Portal. Próximamente estará disponible la compatibilidad para la configuración de las alertas de métricas casi en tiempo real mediante PowerShell, la interfaz de línea de comandos (CLI) y la API de REST de Azure Monitor.

1. En el [portal](https://portal.azure.com/), busque el recurso que desea supervisar y selecciónelo. Este recurso debe ser de uno de los tipos de recursos que aparecen en la [sección anterior](#what-resources-can-i-create-near-real-time-metric-alerts-for). También puede hacer lo mismo para todos los tipos de recursos admitidos centralmente desde Monitor>Alertas.

2. En la sección SUPERVISIÓN, seleccione **Alertas** o **Reglas de alerta**. El texto y el icono pueden variar ligeramente en los distintos recursos.
   ![Supervisión](./media/insights-alerts-portal/AlertRulesButton.png)

3. Haga clic en el comando **Alerta de métricas de Near Real Time (versión preliminar)**. Si el comando está atenuado, asegúrese de que el recurso esté seleccionado en el filtro.

    ![Botón Agregar alerta de métricas de Near Real Time](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. Asígnele un **nombre** a la regla de alerta y elija una **descripción**, que también se muestra los correos electrónicos de notificación.
5. Seleccione la **métrica** que desea supervisar y elija un valor de **Condición**, **Agregación de tiempo** y **Umbral** para la métrica. Como alternativa, seleccione otra **métrica** que desee supervisar y elija un valor de **Condición**, **Agregación de tiempo** y **Umbral** para la segunda métrica. 

    ![Agregar alerta de métricas casi en tiempo real1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png)![Agregar alerta de métricas casi en tiempo real2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. Elija el **período** de la regla de métrica que se debe cumplir antes de que se desencadene la alerta. Por ejemplo, si usa el período “En los últimos 5 minutos” y la alerta busca una CPU por encima del 80 % (y Datos de entrada de red por encima de 500 MB), la alerta se desencadena cuando la CPU ha estado por encima del 80 % durante 5 minutos de manera uniforme. Una vez que se desencadena por primera vez, se vuelve a desencadenar cuando la CPU se mantiene por debajo del 80% durante 5 minutos. La alerta se evalúa según la **Frecuencia de evaluación**.


6. Elija una **Gravedad** adecuada en la lista desplegable.

7. Especifique si desea usar un **Grupo de acciones** nuevo o existente.

8. Si decide crear un **Nuevo** grupo de acciones, asigne un nombre y un nombre corto al grupo de acciones, especifique acciones (SMS, correo electrónico, webhook) y rellene los detalles correspondientes.


8. Seleccione **Aceptar** cuando termine para crear la alerta.   

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="managing-near-real-time-metric-alerts"></a>Administración de alertas de métricas casi en tiempo real
Una vez que haya creado una alerta, puede seleccionarla y:

* Ver un gráfico que muestre el umbral de las métricas y los valores reales del día anterior.
* Editar la alerta o eliminarla.
* **Deshabilitar** la alerta, si desea dejar de recibir notificaciones de esa alerta de manera temporal, o **habilitarla** si desea reanudar sus notificaciones.



