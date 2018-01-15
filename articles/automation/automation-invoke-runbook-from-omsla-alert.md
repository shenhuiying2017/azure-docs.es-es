---
title: Llamada a un runbook de Azure Automation desde una alerta de Log Analytics | Microsoft Docs
description: "En este artículo se ofrece información general acerca cómo invocar un runbook de Automation desde una alerta de Log Analytics de Operations Management Suite."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: a10be867965eef9746a0f4cc9b14c4fc429f6e35
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Llamada a un runbook de Azure Automation desde una alerta de Log Analytics

En Azure Log Analytics se puede configurar una alerta para crear un registro de alertas cuando los resultados coincidan con los criterios. Luego, dicha alerta puede ejecutar automáticamente un runbook de Azure Automation en un intento de remediar automáticamente el problema. 

Por ejemplo, una alerta puede indicar un pico prolongado en el uso del procesador. O bien, puede indicar si se produce un error en un proceso que resulta fundamental para la funcionalidad de una aplicación empresarial. Luego, un runbook puede escribir un evento correspondiente en el registro de eventos de Windows.  

Hay dos opciones para llamar a un runbook en la configuración de alertas:

* Usar un webhook.
   * Esta es la única opción disponible si el área de trabajo de Operations Management Suite no está vinculada a una cuenta de Automation.
   * Si ya hay una cuenta de Automation vinculada a un área de trabajo de Operations Management Suite, esta opción seguirá estando disponible.  

* Seleccionar un runbook directamente.
   * Esta opción solo está disponible si el área de trabajo de Operations Management Suite está vinculada a una cuenta de Automation.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Llamada a un runbook mediante un webhook

Puede usar un webhook para iniciar un runbook determinado en Azure Automation a través de una sola solicitud HTTP. Antes de configurar la [alerta de Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) para que llame al runbook mediante el uso de un webhook como acción de alerta, es preciso [crear un webhook](automation-webhooks.md#creating-a-webhook) para el runbook al que se llama a través de este método. No olvide registrar la dirección URL del webhook para poder hacer referencia a ella al configurar la regla de alerta.   

## <a name="calling-a-runbook-directly"></a>Llamada a runbook directamente

Puede instalar y configurar la oferta de Automation and Control en el área de trabajo de Operations Management Suite. Al configurar la opción de acciones de runbook para la alerta, puede ver todos los runbooks en la lista desplegable **Seleccionar un runbook** y seleccionar el runbook concreto que desea ejecutar en respuesta a la alerta. Este se puede ejecutar tanto en un área de trabajo de Azure como en un trabajo de runbook híbrido. 

Tras crear la alerta mediante la opción del runbook, se crea un webhook para el runbook. Para ver el webhook, vaya a la cuenta de Automation y abra al panel de webhooks del runbook seleccionado. 

Aunque elimine la alerta, el webhook no se elimina. Pero esto no supone ningún problema. El webhook simplemente se convierte en un elemento huérfano que tarde o temprano hay que eliminar manualmente para que no se desorganice la cuenta Automation.  

## <a name="characteristics-of-a-runbook"></a>Características de un runbook

Los dos métodos para llamar al runbook desde la alerta de Log Analytics tienen distintas características que hay que conocer antes de configurar las reglas de alerta. 

Los datos de la alerta están en formato JSON en una propiedad única denominada **SearchResult**. Este formato es para las acciones de runbook y webhook con una carga estándar. En el caso de las acciones de webhook con cargas personalizadas (que incluyen **IncludeSearchResults:True** en **RequestBody**) la propiedad es **SearchResults**.

Debe tener un parámetro de entrada de runbook denominado **WebhookData** del tipo **Objeto**. Esto puede ser obligatorio u opcional. Dicho parámetro se usa para que la alerta pase los resultados de búsqueda al runbook.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
También debe tener código para convertir **WebhookData** en un objeto de PowerShell.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** es una matriz de objetos, cada uno de los cuales contiene los campos con valores de un resultado de búsqueda.


## <a name="example-walkthrough"></a>Tutorial de ejemplo

El siguiente ejemplo de un runbook gráfico muestra cómo funciona este proceso. Inicia un servicio de Windows.

![Runbook gráfico para iniciar un servicio de Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

El runbook tiene un parámetro de entrada del tipo **Objeto** que se llama **WebhookData**. Incluye los datos de webhook pasados desde la alerta que contiene **SearchResult**.

![Parámetros de entrada de Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

En este ejemplo, hemos creado dos campos personalizados en Log Analytics: **SvcDisplayName_CF** y **SvcState_CF**. Dichos campos extraen el nombre para mostrar y el estado del servicio (en ejecución o detenido) del evento que está escrito en el registro de eventos del sistema. Luego, se crea una regla de alerta con la siguiente consulta de búsqueda:, con el fin de poder detectar cuándo se detiene el servicio del Administrador de trabajos de impresión en el sistema de Windows:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Puede ser cualquier servicio que le interese, pero en este ejemplo se hace referencia a uno de los servicios preexistentes que se incluyen con el sistema operativo Windows. La acción de la alerta se configura para ejecutar el runbook que se usa en este ejemplo y se ejecuta en el trabajo de runbook híbrido, que se habilita en el sistema de destino.   

La actividad del código del runbook **Get Service Name from LA** convierte la cadena con formato JSON en un tipo de objeto y filtra por el elemento **SvcDisplayName_CF**. Extrae el nombre para mostrar del servicio de Windows y pasa dicho valor a la siguiente actividad, que comprueba que el servicio está detenido antes de intentar reiniciarlo. **SvcDisplayName_CF** es un [campo personalizado](../log-analytics/log-analytics-custom-fields.md) que se crea en Log Analytics para demostrar este ejemplo.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Cuando se detiene el servicio, la regla de alerta de Log Analytics detecta una coincidencia, desencadena el runbook y le envía el contexto de la alerta. El runbook intenta comprobar que el servicio se detiene. En ese caso, el runbook intenta reiniciar el servicio, comprobar que se ha iniciado correctamente y muestra los resultados.     

O bien, si no tiene la cuenta de Automation vinculada a un área de trabajo de Operations Management Suite, puede configurar la regla de alerta con una acción de webhook. La acción de webhook desencadena el runbook. También configura el runbook para convertir la cadena con formato JSON y filtrar por **SearchResult** siguiendo las instrucciones anteriores.    

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), la carga del webhook ha cambiado. Los detalles del formato se encuentran en la [API de REST de Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>pasos siguientes

* Para más información acerca de las alertas de Log Analytics y cómo crear una, consulte [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md).

* Para saber cómo desencadenar runbooks mediante un webhook, consulte [Inicio de un runbook de Azure Automation con un webhook](automation-webhooks.md).
