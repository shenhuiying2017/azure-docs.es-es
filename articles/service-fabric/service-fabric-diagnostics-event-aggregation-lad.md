---
title: "Agregación de eventos de Azure Service Fabric con Linux Azure Diagnostics | Microsoft Docs"
description: "Obtenga información sobre la agregación y la recopilación de eventos con LAD para la supervisión y el diagnóstico de clústeres de Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b70780b2e1d169aced3412f6fe6d13ad4dab82be
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Recopilación y agregación de eventos con Linux Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas del clúster o de las aplicaciones y los servicios que se ejecutan en ese clúster.

Uno de los métodos para cargar y recopilar registros es usar la extensión Linux Azure Diagnostics (LAD), que carga registros en Azure Storage, y también tiene la opción de enviar registros a Azure Application Insights o Event Hubs. También puede usar un proceso externo para leer los eventos desde el almacenamiento y colocarlos en un producto de plataforma de análisis, como [Log Analytics de OMS](../log-analytics/log-analytics-service-fabric.md) u otra solución de análisis de registros.

## <a name="log-and-event-sources"></a>Orígenes de eventos y registros

### <a name="service-fabric-platform-events"></a>Eventos de plataforma de Service Fabric
Service Fabric genera una serie de registros de serie a través de [LTTng](http://lttng.org), incluidos eventos de runtime o eventos operativos. Estos registros se almacenan en la ubicación que la plantilla de Resource Manager del clúster especifica. Para obtener o establecer los detalles de la cuenta de almacenamiento, busque la etiqueta **AzureTableWinFabETWQueryable** y busque **StoreConnectionString**.

### <a name="application-events"></a>Eventos de aplicación
 Eventos emitidos desde el código de los servicios y las aplicaciones especificados por el usuario al instrumentar el software. Puede utilizar cualquier solución de registro que escriba archivos de registro basados en texto, por ejemplo, LTTng. Para obtener más información, consulte la documentación de LTTng sobre el seguimiento de la aplicación.

[Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>Implementación de la extensión de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. 

Para implementar la extensión de Diagnósticos en las máquinas virtuales del clúster como parte de la creación de dicho clúster, establezca **Diagnósticos** en **Activado**. Después de crear el clúster, no se puede cambiar esta configuración mediante el portal, por lo que tendrá que realizar los cambios apropiados en la plantilla de Resource Manager.

Esto configura el agente LAD para supervisar los archivos de registro especificados. Siempre que se anexa una nueva línea al archivo, se crea una entrada syslog que se envía al almacenamiento (tabla) especificado.


## <a name="next-steps"></a>Pasos siguientes

1. Compruebe la [documentación de LTTng](http://lttng.org/docs) y el [uso de LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para obtener información más detallada sobre qué eventos debe examinar durante la solución de problemas.
2. [Configuración del agente de OMS](service-fabric-diagnostics-event-analysis-oms.md) con el fin de recopilar métricas, supervisar contenedores implementados en el clúster y visualizar los registros 