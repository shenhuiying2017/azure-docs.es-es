---
title: "Información general sobre Azure Monitor | Microsoft Docs"
description: "Azure Monitor recopila estadísticas para usarse en procesos de alertas, webhooks, escalado automático y automatización. En el artículo también se muestran otras opciones de supervisión de Microsoft."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: robb
ms.custom: mvc
ms.openlocfilehash: ed9ace24778f000b42013cc0ce4d7dacf4a1d4fb
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="overview-of-azure-monitor"></a>Información general sobre Azure Monitor
En este artículo se proporciona información general sobre el servicio Azure Monitor de Microsoft Azure. Se describe la utilidad de Azure Monitor y se ofrecen vínculos a información adicional sobre cómo usar este servicio.  Si prefiere un vídeo de introducción, consulte los vínculos de pasos siguientes al final de este artículo. 

## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Azure Monitor y otros productos de supervisión de Microsoft
Azure Monitor ofrece registros y métricas de infraestructuras a nivel básico para la mayoría de los servicios de Microsoft Azure. Los servicios de Azure que aún no han colocado sus datos en Azure Monitor lo harán allí en un futuro.

Microsoft entrega productos y servicios adicionales que ofrecen funcionalidades de supervisión adicionales para desarrolladores, DevOps u operaciones de TI que también tienen instalaciones locales. Para obtener una introducción e información sobre cómo estos distintos productos y servicios funcionan de forma conjunta, vea [Supervisión en Microsoft Azure](monitoring-overview.md).

## <a name="portal-overview-page"></a>Página de Información general del portal

Azure Monitor tiene una página de aterrizaje que ayuda a los usuarios a lo siguiente: 
- Comprender las funcionalidades de supervisión que ofrece Azure.
- Detectar, configurar e incorporar la plataforma y las funcionalidades de supervisión premium de Azure.

Si bien el servicio Azure Monitor está publicado, la página de aterrizaje de información general se encuentra en versión preliminar. 

La página es un punto inicial para la navegación, incluida la incorporación. Muestra problemas pertinentes creados desde distintos servicios y permite al usuario navegar a ellos en contexto.
 
![Modelo para la supervisión y el diagnóstico de recursos que no son de proceso](./media/monitoring-overview-azure-monitor/monitor-overview-ux2.png)

Cuando abre la página, puede seleccionar entre las suscripciones en las que tiene acceso de lectura. Para una suscripción seleccionada, puede ver:

- **Alertas desencadenadas y orígenes de alertas**: en esta tabla se muestran los recuentos de resumen, los orígenes de alertas y cuántas veces se activaron las alertas durante el período seleccionado. Se aplica a las alertas de métricas y de registro de actividad. *<Editar: Alerts (versión preliminar) con experiencia unificada también se muestra para todas las alertas: eventos, métricas y registros>*
- **Errores del registro de actividad**: si cualquiera de los recursos de Azure registra eventos con una gravedad de nivel de error, puede ver un recuento de alto nivel y hacer clic en la página del registro de actividad para investigar cada evento.
- **Azure Service Health**: puede ver un recuento de los problemas del servicio de Service Health, eventos de mantenimiento planeados y avisos de mantenimiento. Azure Service Health proporciona información personalizada cuando los problemas en la infraestructura de Azure afectan a los servicios.  Para obtener más información, consulte [Azure Service Health](../service-health/service-health-overview.md).  
- **Application Insights**: vea KPI para cada recurso de AppInsights en la suscripción actual. Los KPI están optimizados para la supervisión de aplicaciones del lado servidor a través de las aplicaciones web de ASP.NET, Java, Node y tipos de aplicación General. Los KPI incluyen métricas para la tasa de solicitud, la duración de respuesta, la tasa de error y el % de disponibilidad. 

Si no se ha incorporado en Log Analytics ni Application Insights, o si no ha configurado ninguna alerta de Azure en la suscripción actual, la página proporciona vínculos para comenzar el proceso de incorporación.



