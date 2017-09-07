---
title: "Supervisión de Azure Functions | Microsoft Docs"
description: Aprenda a supervisar Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 3ab6123b6acfdec57f1ca71b404c9e1123d1ff6d
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---

# <a name="monitoring-azure-functions"></a>Supervisión de Azure Functions

## <a name="overview"></a>Información general 


La pestaña **Monitor** (Supervisar) en cada función le permite revisar cada ejecución de una función.

![Pestaña de supervisión de Azure Functions](./media/functions-monitoring/monitor-tab.png) 

Al hacer clic en una ejecución puede revisar la duración, los datos de entrada, los errores y los archivos de registro asociados. Esto resulta de utilidad con vistas a la depuración y la optimización del rendimiento de sus funciones.

> [!IMPORTANT]
> Al usar el [plan de hospedaje de consumo](functions-overview.md#pricing) para Azure Functions, el icono **Monitoring** (Supervisión) de Function App no muestra ningún dato. Esto se debe a que la plataforma escala y administra de forma dinámica las instancias de proceso. Estas métricas no son significativas en un plan de consumo. Para supervisar el uso de las aplicaciones de función, debe guiarse por los pasos que se indican en este artículo.
> 
> La siguiente captura de pantalla muestra un ejemplo:
> 
> ![Supervisión de la función](./media/functions-monitoring/app-service-overview-monitoring.png)


## <a name="real-time-monitoring"></a>Supervisión en tiempo real

La supervisión en tiempo real está disponible cuando se hace clic en **secuencia de eventos en directo**, como se muestra a continuación. 

![Opción de secuencia de eventos en directo de la pestaña de supervisión](./media/functions-monitoring/monitor-tab-live-event-stream.png)

El flujo de eventos en directo se muestra en un gráfico en una nueva pestaña del explorador, como en el ejemplo siguiente: 

![Ejemplo de secuencia de eventos en directo](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> Existe un problema conocido que puede provocar que no se pueden rellenar los datos. Si experimenta este problema, puede que tenga que cerrar la pestaña del explorador que contiene la secuencia de eventos en directo y luego hacer clic de nuevo en **secuencia de eventos en directo** para poder rellenar correctamente los datos de la secuencia de eventos. 

La secuencia de eventos en directo representará gráficamente las siguientes estadísticas de su función:

* Ejecuciones iniciadas por segundo
* Ejecuciones finalizadas por segundo
* Ejecuciones con error por segundo
* Tiempo medio de ejecución en milisegundos

Estas estadísticas son en tiempo real, pero el gráfico real de los datos de ejecución puede tener alrededor de 10 segundos de latencia.


## <a name="monitoring-log-files-from-a-command-line"></a>Supervisión de archivos de registro desde una línea de comandos

Puede transmitir archivos de registro a una sesión de línea de comandos en una estación de trabajo local mediante la interfaz de la línea de comandos (CLI) de Azure 1.0 o PowerShell.

### <a name="monitoring-function-app-log-files-with-the-azure-cli-10"></a>Supervisión de archivos de registro de la aplicación de función con la CLI de Azure 1.0

Para comenzar, [instale la CLI de Azure 1.0](../cli-install-nodejs.md)

Inicie sesión en la cuenta de Azure mediante el siguiente comando o cualquiera de las otras opciones tratadas en [Inicio de sesión en Azure desde la CLI de Azure 1.0](../xplat-cli-connect.md).

    azure login

Use el siguiente comando para habilitar la CLI de Azure 1.0 en modo clásico de administración de servicios:

    azure config mode asm

Si tiene varias suscripciones, use los siguientes comandos para mostrar las suscripciones y establecer la suscripción actual como la suscripción que contiene la aplicación de función.

    azure account list
    azure account set <subscriptionNameOrId>

El siguiente comando transmite los archivos de registro de la aplicación de función a la línea de comandos:

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>Supervisión de archivos de registro de aplicación de función con PowerShell

Para comenzar, [instale y configure Azure PowerShell](/powershell/azure/overview).

Agregue la cuenta de Azure; para ello, haga clic en el siguiente comando:

    PS C:\> Add-AzureAccount

Si tiene varias suscripciones, puede mostrarlas por nombre con el siguiente comando para ver si la suscripción correcta es la seleccionada actualmente según la propiedad `IsCurrent`:

    PS C:\> Get-AzureSubscription

Si necesita que la suscripción activa sea la que contiene la aplicación de función, use el siguiente comando:

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

Transmita los registros a la sesión de PowerShell con el siguiente comando:

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

Para más información, vea [How to: Stream logs for web apps (Cómo: Transmitir registros para aplicaciones web)](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Prueba de una función](functions-test-a-function.md)
* [Escalar una función](functions-scale.md)


