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
ms.date: 05/26/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 1152893c4c686fa69f7e06ffa7e2d2b2272bc772
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


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

### <a name="service-fabric-infrastructure-events"></a>Eventos de la infraestructura de Service Fabric
Service Fabric genera una serie de registros de serie a través de [LTTng](http://lttng.org), incluidos eventos de runtime o eventos operativos. Estos registros se almacenan en la ubicación que la plantilla de Resource Manager del clúster especifica. Para obtener o establecer los detalles de la cuenta de almacenamiento, busque la etiqueta **AzureTableWinFabETWQueryable** y busque **StoreConnectionString**.

### <a name="application-events"></a>Eventos de aplicación
 Eventos emitidos desde el código de los servicios y las aplicaciones especificados por el usuario al instrumentar el software. Puede utilizar cualquier solución de registro que escriba archivos de registro basados en texto, por ejemplo, LTTng. Para obtener más información, consulte la documentación de LTTng sobre el seguimiento de la aplicación.

[Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="deploy-the-diagnostics-extension"></a>Implementación de la extensión de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían en función de si se utiliza Azure Portal o Azure Resource Manager.

Para implementar la extensión de Diagnósticos en las máquinas virtuales del clúster como parte de la creación de dicho clúster, establezca **Diagnósticos** en **Activado**. Después de crear el clúster, no puede cambiar esta configuración mediante el portal.

Después, configure Diagnósticos de Azure de Linux (LAD) para recopilar los archivos y colocarlos en su cuenta de almacenamiento. En este proceso se explica en el escenario 3 ("Carga de sus propios archivos de registro") en el artículo sobre cómo [utilizar LAD para supervisar y diagnosticar máquinas virtuales Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Con este proceso puede acceder a los seguimientos. Puede cargar los seguimientos en un visualizador de su elección.

También puede implementar la extensión de Diagnósticos mediante Azure Resource Manager. El proceso es el mismo para Windows y Linux, y está documentado para los clústeres de Windows en [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md).

También puede usar Operations Management Suite, como se describe en [Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Análisis de registros de Operations Management Suite con Linux).

Una vez finalizada esta configuración, el agente de LAD supervisa los archivos de registro especificados. Siempre que se anexa una nueva línea al archivo, se crea una entrada syslog que se envía al almacenamiento especificado.

## <a name="next-steps"></a>Pasos siguientes

Compruebe la [documentación de LTTng](http://lttng.org/docs) y el [uso de LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para obtener información más detallada sobre qué eventos debe examinar durante la solución de problemas.
