---
title: Información general sobre las métricas en Microsoft Azure | Microsoft Docs
description: Información general sobre las métricas y sus usos en Microsoft Azure
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: ashwink

---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Información general sobre las métricas en Microsoft Azure
En este artículo se explica qué son las métricas en Microsoft Azure, sus beneficios y cómo comenzar a utilizarlas.  

## <a name="what-are-metrics?"></a>¿Qué son las métricas?
Azure Monitor permite utilizar telemetría para obtener información sobre el rendimiento y el estado de las cargas de trabajo en Azure. El tipo de telemetría de Azure más importante son las métricas (también denominadas contadores de rendimiento) emitidas por la mayoría de los recursos de Azure. Azure Monitor proporciona varias maneras de configurar y usar estas métricas para supervisar y solucionar problemas.

## <a name="what-can-you-do-with-metrics?"></a>¿Para qué sirven las métricas?
Las métricas son una valiosa fuente de telemetría y le permiten hacer lo siguiente:

* **Realizar un seguimiento del rendimiento** del recurso (por ejemplo, una máquina virtual, un sitio web o una aplicación lógica) mediante el trazado de las métricas en un gráfico de portal y anclando ese gráfico a un panel.
* **Recibir una notificación de un problema** que afecta al rendimiento del recurso cuando una métrica cruza un umbral determinado.
* **Configurar acciones automatizadas**, como escalar automáticamente un recurso o activar un runbook cuando una métrica cruza un umbral determinado.
* **Realizar análisis avanzados** o elaborar informes de tendencias de rendimiento o de uso de los recursos.
* **Archivar** el historial de rendimiento o estado de los recursos **para fines de cumplimiento y auditoría**.

## <a name="metric-characteristics"></a>Características de las métricas
Las métricas presentan las características siguientes:

* Todas las métricas tienen una **frecuencia de 1 minuto**. Recibe un valor de métrica del recurso cada minuto, lo que proporciona visibilidad en tiempo real del estado y la integridad del recurso.
* Las métricas **están disponibles de fábrica, por lo que no es necesario activar** o configurar diagnósticos adicionales.
* Puede acceder a **30 días del historial** de cada métrica. Puede buscar rápidamente las tendencias recientes y mensuales en el rendimiento o el estado del recurso.

Puede:

* Detectar fácilmente todas las métricas, acceder a ellas y **verlas** en Azure Portal al seleccionar un recurso y trazarlo en un gráfico. 
* Configurar una **regla de alerta de métrica que envía una notificación o realiza una acción automatizada** cuando la métrica cruza el umbral establecido. El escalado automático es una acción automatizada especial que permite escalar horizontalmente los recursos para satisfacer las solicitudes entrantes o cargar en el sitio web o en los recursos de procesos. Puede definir una regla de configuración de escalado automático para escalar o reducir horizontalmente si una métrica cruza un umbral.
* **Archivar** métricas durante más tiempo o usarlas para crear informes sin conexión. Puede redirigir las métricas a Blob Storage al configurar opciones de diagnóstico para el recurso.
* **Transmitir** métricas a un centro de eventos, lo que permite redirigirlas a Azure Stream Analytics o a aplicaciones personalizadas para análisis casi en tiempo real. Puede hacer uso de la configuración de diagnóstico.
* **Redirigir** todas las métricas a Log Analytics de OMS para desbloquear análisis instantáneos, búsquedas y alertas presonalizadas de los datos de métricas de los recursos.
* **Consumir** las métricas a través de las nuevas API de REST de Azure Monitor.
* **Consultar** métricas con los cmdlets de PowerShell o la API de REST multiplataforma.
  
  ![Enrutamiento de métricas en Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Acceso a métricas a través del portal
Aquí se ofrece una guía rápida sobre cómo crear un gráfico de métricas a través de Azure Portal.

### <a name="view-metrics-after-creating-a-resource"></a>Visualización de métricas después de crear un recurso
1. Abra Azure Portal.
2. Cree un sitio web nuevo en App Service.
3. Después de crear un sitio web, vaya a la hoja de información general del sitio web.
4. Puede ver las nuevas métricas como un icono "Supervisión". Puede editar el icono y seleccionar métricas adicionales.
   
   ![Métricas de un recurso en Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Acceso a todas las métricas en un solo lugar
1. Abra Azure Portal. 
2. Vaya a la nueva pestaña Monitor y seleccione en ella la opción Métricas. 
3. Puede seleccionar la suscripción, el grupo de recursos y el nombre del recurso en la lista desplegable. 
4. Ahora puede ver la lista de métricas disponibles. 
5. Seleccione la métrica que le interesa y trácela. 
6. Puede anclarla al panel haciendo clic en la opción para anclar que se encuentra en la esquina superior derecha.
   
   ![Acceso a todas las métricas en un solo lugar en Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png) 

> [!NOTE]
> Puede acceder a las métricas a nivel de host de máquinas virtuales (basadas en Azure Resource Manager) y conjuntos de escalas de máquina virtual sin ninguna configuración adicional de diagnóstico. Estas nuevas métricas a nivel de host están disponibles para las instancias de Windows y Linux. Estas métricas no deben confundirse con las métricas a nivel de SO invitado a las que se accede al activar Diagnósticos de Azure en máquinas virtuales o conjuntos de escalas de máquina virtual. Para obtener más información sobre la configuración de Diagnósticos de Azure, vea [¿Qué es Diagnósticos de Microsoft Azure?](../azure-diagnostics.md)
> 
> 

## <a name="access-metrics-via-rest-api"></a>Acceso a métricas a través de la API de REST
Se puede acceder a las métricas de Azure a través de las API de Azure Monitor. Hay dos API que facilitan la detección de métricas y el acceso a ellas. Use lo siguiente: 

* [API de REST para definiciones de métricas de Azure Monitor](https://msdn.microsoft.com/library/mt743621.aspx) para acceder a la lista de métricas disponibles para un servicio.
* [API de REST de métricas de Azure Monitor](https://msdn.microsoft.com/library/mt743622.aspx) para acceder a los datos de métricas reales.

> [!NOTE]
> En este artículo se tratan las métricas a través de la [API nueva para las métricas](https://msdn.microsoft.com/library/dn931930.aspx) para recursos de Azure. La versión de API para las nuevas definiciones de métricas es 2016-03-01 y la versión de la API de métricas es 2016-09-01. Se puede acceder a las métricas y definiciones de métricas heredadas con la versión de API 2014-04-01.
> 
> 

Para ver un tutorial más detallado mediante las API de REST de Monitor de Azure, vea el [Tutorial sobre la API de REST de Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opciones de exportación de métricas
Puede ir a la hoja Registros de diagnóstico en la pestaña Monitor y ver las opciones de exportación de las métricas. Puede seleccionar las métricas (y los registros de diagnóstico) para redirigirlas a Blob Storage, Event Hubs o a Log Analytics de OMS para casos de uso que se han mencionado anteriormente en este artículo. 

 ![Opciones de exportación de métricas en Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Puede configurar esto a través de plantillas de Resource Manager, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) o [API de REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Realización de acciones en función de las métricas
Puede recibir notificaciones o realizar acciones automatizadas basadas en los datos de métricas. Puede configurar reglas de alerta o valores de escalado automático para ello.

### <a name="alert-rules"></a>Reglas de alertas
Puede configurar reglas de alerta basadas en las métricas. Estas reglas de alerta pueden comprobar si una métrica ha superado un umbral determinado y enviar una notificación por correo electrónico o activar un webhook que puede utilizarse para ejecutar cualquier script personalizado. También puede utilizar el webhook para configurar integraciones de productos de terceros.

 ![Métricas y reglas de alerta en Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoscale
Algunos recursos de Azure admiten varias instancias para escalar o reducir horizontalmente para controlar las cargas de trabajo. El escalado automático se aplica a App Services (Web Apps), Conjuntos de escalas de máquina virtual (VMSS) y Cloud Services en versión clásica. Puede configurar reglas de escalado automático para escalar o reducir horizontalmente cuando una métrica determinada que afecta a su carga de trabajo cruza un umbral especificado. Para obtener más información, vea la [información general sobre la funcionalidad de escalado automático](monitoring-overview-autoscale.md).

 ![Métricas y escalado automático en Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Servicios y métricas admitidos
Azure Monitor es una nueva infraestructura de métricas. Proporciona compatibilidad para los siguientes servicios de Azure en Azure Portal y la nueva versión de la API de Azure Monitor:

* máquinas virtuales (basadas en Azure Resource Manager)
* Conjuntos de escalas de máquina virtual
* Lote
* Espacio de nombres del Centro de eventos 
* Espacio de nombres de Service Bus (solo SKU premium)
* SQL (versión 12)
* Elastic SQL Pool
* Sitios web
* Granjas de servidores web
* Aplicaciones lógicas
* IoT Hubs
* Caché en Redis
* Redes: Puertas de enlace de aplicaciones
* Search

Puede ver una lista detallada de todos los servicios admitidos y sus métricas en [Métricas de Azure Monitor: métricas admitidas por tipo de recurso](monitoring-supported-metrics.md). 

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos de este artículo. Además:  

* Obtenga información sobre [métricas comunes de escalado automático de Azure Insights](insights-autoscale-common-metrics.md)
* Vea la información sobre cómo [crear reglas de alertas](insights-alerts-portal.md)

<!--HONumber=Oct16_HO2-->


