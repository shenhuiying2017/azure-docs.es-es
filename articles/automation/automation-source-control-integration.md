---
title: "Integración del control de código fuente en Azure Automation | Microsoft Docs"
description: "En este artículo se describe la integración del control de código fuente con GitHub en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 224d7375-9887-44dd-b137-06ffe396a4b4
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;sngun
ms.openlocfilehash: bb1ce4ceaa3d0c9aea014fc810ea269641dec14c
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="source-control-integration-in-azure-automation"></a>Integración del control de código fuente en Azure Automation
La integración del control de código fuente permite asociar runbooks de su cuenta de Automation a un repositorio de control de código fuente de GitHub. El control de código fuente le permite colaborar fácilmente con su equipo, realizar el seguimiento de los cambios y revertir a versiones anteriores de los runbooks. Por ejemplo, le permite sincronizar distintas ramas de control de código fuente con sus cuentas de Automation de desarrollo, prueba o producción, lo que facilita la promoción de código que se ha probado en el entorno de desarrollo a la cuenta de Automation de producción.

El control de código fuente le permite insertar código de Azure Automation en el control de código fuente o extraer sus runbooks del control de código fuente para llevarlos a Azure Automation. En este artículo se describe cómo configurar el control de código fuente en su entorno de Azure Automation. Para comenzar, vamos a configurar Azure Automation para acceder al repositorio de GitHub y a recorrer diferentes operaciones que pueden realizarse mediante la integración del control de código fuente. 

> [!NOTE]
> El control de código fuente admite la extracción e inserción de [runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) y de [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks). Los [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) aún no se admiten.<br><br>
> 
> 

Hay dos pasos sencillos necesarios para configurar el control de código fuente para su cuenta de Automation y solo uno si ya tiene una cuenta GitHub. Son las siguientes:

## <a name="step-1--create-a-github-repository"></a>Paso 1: Creación de un repositorio de GitHub
Si ya tiene una cuenta de GitHub y un repositorio que desea vincular a Azure Automation, inicie sesión en la cuenta y comience desde el paso 2. De lo contrario, vaya a [GitHub](https://github.com/), suscríbase a una cuenta nueva y [cree un nuevo repositorio](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Paso 2: Configuración del control de código fuente en Azure Automation
1. En la página de la cuenta de Automation de Azure Portal, haga clic en **Configurar control de código fuente**. 
   
    ![Configuración del control de código fuente](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
2. Se abre la página **Control de código fuente**, donde puede configurar los detalles de la cuenta de GitHub. A continuación se muestra la lista de parámetros de configuración:  
   
   | **Parámetro** | **Descripción** |
   |:--- |:--- |
   | Elegir origen |Seleccione el origen. Actualmente, solo se admite **GitHub** . |
   | Autorización |Haga clic en el botón **Autorizar** para conceder acceso a Azure Automation al repositorio de GitHub. Si ya inició sesión con su cuenta de GitHub en una ventana diferente, se usan las credenciales de dicha cuenta. Cuando la autorización sea correcta, en la página se mostrará su nombre de usuario de GitHub en **Authorization Property** (Propiedad de autorización). |
   | Selección del repositorio |Seleccione un repositorio de GitHub en la lista de repositorios disponibles. |
   | Elegir rama |Seleccione una rama en la lista de ramas disponibles. Solo se muestra la rama **principal** si no creó ninguna rama. |
   | Ruta de acceso de la carpeta de runbook |La ruta de acceso de la carpeta de runbook especifica la ruta de acceso en el repositorio de GitHub desde el que desea insertar o extraer el código. Debe especificarse en formato **/nombreCarpeta/nombreDeSubcarpeta**. Solo se pueden sincronizar los runbooks de la ruta de acceso de la carpeta de runbook con la cuenta de Automation. Los runbooks de las subcarpetas de la ruta de acceso de la carpeta de runbook **no** se sincronizarán. Use **/** para sincronizar todos los runbooks en el repositorio. |
3. Por ejemplo, si tiene un repositorio denominado **scriptsDePowerShell** que contiene una carpeta denominada **carpetaRaíz**, que a su vez contiene una carpeta denominada **subCarpeta**. Puede usar las siguientes cadenas para cada nivel de la carpeta de sincronización:
   
   1. Para sincronizar runbooks desde el **repositorio**, la ruta de acceso de la carpeta de runbook es */*
   2. Para sincronizar runbooks desde el **carpetaRaíz**, la ruta de acceso de la carpeta de runbook es */carpetaRaíz*
   3. Para sincronizar runbooks desde la **subCarpeta**, la ruta de acceso de la carpeta de runbook es */carpetaRaíz/subCarpeta*.
4. Después de configurarlos, los parámetros se muestran en la página **Configurar control de código fuente**.  
   
    ![Página Configurar control de código fuente](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. Al hacer clic en **Aceptar**, la integración del control de código fuente estará configurada para la cuenta de Automation y debe actualizarse con la información de GitHub. Ahora puede hacer clic en este elemento para ver todo el historial de trabajos de sincronización del control de código fuente.  
   
    ![Valores de repositorio](media/automation-source-control-integration/automation_03_RepoValues.png)
6. Una vez configurado el control de código fuente, se crearán los siguientes recursos de Automation en su cuenta de Automation:  
   Se crean dos [recursos de variables](automation-variables.md).  
   
   * La variable **Microsoft.Azure.Automation.SourceControl.Connection** contiene los valores de la cadena de conexión, tal como se muestra a continuación.  
     
     | **Parámetro** | **Valor** |
     |:--- |:--- |
     | Nombre |Microsoft.Azure.Automation.SourceControl.Connection |
     | Tipo |String |
     | Valor |{"Branch":\<*nombreDeRama*>,"RunbookFolderPath":\<*rutaDeCarpetaDeRunbook*>,"ProviderType":\<*tiene un valor de 1 para GitHub*>,"Repository":\<*nombreDelRepositorio*>,"Username":\<*nombreDe UsuarioDeGitHub*>} |

    * La variable **Microsoft.Azure.Automation.SourceControl.OAuthToken**contiene el valor cifrado seguro de OAuthToken.  

    |**Parámetro**            |**Valor** |
    |:---|:---|
    | Nombre  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Tipo | Unknown(Encrypted) |
    | Valor | <*OAuthToken cifrado*> |  

    ![Variables](media/automation-source-control-integration/automation_04_Variables.png)  

    * 
            **control de código fuente de Automation** como una aplicación autorizada a su cuenta de GitHub. Para ver la aplicación: desde la página principal de GitHub, vaya a **perfil** > **Configuración** > **Aplicaciones**. Esta aplicación permite que Azure Automation sincronice el repositorio de GitHub con una cuenta de Automation.  

    ![Aplicación Git](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Uso del control de código fuente en Automation
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Inserción de un runbook de Azure Automation en el repositorio de control de código fuente
La inserción de runbooks en el repositorio permite insertar los cambios realizados en un runbook de Azure Automation en el repositorio de control de código fuente. A continuación se muestran los pasos necesarios para insertar un runbook en el repositorio:

1. Desde su cuenta de Automation, [cree un nuevo runbook textual](automation-first-runbook-textual.md) o [edite un runbook textual existente](automation-edit-textual-runbook.md). Este runbook puede ser un flujo de trabajo de PowerShell o un runbook de scripts de PowerShell.  
2. Después de editar el runbook, guárdelo y haga clic en **Insertar en el repositorio** en la página **Editar**.  
   
    ![Botón Proteger](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Con la inserción en el repositorio de Azure Automation se sobrescribe el código existente en el control de código fuente. La instrucción de la línea de comandos de Git equivalente para la inserción en el repositorio es **git add + git commit + git push**  

1. Al hacer clic en **Insertar en el repositorio**, se muestra un mensaje de confirmación. Haga clic en **Sí** para continuar.  
   
    ![Mensaje de protección](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. La protección se inicia en el runbook de control de código fuente: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook se conecta a GitHub y aplica los cambios realizados de Azure Automation al repositorio. Para ver el historial de trabajos de inserción en el repositorio, vuelva a la pestaña **Integración del control de código fuente** y haga clic para abrir la página Sincronización de repositorios. Esta página muestra todos los trabajos de control de código fuente.  Seleccione el trabajo que quiere ver y haga clic para ver los detalles.  
   
    ![Runbook de protección](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Los runbooks de control de código fuente son runbooks de Automation especiales que no se pueden ver ni editar. Aunque no se muestran en la lista de runbooks, verá que se muestran los trabajos de sincronización en la lista de trabajos.
   > 
   > 
3. El nombre del runbook modificado se envía como parámetro de entrada al runbook de inserción en el repositorio. También puede [ver los detalles del trabajo](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) si expande el runbook en la página **Sincronización de repositorios**.  
   
    ![Entrada de protección](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Actualice el repositorio de GitHub cuando finalice el trabajo para ver los cambios.  Debe haber una confirmación en el repositorio con un mensaje de confirmación: ***Nombre de runbook* actualizado en Azure Automation**.  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Sincronización de runbooks de control de código fuente a Azure Automation
El botón de sincronización que se encuentra en la página Sincronización de repositorios permite extraer todos los runbooks desde la ruta de acceso de la carpeta de runbooks del repositorio y llevarlos a la cuenta de Automation. El mismo repositorio puede sincronizarse con más de una cuenta de Automation. A continuación se muestran los pasos necesarios para sincronizar un runbook:

1. En la cuenta de Automation donde se configuró el control de código fuente, abra la página **Integración del control de código fuente/Sincronización de repositorios** y haga clic en **Sincronizar**.  En el mensaje de confirmación que se muestra, haga clic en **Sí** para continuar.  
   
    ![Botón Sincronizar](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. La sincronización inicia el runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Este runbook se conecta a GitHub y aplica los cambios realizados del repositorio a Azure Automation. Debería ver un nuevo trabajo en la página **Sincronización de repositorios** para esta acción. Para ver detalles sobre el trabajo de sincronización, haga clic para abrir la página de detalles del trabajo.  
   
    ![Runbook de sincronización](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > Una sincronización del control de código fuente sobrescribe la versión de borrador de los runbooks que existen actualmente en la cuenta de Automation para **TODOS** los runbooks que están actualmente en el control del código fuente. La instrucción de la línea de comandos de Git equivalente para realizar la sincronización es **git pull**


## <a name="troubleshooting-source-control-problems"></a>Solución de problemas de control de código fuente
Si hay errores en el trabajo de inserción en el repositorio o de sincronización, el estado del trabajo debe suspenderse. Verá más detalles sobre el error en la página del trabajo.  En la sección **Todos los registros** se muestran todos los flujos de PowerShell asociadas a ese trabajo. De esta forma tiene los detalles necesarios que le ayudarán a solucionar los problemas con la inserción en el repositorio o la sincronización. También se muestra la secuencia de acciones que se produjeron mientras se sincronizaba o insertaba un runbook en el repositorio.  

![Imagen de todos los registros](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Desconexión del control de código fuente
Para desconectarse de la cuenta de GitHub, abra la página Sincronización de repositorios y haga clic en **Desconectar**. Cuando desconecte el control de código fuente, los runbooks que se sincronizaron seguirán estando en su cuenta de Automation, pero no se habilitará la página Sincronización de repositorios.  

  ![Botón Desconectar](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la integración del control de código fuente, consulte los siguientes recursos:  

* 
            [Azure Automation: integración del control de código fuente en Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Vote por su sistema de control de código fuente favorito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* 
            [Azure Automation: integración del control de código fuente de runbook mediante Visual Studio Team Services](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

