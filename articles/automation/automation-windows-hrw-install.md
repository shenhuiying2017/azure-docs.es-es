---
title: Hybrid Runbook Worker de Azure Automation en Windows
description: En este artículo encontrará información sobre cómo instalar un Hybrid Runbook Worker de Azure Automation que le permita ejecutar runbooks en equipos con Windows en su centro de datos local o entorno de nube.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 30eda7683a1e8c27eb117b92744bf90eae3fd5d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193839"
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Cómo implementar un Hybrid Runbook Worker en Windows

La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados. En este artículo, se describe cómo instalar Hybrid Runbook Worker en una máquina Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Windows

Para instalar y configurar Hybrid Runbook Worker en Windows, hay dos métodos disponibles. El método recomendado usa un runbook de Automation a fin de automatizar completamente el proceso necesario para configurar un equipo Windows. El segundo método es un procedimiento paso a paso para instalar y configurar el rol manualmente.

> [!NOTE]
> Para administrar la configuración de los servidores que admiten el rol de Hybrid Runbook Worker con la configuración de estado deseado (DSC), debe agregarlos como nodos de DSC.

Estos son los requisitos mínimos de un Hybrid Runbook Worker de Windows.

* Windows Server 2012 o posterior.
* Se requiere Windows PowerShell 4.0 o posterior ([descargar WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Windows PowerShell 5.1 ([descargar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) se recomienda para mayor confiabilidad.
* .NET Framework 4.6.2 o posterior.
* Dos núcleos como mínimo.
* Un mínimo de 4 GB de RAM.
* Puerto 443 (saliente)

Para ver los requisitos de red adicionales para Hybrid Runbook Worker, consulte el apartado sobre [configuración de la red](automation-hybrid-runbook-worker.md#network-planning).

Para obtener más información sobre su incorporación para la administración con DSC, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).
Si habilita la [solución Update Management](../operations-management-suite/oms-solution-update-management.md), los equipos Windows conectados al área de trabajo de Log Analytics se configurarán automáticamente como Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución. Sin embargo, no está registrado en ningún grupo de Hybrid Worker ya definido en la cuenta de Automation. El equipo se puede agregar a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir los runbooks de Automation siempre que use la misma cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker. Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.

Después de haber implementado correctamente un trabajo de runbook, revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.

### <a name="automated-deployment"></a>Implementación automatizada

Realice los pasos siguientes para automatizar la instalación y configuración del rol Hybrid Worker en Windows.

1. Descargue el script *New-OnPremiseHybridWorker.ps1* de la [Galería de PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) directamente desde el equipo que ejecuta el rol Hybrid Runbook Worker o desde otro equipo de su entorno y cópielo en el rol de trabajo.

   El script *New-OnPremiseHybridWorker.ps1* requiere los siguientes parámetros durante la ejecución:

   * *AutomationAccountName* (obligatorio): el nombre de la cuenta de Automation.
   * *AAResourceGroupName* (obligatorio): el nombre del grupo de recursos asociado a su cuenta de Automation.
   * *OMSResourceGroupName* (opcional): el nombre del grupo de recursos para el área de trabajo de OMS. Si no se especifica, se utiliza AAResourceGroupName.
   * *HybridGroupName* (obligatorio): el nombre de un grupo de Hybrid Runbook Worker que se especifica como destino para los runbooks que admiten este escenario.
   * *SubscriptionID* (obligatorio): el id. de suscripción de Azure en el que se encuentra su cuenta de Automation.
   * *WorkspaceName* (opcional): el nombre del área de trabajo de Log Analytics. Si no tiene un área de trabajo de Log Analytics, el script creará y configurará una.

     > [!NOTE]
     > Actualmente estas son las únicas regiones que admiten la integración de Automation con Log Analytics: **Sudeste de Australia**, **Este de EE. UU. 2**, **Sudeste Asiático** y **Europa Occidental**. Si su cuenta de Automation no está en una de esas regiones, el script crea un área de trabajo de Log Analytics, pero le advierte que no se puede vincular.

1. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** en modo de administrador.
1. Desde el shell de la línea de comandos de PowerShell, vaya hasta la carpeta que contiene el script que descargó y ejecútelo cambiando los valores de los parámetros *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* y *-WorkspaceName*.

     > [!NOTE]
     > Se le solicitará que se autentique en Azure después de ejecutar el script. **Debe** iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Se le pedirá que acepte la instalación de **NuGet** y que se autentique con sus credenciales de Azure.

1. Una vez completado el script, la página Grupos de Hybrid Worker muestra el nuevo grupo y el número de miembros o, si es un grupo existente, el número de miembros aumenta. Puede seleccionar el grupo de la lista en la página **Grupos de Hybrid Worker** y seleccionar el icono **Hybrid Workers**. En la página **Hybrid Workers**, verá que aparece cada miembro del grupo.

### <a name="manual-deployment"></a>Implementación manual

Realice los dos primeros pasos una vez para su entorno de Automation y después repita los pasos restantes en cada equipo de trabajo.

#### <a name="1-create-log-analytics-workspace"></a>1. Creación de un área de trabajo de Log Analytics

Si todavía no tiene un área de trabajo de Log Analytics, puede crear una mediante las instrucciones que se indican en [Administración del área de trabajo](../log-analytics/log-analytics-manage-access.md). Si cuenta con un área de trabajo existente, puede usarla.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Adición de la solución de Automation al área de trabajo de Log Analytics

Las soluciones agregan funcionalidad a Log Analytics. La solución de Automation agrega funcionalidad a Azure Automation, incluida la compatibilidad con Hybrid Runbook Worker. Cuando se agrega la solución al área de trabajo, se insertan automáticamente los componentes de trabajo al equipo del agente que va a instalar en el paso siguiente.

Siga las instrucciones que se indican en [Adición de soluciones mediante la galería de soluciones](../log-analytics/log-analytics-add-solutions.md) para agregar la solución de **Automation** l área de trabajo de Log Analytics.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalación de Microsoft Monitoring Agent

Microsoft Monitoring Agent conecta los equipos a Log Analytics. Cuando instala el agente en el equipo local y se conecte al espacio de trabajo, se descargarán automáticamente los componentes necesarios para Hybrid Runbook Worker.

Siga las instrucciones que se encuentran en [Conexión de equipos Windows a Log Analytics](../log-analytics/log-analytics-windows-agent.md) para instalar el agente en el equipo local. Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

Cuando el agente se ha conectado correctamente a Log Analytics, se mostrará en la pestaña **Orígenes conectados** del panel **Configuración** de Log Analytics. Puede comprobar que el agente ha descargado correctamente la solución Automation cuando tiene una carpeta llamada **AzureAutomationFiles** en C:\Program Files\Microsoft Monitoring Agent\Agent. Para confirmar la versión de Hybrid Runbook Worker, puede ir a C:\Archivos de programa\Microsoft Monitoring Agent\Agent\AzureAutomation\ y examinar la subcarpeta \\*version*.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalación del entorno de runbook y conexión con Azure Automation

Cuando se agrega un agente a Log Analytics, la solución de Automation inserta el módulo **HybridRegistration** de PowerShell, que contiene el cmdlet **Add-HybridRunbookWorker**. Este cmdlet se usa para instalar el entorno de runbook en el equipo y registrarlo con Azure Automation.

Abra una sesión de PowerShell en modo de Administrador y ejecute el comando siguientes para importar el módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

A continuación, ejecute el cmdlet **Add-HybridRunbookWorker** con la siguiente sintaxis:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Puede obtener la información necesaria para este cmdlet en la página **Administrar claves** de Azure Portal. Para abrir esta página, seleccione la opción **Claves** en la página **Configuración** de su cuenta de Automation.

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** es el nombre del grupo de Hybrid Runbook Worker. Si este grupo ya existe en la cuenta de Automation, se le agregará el equipo actual. Si todavía no existe, se agregará.
* **EndPoint** es el campo **URL** de la página **Administrar claves**.
* **Token** es la **Clave de acceso primaria** en la página **Administrar claves**.

Use el modificador **-Verbose** con **Add-HybridRunbookWorker** para recibir información detallada sobre la instalación.

#### <a name="5-install-powershell-modules"></a>5. Instalación de módulos PowerShell

Los runbooks pueden usar cualquiera de las actividades y los cmdlets definidos en los módulos instalados en el entorno de Azure Automation. Estos módulos no se implementan automáticamente en equipos locales, por lo que debe instalarlos de forma manual. La excepción es el módulo de Azure, que se instala de manera predeterminada y brinda acceso a los cmdlets para todos los servicios y actividades de Azure para Azure Automation.

Debido a que el propósito principal de la característica Hybrid Runbook Worker es administrar recursos locales, es muy probable que deba instalar los módulos que admiten estos recursos. Puede consultar [Instalación de módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obtener información sobre cómo instalar módulos de Windows PowerShell. Los módulos instalados deben estar en una ubicación a la que haga referencia la variable de entorno PSModulePath para que Hybrid Worker los importe automáticamente. Para más información, consulte [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Modificación de la ruta de instalación PSModulePath).

## <a name="troubleshooting"></a>solución de problemas

Hybrid Runbook Worker depende de Microsoft Monitoring Agent para comunicarse con su cuenta de Automation para registrar el trabajo, recibir trabajos de runbook e informar del estado. Si se produce un error de registro del trabajo, estas son algunas de las causas posibles:

### <a name="the-microsoft-monitoring-agent-is-not-running"></a>No se está ejecutando Microsoft Monitoring Agent.

El servicio de Windows Microsoft Monitoring Agent no se está ejecutando, lo que impide que Hybrid Runbook Worker se comunique con Azure Automation. Compruebe el agente se está ejecutando escribiendo el comando siguiente en PowerShell: `Get-Service healthservice`. Si se detiene el servicio, escriba el siguiente comando de PowerShell para iniciar el servicio: `Start-Service healthservice`.

### <a name="event-4502-in-operations-manager-log"></a>Evento 4502 del registro de Operations Manager

En el registro de eventos **Registros de aplicaciones y servicios\Operations Manager**, puede ver el evento 4502 y un mensaje de evento que contiene **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**, donde se indica que *el certificado presentado por el servicio \<wsid\>.oms.opinsights.azure.com no fue emitido por una entidad de certificación usada para los servicios de Microsoft. Póngase en contacto con el administrador de red para comprobar si están ejecutando un proxy que intercepta la comunicación TLS/SSL. El artículo KB3126513 incluye información adicional para la solución de problemas de conectividad.*

Esto puede deberse a que el firewall de red o de proxy está bloqueando la comunicación con Microsoft Azure. Compruebe que el equipo tenga acceso saliente a *.azure-automation.net en los puertos 443.

Los registros se almacenan localmente en cada Hybrid Worker en C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Puede comprobar si hay algún evento de error o advertencia en el registro de eventos **Registros de aplicaciones y servicios\Microsoft-SMA\Operations** y **Registros de aplicaciones y servicios\Operations Manager** que podría indicar que un problema de conectividad u otro problema afecta a la incorporación del rol a Azure Automation o un problema al realizar las operaciones normales.

Para obtener pasos adicionales sobre cómo solucionar problemas relacionados con Update Management, consulte [Update Management: solución de problemas](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Pasos siguientes

* Revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.
* Para obtener instrucciones sobre cómo quitar Hybrid Runbook Workers, vea [Quitar Hybrid Runbook Workers de Azure Automation](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker).