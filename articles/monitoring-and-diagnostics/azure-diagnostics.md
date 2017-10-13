---
title: "Introducción a Diagnósticos de Microsoft Azure | Microsoft Docs"
description: "Use Diagnósticos de Azure para realizar tareas de depuración, medición de rendimiento, supervisión y análisis de tráfico en servicios en la nube, en máquinas virtuales y en Service Fabric"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: 0c6e4d9d2a3744f607b72364f3944c700acd070c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-diagnostics"></a>¿Qué es Diagnósticos de Azure?
Diagnósticos de Azure es la funcionalidad de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede utilizar la extensión de diagnóstico desde un número de orígenes diferentes. Actualmente se admiten los roles web y de trabajo del Servicio en la nube de Azure, las máquinas virtuales de Azure que ejecutan Microsoft Windows y Service Fabric. Otros servicios de Azure tienen su propio diagnóstico independiente.

## <a name="data-you-can-collect"></a>Datos que puede recopilar
Diagnósticos de Azure puede recopilar los tipos de datos siguientes:

| Origen de datos | Description |
| --- | --- |
| Contadores de rendimiento |Sistema operativo y contadores de rendimiento personalizados |
| Registros de aplicación |Seguimiento de mensajes escritos por la aplicación |
| Registros de eventos de Windows |Información enviada al sistema de registro de eventos de Windows |
| Origen de eventos de .NET |Eventos de escritura de código mediante la clase [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) de .NET |
| Registros IIS |Información sobre los sitios web de IIS |
| ETW basado en manifiesto |Eventos de Seguimiento de eventos para Windows generados por cualquier proceso |
| Volcados de memoria |Información sobre el estado del proceso en caso de bloqueo de una aplicación |
| Registros de errores personalizados |Registros creados por su aplicación o servicio |
| Registros de infraestructura de diagnóstico de Azure |Información sobre Diagnósticos |

La extensión de diagnósticos de Azure puede transferir estos datos a una cuenta de Azure Storage o enviarlos a servicios como [Application Insights](../application-insights/app-insights-cloudservices.md). Estos datos se pueden usar para depurar y solucionar problemas, medir el rendimiento, supervisar el uso de los recursos, analizar el tráfico, planear la capacidad y realizar auditorías.

## <a name="versioning"></a>Control de versiones
Consulte el [historial de versiones de Diagnósticos de Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Pasos siguientes
Seleccione el servicio sobre el cual intenta recopilar diagnósticos y utilice los siguientes artículos para comenzar. Utilice los vínculos de diagnósticos generales de Azure como referencia para tareas específicas.

## <a name="web-apps"></a>Aplicaciones web
Tenga en cuenta que las aplicaciones web no utilizan Diagnósticos de Azure. Puede encontrar información equivalente en [Web Apps](../app-service/web-sites-enable-diagnostic-log.md).

## <a name="cloud-services-using-azure-diagnostics"></a>Servicios en la nube que usan Diagnósticos de Azure
* Si utiliza Visual Studio, consulte el artículo sobre el [uso de Visual Studio para rastrear una aplicación de Cloud Services](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para comenzar. De lo contrario, consulte
* [Supervisión de Cloud Services con Diagnósticos de Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configuración de Diagnósticos de Azure una aplicación de Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md)

Para temas más avanzados, consulte

* [Uso de Diagnósticos de Azure con Application Insights para Cloud Services](../application-insights/app-insights-cloudservices.md)
* [Seguimiento del flujo en una aplicación de Cloud Services con Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Uso de PowerShell para configurar los diagnósticos en Cloud Services](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Máquinas virtuales que usan Diagnósticos de Azure
* Si utiliza Visual Studio, consulte el artículo sobre el [uso de Visual Studio para rastrear Azure Virtual Machines](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para comenzar. De lo contrario, consulte
* [Configuración de Diagnósticos de Azure en Azure Virtual Machine](../virtual-machines-dotnet-diagnostics.md)

Para temas más avanzados, consulte

* [Uso de PowerShell para configurar los diagnósticos en Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante una plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric con Diagnósticos de Azure
Empiece por el artículo sobre la [supervisión de una aplicación de Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Cuando acceda a este artículo, verá muchos otros artículos de diagnósticos de Service Fabric disponibles en el árbol de navegación de la izquierda.

## <a name="general-azure-diagnostics-articles"></a>Artículos generales de Diagnósticos de Azure
* [Configuración del esquema de Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) : obtenga información sobre cómo cambiar el archivo de esquema para recopilar y redirigir los datos de diagnóstico. Tenga en cuenta que también puede utilizar Visual Studio para cambiar el archivo del esquema.
* [Almacenamiento de los datos de Diagnóstico de Azure en Azure Storage](../cloud-services/cloud-services-dotnet-diagnostics-storage.md): conozca los nombres de las tablas y los blobs donde se escriben los datos de diagnóstico.
* Aprenda a [utilizar los contadores de rendimiento en Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
* Aprenda a [redirigir la información de diagnóstico de Azure a Application Insights](azure-diagnostics-configure-application-insights.md).
* Si tiene problemas al iniciar los diagnósticos o al buscar datos en las tablas de Azure Storage, consulte [Solución de problemas de Diagnósticos de Azure](azure-diagnostics-troubleshooting.md).
