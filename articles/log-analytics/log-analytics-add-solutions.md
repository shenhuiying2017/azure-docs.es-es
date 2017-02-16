---
title: "Incorporación de soluciones de administración de Operations Management Suite (OMS) | Microsoft Docs"
description: "Las soluciones de administración de Operations Management Suite (OMS)/Log Analytics son una colección de reglas de lógica, visualización y adquisición de datos que proporcionan métricas que giran en torno a una determinada área de problemas."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: ebfa17fd5a587cbc985689c37bb14167daaee4bb
ms.openlocfilehash: 531820db1ff80fb4dcfc72743bf28104fc1014f1


---
# <a name="add-operations-management-suite-oms-management-solutions"></a>Incorporación de soluciones de administración de Operations Management Suite (OMS)

Las soluciones de administración de Operations Management Suite (OMS) son una colección de **reglas de lógica**, **visualización** y **adquisición de datos** que proporcionan métricas que giran en torno a una determinada área de problemas. En este artículo se enumeran soluciones de administración compatibles con Log Analytics, y se indica cómo agregarlas y quitarlas de un área de trabajo mediante Azure Portal. También puede agregar soluciones en el portal OMS con la Galería de soluciones.

Las soluciones de administración permiten obtener información más detallada para:

* ayudar a investigar y resolver los problemas operativos con más rapidez.
* recopilar y correlacionar diversos tipos de datos de la máquina.
* ayudarle a ser proactivo con las actividades que la solución expone.

> [!NOTE]
> Log Analytics incluye la función de búsqueda de registros, por lo que no es necesario instalar una solución de administración para habilitarla. Sin embargo, si agrega soluciones de administración a su área de trabajo, obtendrá visualizaciones de datos, sugerencias de búsqueda e información detallada.

Con este artículo se agregan soluciones de administración a un área de trabajo mediante el portal de Azure Marketplace. Una vez que agrega una solución, se recopilan datos desde los servidores de la infraestructura y se envían al servicio de OMS. El servicio OMS suele tardar en procesar esto entre unos minutos y una hora. Una vez que el servicio procesa los datos, puede verlos en OMS.

Es posible quitar fácilmente una solución de administración cuando ya no se necesita. Cuando se quita una solución de administración, sus datos no se envían a OMS. Si se encuentra en el plan de tarifa Gratis, al quitar una solución, se puede reducir la cantidad de datos usados, lo que ayuda a mantenerle dentro de la cuota diaria de datos.

