---
title: "Creación de una tarea de monitor de la cuenta de Azure Automation | Microsoft Docs"
description: "Obtenga información acerca de cómo crear una tarea de monitor de la cuenta de Azure Automation para inspeccionar los nuevos archivos que se creen en una carpeta."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Las tareas de monitor de Azure Automation le permiten responder a eventos que ocurren en el centro de datos local

En este tutorial, aprenderá a crear una nueva tarea de monitor para:

> [!div class="checklist"]
> * Crear un runbook de monitor que compruebe si hay nuevos archivos en un directorio.
> * Crear una variable de Automation para conocer la última vez que el monitor procesó un archivo.
> * Crear un runbook de acción al que se llamará cuando el runbook de monitor encuentre un archivo nuevo.
> * Crear una tarea de monitor que selecciona el runbook de monitor y el runbook de acción.
> * Desencadenar un monitor mediante la adición de un nuevo archivo a un directorio.
> * Revisar el resultado del runbook de acción que muestra información acerca del nuevo archivo.  

## <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial.
+ Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
+ [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) donde se ejecuta la tarea de monitor.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Crear un runbook de monitor que compruebe si hay nuevos archivos
1.  Abra la cuenta de Automation y haga clic en la página Runbooks.
2.  Haga clic en el botón "Examinar galería".
![Lista de runbook desde la interfaz de usuario](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Busque "Watch-NewFile" e importe el runbook en la cuenta de Automation.
![Publicación del runbook desde la interfaz de usuario](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Haga clic en "Editar" para ver el origen del runbook y haga clic en el botón "Publicar".

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Crear una variable de Automation para conocer la última vez que el monitor procesó un archivo
1.  Abra la página de variables en RECURSOS COMPARTIDOS y haga clic en "Agregar variable". ![Enumerar variables desde la interfaz de usuario](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Escriba "Watch-NewFileTimestamp" para el nombre
3.  Seleccione el tipo como fecha y hora y, después, haga clic en el botón "Crear".
![Crear la variable de marca de agua desde la interfaz de usuario](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Crear un runbook de acción al que se llamará cuando el runbook de monitor encuentre un archivo nuevo
1.  Haga clic en la página Runbooks en la categoría "AUTOMATIZACIÓN DE PROCESOS".
2.  Haga clic en el botón "Examinar galería".
3.  Busque "Process-NewFile" e importe el runbook en la cuenta de Automation.
4.  Haga clic en "Editar" para ver el origen del runbook y haga clic en el botón "Publicar".
![Monitor de proceso desde la interfaz de usuario](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Crear una tarea de monitor que selecciona el runbook de monitor y el runbook de acción
1.  Abra la página de tareas de monitor y haga clic en el botón "Agregar una tarea de monitor".
![Lista de monitor desde la interfaz de usuario](media/automation-watchers-tutorial/WatchersList.png)
2.  Escriba "WatchMyFolder" en el nombre.
3.  Seleccione "Configurar monitor" y seleccione el runbook "Watch-NewFile".
![Configurar monitor desde la interfaz de usuario](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Escriba los siguientes valores para los parámetros:
    *   FOLDERPATH: carpeta de Hybrid Worker donde se crean nuevos archivos.
    *   EXTENSION: deje este parámetro en blanco para procesar todas las extensiones de archivo.
    *   RECURSE: deje el valor predeterminado.
    *   RUN SETTINGS: seleccione Hybrid Worker.
5.  Haga clic en Aceptar y, después, seleccione esta opción para volver a la página de monitor.
6.  Seleccione "Configurar acción" y el runbook "Process-NewFile".
![Configurar acción de monitor desde la interfaz de usuario](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Escriba los siguientes valores para los parámetros:
    *   EVENTDATA: déjelo en blanco. Los datos se pasan desde el runbook de monitor.
    *   RUN SETTINGS: déjelo como Azure porque este runbook se ejecuta en el servicio Automation.
8.  Haga clic en Aceptar y, después, seleccione esta opción para volver a la página de monitor.
9.  Haga clic en Aceptar para crear la tarea de monitor.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Desencadenar un monitor mediante la adición de un nuevo archivo a un directorio
1.  Remoto en Hybrid Worker
2.  Agregue un nuevo archivo de texto a la carpeta que supervisa la tarea de monitor.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Revisar el resultado del runbook de acción que muestra información acerca del nuevo archivo
1.  Haga clic en la tarea de monitor de "WatchMyFolder".
2.  Haga clic en "Ver secuencias de monitor" para ver que el monitor encontró el archivo nuevo e inició el runbook de acción.
3.  Haga clic en "Ver trabajos de acción de monitor" para ver el trabajo del runbook de acción.
![Trabajos de acción de monitor desde la interfaz de usuario](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Pasos siguientes:

Para obtener más información, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).








