---
title: "Azure Service Fabric: Supervisión del rendimiento con la extensión de Microsoft Azure Diagnostics | Microsoft Docs"
description: "Utilice Windows Azure Diagnostics para recopilar contadores de rendimiento para los clústeres de Azure Service Fabric."
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
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Supervisión del rendimiento con la extensión de Microsoft Azure Diagnostics

Este documento incluye los pasos necesarios para configurar los contadores de rendimiento a través de la extensión de Microsoft Azure Diagnostics para los clústeres de Windows. Para los clústeres de Linux, configure el [agente de OMS](service-fabric-diagnostics-oms-agent.md) para recopilar contadores de rendimiento para los nodos. 

 > [!NOTE]
> Se debe implementar la extensión de Microsoft Azure Diagnostics en el clúster para que estos pasos funcionen. Si no se configura, diríjase a [Recopilación y agregación de eventos con Azure Diagnostics de Windows](service-fabric-diagnostics-event-aggregation-wad.md) y siga los pasos descritos en *Implementación de la extensión de Diagnostics*.

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

3. Agregue los contadores de rendimiento que desee recopilar a `PerformanceCounterConfiguration`, que se declaró en el paso anterior. Cada contador que desee recopilar se define con `counterSpecifier`, `sampleRate`, `unit`, `annotation` y cualquier `sinks` relevante. Este es un ejemplo de un contador de rendimiento para el *tiempo total de procesador* (la cantidad de tiempo que la CPU ha estado usándose para las operaciones de procesamiento):

    ```json
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
            }
        ]
    }
    ```

    La velocidad de muestreo para el contador se puede modificar según sus necesidades. El formato es `PT<time><unit>`, por lo que si desea que el contador realice la recopilación cada segundo, debe establecer `"sampleRate": "PT15S"`.

    De forma similar, puede recopilar otros contadores de rendimiento agregándolos a la lista en `PerformanceCounterConfiguration`. Aunque puede utilizar `*` para especificar grupos de contadores de rendimiento con nombres similares, el envío de todos los contadores a través de un receptor (para Application Insights) requiere que se declaren individualmente. 

4. Una vez que haya agregado los contadores de rendimiento adecuados que se deben recopilar, debe actualizar el recurso de clúster de manera que estos cambios se reflejan en el clúster de ejecución. Guarde su `template.json` modificado y abra PowerShell. Puede actualizar el clúster con `New-AzureRmResourceGroupDeployment`. La llamada requiere el nombre del grupo de recursos, el archivo de plantilla actualizado y el archivo de parámetro, y solicita a Resource Manager realizar los cambios adecuados en los recursos que se ha actualizado. Una vez que haya iniciado sesión en su cuenta y esté en la suscripción correcta, utilice el siguiente comando para ejecutar la actualización:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Una vez que la actualización finalice la implementación (tarda entre 15 y 45 minutos), Microsoft Azure Diagnostics debe recopilar los contadores de rendimiento y enviarlos a una tabla en la cuenta de almacenamiento declarada en `WadCfg`.

## <a name="next-steps"></a>Pasos siguientes
* Vea los contadores de rendimiento en Application Insights agregando el receptor de Application Insights receptor a `WadCfg`. Lea la sección *Adición del receptor de AI a la plantilla de Resource Manager* en [Análisis y visualización de eventos con Application Insights ](service-fabric-diagnostics-event-analysis-appinsights.md) para configurar el receptor de Application Insights.
* Recopile más contadores de rendimiento para el clúster. Vea [Métricas de rendimiento](service-fabric-diagnostics-event-generation-perf.md) para obtener una lista de contadores que debe recopilar.
* [Use la supervisión y diagnóstico con una máquina virtual Windows y plantillas de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) para realizar más modificaciones en `WadCfg`, incluida la configuración de cuentas de almacenamiento adicionales a la que enviar datos de diagnóstico.