## <a name="add-a-management-solution"></a>Incorporación de una solución de administración
1. Si aún no lo ha hecho, inicie sesión en [Azure Portal](https://portal.azure.com) mediante su suscripción de Azure.
2. En la hoja **Nuevo**, en **Marketplace**, seleccione **Supervisión y administración**.
3. En la hoja **Supervisión y administración**, haga clic en **Ver todo**.  
    ![Hoja Supervisión y administración](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. A la derecha de **Soluciones de administración de**, haga clic en **Más**.
5. En la hoja **Soluciones de administración**, seleccione la solución de administración que desea agregar a un área de trabajo.  
    ![Hoja Supervisión y administración](./media/log-analytics-add-solutions/management-solutions.png)  
6. En la hoja Solución de administración, revise la información sobre la solución de administración y haga clic en **Crear**.
7. En la hoja *nombre del área de trabajo*, seleccione el área de trabajo que desea asociar a la solución de administración.
8. Tiene la opción de cambiar la suscripción de Azure, el grupo de recursos y la ubicación en la configuración del área de trabajo. También puede elegir **Opciones de Automation**. Haga clic en **Crear**.  
    ![área de trabajo de solución](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Para empezar a usar la solución de administración que ha agregado al área de trabajo, vaya a **Log Analytics** > **Suscripciones** > ***nombre del área de trabajo*** > **Información general**. Se muestra un icono nuevo para la solución de administración. Haga clic en el icono para abrirlo y empezar a usar la solución después de recopilar los datos para la solución.

## <a name="remove-a-management-solution"></a>Eliminación de una solución de administración

1. En [Azure Portal](https://portal.azure.com), vaya a **Log Analytics** > **Suscripciones** > ***nombre del área de trabajo*** y, en la hoja ***nombre del área de trabajo***, haga clic en **Soluciones**.
2. En la lista de soluciones de administración, seleccione la solución que desea quitar.
3. En la hoja de la solución para el área de trabajo, haga clic en **Eliminar**.  
    ![eliminar solución](./media/log-analytics-add-solutions/solution-delete.png)  
4. En el cuadro de diálogo de confirmación, haga clic en **Sí**.

## <a name="offers-and-pricing-tiers"></a>Ofertas y planes de tarifa

En la siguiente tabla, se identifican las soluciones de administración que pertenecen a cada oferta de seguridad y Operations Management. En la tabla también se identifican los planes de tarifa que están disponibles para cada solución de administración. Todas las soluciones de la tabla siguiente están disponibles en Azure Portal y la galería de soluciones en el portal de Log Analytics.

| Solución de administración                                                                       | Oferta                                                                     | Planes de tarifa<sup>1</sup>                                                 | Notas | 
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| Análisis de registros de actividad                                                                    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | 90 días de datos disponibles de forma gratuita |
| [Evaluación de AD](log-analytics-ad-assessment.md)                                           | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Estado de replicación de AD](log-analytics-ad-replication-status.md)                           | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Estado de mantenimiento de los agentes                                                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Administración de alertas](log-analytics-solution-alert-management.md)                            | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Application Insights Connector <br>(versión preliminar)                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Automation Hybrid Worker                                                                  | <ul><li>Automation and Control</li></ul>                                  | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Requiere que el área de trabajo de Log Analytics esté vinculada a una cuenta de Automation |
| [Azure Networking Analytics](log-analytics-azure-networking-analytics.md) <br>(versión preliminar)   | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Azure SQL Analytics <br>(versión preliminar)                                                         | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br>Por&nbsp;nodo&nbsp;(OMS)                                                                          | Requiere que el área de trabajo de Log Analytics esté vinculada a una cuenta de Automation|
| Backup                                                                                    | <ul><li>Insight and Analytics</li></ul>                                   | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Requiere un almacén de copia de seguridad clásico |
| Capacidad y rendimiento <br>(versión preliminar)                                                    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Seguimiento de cambios](log-analytics-change-tracking.md)                                       | <ul><li>Automation and Control</li></ul>                                  | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Requiere que el área de trabajo de Log Analytics esté vinculada a una cuenta de Automation |
| [Contenedores](log-analytics-containers.md)                                                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Supervisión de HDInsight HBase <br>(versión preliminar)                                                  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Análisis de Key Vault](log-analytics-azure-key-vault.md) <br>(versión preliminar)                     | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Evaluación de malware](log-analytics-malware.md)                                            | <ul><li>Seguridad y cumplimiento normativo</li></ul>                                 | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | |
| [Monitor de rendimiento de red](log-analytics-network-performance-monitor.md) <br>(versión preliminar) | <ul><li>Insight and Analytics</li></ul>                                   | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | |
| Office 365 Analytics <br>(versión preliminar)                                                        | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Seguridad y auditoría](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Seguridad&nbsp;y&nbsp;cumplimiento</li></ul>                       | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Se necesita esta solución para recopilar registros de eventos de seguridad |
| [Análisis de Service Fabric](log-analytics-service-fabric.md) <br>(versión preliminar)                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Mapa de servicio](../operations-management-suite/operations-management-suite-service-map.md) <br>(versión preliminar)| <ul><li>Insight and Analytics</li></ul>                       | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Disponible en Este de EE. UU. y Europa Occidental  |
| Site Recovery                                                                             | <ul><li>Insight and Analytics</li></ul>                                   | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Requiere un almacén de Site Recovery clásico |
| [Evaluación de SQL](log-analytics-sql-assessment.md)                                         | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Inicio y detención de máquinas virtuales durante las horas de trabajo<br>(versión preliminar)                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Requiere que el área de trabajo de Log Analytics esté vinculada a una cuenta de Automation |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| [Evaluación de System Center Operations Manager](log-analytics-scom-assessment.md) <br>(versión preliminar) | <ul><li>Insight and Analytics</li><li>Log Analytics</li></ul>         | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Administración de actualizaciones                                                                         | <ul><li>Automation and Control</li></ul>                                  | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Requiere que el área de trabajo de Log Analytics esté vinculada a una cuenta de Automation |
| Upgrade Analytics <br>(versión preliminar)                                                           | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | Sin cargos para datos ni nodos |
| [Supervisión de VMware](log-analytics-vmware.md) <br>(versión preliminar)                                | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Estándar<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(independiente)<br> Por&nbsp;nodo&nbsp;(OMS)   | |
| Wire Data 2.0<br>(versión preliminar)                                                                | <ul><li>Insight and Analytics</li></ul>                                   | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)                                                                         | Disponible en Este de EE. UU. y Europa Occidental |

