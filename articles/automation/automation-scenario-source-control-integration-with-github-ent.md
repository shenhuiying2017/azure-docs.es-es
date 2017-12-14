---
title: "Integración del código fuente de Azure Automation con GitHub Enterprise | Microsoft Docs"
description: "Se describen los detalles de cómo configurar la integración con GitHub Enterprise para el control del código fuente de Runbooks de Automation."
services: automation
documentationCenter: 
authors: georgewallace
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: cf72c6d05e2872bea84b8a7218bd318d5b8c9694
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Escenario de Azure Automation: integración del control del código fuente de Automation con GitHub Enterprise

Automation admite actualmente la integración del control del código fuente, que permite asociar Runbooks de su cuenta de Automation a un repositorio de control del código fuente de GitHub.  Sin embargo, los clientes que han implementado [GitHub Enterprise](https://enterprise.github.com/home) para permitir la compatibilidad con sus prácticas de DevOps, también lo quieren usar para administrar el ciclo de vida de los Runbooks desarrollados para automatizar los procesos de negocio y las operaciones de administración de servicios.  

En este escenario, tendrá un equipo Windows en su centro de datos configurado como un Hybrid Runbook Worker que tiene instalados los módulos de Azure Resource Manager y herramientas de Git.  La máquina de Hybrid Worker tendrá un clon del repositorio de GIT local.  Cuando el Runbook se ejecuta en el Hybrid Worker, el directorio de GIT se sincroniza y el contenido de los archivos del Runbook se importa en la cuenta de Automation.

En este artículo se describe cómo realizar esta configuración en el entorno de Azure Automation. Para comenzar, se configurará Automation con las credenciales de seguridad, los Runbooks necesarios para admitir este escenario y la implementación de un Hybrid Runbook Worker en su centro de datos para ejecutar los Runbooks y acceder al repositorio de GitHub Enterprise para sincronizarlos con la cuenta de Automation.  


## <a name="getting-the-scenario"></a>Obtención del escenario

Este escenario consta de dos Runbooks de PowerShell que puede importar directamente desde la [Galería de Runbooks](automation-runbook-gallery.md) en Azure Portal o descargar de la [Galería de PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Descripción| 
--------|------------|
Export-RunAsCertificateToHybridWorker | El Runbook exportará un certificado de ejecución desde una cuenta de Automation a un Hybrid Worker de forma que los Runbooks de trabajo puedan autenticarse con Azure a fin de importarlos en la cuenta de Automation.| 
Sync-LocalGitFolderToAutomationAccount | El Runbook sincronizará la carpeta GIT local de la máquina híbrida y luego importará los archivos de Runbook (*.ps1) en la cuenta de Automation.|

### <a name="credentials"></a>Credenciales

Credential: | Descripción|
-----------|------------|
GitHRWCredential | El recurso de credencial que creará que contiene el nombre de usuario y la contraseña de un usuario con permisos para Hybrid Worker.|

## <a name="installing-and-configuring-this-scenario"></a>Instalación y configuración de este escenario

### <a name="prerequisites"></a>Requisitos previos

1. El Runbook Sync-LocalGitFolderToAutomationAccount se autentica mediante la [cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md). 

2. También se necesita un área de trabajo de Microsoft Operations Management Suite (OMS) con la solución Azure Automation habilitada y configurada.  Si no tiene una asociada con la cuenta de Automation usada para instalar y configurar este escenario, se creará y configurará automáticamente al ejecutar el script **New-OnPremiseHybridWorker.ps1** desde Hybrid Runbook Worker.        

    > [!NOTE]
    > Actualmente, solo las siguientes regiones admiten la integración de Automation con OMS: **sudeste de Australia**, **este de EE. UU. 2**, **Sudeste Asiático** y **Europa Occidental**. 

3. Un equipo que puede funcionar como Hybrid Runbook Worker dedicado que también hospedará el software de GitHub y mantendrá los archivos de Runbook (*runbook*.ps1) en un directorio de origen en el sistema de archivos para sincronizarlos entre GitHub y la cuenta de Automation.

### <a name="import-and-publish-the-runbooks"></a>Instalación y publicación de los Runbooks

Para importar los Runbooks *Export-RunAsCertificateToHybridWorker* y *Sync-LocalGitFolderToAutomationAccount* desde la Galería de Runbooks de su cuenta de Automation en Azure Portal, siga los procedimientos que se describen en [Importación de Runbooks desde la Galería de Runbooks](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publique los Runbooks después de que se hayan importado correctamente en su cuenta de Automation.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Implementación y configuración de Hybrid Runbook Worker

Si no tiene ya implementada una solución Hybrid Runbook Worker en su centro de datos, debe revisar los requisitos y seguir los pasos de instalación automatizada mediante el procedimiento que se describe en [Azure Automation Hybrid Runbook Workers: automatización de la instalación y la configuración](automation-hybrid-runbook-worker.md#automated-deployment).  Cuando haya instalado correctamente el Hybrid Worker en un equipo, realice los siguientes pasos para completar su configuración y hacer posible este escenario.

1. Inicie sesión en el equipo que hospeda el rol Hybrid Runbook Worker con una cuenta que tenga derechos administrativos y cree un directorio que contenga los archivos de Runbook de GIT.  Clone el repositorio de GIT interno en el directorio.
2. Si aún no tiene una cuenta de ejecución creada o quiere crear una nueva dedicada para este fin, en Azure Portal, vaya a las cuentas de Automation, seleccione su cuenta de Automation y cree un [recurso de credencial](automation-credentials.md) que contenga el nombre de usuario y la contraseña de un usuario con permisos para el Hybrid Worker.  
3. Desde su cuenta de Automation, [edit el Runbook](automation-edit-textual-runbook.md) **Export-RunAsCertificateToHybridWorker** y modifique el valor de la variable *$Password* con una contraseña segura.  Después de modificar el valor, haga clic en **Publicar** para publicar la versión de borrador del Runbook. 
5. Inicie el Runbook **Export-RunAsCertificateToHybridWorker**, y en la hoja **Start Runbook** (Iniciar Runbook), en la opción **Parámetros de ejecución**, seleccione la opción **Hybrid Worker**. En la lista desplegable, seleccione el grupo de Hybrid Worker que creó anteriormente para este escenario.  

    Esta acción exportará un certificado al Hybrid Worker para que los Runbooks de trabajo puedan autenticarse con Azure mediante la conexión de ejecución para administrar los recursos de Azure (para este escenario en concreto, importe los Runbooks en la cuenta de Automation).

4. En su cuenta de Automation, seleccione el grupo de Hybrid Worker creado anteriormente y [especifique una cuenta de ejecución](automation-hrw-run-runbooks.md#runas-account) para él; a continuación, elija el recurso de credencial que ya había creado o que acaba de crear.  Esto garantiza que el Runbook de sincronización puede ejecutar comandos de GIT. 
5. Inicie el Runbook **Sync-LocalGitFolderToAutomationAccount**, proporcione los siguientes valores de parámetros de entrada necesarios y, en la hoja **Start Runbook** (Iniciar Runbook), en la opción **Parámetros de ejecución**, seleccione la opción **Hybrid Worker**. En la lista desplegable, seleccione el grupo de Hybrid Worker que creó anteriormente para este escenario:
    * *ResourceGroup*: el nombre del grupo de recursos asociado con la cuenta de Automation.
    * *AutomationAccountName* : el nombre de la cuenta de Automatización.
    * *GitPath*: la carpeta local o el archivo en el Hybrid Runbook Worker donde está configurado GIT para extraer los últimos cambios.

    La carpeta de GIT local se sincronizará en el equipo de Hybrid Worker y luego se importarán los archivos .ps1 desde el directorio de origen hasta la cuenta de Automation.

    ![Start Sync-LocalGitFolderToAutomationAccount Runbook](media/automation-scenario-source-control-integration-with-github-ent/start-runbook-synclocalgitfoldertoautoacct.png)<br>

7. Vea los detalles de resumen del trabajo del runbook; para ello, selecciónelo en la hoja **Runbooks** de su cuenta de Automation y luego seleccione el icono **Trabajos**.  Para confirmar que se ha completado correctamente, seleccione el icono **All logs** (Todos los registros) y revise la secuencia de registro detallada.  

## <a name="next-steps"></a>Pasos siguientes

-  Para obtener más información sobre los tipos de runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Azure Automation](automation-runbook-types.md)
-  Para obtener más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
