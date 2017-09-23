---
title: Uso de Azure Automation para desencadenar un trabajo | Microsoft Docs
description: "Aprenda a usar Azure Automation para desencadenar trabajos de StorSimple Data Manager (versión preliminar privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.contentlocale: es-es
ms.lasthandoff: 03/21/2017

---

# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Uso de Azure Automation para desencadenar un trabajo (versión preliminar privada)

En este artículo se describe cómo usar Azure Automation para desencadenar un trabajo de StorSimple Data Manager.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que dispone de:

*   Azure Powershell instalado. [Descarga de Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Valores de configuración para inicializar el trabajo de transformación de datos (se incluyen las instrucciones necesarias para obtener dichos valores).
*   Una definición del trabajo que se ha configurado correctamente en un recurso de datos híbridos de un grupo de recursos.
*   Descargue el archivo [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) `DataTransformationApp.zip` del repositorio de GitHub.
*   Descargue el [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` del repositorio de GitHub.
*   Descargue el [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) `Trigger-DataTransformation-Job.ps1` del repositorio de GitHub.

## <a name="step-by-step"></a>Paso a paso

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Obtención de permisos de Azure Active Directory para que el trabajo de automatización ejecute la definición de trabajo

1. Para recuperar los parámetros de configuración de Active Directory, siga estos pasos:

    1. Abra Windows PowerShell en la máquina local. Asegúrese de que [Azure PowerShell](https://azure.microsoft.com/downloads/) esté instalado.
    1. Ejecute el script `Get-ConfigurationParams.ps1` (en la carpeta que descargó anteriormente). Escriba el comando siguiente en la ventana de PowerShell:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey es una contraseña que usará más adelante. Escriba una contraseña de su elección. AppName puede ser cualquier cadena.

2. Este script genera los valores siguientes que se deben usar mientras se desencadena el runbook de automatización. Anote estos valores.

    - Id. de cliente
    - Id. de inquilino
    - Clave de Active Directory (la misma que escribió anteriormente)
    - Id. de suscripción

### <a name="set-up-the-automation-account"></a>Configuración de la cuenta de Automation

1. Inicie sesión en Azure y abra su cuenta de Automation.
2. Haga clic en el icono **Recursos** para abrir la lista de recursos.
3. Haga clic en el icono **Módulos** para abrir la lista de módulos.
4. Haga clic en el botón **+ Agregar un módulo**; se iniciará la hoja Agregar módulo.

    ![Configuración de la cuenta de Automation](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Una vez que seleccione el archivo `DataTransformationApp.zip` en el equipo local, haga clic en **Aceptar** para importar el módulo.

   Cuando Azure Automation importa un módulo a su cuenta, extrae los metadatos sobre el módulo. Esta operación puede demorar unos minutos.

   ![Configuración de la cuenta de Automation](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Recibe una notificación que le indica que el módulo se está implementado y otra notificación cuando se complete el proceso.  También puede revisar el estado en el icono **Módulos**.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Para importar el runbook que desencadena la definición de trabajo

1. En el Portal de Azure, abra su cuenta de Automatización.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en **+ Agregar un runbook** y, luego, en **Importar un runbook existente**.

   ![Importar un runbook existente](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Haga clic en **Archivo de runbook** y seleccione el archivo que se va a importar `Trigger-DataTransformation-Job.ps1`.
5. Haga clic en **Crear** para importar el runbook. El runbook nuevo aparece en la lista de runbooks de la cuenta de Automation.
7. Haga clic en el runbook **Trigger-DataTransformation-Job** y, luego, en **Editar**.
8. Haga clic en **Publicar** y, luego, en **Sí** cuando se pida confirmación.


### <a name="to-run-the-runbook"></a>Para ejecutar el runbook:
1. En el Portal de Azure, abra su cuenta de Automatización.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en **Trigger-DataTransformation-Job**.
4. Haga clic en **Iniciar** para iniciar el runbook.

   ![Iniciar runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. En la hoja **Iniciar runbook**, escriba todos los parámetros. Haga clic en **Aceptar** para enviar el trabajo de Transformación de datos.

   ![Iniciar runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Pasos siguientes

[Manage using the StorSimple Data Manager service UI](storsimple-data-manager-ui.md) (Administración del uso de la interfaz de usuario del servicio StorSimple Data Manager).