<sup>1</sup> Los planes de tarifa *Estándar* y *Premium (OMS)* solo están disponibles para los clientes que crearon su área de trabajo de Log Analytics antes del 21 de septiembre de 2016.

### <a name="community-provided-management-solutions"></a>Soluciones de administración proporcionadas por la comunidad

Las soluciones proporcionadas por la comunidad están disponibles en la [galería de plantillas de Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) y directamente de los autores.

| Solución de administración               | Oferta                                                                     | Planes de tarifa                         | Notas | 
| ---                               | ---                                                                       | ---                                   | ---   |
| Todas las soluciones proporcionadas por la comunidad  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Gratuito<br> Por&nbsp;nodo&nbsp;(OMS)     |   Requiere que el área de trabajo de Log Analytics esté vinculada a una cuenta de Automation |




## <a name="data-collection-details"></a>Detalles de la recopilación de datos
En las siguientes tablas se muestran los métodos de recopilación de datos y otros detalles sobre cómo se recopilan datos para los orígenes de datos y las soluciones de administración de Log Analytics. Las tablas se clasifican por ofertas de soluciones, que equivalen a los [planes de tarifa de suscripción](https://go.microsoft.com/fwlink/?linkid=827926). La solución Análisis de registros de actividad está disponible para todos los planes de tarifa de forma gratuita.

El agente de Windows de Log Analytics y el agente de System Center Operations Manager son básicamente iguales. El agente de Windows incluye funcionalidad adicional que permite que conectarse al área de trabajo de OMS y enrutarse a través de un servidor proxy. Si usa un agente de Operations Manager, debe dirigirse como un agente de OMS para comunicarse con OMS. Los agentes de Operations Manager de esta tabla son agentes de OMS que están conectados a Operations Manager. Para información sobre cómo conectar el entorno de Operations Manager existente con OMS, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> El tipo de agente que use determina cómo se envían los datos a OMS, con las condiciones siguientes:
> - Use el agente de Windows o un agente de OMS conectado a Operations Manager.
> - Cuando se requiere Operations Manager, siempre se envían los datos del agente de Operations Manager para la solución a OMS mediante el grupo de administración de Operations Manager. Además, cuando se requiere Operations Manager, la solución solo usa el agente de Operations Manager.
> - Cuando no se necesita Operations Manager y en la tabla se muestra que se envían los datos del agente de Operations Manager a OMS mediante el grupo de administración, los datos del agente de Operations Manager siempre se envían a OMS mediante grupos de administración. Los agentes de Windows omiten el grupo de administración y envían los datos directamente a OMS.
> - Cuando los datos del agente de Operations Manager no se envían mediante un grupo de administración, se envían directamente a OMS, pasando por alto el grupo de administración.

### <a name="insight--analytics--log-analytics"></a>Insight and Analytics/Log Analytics 

| Solución de administración | Plataforma | Microsoft Monitoring Agent | Agente de Operations Manager | Almacenamiento de Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Análisis de registros de actividad | Las tablas de Azure | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | según notificación |
| Evaluación de AD |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 días |
| Estado de replicación de AD |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |5 días |
| Estado de mantenimiento de los agentes | Windows y Linux | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![yes](./media/log-analytics-add-solutions/oms-bullet-green.png) | 1 minuto |
| Administración de alertas (Nagios) |Linux |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |a la llegada |
| Administración de alertas (Zabbix) |Linux |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 minuto |
| Administración de alertas (Operations Manager) |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |3 minutos |
| Application Insights Connector (versión preliminar) | Las tablas de Azure | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | según notificación |
| Análisis de red de Azure (versión preliminar) | Las tablas de Azure | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 minutos |
| Administración de la capacidad |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |cada hora |
| Contenedores | Linux | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 minutos |
| Key Vault Analytics (versión preliminar) |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 minutos |
| Monitor de rendimiento de red | Windows | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | Protocolos de enlace TCP cada 5 segundos; se envían datos cada 3 minutos. |
| Office 365 Analytics (versión preliminar) |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |según notificación |
| Service Fabric Analytics |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 minutos |
| Mapa de servicio | Windows y Linux | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | 15 segundos |
| Evaluación de SQL |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 días |
| SurfaceHub |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |a la llegada |
| Evaluación de System Center Operations Manager (versión preliminar) | Windows | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | siete días |
| Upgrade Analytics (versión preliminar) | Windows | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | 2 días |
| Supervisión de VMware (versión preliminar) | Linux | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 minutos |
| Wire Data |Windows (2012 R2 / 8.1 o posterior) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | 1 minuto |


### <a name="automation--control"></a>Automation & Control

| Solución de administración | Plataforma | Microsoft Monitoring Agent | Agente de Operations Manager | Almacenamiento de Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automation Hybrid Worker | Windows | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | N/D |
| Seguimiento de cambios |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |cada hora |
| Seguimiento de cambios |Linux |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |cada hora |
| Administración de actualizaciones | Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |al menos 2 veces al día y 15 minutos después de instalar una actualización |

### <a name="security--compliance"></a>Seguridad y cumplimiento

| Solución de administración | Plataforma | Microsoft Monitoring Agent | Agente de Operations Manager | Almacenamiento de Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Evaluación antimalware |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |cada hora |
| Seguridad y auditoría<sup>1</sup> | Windows y Linux | ![Algunos](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Algunos](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Algunos](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Algunos](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | varias |

<sup>1</sup>La solución Seguridad y auditoría puede recopilar registros de agentes de Windows, Operations Manager y Linux. Consulte [Orígenes de datos](#data-sources) para información sobre la recopilación de datos para:

- syslog
- Registros de eventos de seguridad de Windows
- Registros de Firewall de Windows
- Registros de eventos de Windows



### <a name="protection--recovery"></a>Protección y recuperación

| Solución de administración | Plataforma | Microsoft Monitoring Agent | Agente de Operations Manager | Almacenamiento de Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | Las tablas de Azure | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | N/D |
| Azure Site Recovery | Las tablas de Azure | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![No](./media/log-analytics-add-solutions/oms-bullet-red.png) | N/D |


### <a name="data-sources"></a>Orígenes de datos


| Origen de datos | Plataforma | Microsoft Monitoring Agent | Agente de Operations Manager | Almacenamiento de Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ETW |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 minutos |
| Registros IIS |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 minutos |
| Puertas de enlace de aplicaciones |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 minutos |
| Grupos de seguridad de red |Windows |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 minutos |
| Contadores de rendimiento |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |mínimo de 10 segundos, según lo programado |
| Contadores de rendimiento |Linux |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |mínimo de 10 segundos, según lo programado |
| syslog |Linux |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |De Almacenamiento de Azure: 10 minutos; del agente: a la llegada |
| Registros de eventos de seguridad de Windows |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |para Almacenamiento de Azure: 10 minutos; para el agente: a la llegada |
| Registros de Firewall de Windows |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |a la llegada |
| Registros de eventos de Windows |Windows |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |![No](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png) |para Almacenamiento de Azure: 1 minuto; para el agente: a la llegada |



## <a name="preview-management-solutions-and-features"></a>Versión preliminar de características y soluciones de administración
Al ejecutar un servicio y seguir los procedimientos de DevOps, podemos asociarnos con clientes para desarrollar soluciones y características.

Durante una versión preliminar privada, damos acceso a un pequeño grupo de clientes a una primera implementación de la característica o solución con objeto de recibir comentarios y efectuar mejoras. Esta implementación temprana posee las características y funciones operativas mínimas.

Nuestro objetivo es probarlas rápidamente para saber qué funciona y qué no. Pasamos varias veces por este mismo proceso hasta que los comentarios de los clientes de la vista previa privada indican que estamos listos para lanzar una vista previa pública.

Durante la vista previa pública, ofrecemos la característica o solución a todos los usuarios para obtener más comentarios y validar nuestra capacidad de escalado y eficacia. Durante esta fase:

* Las características en versión preliminar aparecen en la pestaña Configuración y cualquier usuario puede habilitarlas.
* Las soluciones en versión preliminar se agregan mediante la galería o un script.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>¿Qué hay que saber sobre la versión preliminar de las características y soluciones?
Estamos entusiasmados con las nuevas características y soluciones de administración, y nos encanta colaborar con los usuarios para desarrollarlas.

Las soluciones y características en versión preliminar no son adecuadas para todos los usuarios. Antes de solicitar unirse a una versión preliminar privada o de habilitar una versión preliminar pública, confirme que no le importa trabajar con algo que está en fase de desarrollo.

Cuando habilita una característica en versión preliminar a través del portal, aparece una advertencia para recordarle que la característica está en versión preliminar.

#### <a name="for-both-private-and-public-preview"></a>Común a las versiones preliminares *públicas* y *privadas*
Las siguientes afirmaciones pueden aplicarse a las vistas previas tanto públicas como privadas:

* Las cosas no siempre funcionan como deben.
  * Las incidencias pueden ir desde una pequeña minucia hasta que algo directamente no funcione.
* Existe la posibilidad de que la versión preliminar tenga un impacto negativo en sus sistemas o su entorno.
  * Intentamos evitar que se produzcan situaciones adversas en los sistemas que se usan con OMS, pero a veces surgen imprevistos.
* Se pueden producir pérdidas de datos y daños.
* Puede que le pidamos que recopile registros de diagnóstico u otros datos como ayuda para solucionar problemas.
* La característica o solución se puede quitar (temporal o permanentemente).
  * Según la información que recabemos durante la versión preliminar, podemos decidir no lanzar la característica o solución.
* Puede que las vistas previas no funcionen o no se hayan probado con todas las configuraciones, a raíz de lo cual podremos limitar:
  * Los sistemas operativos que se pueden usar (por ejemplo, puede que una característica solo sea válida en Linux mientras está en versión preliminar).
  * El tipo de agente (MMA, Operations Manager) que se puede usar (por ejemplo, es posible que una característica no funcione con Operations Manager en la versión preliminar).  
* Las versiones preliminares de características y soluciones no están cubiertas por el acuerdo de nivel de servicio.
* El uso de las características en versión preliminar conllevará gastos.
* Puede que las características o las funcionalidades que necesite para que la característica o solución le resulte útil no estén o estén incompletas.
* Puede que las características o soluciones no estén disponibles en todas las regiones.
* Puede que las características o soluciones no estén localizadas.
* Puede que las características o soluciones tengan un límite en cuanto al número de clientes o dispositivos que pueden usarlas.
* Puede que haya que usar scripts para realizar la configuración y habilitar la característica o la solución.
* La interfaz de usuario está incompleta y puede variar de un día para otro.
* Puede que las versiones preliminares públicas no sean adecuadas en sistemas críticos o de producción.

#### <a name="for-private-preview"></a>Vistas previas *privadas*
Aparte de los puntos anteriores, los que siguen son específicos de las vistas previas privadas:

* Esperamos que nos envíe comentarios sobre su experiencia para que podamos mejorar la solución o la característica.
* Puede que nos pongamos en contacto con usted para pedirle comentarios mediante encuestas, llamadas de teléfono o correo electrónico.
* No todo funcionará siempre como debiera.
* Puede que requiramos un acuerdo de confidencialidad para participar o que incluyamos contenido confidencial.
  * Antes de crear entradas en blog, tweets o de comunicarse de algún otro modo con terceros, acuda al administrador de programas responsable de la versión preliminar para conocer todas las restricciones sobre la divulgación de contenido.
* No ejecute la versión preliminar en sistemas críticos o de producción.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>¿Cómo se puede obtener acceso a las características y soluciones de la vista previa privada?
Invitamos a los clientes a las vistas previas privadas de diversas maneras, dependiendo de la vista previa.

* Si responde las encuestas de cliente mensuales y nos permite llevar un seguimiento al respecto, aumentarán las posibilidades de que se le invite a una vista previa privada.
* El equipo de cuentas de Microsoft puede elegirle.
* Puede registrarse conforme a la información publicada en [msopsmgmt](https://twitter.com/msopsmgmt).
* Puede registrarse basándose en detalles compartidos en eventos comunitarios: búsquenos en encuentros, conferencias y en comunidades en línea.

## <a name="next-steps"></a>Pasos siguientes
* [Busque en los registros](log-analytics-log-searches.md) para ver información detallada recopilada por las soluciones de administración.



<!--HONumber=Jan17_HO3-->


