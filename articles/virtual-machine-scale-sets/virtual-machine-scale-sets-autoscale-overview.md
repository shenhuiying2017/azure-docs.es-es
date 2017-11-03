---
title: "Introducción a los registros de escalado automático con conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre los distintos modos en que puede escalar automáticamente un conjunto de escalado de máquinas virtuales de Azure en función del rendimiento o de una programación fija."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 868523a3aca441a47218297be2ce9f9e46dd84a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2017
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Introducción a los registros de escalado automático con conjuntos de escalado de máquinas virtuales de Azure
Un conjunto de escalado de máquinas virtuales de Azure puede aumentar o reducir automáticamente el número de instancias de máquinas virtuales que ejecutan la aplicación. Este comportamiento automatizado y elástico reduce la sobrecarga de administración para supervisar y optimizar el rendimiento de la aplicación. Puede crear reglas que definan el rendimiento mínimamente aceptable para una experiencia positiva del cliente. Al cumplirse esos umbrales definidos, las reglas de escalado automático actúan para ajustar la capacidad del conjunto de escalado. También puede programar eventos para aumentar o reducir automáticamente la capacidad del conjunto de escalado en determinados momentos. En este artículo se proporciona información general de las métricas de rendimiento que están disponibles y las acciones que puede realizar el escalado automático.


## <a name="benefits-of-autoscale"></a>Ventajas del escalado automático
Si aumenta la demanda de la aplicación, la carga de las instancias de máquinas virtuales del conjunto de escalado aumenta. Si este aumento de la carga es continuado, en lugar de ser algo puntual, puede configurar reglas de escalado automático para aumentar el número de instancias de máquinas virtuales en el conjunto de escalado.

Cuando se crean estas instancias de máquinas virtuales y se implementan las aplicaciones, el conjunto de escalado empieza a distribuir el tráfico entre ellas mediante el equilibrador de carga. Puede controlar qué métricas se deben supervisar como, por ejemplo, la CPU o la memoria, cuánto tiempo debe cumplir la carga de la aplicación un límite determinado y cuántas instancias de máquinas virtuales se deben agregar al conjunto de escalado.

La demanda de la aplicación puede reducirse por las tardes o durante los fines de semana. Si esta reducción es constante a lo largo de un período de tiempo, puede configurar reglas de escalado automático para reducir el número de instancias de máquinas virtuales del conjunto de escalado. Esta acción de reducción horizontal permite reducir el costo a la hora de ejecutar el conjunto de escalado ya que solo se ejecuta el número de instancias necesario para satisfacer la demanda actual.


## <a name="use-host-based-metrics"></a>Uso de métricas basadas en host
Puede crear reglas de escalado automático que hagan que las métricas de host estén disponibles en las instancias de máquinas virtuales. Las métricas de host le dan visibilidad sobre el rendimiento de las instancias de máquinas virtuales en un conjunto de escalado sin la necesidad de instalar o configurar recopilaciones de datos y agentes adicionales. Las reglas de escalado automático que usan estas métricas pueden reducir verticalmente u horizontalmente el número de instancias de máquinas virtuales en respuesta al uso de CPU, la demanda de memoria o el acceso a disco.

Las reglas de escalado automático que usan métricas basadas en host se pueden crear con una de las herramientas siguientes:

- [Portal de Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [CLI de Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Para crear reglas de escalado automático que usen métricas de rendimiento más detalladas, puede [instalar y configurar la extensión de Azure Diagnostics](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) en instancias de máquinas virtuales o [configurar la aplicación para usar App Insights](#application-level-metrics-with-app-insights).

Las reglas de escalado automático usan métricas basadas en host, métricas de máquinas virtuales hospedadas internamente con la extensión de Azure Diagnostics, y App Insights puede usar los siguientes valores de configuración.

### <a name="metric-sources"></a>Orígenes de métricas
Las reglas de escalado automático pueden usar métricas de uno de los siguientes orígenes:

| Origen de métricas        | Caso de uso                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de escalado actual    | Para métricas basadas en host que no requieren la instalación o configuración de agentes adicionales.                                  |
| Cuenta de almacenamiento      | La extensión de Azure Diagnostics escribe métricas de rendimiento a Azure Storage, que a continuación se consume para desencadenar reglas de escalado automático. |
| Cola de ServiceBus    | Su aplicación u otros componentes pueden transmitir mensajes en una cola de Azure Service Bus para desencadenar reglas.                   |
| Application Insights | Un paquete de instrumentación instalado en la aplicación que hace streaming de métricas directamente en la aplicación.                         |


### <a name="autoscale-rule-criteria"></a>Criterios de las reglas de escalado automático
Las siguientes métricas basadas en host están disponibles para su uso al crear reglas de escalado automático. Si usa la extensión de Azure Diagnostics o App Insights, puede definir las métricas que se van a supervisar y utilizar con las reglas de escalado automático.

| Nombre de métrica               |
|---------------------------|
| Porcentaje de CPU            |
| Red interna                |
| Red interna               |
| Bytes de lectura de disco           |
| Bytes de escritura de disco          |
| Operaciones de lectura de disco por segundo  |
| Operaciones de escritura por segundo en disco |
| Créditos de CPU restantes     |
| Créditos de CPU consumidos      |

Al crear reglas de escalado automático para supervisar una métrica especificada, las reglas se fijan en una de las siguientes acciones de agregación de métricas:

| Tipo de agregación |
|------------------|
| Media          |
| Mínima          |
| Máxima          |
| Total            |
| Último             |
| Recuento            |

A continuación, las reglas de escalado automático se desencadenan al compararse las métricas con su umbral definido con uno de los siguientes operadores:

| operador                 |
|--------------------------|
| Mayor que             |
| Mayor o igual que |
| Menor que                |
| Menor o igual que    |
| Igual a                 |
| No igual a             |


### <a name="actions-when-rules-trigger"></a>Acciones al desencadenarse las reglas
Al desencadenarse una regla de escalado automático, su conjunto de escalado puede escalar automáticamente de una de las siguientes formas:

| Operación de escalado     | Caso de uso                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar recuento en   | Un número fijo de instancias de máquinas virtuales que se van a crear. Útil en conjuntos de escalado con un número menor de máquinas virtuales.                                           |
| Aumentar porcentaje en | Un aumento basado en porcentajes de instancias de máquinas virtuales. Bueno para conjuntos de escalado mayores donde es posible que un aumento fijo no mejore el rendimiento considerablemente. |
| Aumentar recuento en   | Cree tantas instancias de máquinas virtuales como sean necesarias para alcanzar una cantidad máxima deseada.                                                            |
| Reducir recuento en   | Un número fijo de instancias de máquinas virtuales que se van a quitar. Útil en conjuntos de escalado con un número menor de máquinas virtuales.                                           |
| Reducir porcentaje en | Un descenso basado en porcentajes de instancias de máquinas virtuales. Bueno para conjuntos de escalado mayores donde es posible que un aumento fijo no reduzca los costos y el consumo de recursos considerablemente. |
| Reducir recuento en   | Quite tantas instancias de máquinas virtuales como sean necesarias para alcanzar una cantidad mínima deseada.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas de máquinas virtuales hospedadas internamente con la extensión de Azure Diagnostics
La extensión de Azure Diagnostics es un agente que se ejecuta dentro de una instancia de máquinas virtuales. El agente supervisa y guarda métricas de rendimiento en Azure Storage. Estas métricas de rendimiento contienen información más detallada sobre el estado de la máquina virtual, como *AverageReadTime* para discos o *PercentIdleTime* para CPU. Puede crear reglas de escalado automático basadas en un reconocimiento más detallado del rendimiento de la máquina virtual, no solo el porcentaje de uso de CPU o el consumo de memoria.

Para usar la extensión de Azure Diagnostics, debe crear cuentas de Azure Storage para las instancias de máquinas virtuales, instalar el agente de Azure Diagnostics y, a continuación, configurar las máquinas virtuales para hacer streaming de contadores de rendimiento específicos en la cuenta de almacenamiento.

Para obtener más información, consulte los artículos sobre cómo habilitar la extensión de Azure Diagnostics en una [VM Linux](../virtual-machines/linux/diagnostic-extension.md) o [VM Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nivel de aplicación con App Insights
Para obtener mayor visibilidad en el rendimiento de sus aplicaciones, puede usar Application Insights. Puede instalar un paquete de instrumentación pequeño en su aplicación que supervisa la aplicación y envía telemetría a Azure. Puede supervisar métricas como los tiempos de respuesta de su aplicación, el rendimiento de carga de las páginas y el recuento de sesiones. Estas métricas de la aplicación se pueden usar para crear reglas de escalado automático en un nivel pormenorizado e incrustado al desencadenar reglas basadas en conocimiento útil que puede afectar a la experiencia del cliente.

Para obtener más información sobre App Insights, consulte [¿Qué es Application Insights?](../application-insights/app-insights-overview.md)


## <a name="scheduled-autoscale"></a>Escalado automático programado
También puede crear reglas de escalado automático basadas en programaciones. Estas reglas basadas en programación permiten escalar automáticamente el número de instancias de máquinas virtuales en determinados momentos. Con las reglas basadas en el rendimiento, puede haber un impacto del rendimiento en la aplicación antes de que las reglas de escalado automático se desencadenen y de que las nuevas instancias de máquinas virtuales se aprovisionen. Si puede anticipar tal demanda, las instancias de máquinas virtuales adicionales se aprovisionan y están listas para su uso adicional por parte del cliente y la demanda de la aplicación.

Los siguientes ejemplos son escenarios que pueden beneficiar el uso de reglas de escalado automático basadas en programación:

- Reduzca verticalmente de forma automática el número de instancias de máquinas virtuales al principio de la jornada laboral, cuando la demanda del cliente aumenta. Al final de la jornada laboral, reduzca horizontalmente de forma automática el número de instancias de máquinas virtuales para minimizar los costos de recursos, cuando el uso de la aplicación es bajo.
- Si un departamento usa mucho una aplicación en determinados momentos del mes o ciclo fiscal, escala automáticamente el número de instancias de máquinas virtuales para adaptarse a sus demandas adicionales.
- Cuando hay un evento de marketing, una promoción o una oferta de fiesta, puede escalar automáticamente el número de instancias de máquinas virtuales frente a la demanda anticipada del cliente. 


## <a name="next-steps"></a>Pasos siguientes
Puede crear reglas de escalado automático que usen métricas basadas en host con una de las herramientas siguientes:

- [Portal de Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [CLI de Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md)

En esta introducción se detallaba cómo utilizar reglas de escalado automático para escalar horizontalmente y aumentar o reducir el *número* de instancias de máquinas virtuales del conjunto de escalado. También puede escalar verticalmente para aumentar o reducir el *tamaño* de la instancia de máquinas virtuales. Para obtener más información, consulte [Escalado automático vertical con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obtener más información sobre cómo administrar las instancias de máquinas virtuales, consulte [Manage virtual machine scale sets with Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) (Administración de conjuntos de escalado de máquinas virtuales con Azure PowerShell).

Para obtener más información sobre cómo generar alertas cuando la regla de escalado automático se desencadena, consulte [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). También puede [usar registros de auditoría para enviar notificaciones de alerta por correo electrónico y webhook en Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).