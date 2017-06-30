---
title: "Recopilación de registros con Diagnósticos de Azure de Linux | Microsoft Docs"
description: "En este artículo se describe cómo configurar Diagnósticos de Azure para recopilar registros de un clúster de Service Fabric de Linux que se ejecute en Azure."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a160d469-8b7d-4560-82dd-8500db34a44a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 46b62b0ebc5b81241815e3b5b4fa3fe275b88af1
ms.contentlocale: es-es
ms.lasthandoff: 03/27/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Recopilación de registros con Diagnósticos de Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas, ya estén en sus servicios, sus aplicaciones o en el propio clúster. Uno de los métodos para cargar y recopilar registros es usar la extensión de Diagnósticos de Azure, que carga registros en Azure Storage, Azure Application Insights o Azure Event Hubs. También puede leer los eventos de almacenamiento o Event Hubs y colocarlos en un producto como [Log Analytics](../log-analytics/log-analytics-service-fabric.md) u otra solución de análisis de registro. [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) tiene integrado un servicio de análisis y búsqueda de registros integral.

## <a name="log-sources-that-you-might-want-to-collect"></a>Orígenes de registros que puede recopilar
* **Registros de Service Fabric:** emitidos por la plataforma mediante [LTTng](http://lttng.org) y cargados en la cuenta de almacenamiento. Los registros pueden ser eventos operativos o eventos de tiempo de ejecución que la plataforma emite. Estos registros se almacenan en la ubicación que el manifiesto de clúster especifica. (Para obtener los detalles de la cuenta de almacenamiento, busque la etiqueta **AzureTableWinFabETWQueryable** y busque **StoreConnectionString**).
* **Eventos de aplicación:** eventos emitidos desde el código de servicios. Puede utilizar cualquier solución de registro que escriba archivos de registro basados en texto, por ejemplo, LTTng. Para obtener más información, consulte la documentación de LTTng sobre el seguimiento de la aplicación.  

## <a name="deploy-the-diagnostics-extension"></a>Implementación de la extensión de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían en función de si se utiliza Azure Portal o Azure Resource Manager.

Para implementar la extensión de Diagnósticos en las máquinas virtuales del clúster como parte de la creación de dicho clúster, establezca **Diagnósticos** en **Activado**. Después de crear el clúster, no puede cambiar esta configuración mediante el portal.

Después, configure Diagnósticos de Azure de Linux (LAD) para recopilar los archivos y colocarlos en su cuenta de almacenamiento. En este proceso se explica en el escenario 3 ("Carga de sus propios archivos de registro") en el artículo sobre cómo [utilizar LAD para supervisar y diagnosticar máquinas virtuales Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Con este proceso puede acceder a los seguimientos. Puede cargar los seguimientos en un visualizador de su elección.

También puede implementar la extensión de Diagnósticos mediante Azure Resource Manager. El proceso es el mismo para Windows y Linux, y está documentado para los clústeres de Windows en [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md).

También puede usar Operations Management Suite, como se describe en [Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Análisis de registros de Operations Management Suite con Linux).

Una vez finalizada esta configuración, el agente de LAD supervisa los archivos de registro especificados. Siempre que se anexa una nueva línea al archivo, se crea una entrada syslog que se envía al almacenamiento especificado.

## <a name="next-steps"></a>Pasos siguientes
Compruebe la [documentación de LTTng](http://lttng.org/docs) y el [uso de LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para obtener información más detallada sobre qué eventos debe examinar durante la solución de problemas.


