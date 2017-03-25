---
title: "Supervisión de Azure Functions | Microsoft Docs"
description: Aprenda a supervisar Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 831b7cc21ee34f900f7e35885a36f6e0faf38211
ms.lasthandoff: 03/21/2017

---

# <a name="monitoring-azure-functions"></a>Supervisión de Azure Functions

## <a name="overview"></a>Información general 


La pestaña **Monitor** (Supervisar) en cada función le permite revisar cada ejecución de una función.

![Pestaña de supervisión de Azure Functions](./media/functions-monitoring/monitor-tab.png) 

Al hacer clic en una ejecución puede revisar la duración, los datos de entrada, los errores y los archivos de registro asociados. Esto resulta de utilidad con vistas a la depuración y la optimización del rendimiento de sus funciones.


> [!IMPORTANT]
> Al usar el [plan de hospedaje de consumo](functions-overview.md#pricing) para Azure Functions, el icono **Monitoring** (Supervisión) de la hoja de información general de Function App no muestra ningún dato. Esto se debe a que la plataforma escala y administra las instancias de proceso de manera dinámica sin intervención del usuario, por lo que estas métricas no son significativas en un plan de consumo. Para supervisar el uso de las aplicaciones de función, debe guiarse por los pasos que se indican en este artículo.
> 
> La siguiente captura de pantalla muestra un ejemplo:
> 
> ![Hoja de supervisión del recurso principal](./media/functions-monitoring/app-service-overview-monitoring.png)



## <a name="real-time-monitoring"></a>Supervisión en tiempo real

La supervisión en tiempo real está disponible cuando se hace clic en **secuencia de eventos en directo**, como se muestra a continuación. 

![Opción de secuencia de eventos en directo de la pestaña de supervisión](./media/functions-monitoring/monitor-tab-live-event-stream.png)

La secuencia de eventos en directo se representa gráficamente en una nueva pestaña de explorador, como se muestra a continuación. 

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


Puede transmitir archivos de registro a una sesión de línea de comandos en una estación de trabajo local mediante la interfaz de la línea de comandos (CLI) de Azure o PowerShell.

### <a name="monitoring-function-app-log-files-with-the-azure-cli"></a>Supervisión de archivos de registro de aplicación de función con la CLI de Azure

Para comenzar, [instale la CLI de Azure](../cli-install-nodejs.md).

Inicie sesión en su cuenta de Azure mediante el siguiente comando, o cualquiera de las otras opciones que se tratan en [Inicio de sesión en Azure desde la CLI de Azure](../xplat-cli-connect.md).

    azure login

Use el siguiente comando para habilitar el modo de administración de servicios de la CLI de Azure (ASM).

    azure config mode asm

Si tiene varias suscripciones, use los siguientes comandos para mostrar las suscripciones y establecer la suscripción actual como la suscripción que contiene la aplicación de función.

    azure account list
    azure account set <subscriptionNameOrId>

El siguiente comando transmitirá los archivos de registro de la aplicación de función a la línea de comandos:

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>Supervisión de archivos de registro de aplicación de función con PowerShell

Para comenzar, [instale y configure Azure PowerShell](/powershell/azureps-cmdlets-docs).

Agregue la cuenta de Azure; para ello, haga clic en el siguiente comando:

    PS C:\> Add-AzureAccount

Si tiene varias suscripciones, puede mostrarlas por nombre con el siguiente comando para ver si la suscripción correcta es la seleccionada actualmente según la propiedad `IsCurrent`:

    PS C:\> Get-AzureSubscription

Si necesita que la suscripción activa sea la que contiene la aplicación de función, use el siguiente comando:

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

Transmita los registros a la sesión de PowerShell con el siguiente comando:

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

Para más información, consulte [Transmisión de registros para aplicaciones web](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Prueba de una función](functions-test-a-function.md)
* [Escalar una función](functions-scale.md)


