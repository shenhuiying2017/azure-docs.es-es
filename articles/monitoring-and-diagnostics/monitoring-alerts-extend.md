---
title: 'Extensión (copia) de alertas del portal de OMS a Azure: introducción | Microsoft Docs'
description: Introducción al proceso de copia de alertas desde el portal de OMS a Alertas de Azure y detalles en torno a las inquietudes más frecuentes de los usuarios.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 25dcbad8607a651a7dd4b79f4f418cc473a2bf0e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166511"
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>Extensión (copia) de alertas del portal de OMS a Azure
El portal de Operations Management Suite (OMS) muestra solo las alertas de Log Analytics.  La nueva experiencia de Alertas ahora integra la experiencia de alertas en distintos servicios y componentes de Microsoft Azure. La nueva experiencia, disponible como **Alertas** en Azure Monitor en Azure Portal, contiene las alertas de los registros de actividad, las alertas de métricas y las alertas de registros en Log Analytics y también en Application Insights. 


Pero para algunos usuarios, el uso de Log Analytics y las funcionalidades relacionadas, como las alertas, se ha realizado a través del [portal de Microsoft Operation Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Por lo tanto, Microsoft ha garantizado sistemáticamente que las funcionalidades del portal de OMS también estén disponibles en Azure Portal para permitirles administrar de manera sencilla sus otros recursos de Azure mientras usan Log Analytics. En ese sentido, Alertas de Azure ya permite que los usuarios administren alertas basadas en consultas para Log Analytics. Para más información, consulte el artículo sobre las [alertas de registro en Alertas de Azure](monitor-alerts-unified-log.md). En Alertas de Azure Monitor, las alertas que se crean en el portal de OMS ya aparecen en el área de trabajo de Log Analytics correspondiente. Sin embargo, cualquier edición o modificación de dichas alertas creadas en el portal de OMS requiere que el usuario salga de Azure y use el portal de OMS y que luego vuelva a Azure si necesita administrar cualquier otro servicio. Para disminuir esta complicación, Microsoft ahora permite que los usuarios extiendan sus alertas del portal de OMS a Azure.

## <a name="benefits-of-extending-your-alerts"></a>Ventajas de extender las alertas
Además de la ventaja que implica no tener que salir de Azure Portal, extender las alertas del portal de OMS a Azure genera otras ventajas importantes

- A diferencia del portal de OMS, donde solo se pueden crear y ver 250 alertas, en Alertas de Azure no existe este límite
- A diferencia de lo que ocurre con el portal de OMS, en Alertas de Azure, puede administrar, enumerar y ver todos los tipos de alertas y no solo las alertas de Log Analytics
- Controle el acceso de los usuarios solo a Supervisión y alertas, mediante el [rol de Azure Monitor](monitoring-roles-permissions-security.md)
- Alertas de Azure usa los [grupos de acciones](monitoring-action-groups.md), que permiten tener más de una acción para cada alerta, entre las que se incluyen SMS, llamada de voz, runbook de Automation, webhook, Conector de Administración de servicios de TI, etc. 

## <a name="process-of-extending-your-alerts"></a>Proceso de extensión de las alertas
El proceso de extender las alertas del portal de OMS a Azure **no** implica cambiar de ningún modo la configuración, la consulta ni la definición de las alertas. El único cambio necesario es que, en Azure, todas las acciones, como notificaciones de correo electrónico, llamadas de webhook, la ejecución de runbooks de Automation o la conexión a la herramienta de ITSM, se realizan a través del grupo de acciones. Por lo tanto, si el grupo de acciones correspondiente está asociado con la alerta, se extenderán a Azure.

Como el proceso de extensión no es destructivo ni interrumpe las operaciones, Microsoft extenderá automáticamente las alertas creadas en el portal de OMS a Alertas de Azure a partir del **14 de mayo de 2018**. A partir de esa fecha, Microsoft comenzará a programar la extensión de las alertas a Azure y, de manera gradual, hará que todas las alertas que existen en el portal de OMS se puedan administrar también desde Azure Portal. 

> [!NOTE]
> A partir del 14 de mayo de 2018, Microsoft comenzará el proceso de extensión automática de alertas a Azure. No todas las áreas de trabajo y alertas se ampliarán ese día, sino que Microsoft comenzará a extender automáticamente las alertas por tramos en las próximas semanas. Por lo tanto, las alertas en el portal OMS no se extenderán automáticamente a Azure inmediatamente el 14 de mayo de 2018 y los usuarios todavía pueden [extender manualmente sus alertas](monitoring-alerts-extend-tool.md) durante este tiempo.

Cuando las alertas de un área de trabajo de Log Analytics se programen para su extensión a Azure, van a seguir funcionando y su supervisión **no** se verá comprometida de ninguna manera. Una vez programadas, temporalmente no se podrán modificar ni editar las alertas. Sin embargo, en este corto tiempo sí se podrán seguir creando nuevas alertas de Azure. En este breve período, si se edita o crea alguna alerta desde el portal de OMS, los usuarios tendrán la opción de seguir haciéndolo en Azure Log Analytics o en Alertas de Azure.

 ![Durante el período programado, se redirigirá a Azure al usuario que realice cualquier acción sobre las alertas.](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> No se cobrará por la extensión de alertas del portal de OMS a Azure y el uso de Alertas de Azure para las alertas de Log Analytics basadas en consulta no se facturará, siempre que el uso cumpla con los límites y las condiciones que se estipulan en la [directiva de precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  

Los usuarios pueden disfrutar de las ventajas que implica extender las alertas antes de esta fecha si deciden aceptar que sus alertas se puedan administrar en Azure.

### <a name="how-to-voluntarily-extending-your-alerts"></a>Extensión voluntaria de las alertas
Para permitir que los usuarios de OMS disfruten de una transición sencilla a Alertas de Azure, Microsoft creó herramientas que permiten extender las alertas. Los clientes del portal de OMS pueden extender sus alertas a Azure desde un asistente en el portal de OMS o bien con un enfoque de programación mediante una API nueva. Para más información, consulte el artículo sobre [cómo extender las alertas a Azure mediante la API y el portal de OMS](monitoring-alerts-extend-tool.md).


## <a name="usage-after-extending-your-alerts"></a>Uso después de extender las alertas
Como ya se mencionó, una vez que las alertas creadas en Microsoft Operations Management Suite se extiendan a Alertas de Microsoft, podrá administrarlas desde Azure. Las alertas seguirán apareciendo en la sección de configuración de alertas del portal de OMS. Esto se ilustra a continuación:

 ![Portal de OMS con las alertas una vez extendidas a Azure](./media/monitor-alerts-extend/PostExtendList.png)

Para realizar cualquier operación sobre las alertas, como la edición o la creación que se realice en el portal de OMS, se dirigirá a los usuarios a Alertas de Azure sin problemas. 

> [!NOTE]
> Dado que se dirige sin problemas a los usuarios a Azure, en cualquier adición o acción de edición sobre una alerta de OMS, asegúrese de que se asignan a los usuarios los [permisos para usar Azure Monitor and Alerts](monitoring-roles-permissions-security.md) apropiados

La creación de alertas continuará desde la [API de Log Analytics](../log-analytics/log-analytics-api-alerts.md) y la [plantilla de recursos de Log Analytics](../monitoring/monitoring-solutions-resources-searches-alerts.md) existentes como antes. El único cambio menor es que, después de extender las alertas a Azure, los grupos de acción deben estar asociados en la programación.

## <a name="next-steps"></a>Pasos siguientes

* Conozca las herramientas para [iniciar la extensión de alertas de OSM a Azure](monitoring-alerts-extend-tool.md).
* Más información sobre la nueva [experiencia de Alertas de Azure](monitoring-overview-unified-alerts.md).
* Más información sobre las [alertas de registro en Alertas de Azure](monitor-alerts-unified-log.md).