## <a name="azure-monitor-sources---compute-subset"></a>Orígenes de Azure Monitor: subconjunto Compute

![Modelo para la supervisión y el diagnóstico de recursos que no son de proceso](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)


Los servicios de Compute aquí incluyen: 
- Cloud Services 
- Virtual Machines 
- Conjuntos de escalado de máquina virtual 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicación: registros de diagnóstico, registros de aplicación y métricas
Las aplicaciones se pueden ejecutar sobre el SO invitado en el modelo de proceso. Emiten su propio conjunto de registros y métricas. Azure Monitor se basa en la extensión Diagnósticos de Azure (Windows o Linux) para recopilar la mayoría de los registros y las métricas a nivel de aplicación. Estos tipos incluyen:

* Contadores de rendimiento
* Registros de aplicación
* Registros de eventos de Windows
* Origen de eventos de .NET
* Registros IIS
* ETW basado en manifiesto
* Volcados de memoria
* Registros de errores personalizados

Sin la extensión de diagnósticos, solo se encuentran disponibles algunas métricas, como el uso de CPU. 

### <a name="host-and-guest-vm-metrics"></a>Métricas de máquinas virtuales host e invitadas
Los recursos de procesos enumerados anteriormente tienen una máquina virtual host dedicada y un SO invitado con el que interactúan. La máquina virtual host y el SO invitado son los equivalentes a la máquina virtual raíz y a la máquina virtual invitada del modelo de hipervisor de Hyper-V. Puede recopilar métricas en ambos. También puede recopilar registros de diagnóstico en el SO invitado.   

### <a name="activity-log"></a>Registro de actividad
Puede buscar el registro de actividad (anteriormente llamado registro operativo o de actividad) para obtener información sobre los recursos, tal y como se ven en la infraestructura de Azure. El registro contiene información tal como el número de veces que se crean o se destruye recursos.  Para más información, vea [Información general sobre el registro de actividad de Azure](monitoring-overview-activity-logs.md). 

## <a name="azure-monitor-sources---everything-else"></a>Orígenes de Azure Monitor: todo el resto

