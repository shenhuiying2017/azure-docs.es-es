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
ms.date: 12/11/2017
ms.author: eamono
ms.openlocfilehash: 294faa48f9840919b087594835706bad8048d45b
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Creación de tareas de monitor de Azure Automation para realizar un seguimiento de los cambios de archivo en un equipo local

Azure Automation usa tareas de monitor para inspeccionar los eventos y desencadenar acciones. Este tutorial lo guía en la creación de una tarea de monitor para supervisar cuándo se agrega un nuevo archivo a un directorio.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Importar un runbook de monitor
> * Crear una variable de Automation
> * Crear un runbook de acción
> * Crear una tarea de monitor
> * Desencadenar un monitor
> * Inspeccionar la salida

## <a name="prerequisites"></a>requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) donde se ejecuta la tarea de monitor.

## <a name="import-a-watcher-runbook"></a>Importar un runbook de monitor

Este tutorial usa un runbook de monitor denominado **Watch-NewFile** para buscar nuevos archivos en un directorio. El runbook de monitor recupera la última hora de escritura conocida en los archivos de una carpeta y examina los archivos más recientes que esa marca de agua. En este paso, importe este runbook en la cuenta de Automation.

1. Abra la cuenta de Automation y haga clic en la página **Runbooks**.
1. Haga clic en el botón **Examinar galería**.
1. Busque "Runbook de monitor", seleccione **Watcher runbook that looks for new files in a directory** (Runbook de monitor que busca archivos nuevos en un directorio) y seleccione **Importar**.
  ![Importación de un runbook de Automation desde la interfaz de usuario](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Asigne al runbook un nombre y una descripción y seleccione **Aceptar** para importar el runbook en su cuenta de Automation.
1. Seleccione **Editar** y luego haga clic en **Publicar**. Cuando se le pida, seleccione **Sí** para publicar el runbook.

## <a name="create-an-automation-variable"></a>Creación de una variable de Automation

Se usa una [variable de Automation](automation-variables.md) para almacenar las marcas de tiempo que el runbook anterior lee y almacena de cada archivo. 

1. Seleccione **Variables** en **RECURSOS COMPARTIDOS** y seleccione **+ Agregar una variable**.
1. Escriba "Watch-NewFileTimestamp" para el nombre.
1. Seleccione DateTime para el tipo.
1. Haga clic en el botón **Crear**. Se crea la variable de Automation.

## <a name="create-an-action-runbook"></a>Crear un runbook de acción

Un runbook de acción se usa en una tarea de monitor para actuar sobre los datos transferidos a él desde un runbook de monitor. En este paso, va a actualizar la importación de un runbook de acción predefinido denominado "Process-NewFile".

1. Vaya a la cuenta de Automation y seleccione **Runbooks** en la categoría **AUTOMATIZACIÓN DE PROCESOS**.
1. Haga clic en el botón **Examinar galería**.
1. Busque "Acción de monitor", seleccione **Watcher action that processes events triggered by a watcher runbook** (Acción de monitor que procesa eventos desencadenados por un runbook de monitor) y después seleccione **Importar**.
  ![Importación de un runbook de acción desde la interfaz de usuario](media/automation-watchers-tutorial/importsourceaction.png)
1. Asigne al runbook un nombre y una descripción y seleccione **Aceptar** para importar el runbook en su cuenta de Automation.
1. Seleccione **Editar** y luego haga clic en **Publicar**. Cuando se le pida, seleccione **Sí** para publicar el runbook.

## <a name="create-a-watcher-task"></a>Crear una tarea de monitor

La tarea de monitor consta de dos partes. El monitor y la acción. El monitor se ejecuta en un intervalo definido en la tarea de monitor. Los datos del runbook de monitor se pasan al runbook de acción. En este paso, va a configurar la tarea de monitor que hace referencia a los runbooks de monitor y acción definidos en los pasos anteriores.

1. Vaya a la cuenta de Automation y seleccione **Tareas de monitor** en la categoría **AUTOMATIZACIÓN DE PROCESOS**.
1. Seleccione la página de tareas de monitor y haga clic en el botón **+ Agregar una tarea de monitor**.
1. Escriba "WatchMyFolder" en el nombre.

1. Seleccione **Configurar monitor** y seleccione el runbook **Watch-NewFile**.

1. Escriba los siguientes valores para los parámetros:

   * **FOLDERPATH**: carpeta de Hybrid Worker donde se crean archivos. d:\examplefiles
   * **EXTENSION**: deje este parámetro en blanco para procesar todas las extensiones de archivo.
   * **RECURSE**: deje este valor como el predeterminado.
   * **RUN SETTINGS**: seleccione Hybrid Worker.

1. Haga clic en Aceptar y, después, seleccione esta opción para volver a la página de monitor.
1. Seleccione **Configurar acción** y el runbook "Process-NewFile".
1. Escriba los siguientes valores para los parámetros:

   *    **EVENTDATA**: deje este valor en blanco. Los datos se pasan desde el runbook de monitor.  
   *    **Run Settings**: déjelo como Azure porque este runbook se ejecuta en el servicio Automation.

1. Haga clic en **Aceptar** y, después, seleccione esta opción para volver a la página de monitor.
1. Haga clic en **Aceptar** para crear la tarea de monitor.

![Configurar acción de monitor desde la interfaz de usuario](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Desencadenar un monitor

Para probar si el monitor función según lo previsto, debe crear un archivo de prueba.

Remoto en Hybrid Worker. Abra **PowerShell** y cree un archivo de prueba en la carpeta.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

En el siguiente ejemplo se muestra la salida esperada.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Inspeccionar la salida

1. Vaya a la cuenta de Automation y seleccione **Tareas de monitor** en la categoría **AUTOMATIZACIÓN DE PROCESOS**.
1. Seleccione la tarea de monitor "WatchMyFolder".
1. Haga clic en **Ver secuencias de monitor** en **Secuencias** para ver si el monitor encontró el archivo nuevo e inició el runbook de acción.
1. Haga clic en **Ver trabajos de acción de monitor** para ver los trabajos del runbook de acción. Cada trabajo puede seleccionarse en la vista de detalles del trabajo.

   ![Trabajos de acción de monitor desde la interfaz de usuario](media/automation-watchers-tutorial/WatcherActionJobs.png)

La salida esperada cuando se encuentra el archivo nuevo puede verse en el ejemplo siguiente:

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Importar un runbook de monitor
> * Creación de una variable de Automation
> * Crear un runbook de acción
> * Crear una tarea de monitor
> * Desencadenar un monitor
> * Inspeccionar la salida

Siga este vínculo para más información sobre la creación de runbooks propios.

> [!div class="nextstepaction"]
> [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).
