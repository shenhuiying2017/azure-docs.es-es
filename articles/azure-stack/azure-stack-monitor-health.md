---
title: Supervisar el estado y las alertas en Azure Stack | Microsoft Docs
description: "Aprenda cómo supervisar el estado y las alertas en Azure Stack."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: b2ba7ec922341464ea7160d08e475999c941c42a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Supervisar el estado y las alertas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Azure Stack incluye funciones de supervisión de infraestructura que le permiten ver el estado y las alertas de una región de Azure Stack. El icono **Region management** (Administración de regiones), anclado en el portal de administración de manera predeterminada para la suscripción de proveedor predeterminada, enumera todas las regiones implementadas de Azure Stack. El icono muestra el número de alertas críticas y de advertencia activas para cada región y representa el punto de entrada a la funcionalidad de mantenimiento y de alertas de Azure Stack.

 ![Icono Region Management (Administración de regiones)](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Comprender el estado en Azure Stack

 El proveedor de recursos de estado administra el estado y las alertas. Los componentes de infraestructura de Azure Stack se registran en el proveedor de recursos de estado durante la implementación y configuración de Azure Stack. Este registro permite mostrar el estado y las alertas para cada componente. El estado en Azure Stack es un concepto simple. Si existen alertas para una instancia registrada de un componente, el estado de mantenimiento de dicho componente refleja la gravedad de la peor alerta activa: advertencia o crítica.
 
 ## <a name="view-and-manage-component-health-state"></a>Ver y administrar el estado de mantenimiento de un componente
 
 Como operador de Azure Stack, puede ver el estado de mantenimiento de los componentes en el portal de administración y a través de la API de REST y PowerShell.
 
Para ver el estado de mantenimiento en el portal, haga clic en la región que quiera ver en la ventana **Region management** (Administración de regiones). Puede ver el estado de mantenimiento de los roles de infraestructura y de los proveedores de recursos. En esta versión, el proveedor de recursos de Compute no informa del estado de mantenimiento.

![Lista de roles de infraestructura](media/azure-stack-monitor-health/image2.png)

Puede hacer clic en un proveedor de recursos o rol de infraestructura para ver información más detallada.

> [!WARNING]
>Si hace clic en un rol de infraestructura y, luego, hace clic en la instancia de rol, hay opciones para iniciar, reiniciar o apagar. No use estas acciones al aplicar actualizaciones a un sistema integrado. **No** use tampoco estas opciones en un entorno de Azure Stack Development Kit. Estas opciones están diseñadas únicamente para un entorno de sistemas integrados, donde hay más de una instancia de rol por rol de infraestructura. Reiniciar una instancia de rol (especialmente AzS-Xrp01) en el kit de desarrollo provoca inestabilidad del sistema. Para obtener asistencia para solucionar problemas, publique su problema en el [foro de Azure Stack](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Visualización de alertas

La lista de alertas activas para cada región de Azure Stack está disponible directamente desde la hoja **Region management** (Administración de regiones). El primer icono en la configuración predeterminada es **Alertas**, que muestra un resumen de las alertas críticas y de advertencia para la región. Puede anclar la ventana de alertas, al igual que cualquier otra ventana en esta hoja, al panel para un acceso rápido.   

![Ventana Alerts que muestra una advertencia](media/azure-stack-monitor-health/image3.png)

Al seleccionar la parte superior del icono **Alertas**, irá a la lista de todas las alertas activas de la región. Si selecciona un elemento de línea **Crítica** o **Advertencia** en la ventana, navega a una lista filtrada de alertas (Crítica o Advertencia). 

![Alertas de advertencia filtradas](media/azure-stack-monitor-health/image4.png)
  
La hoja **Alertas** admite la capacidad de filtrar tanto por estado (Activa o Cerrada) como por gravedad (Crítica o Advertencia). La vista predeterminada muestra todas las alertas activas. Todas las alertas cerradas se quitan del sistema después de siete días.

![Panel Filtro para filtrar por estado crítico o de advertencia](media/azure-stack-monitor-health/image5.png)

En la acción **View API** (Ver API) se muestra la API de REST que se usó para generar la vista de lista. Esta acción proporciona una forma rápida de familiarizarse con la sintaxis de la API de REST que puede usar para consultar las alertas. Puede utilizar esta API para automatización o para integración con las soluciones existentes de supervisión, informe y vales del centro de datos. 

![Opción View API (Ver API) que muestra la API de REST](media/azure-stack-monitor-health/image6.png)

Puede hacer clic en una alerta específica para ver los detalles de la alerta. En los detalles de alerta se muestran todos los campos que están asociados a la alerta y se permite la navegación rápida al componente afectado y al origen de la alerta. Por ejemplo, la alerta siguiente se produce si una de las instancias de rol de infraestructura se queda sin conexión o no está accesible.  

![Hoja Detalles de alerta](media/azure-stack-monitor-health/image7.png)

Después de que la instancia de rol de la infraestructura vuelve a estar en línea, esta alerta se cierra automáticamente. Muchas alertas (pero no todas) se cierran automáticamente cuando se resuelve el problema subyacente. Le recomendamos que seleccione **Cerrar alerta** cuando haya acabado de seguir los pasos de corrección. Si el problema persiste, Azure Stack genera una alerta nueva. Si se resuelve el problema, la alerta permanecerá cerrada y no se tendrán que efectuar más acciones.

## <a name="next-steps"></a>Pasos siguientes

[Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)

[Administración de regiones en Azure Stack](azure-stack-region-management.md)
