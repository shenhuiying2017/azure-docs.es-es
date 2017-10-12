---
title: "Procedimientos recomendados para la solución de administración de OMS | Microsoft Docs"
description: 
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: b3d07ad3164609a5628c0d9805de55a32870ab94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-management-solutions-in-operations-management-suite-oms-preview"></a>Procedimientos recomendados para crear soluciones de administración en Operations Management Suite (OMS) (versión preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.  

En este artículo se presentan procedimientos recomendados para [crear un archivo de solución de administración](operations-management-suite-solutions-solution-file.md) en Operations Management Suite (OMS).  Esta información se actualizará a medida que se detecten procedimientos recomendados adicionales.

## <a name="data-sources"></a>Orígenes de datos
- Los orígenes de datos se pueden [configurar con una plantilla de Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), pero no deben incluirse en un archivo de solución.  El motivo es que la configuración de orígenes de datos no es actualmente idempotente, lo que significa que la solución podría sobrescribir la configuración existente en el área de trabajo del usuario.<br><br>Por ejemplo, la solución podría requerir eventos de advertencia y error desde el registro de eventos de la aplicación.  Si se especifica esto como un origen de datos en la solución, corre el riesgo de quitar eventos de información si el usuario lo tuviera configurado en su área de trabajo.  Si incluyera todos los eventos, podría estar recopilando excesivos eventos de información en el área de trabajo del usuario.

- Si la solución requiere datos de uno de los orígenes de datos estándar, debe definirlo como requisito previo.  Especifique en la documentación que el cliente debe configurar el origen de datos por sí mismo.  
- Agregue un mensaje de [Comprobación del flujo de datos](../log-analytics/log-analytics-view-designer-tiles.md) a todas las vistas de la solución para indicar al usuario cuáles son los orígenes de datos que deben configurarse para que se recopilen los datos necesarios.  Este mensaje se muestra en el icono de la vista cuando no se encuentran los datos necesarios.


## <a name="runbooks"></a>Runbooks
- Agregue una [programación de Automation](../automation/automation-schedules.md) para cada runbook de la solución que necesite ejecutar según una programación.
- Incluya el [módulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) en la solución para que lo usen los runbooks que escriben datos en el repositorio de Log Analytics.  Configure la solución para que [haga referencia](operations-management-suite-solutions-solution-file.md#solution-resource) a este recurso de manera que permanezca si se quita la solución.  Esto permite que varias soluciones compartan el módulo.
- Use [las variables de Automation](../automation/automation-schedules.md) para proporcionar valores a la solución que los usuarios pueden cambiar más adelante.  Incluso si la solución se configura para contener la variable, su valor puede cambiarse.

## <a name="views"></a>Vistas
- Todas las soluciones deben incluir una sola vista que se muestra en el portal del usuario.  La vista puede contener varios [elementos de visualización](../log-analytics/log-analytics-view-designer-parts.md) para ilustrar los distintos conjuntos de datos.
- Agregue un mensaje de [Comprobación del flujo de datos](../log-analytics/log-analytics-view-designer-tiles.md) a todas las vistas de la solución para indicar al usuario cuáles son los orígenes de datos que deben configurarse para que se recopilen los datos necesarios.
- Configure la solución para que [contenga](operations-management-suite-solutions-solution-file.md#solution-resource) la vista de manera que se quite si la solución se quita.

## <a name="alerts"></a>Alertas
- Defina la lista de destinatarios como un parámetro en el archivo de solución de manera que el usuario pueda definirlos al instalar la solución.
- Configure la solución para que [haga referencia](operations-management-suite-solutions-solution-file.md#solution-resource) a reglas de alerta de manera que el usuario pueda cambiar su configuración.  Es posible que desee realizar cambios, como modificar la lista de destinatarios, cambiar el umbral de la alerta o deshabilitar la regla de alerta. 


## <a name="next-steps"></a>Pasos siguientes
* Recorra el proceso básico de [diseño y creación de una solución de administración](operations-management-suite-solutions-creating.md).
* Aprenda a [crear un archivo de solución](operations-management-suite-solutions-solution-file.md).
* [Incorporación de búsquedas y alertas guardadas](operations-management-suite-solutions-resources-searches-alerts.md) a la solución de administración.
* [Incorporación de vistas](operations-management-suite-solutions-resources-views.md) a la solución de administración.
* [Incorporación de runbooks de Automation y otros recursos](operations-management-suite-solutions-resources-automation.md) a la solución de administración.

