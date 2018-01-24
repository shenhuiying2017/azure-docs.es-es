---
title: "Escalado vertical de conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Cómo escalar verticalmente una máquina virtual en respuesta a las alertas de supervisión con Azure Automation"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: negat
ms.openlocfilehash: 6e4733e023d1dc27fb099216f9afea07fe07446c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Escalado automático vertical con conjuntos de escalado de máquinas virtuales
Este artículo describe cómo escalar verticalmente [conjuntos de escalado de máquinas virtuales](https://azure.microsoft.com/services/virtual-machine-scale-sets/) de Azure con o sin reaprovisionamiento. Para el escalado vertical de máquinas virtuales que no están en conjuntos de escalado, consulte [Escalado vertical de máquinas virtuales con Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

El escalado vertical, también conocido como *ampliación vertical* y *reducción vertical*, significa aumentar o disminuir el tamaño de las máquinas virtuales (VM) en respuesta a una carga de trabajo. Compare este comportamiento con el [escalado horizontal](virtual-machine-scale-sets-autoscale-overview.md), también denominado *Escalabilidad horizontal* y *Reducción horizontal*, donde se modifica el número de máquinas virtuales según la carga de trabajo.

Reaprovisionar significa quitar una máquina virtual existente y reemplazarla por una nueva. Al aumentar o disminuir el tamaño de las máquinas virtuales en un conjunto de escalado de máquinas virtuales, en algunos casos puede que quiera cambiar el tamaño de las máquinas virtuales existentes y conservar los datos, aunque en otros casos puede que necesite implementar nuevas máquinas virtuales con el nuevo tamaño. Este documento describe ambos casos.

El escalado vertical puede resultar útil cuando:

* Un servicio integrado en máquinas virtuales se está infrautilizando (por ejemplo, los fines de semana). Reducir el tamaño de la máquina virtual puede reducir los costos mensuales.
* Aumentar el tamaño de la máquina virtual para hacer frente a una mayor demanda sin crear máquinas virtuales adicionales.

Puede configurar el escalado vertical para que se desencadene en función de las alertas basadas en métricas de su conjunto de escalado de máquinas virtuales. Cuando la alerta se activa, inicia un webhook que desencadena un runbook que puede ampliar o reducir verticalmente el conjunto de escalado. El escalado vertical puede configurarse siguiendo estos pasos:

1. Cree una cuenta de Azure Automation con funciones de ejecución.
2. Importe a la suscripción los runbooks de escalado vertical de Azure Automation para los conjuntos de escalado de máquinas virtuales.
3. Agregue un webhook al Runbook.
4. Agregue una alerta a su conjunto de escalado de máquinas virtuales mediante una notificación de webhook.

> [!NOTE]
> La autoescala vertical solo se puede realizar en determinados intervalos de tamaños de máquina virtual. Compare las especificaciones de cada tamaño antes de decidir si escalar de una a otra (un número alto no siempre indica un mayor tamaño de máquina virtual). Puede elegir escalar entre los siguientes pares de tamaños:
> 
> | Pares de escalado de tamaños de VM |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Creación de una cuenta de Azure Automation con funciones de ejecución
Lo primero que debe hacer es crear una cuenta de Azure Automation que hospedará los runbooks que se usan para escalar las instancias del conjunto de escalado de máquinas virtuales. Recientemente, [Azure Automation](https://azure.microsoft.com/services/automation/) presentó la característica "Cuenta de ejecución", que permite realizar la configuración de la entidad de servicio para ejecutar los runbooks automáticamente en nombre de un usuario. Para obtener más información, consulte 

* [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importación de runbooks de escalado vertical de Azure Automation a la suscripción
Los runbooks necesarios para el escalado vertical de los conjuntos de escalado de máquinas virtuales están publicados en la galería de runbooks de Azure Automation. Para importarlos a su suscripción, siga los pasos de este artículo:

* [Galerías de runbooks y módulos para Azure Automation](../automation/automation-runbook-gallery.md)

Elija la opción Examinar la galería en el menú Runbooks:

![Runbooks que desea importar][runbooks]

Se muestran los Runbooks que es necesario importar. Seleccione el Runbook en función de si desea un escalado vertical con o sin reaprovisionamiento:

![Galería de Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Agregar un webhook al runbook.
Una vez que importe los runbooks, agregue un webhook al runbook para que, de este modo, una alerta de un conjunto de escalado de máquinas virtuales pueda desencadenarlo. En este artículo puede leer los detalles sobre cómo crear un webhook para el Runbook:

* [Webhooks de Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Asegúrese de copiar el URI del webhook antes de cerrar el cuadro de diálogo del webhook, porque necesitará esta dirección en la siguiente sección.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Adición de una alerta al conjunto de escalado de máquinas virtuales
A continuación, se presenta un script de PowerShell que muestra cómo agregar una alerta a un conjunto de escalado de máquinas virtuales. Consulte el siguiente artículo para obtener el nombre de la métrica para desencadenar la alerta en: [Métricas comunes de escalado automático de Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Se recomienda configurar un período de tiempo razonable para la alerta con el fin de evitar activar el escalado vertical, y las interrupciones de servicio asociadas, con demasiada frecuencia. Considere un intervalo mínimo de 20 a 30 minutos. Considere la posibilidad de usar escalado horizontal si necesita evitar cualquier interrupción.
> 
> 

Para más información sobre cómo crear alertas, consulte los artículos siguientes:

* [Ejemplos de inicio rápido de PowerShell de Azure Monitor](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Ejemplos de inicio rápido de CLI multiplataforma de Azure Monitor](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Resumen
En este artículo se mostraron ejemplos sencillos de escalado vertical. Con estos bloques de creación (cuenta de Automation, runbooks, webhooks, alertas) puede conectar una gran variedad de eventos con un conjunto personalizado de acciones.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
