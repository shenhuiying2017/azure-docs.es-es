---
title: Introducción a la extensión Azure Diagnostics | Microsoft Docs
description: Use Diagnósticos de Azure para realizar tareas de depuración, medición de rendimiento, supervisión y análisis de tráfico en servicios en la nube, en máquinas virtuales y en Service Fabric
services: multiple
documentationcenter: .net
author: rboucher
manager: ''
editor: ''
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/01/2018
ms.author: robb
ms.openlocfilehash: daeaddefa461e71fcc62af4efc4fb7084b237cf9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886402"
---
# <a name="what-is-azure-diagnostics-extension"></a>Qué es la extensión Azure Diagnostics
La extensión Azure Diagnostics es un agente dentro de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede utilizar la extensión de diagnóstico desde un número de orígenes diferentes. Actualmente se admiten los roles web y de trabajo de Azure Cloud Services, las máquinas virtuales, los conjuntos de escalado de máquinas virtuales y Service Fabric. Otros servicios de Azure tienen métodos de diagnóstico diferentes. Consulte [Introducción a la supervisión en Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Agente Linux
Una [versión Linux de la extensión](../virtual-machines/linux/diagnostic-extension.md) está disponible para máquinas virtuales que ejecutan Linux. Las estadísticas recopiladas y los comportamientos varían frente a la versión Windows. 

## <a name="data-you-can-collect"></a>Datos que puede recopilar
La extensión Azure Diagnostics puede recopilar los siguientes tipos de datos:

| Origen de datos | DESCRIPCIÓN |
| --- | --- |
| contadores de rendimiento |Sistema operativo y contadores de rendimiento personalizados |
| Registros de aplicación |Seguimiento de mensajes escritos por la aplicación |
| Registros de eventos de Windows |Información enviada al sistema de registro de eventos de Windows |
| Origen de eventos de .NET |Eventos de escritura de código mediante la clase [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) de .NET |
| Registros IIS |Información sobre los sitios web de IIS |
| ETW basado en manifiesto |Eventos de Seguimiento de eventos para Windows generados por cualquier proceso |
| Volcados de memoria |Información sobre el estado del proceso en caso de bloqueo de una aplicación |
| Registros de errores personalizados |Registros creados por su aplicación o servicio |
| Registros de infraestructura de diagnóstico de Azure |Información sobre Diagnósticos |

## <a name="data-storage"></a>Almacenamiento de datos
La extensión almacena sus datos en una [cuenta de Azure Storage](azure-diagnostics-storage.md) que especifique. 

También puede enviarlos a [Application Insights](../application-insights/app-insights-cloudservices.md). Otra opción es transmitirlos al [Centro de eventos](../event-hubs/event-hubs-what-is-event-hubs.md), que le permite enviarlos a servicios de supervisión que no son de Azure. 


## <a name="versioning-and-configuration-schema"></a>Esquema de configuración y control de versiones
Consulte [Historial y versiones del esquema de configuración de la extensión Azure Diagnostics](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Pasos siguientes
Seleccione el servicio sobre el cual intenta recopilar diagnósticos y utilice los siguientes artículos para comenzar. Utilice los vínculos de diagnósticos generales de Azure como referencia para tareas específicas.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services que usan Diagnósticos de Azure
* Si utiliza Visual Studio, consulte el artículo sobre el [uso de Visual Studio para rastrear una aplicación de Cloud Services](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para comenzar. De lo contrario, consulte
* [Supervisión de Cloud Services con Diagnósticos de Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configuración de Diagnósticos de Azure una aplicación de Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md)

Para temas más avanzados, consulte

* [Uso de Diagnósticos de Azure con Application Insights para Cloud Services](../application-insights/app-insights-cloudservices.md)
* [Seguimiento del flujo en una aplicación de Cloud Services con Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Uso de PowerShell para configurar los diagnósticos en Cloud Services](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Si utiliza Visual Studio, consulte el artículo sobre el [uso de Visual Studio para rastrear Azure Virtual Machines](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para comenzar. De lo contrario, consulte
* [Configuración de Diagnósticos de Azure en Azure Virtual Machine](../virtual-machines-dotnet-diagnostics.md)

Para temas más avanzados, consulte

* [Uso de PowerShell para configurar los diagnósticos en Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante una plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Empiece por el artículo sobre la [supervisión de una aplicación de Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Cuando acceda a este artículo, verá muchos otros artículos de diagnósticos de Service Fabric disponibles en el árbol de navegación de la izquierda.

## <a name="general-articles"></a>Artículos generales
* Aprenda a [utilizar los contadores de rendimiento en Diagnósticos de Azure](../cloud-services/diagnostics-performance-counters.md).
* Si tiene problemas al iniciar los diagnósticos o al buscar datos en las tablas de Azure Storage, consulte [Solución de problemas de Azure Diagnostics](azure-diagnostics-troubleshooting.md).
