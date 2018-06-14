---
title: 'Azure Service Fabric: Supervisión del rendimiento con la extensión de Microsoft Azure Diagnostics | Microsoft Docs'
description: Utilice Windows Azure Diagnostics para recopilar contadores de rendimiento para los clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: srrengar
ms.openlocfilehash: 5c8c1f107e9e70e72c48ea93ef211b2a760ffb5c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213238"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Supervisión del rendimiento con la extensión de Microsoft Azure Diagnostics

Este documento incluye los pasos necesarios para configurar los contadores de rendimiento a través de la extensión de Microsoft Azure Diagnostics para los clústeres de Windows. Para los clústeres de Linux, configure el [agente de OMS](service-fabric-diagnostics-oms-agent.md) para recopilar contadores de rendimiento para los nodos. 

 > [!NOTE]
> Se debe implementar la extensión de Microsoft Azure Diagnostics en el clúster para que estos pasos funcionen. Si no está configurado, diríjase a [Recopilación y agregación de eventos con Azure Diagnostics de Windows](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

## <a name="collect-performance-counters-via-the-wadcfg"></a>Recopilación de contadores de rendimiento a través de WadCfg

Para recopilar contadores de rendimiento a través de WAD, debe modificar la configuración correctamente en la plantilla de Resource Manager del clúster. Siga estos pasos para agregar un contador de rendimiento que desee que recopile la plantilla y ejecute una actualización de recursos de Resource Manager.

1. Busque la configuración de Microsoft Azure Diagnostics en la plantilla del clúster: buscar `WadCfg`. Se agregarán contadores de rendimiento que recopilar en `DiagnosticMonitorConfiguration`.

2. Establezca la configuración para recopilar los contadores de rendimiento mediante la adición de la sección siguiente a `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` define la frecuencia con la que se transfieren los valores de los contadores que se recopilan a la tabla de almacenamiento de Azure y a cualquier receptor configurado. 

3. Agregue los contadores de rendimiento que desee recopilar a `PerformanceCounterConfiguration`, que se declaró en el paso anterior. Cada contador que desee recopilar se define con `counterSpecifier`, `sampleRate`, `unit`, `annotation` y cualquier `sinks` relevante.

Este es un ejemplo de una configuración con el contador para la *Tiempo total de procesador* (la cantidad de tiempo que la CPU estuvo en uso para las operaciones de procesamiento) y las *Invocaciones por segundo del método de actor de Service Fabric*, uno de los contadores de rendimiento personalizados de Service Fabric. Consulte [Lista de eventos y contadores de rendimiento](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) y [Lista de contadores de rendimiento](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) para obtener una lista completa de los contadores de rendimiento personalizados de Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 La velocidad de muestreo para el contador se puede modificar según sus necesidades. El formato es `PT<time><unit>`, por lo que si desea que el contador realice la recopilación cada segundo, debe establecer `"sampleRate": "PT15S"`.

 >[!NOTE]
 >Aunque puede utilizar `*` para especificar grupos de contadores de rendimiento con nombres similares, el envío de todos los contadores a través de un receptor (para Application Insights) requiere que se declaren individualmente. 

4. Una vez que haya agregado los contadores de rendimiento adecuados que se deben recopilar, debe actualizar el recurso de clúster de manera que estos cambios se reflejan en el clúster de ejecución. Guarde su `template.json` modificado y abra PowerShell. Puede actualizar el clúster con `New-AzureRmResourceGroupDeployment`. La llamada requiere el nombre del grupo de recursos, el archivo de plantilla actualizado y el archivo de parámetro, y solicita a Resource Manager realizar los cambios adecuados en los recursos que se ha actualizado. Una vez que haya iniciado sesión en su cuenta y esté en la suscripción correcta, utilice el siguiente comando para ejecutar la actualización:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Una vez que la actualización finalice la implementación (tarda entre 15 y 45 minutos), WAD debería recopilar los contadores de rendimiento y enviarlos a una tabla denominada WADPerformanceCountersTable en la cuenta de almacenamiento asociada al clúster. Para ver los contadores de rendimiento en Application Insights, [agregue el receptor de AI a la plantilla de Resource Manager](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Pasos siguientes
* Recopile más contadores de rendimiento para el clúster. Vea [Métricas de rendimiento](service-fabric-diagnostics-event-generation-perf.md) para obtener una lista de contadores que debe recopilar.
* [Use la supervisión y diagnóstico con una máquina virtual Windows y plantillas de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) para realizar más modificaciones en `WadCfg`, incluida la configuración de cuentas de almacenamiento adicionales a la que enviar datos de diagnóstico.
