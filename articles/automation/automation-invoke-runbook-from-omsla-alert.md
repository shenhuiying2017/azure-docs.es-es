---
title: Llamada a un runbook de Azure Automation desde una alerta de Log Analytics | Microsoft Docs
description: "Este artículo ofrece información general sobre cómo invocar un runbook de Automation desde una alerta de Log Analytics de Microsoft OMS."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8460ed6be3e922fb85f46982662d44eed21dda7c
ms.lasthandoff: 03/18/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Llamada a un runbook de Azure Automation desde una alerta de Log Analytics de OMS

Al configurar una alerta en Log Analytics para crear un registro de alertas si los resultados coinciden con un criterio concreto, como un incremento prolongado del uso del procesador o un error de un proceso determinado crítico para el funcionamiento de una aplicación empresarial y la escritura del evento correspondiente en el registro de eventos de Windows, esa alerta puede ejecutar automáticamente un runbook de Automation para intentar corregir automáticamente el problema.  

Existen dos opciones para llamar a un runbook al configurar la alerta.  Concretamente, puede:

1. Usar un webhook.
   * Esta es la única opción disponible si el área de trabajo de OMS no está vinculada a una cuenta de Automation.
   * Si ya tiene una cuenta de Automation vinculada a un área de trabajo de OMS, esta opción seguirá estando disponible.  

2. Seleccionar un runbook directamente.
   * Esta opción solo está disponible cuando el área de trabajo de OMS está vinculada a una cuenta de Automation.  

## <a name="calling-a-runbook-using-a-webhook"></a>Llamada a un runbook con un webhook

Un webhook le permite iniciar un runbook determinado en Azure Automation a través de una sola solicitud HTTP.  Antes de configurar la [alerta de Log Analytics](../log-analytics/log-analytics-alerts.md#creating-alert-rules) para llamar al runbook mediante un webhook como una acción de alerta, debe crear primero un webhook para el runbook al que se llamará con este método.  Revise y siga los pasos descritos en el artículo acerca de cómo [crear un webhook](automation-webhooks.md#creating-a-webhook) y recuerde registrar la dirección URL del webhook para poder hacer referencia a ella al configurar la regla de alerta.   

## <a name="calling-a-runbook-directly"></a>Llamada a runbook directamente

Si tiene la oferta de Automation & Control instalada y configurada en el área de trabajo de OMS, al configurar la opción de acciones de runbook para la alerta, puede ver todos los runbooks en la lista desplegable **Seleccionar un Runbook** y seleccionar el runbook específico que desea ejecutar en respuesta a la alerta.  Puede ejecutar el runbook seleccionado en un área de trabajo en la nube de Azure o en Hybrid Runbook Worker.  Cuando la alerta se crea mediante la opción del runbook, se creará un webhook para el runbook.  Puede ver el webhook si va a la cuenta de Automation y se desplaza a la hoja del webhook del runbook seleccionado.  Si elimina la alerta, el webhook no se eliminará, pero el usuario podrá eliminarlo manualmente.  Que el webhook no se elimine no supone ningún problema, ya que se trata simplemente de un elemento huérfano que será necesario eliminar en algún momento para mantener organizada la cuenta de Automation.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Características de un runbook (para ambas opciones)

Los dos métodos para llamar al runbook desde la alerta de Log Analytics tienen distintas características de comportamiento que es necesario comprender antes de configurar las reglas de la alerta.  

* Debe tener un parámetro de entrada de runbook denominado **WebhookData** del tipo **Objeto**.  Esto puede ser obligatorio u opcional.  La alerta transmite los resultados de búsqueda al runbook mediante este parámetro de entrada.

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  Debe tener el código para convertir el parámetro WebhookData en un objeto de PowerShell.

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* será una matriz de objetos, cada uno de los cuales contiene los campos con valores de un resultado de búsqueda.

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>Incoherencias de WebhookData entre la opción de webhook y la opción de runbook

* Al configurar una alerta para llamar a un webhook, escriba la dirección URL del webhook que ha creado para un runbook y haga clic en el botón **Test Webhook** (Probar webhook).  El parámetro WebhookData resultante enviado al runbook no contiene *.SearchResult* ni *.SearchResults*.

*  Si guarda esa alerta, cuando esta se desencadene y llame al webhook, el parámetro WebhookData enviado al runbook contendrá *.SearchResult*.
* Si crea una alerta y la configura para llamar a un runbook (que también crea un webhook), cuando la alerta se desencadena, envía el parámetro WebhookData al runbook que contiene *.SearchResults*.

Por lo tanto, en el ejemplo de código anterior, necesitará obtener *.SearchResult* si la alerta llama a un webhook, y *.SearchResults* si la alerta llama directamente a un runbook.

## <a name="example-walkthrough"></a>Tutorial de ejemplo

A continuación, se demuestra el funcionamiento de este procedimiento en el ejemplo de runbook gráfico siguiente, en el que se inicia un servicio de Windows.<br><br> ![Runbook gráfico de inicio de servicio de Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

El runbook tiene un parámetro de entrada de tipo **Objeto** que se llama **WebhookData** e incluye los datos del webhook transferidos desde la alerta que contiene *.SearchResults*.<br><br> ![Parámetros de entrada de Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

En este ejemplo, en Log Analytics se han creado dos campos personalizados, *SvcDisplayName_CF* y *SvcState_CF* para obtener el nombre para mostrar del servicio y el estado del servicio (es decir, en ejecución o detenido) desde el evento escrito en el registro de eventos del sistema.  A continuación, se crea una regla de alerta con la siguiente consulta de búsqueda: `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`, a fin de poder detectar cuándo se detiene el servicio del administrador de trabajos de impresión en el sistema de Windows.  Aunque en este ejemplo se hace referencia a uno de los servicios preexistentes que se incluyen con el sistema operativo Windows, puede ser cualquier servicio que le interese.  La acción de alerta se configura para ejecutar el runbook utilizado en este ejemplo y para ejecutarse en Hybrid Runbook Worker, que están habilitados en los sistemas de destino.   

La actividad de código del runbook **Get Service Name from LA** convertirá la cadena con formato JSON en un tipo de objeto y filtrará por el elemento *SvcDisplayName_CF* para obtener el nombre para mostrar del servicio de Windows y transferirlo a la siguiente actividad, que comprobará que el servicio está detenido antes de intentar reiniciarlo.  *SvcDisplayName_CF* es un [campo personalizado](../log-analytics/log-analytics-custom-fields.md) creado en Log Analytics para demostrar este ejemplo.

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

Cuando se detiene el servicio, la regla de alerta de Log Analytics detectará una coincidencia, desencadenará el runbook y enviará el contexto de la alerta al runbook. El runbook actuará para comprobar que el servicio está detenido y, en tal caso, intentará reiniciarlo, comprobará que se ha reiniciado correctamente y generará los resultados.     

En el caso de que no tenga su cuenta de Automation vinculada a su área de trabajo de OMS, deberá configurar la regla de alerta con una acción de webhook para desencadenar el runbook y configurar el runbook para convertir la cadena con formato JSON y filtrar por *.SearchResult* siguiendo las instrucciones indicadas anteriormente.    

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de las alertas de Log Analytics y cómo crear una, consulte [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md).

* Para entender cómo desencadenar runbooks mediante un webhook, consulte [Webhooks de Azure Automation](automation-webhooks.md).