![Modelo para la supervisión y el diagnóstico de recursos que son de proceso](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>Recurso: métricas y registros de diagnóstico
Las métricas y los registros de diagnóstico que se pueden recopilar varían según el tipo de recurso. Por ejemplo, Web Apps proporciona estadísticas sobre la E/S de disco y % de CPU. Esas métricas no existen para una cola de Service Bus, sino métricas como el tamaño de cola y el rendimiento de mensajes. Una lista de métricas que se pueden recopilar para cada recurso está disponible en [métricas admitidas](monitoring-supported-metrics.md). 

### <a name="host-and-guest-vm-metrics"></a>Métricas de máquinas virtuales host e invitadas
No hay necesariamente una asignación 1:1 entre los recursos y una máquina virtual host o una máquina virtual invitada específica; por tanto, las métricas no están disponibles.

### <a name="activity-log"></a>Registro de actividad
El registro de actividad es el mismo que en el caso de los recursos de procesos.  

## <a name="uses-for-monitoring-data"></a>Usos de los datos de supervisión
Después de recopilar los datos, puede hacer lo siguiente con ellos en Azure Monitor.

### <a name="route"></a>Enrutar
Puede transmitir los datos de supervisión a otras ubicaciones. 

Algunos ejemplos son:

- Enviarlos a Application Insights para poder usar sus herramientas de visualización y análisis mejoradas.
- Enviarlos a Event Hubs para poder enrutarlos a herramientas de terceros. 

### <a name="store-and-archive"></a>Almacenamiento y archivado
Algunos datos de supervisión ya están almacenados y disponibles en Azure Monitor durante un período de tiempo. 
- Las métricas se almacenan durante 30 días. 
- Las entradas de registros de actividad se almacenan durante 90 días. 
- Los registros de diagnóstico no se almacenan. 

Si desea almacenar los datos durante más tiempo que el indicado anteriormente, puede usar Azure Storage. Los datos de supervisión se mantienen en la cuenta de almacenamiento en función de la directiva de retención definida. No tiene que pagar por el espacio que los datos ocupan en Azure Storage. 

Algunas formas de usar estos datos:

- Una vez escritos, puede hacer que otras herramientas, dentro o fuera de Azure, los lean y los procesen.
- Los datos se descargan localmente para un archivo local o se cambia su directiva de retención en la nube para conservar los datos durante largos períodos de tiempo.  
- Los datos se dejan en Azure Storage indefinidamente a efectos de archivado. 

### <a name="query"></a>Consultar
Puede usar la API de REST de Azure Monitor, comandos de la interfaz de la línea de comandos (CLI) multiplataforma, cmdlets de PowerShell o el SDK de .NET para acceder a los datos en el sistema o en Azure Storage.

Algunos ejemplos son:

* Obtener datos para una aplicación de supervisión personalizada que ha escrito.
* Crear consultas personalizadas y enviar los datos a una aplicación de terceros.

### <a name="visualize"></a>Visualizar
Ver los datos de supervisión en gráficos y diagramas ayuda a encontrar tendencias más rápidamente que si se buscan en los propios datos.  

Algunos métodos de visualización incluyen:

* Uso de Azure Portal
* Enrutamiento de datos a Azure Application Insights
* Enrutamiento de datos a Microsoft PowerBI
* Enrutamiento de datos a una herramienta de visualización de terceros mediante streaming en vivo o haciendo que la herramienta lea un archivo de almacenamiento de Azure


### <a name="automate"></a>Automatizar
> [!NOTE]
> Como parte de la evolución en curso de las alertas en Microsoft Azure, ahora hay una versión preliminar de una experiencia unificada de ellas. Más detalles sobre las [alertas de Azure (versión preliminar)](monitoring-overview-unified-alerts.md).

En las alertas de Azure estándar, puede usar datos de supervisión para desencadenar alertas o, incluso, procesos enteros. Algunos ejemplos son:

* Usar los datos para aumentar o reducir automáticamente el número de instancias de proceso según la carga de la aplicación.
* Enviar mensajes de correo electrónico cuando una métrica cruza un umbral predeterminado.
* Llamar a una dirección URL web (webhook) para ejecutar una acción en un sistema externo a Azure.
* Iniciar un runbook en Automatización de Azure para realizar diversas tareas.

## <a name="methods-of-accessing-azure-monitor"></a>Métodos de acceso a Azure Monitor
En general, puede manipular el seguimiento, el enrutamiento y la recuperación de los datos mediante uno de los métodos siguientes. No todos los métodos están disponibles para todas las acciones o tipos de datos.

* [Portal de Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [Interfaz de línea de comandos (CLI) multiplataforma](insights-cli-samples.md)
* [API de REST](https://docs.microsoft.com/rest/api/monitor/)
* [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>pasos siguientes
Más información acerca de
- Hay un tutorial en vídeo de Azure Monitor en  
[Introducción a Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor) 
- Puede consultar un vídeo donde se explica un escenario en el que puede usar Azure Monitor en [Explore Microsoft Azure monitoring and diagnostics](https://channel9.msdn.com/events/Ignite/2016/BRK2234) (Información sobre supervisión y Microsoft Azure Diagnostics) y en un [vídeo sobre Azure Monitor de Ignite 2016](https://myignite.microsoft.com/videos/4977).
- Haga un recorrido por la interfaz de Azure Monitor en [Introducción a Azure Monitor](monitoring-get-started.md)
- Configure las [extensiones de Diagnósticos de Azure](../azure-diagnostics.md) si intenta diagnosticar problemas en su aplicación de Cloud Services, Virtual Machines, Conjuntos de escalado de máquinas virtuales o Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) : si está intentando diagnosticar problemas en su aplicación web de App Service.
- [Solución de problemas de Azure Storage](../storage/common/storage-e2e-troubleshooting.md) : si usa Blob Storage, Table Storage o Queue Storage.
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) y [Operations Management Suite](https://www.microsoft.com/oms/)
