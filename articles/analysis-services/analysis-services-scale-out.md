---
title: Escalabilidad horizontal de Azure Analysis Services | Microsoft Docs
description: "Replicación de servidores de Azure Analysis Services con la escalabilidad horizontal"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: 0e58862684e62a65cf11266cc0320a9acd781f07
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="azure-analysis-services-scale-out"></a>Escalabilidad horizontal de Azure Analysis Services

Con la escalabilidad horizontal, las consultas de cliente pueden distribuirse entre varias *réplicas de consulta* en un grupo de consultas, reduciendo así los tiempos de respuesta durante las cargas de trabajo con un número elevado de consultas. También puede separar el procesamiento del grupo de consultas, lo que garantiza que las operaciones de procesamiento no afecten de forma negativa a las consultas de cliente. La escalabilidad horizontal puede configurarse en Azure Portal o con el uso de la API de REST de Analysis Services.

## <a name="how-it-works"></a>Cómo funciona

En una implementación típica de servidor, un servidor actúa como servidor de procesamiento y servidor de consulta. Si el número de consultas de cliente en los modelos en el servidor supera la unidades de procesamiento de consultas (QPU) del plan de su servidor o el procesamiento del modelo se produce al mismo tiempo que las cargas de trabajo con un número elevado de consultas, el rendimiento podría bajar. 

Con la escalabilidad horizontal, puede crear un grupo de consultas con hasta siete réplicas de consulta adicionales (ocho en total, incluido el servidor). Puede escalar el número de réplicas de consulta para satisfacer las demandas de QPU en los momentos críticos y, además, puede separar un servidor de procesamiento del grupo de consultas en cualquier momento. 

Independientemente del número de réplicas de consultas que tenga en un grupo de consultas, las cargas de trabajo de procesamiento no se distribuyen entre las réplicas de consulta. Un único servidor actúa como el servidor de procesamiento. Las réplicas de consulta realizan solo las consultas en los modelos sincronizados entre cada réplica en el grupo de consultas. 

Una vez completadas las operaciones de procesamiento, debe realizarse una sincronización entre el servidor de procesamiento y los servidores de réplica de consultas. Para automatizar las operaciones de procesamiento, es importante configurar una operación de sincronización tras la realización satisfactoria de tales operaciones.

> [!NOTE]
> La escalabilidad horizontal está disponible para los servidores en el plan de tarifa Estándar. Cada réplica de consulta se factura a la misma tarifa que el servidor.

> [!NOTE]
> La escalabilidad horizontal no aumenta la cantidad de memoria disponible para el servidor. Para aumentar la memoria, debe actualizar el plan.

## <a name="monitor-qpu-usage"></a>Supervisión del uso de QPU

 Para determinar si la escalabilidad horizontal del servidor es necesaria, supervise el servidor en Azure Portal con Métricas. Si se supera el nivel máximo de QPU con regularidad, significa que el número de consultas en los modelos excede el límite de QPU del plan. La métrica de longitud de la cola de trabajos del grupo de consultas también aumenta si el número de consultas de la cola del grupo de subprocesos de consulta excede el número de QPU disponibles. Para más información, vea [Supervisión de las métricas del servidor](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configuración de la escalabilidad horizontal

### <a name="in-azure-portal"></a>En Azure Portal

1. En Azure Portal, haga clic en **Escalabilidad horizontal**. Utilice el control deslizante para seleccionar el número de servidores de réplica de consulta. El número de réplicas elegido se suma al servidor existente.

2. En **Separar el servidor de procesamiento del grupo de consultas**, seleccione Sí para excluir el servidor de procesamiento de los servidores de consulta.

   ![Control deslizante de escalabilidad horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Haga clic en **Guardar** para aprovisionar los nuevos servidores de réplica de consulta. 

Los modelos tabulares del servidor principal se sincronizan con los servidores de réplica. Una vez completada la sincronización, el grupo de consultas empieza a distribuir las consultas entrantes entre los servidores de réplica. 

### <a name="powershell"></a>PowerShell
Use el cmdlet [Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Especifique el valor de parámetro `-Capacity` > 1.

## <a name="synchronization"></a>Sincronización 

Al aprovisionar nuevas réplicas de consulta, Azure Analysis Services replica automáticamente los modelos en todas las réplicas. También puede realizar una sincronización manual. Al procesar los modelos, debería realizar una sincronización para que las actualizaciones se sincronicen entre las réplicas de consulta.

### <a name="in-azure-portal"></a>En Azure Portal

En **Información general** > modelo > **Sincronizar el modelo**.

![Control deslizante de escalabilidad horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API de REST

Sincronización de un modelo   
`POST https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Obtención del estado de sincronización de un modelo  
`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

## <a name="connections"></a>Conexiones

En la página de información general del servidor, hay dos nombres de servidor. Si aún no ha configurado la escalabilidad horizontal de un servidor, los dos nombres de servidor funcionan igual. Una vez configurada la escalabilidad horizontal de un servidor, debe especificar el nombre de servidor adecuado según el tipo de conexión. 

Para las conexiones de cliente para el usuario final como Power BI Desktop, Excel y aplicaciones personalizadas, utilice **Nombre del servidor**. 

Para SSMS, SSDT y cadenas de conexión en PowerShell, las aplicaciones de Azure Functions y AMO usan el **nombre del servidor de administración**. El nombre del servidor de administración incluye un calificador especial `:rw` (lectura y escritura). Todas las operaciones de procesamiento se producen en el servidor de administración.

![Nombres de servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Información relacionada

[Supervisión de las métricas del servidor](analysis-services-monitor.md)   
[Administración de Azure Analysis Services](analysis-services-manage.md) 

