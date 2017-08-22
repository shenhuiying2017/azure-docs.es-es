---
title: Hybrid Runbook Worker de Azure Automation | Microsoft Docs
description: "Este artículo brinda información sobre cómo instalar y usar Hybrid Runbook Worker, una característica de Azure Automation que le permite ejecutar runbooks en máquinas de su centro de datos local o proveedor de la nube."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 783fb22b0154915f2e3d8574ab95538dbd646705
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---

# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatización de recursos en los centros de datos o nube con Hybrid Runbook Worker
Los runbooks de Azure Automation no pueden tener acceso a los recursos de otras nubes o de entorno local, debido a que se ejecutan en la nube de Azure.  La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados.  

Esta funcionalidad se ilustra en la imagen siguiente:<br>  

![Información general de Trabajo híbrido de runbook](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para una descripción general técnica del rol de Hybrid Runbook Worker y las consideraciones de implementación, consulte [Información general sobre la arquitectura de Automation](automation-offering-get-started.md#automation-architecture-overview).    

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Trabajos híbridos de runbook
Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente.  Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en Hybrid Runbook Worker, se especifica el grupo en el que se ejecuta.  Los miembros del grupo determinan qué trabajo atiende a la solicitud.  No es posible especificar un trabajo determinado.

## <a name="relationship-to-service-management-automation"></a>Relación con Service Management Automation
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx)permite ejecutar los mismos Runbooks compatibles con Azure Automation en su centro de datos local. SMA normalmente se implementa junto con Windows Azure Pack, dado que Windows Azure Pack contiene una interfaz gráfica para la administración de SMA. A diferencia de Azure Automation, SMA requiere una instalación local que incluye servidores web para hospedar la API, una base de datos para que contenga la configuración de SMA y de los Runbooks y Runbook Workers para ejecutar los trabajos de Runbook. Azure Automation proporciona estos servicios en la nube y solo requiere que mantenga los Trabajos híbridos de runbook en su entorno local.

Si es un usuario existente de SMA, puede mover los runbooks a Azure Automation para usarlos con Hybrid Runbook Worker sin cambios, suponiendo que realizan su propia autenticación a recursos, tal como se describe en el artículo sobre [ejecución de runbooks para Hybrid Runbook Worker](automation-hrw-run-runbooks.md).  Los runbooks en SMA se ejecutan en el contexto de la cuenta de servicio en el servidor de trabajo, lo que puede proporciona esa autenticación para los runbooks.

Puede usar los criterios siguientes para determinar si Azure Automation con Trabajo híbrido de runbook o Service Management Automation es más adecuado para sus requisitos.

* SMA requiere la instalación local de sus componentes subyacentes que están conectados a Windows Azure Pack, en caso de que sea necesaria una interfaz gráfica de administración. Se requieren más recursos locales con costos de mantenimiento más elevados que Azure Automation, que solo necesita un agente instalado en Runbook Workers locales. Operations Management Suite administra los agentes, con lo que disminuyen los costos de mantenimiento.
* Azure Automation almacena sus Runbooks en la nube y los entrega a Hybrid Runbook Worker locales. Si su directiva de seguridad no permite este comportamiento, deberá usar SMA.
* SMA se incluye con System Center; y por tanto, requiere una licencia de System Center 2012 R2. Azure Automation se basa en un modelo de suscripción en niveles.
* Presenta características avanzadas, como Runbooks gráficos que no están disponibles en SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Windows 

Para instalar y configurar Hybrid Runbook Worker en Windows, hay dos métodos disponibles.  El método recomendado usa un runbook de Automation a fin de automatizar completamente el proceso necesario para configurar un equipo Windows.  El segundo método es un procedimiento paso a paso para instalar y configurar el rol manualmente.  

> [!NOTE]
> Para administrar la configuración de los servidores que admiten el rol de Hybrid Runbook Worker con la configuración de estado deseado (DSC), debe agregarlos como nodos de DSC.  Para obtener más información sobre su incorporación para la administración con DSC, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).           
><br>
>Si habilita la [solución Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md), los equipos Windows conectados al área de trabajo OMS se configurarán automáticamente como Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución.  Sin embargo, no está registrado en ningún grupo de Hybrid Worker ya definido en la cuenta de Automation.  El equipo se puede agregar a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir los runbooks de Automation siempre que use la misma cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker.  Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.  

Revise la siguiente información relacionada con los [requisitos de hardware y software](automation-offering-get-started.md#hybrid-runbook-worker) así como la [información para preparar la red](automation-offering-get-started.md#network-planning) antes de empezar a implementar la característica Hybrid Runbook Worker.  Después de haber implementado correctamente un trabajo de runbook, revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.  
 
### <a name="automated-deployment"></a>Implementación automatizada

Realice los pasos siguientes para automatizar la instalación y configuración del rol Hybrid Worker en Windows.  

1. Descargue el script *New-OnPremiseHybridWorker.ps1* de la [Galería de PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) directamente desde el equipo que ejecuta el rol Hybrid Runbook Worker o desde otro equipo de su entorno y cópielo en el rol de trabajo.  

    El script *New-OnPremiseHybridWorker.ps1* requiere los siguientes parámetros durante la ejecución:

  * *AutomationAccountName* (obligatorio): el nombre de la cuenta de Automation.  
  * *ResourceGroupName* (obligatorio): el nombre del grupo de recursos asociado a su cuenta de Automation.  
  * *HybridGroupName* (obligatorio): el nombre de un grupo de Hybrid Runbook Worker que se especifica como destino para los runbooks que admiten este escenario. 
  *  *SubscriptionID* (obligatorio): el id. de suscripción de Azure en el que se encuentra su cuenta de Automation.
  *  *WorkspaceName* (opcional): el nombre del área de trabajo de OMS.  Si no tiene un área de trabajo de OMS, el script creará y configurará una.  

     > [!NOTE]
     > Actualmente estas son las únicas regiones que admiten la integración de Automation con OMS: **sudeste de Australia**, **este de EE. UU. 2**, **Sudeste Asiático** y **Europa Occidental**.  Si su cuenta de Automation no está en una de esas regiones, el script crea un área de trabajo de OMS, pero le advierte que no se puede vincular.
     > 
2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** en modo de administrador.  
3. Desde el shell de la línea de comandos de PowerShell, vaya hasta la carpeta que contiene el script que descargó y ejecútelo cambiando los valores de los parámetros *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* y *-WorkspaceName*.

     > [!NOTE] 
     > Se le solicitará que se autentique en Azure después de ejecutar el script.  **Debe** iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Se le pedirá que acepte la instalación de **NuGet** y que se autentique con sus credenciales de Azure.<br><br> ![Ejecución del script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Una vez completado el script, la hoja Grupos de Hybrid Worker muestra el nuevo grupo y el número de miembros o, si es un grupo existente, el número de miembros aumenta.  Puede seleccionar el grupo de la lista en la hoja **Grupos de Hybrid Worker** y seleccionar el icono **Hybrid Workers**.  En la hoja **Hybrid Workers**, verá que aparece cada miembro del grupo.  

### <a name="manual-deployment"></a>Implementación manual 
Realice los dos primeros pasos una vez para su entorno de Automation y después repita los pasos restantes en cada equipo de trabajo.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Creación del área de trabajo de Operations Management Suite
Si todavía no tiene un área de trabajo de Operations Management Suite, puede crear uno siguiendo las instrucciones que aparecen en [Administración del área de trabajo](../log-analytics/log-analytics-manage-access.md). Si cuenta con un área de trabajo existente, puede usarla.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Adición de la solución Automation al área de trabajo de Operations Management Suite
Las soluciones agregan funcionalidad a Operations Management Suite.  La solución Automation agrega funcionalidad a Azure Automation, incluida la compatibilidad con Hybrid Runbook Worker.  Cuando se agrega la solución al área de trabajo, se insertan automáticamente los componentes de trabajo al equipo del agente que va a instalar en el paso siguiente.

Siga las instrucciones de [Para agregar una solución mediante la Galería de soluciones](../log-analytics/log-analytics-add-solutions.md) para agregar la solución de **Automation** al área de trabajo de Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalación de Microsoft Monitoring Agent
El agente de Microsoft Monitoring Agent conecta los equipos a Operations Management Suite.  Cuando instala el agente en el equipo local y se conecte al espacio de trabajo, se descargarán automáticamente los componentes necesarios para Hybrid Runbook Worker.

Siga las instrucciones que se encuentran en [Conexión de equipos Windows a Log Analytics](../log-analytics/log-analytics-windows-agents.md) para instalar el agente en el equipo local.  Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

Cuando el agente se conecta correctamente a Operations Management Suite, se enumerará en la pestaña **Orígenes conectados** del panel **Configuración** de Operations Management Suite.  Puede comprobar que el agente ha descargado correctamente la solución Automation cuando tiene una carpeta llamada **AzureAutomationFiles** en C:\Program Files\Microsoft Monitoring Agent\Agent.  Para confirmar la versión de Hybrid Runbook Worker, puede ir a C:\Archivos de programa\Microsoft Monitoring Agent\Agent\AzureAutomation\ y examinar la subcarpeta \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalación del entorno de runbook y conexión con Azure Automation
Cuando agrega un agente a Operations Management Suite, la solución Automation inserta el módulo **HybridRegistration** de PowerShell, que contiene el cmdlet **Add-HybridRunbookWorker**.  Este cmdlet se usa para instalar el entorno de runbook en el equipo y registrarlo con Azure Automation.

Abra una sesión de PowerShell en modo de Administrador y ejecute el comando siguientes para importar el módulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

A continuación, ejecute el cmdlet **Add-HybridRunbookWorker** con la siguiente sintaxis:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Puede obtener la información necesaria para este cmdlet en la hoja **Administrar claves** de Azure Portal.  Para abrir esta hoja, seleccione la opción **Claves** en la hoja **Configuración** de su cuenta de Automation.

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** es el nombre del grupo de Hybrid Runbook Worker. Si este grupo ya existe en la cuenta de Automation, se le agregará el equipo actual.  Si todavía no existe, se agregará.
* **EndPoint** es el campo **URL** de la hoja **Administrar claves**.
* **Token** es la **Clave de acceso primaria** en la hoja **Administrar claves**.  

Use el modificador **-Verbose** con **Add-HybridRunbookWorker** para recibir información detallada sobre la instalación.

#### <a name="5-install-powershell-modules"></a>5. Instalación de módulos PowerShell
Los runbooks pueden usar cualquiera de las actividades y los cmdlets definidos en los módulos instalados en el entorno de Azure Automation.  Estos módulos no se implementan automáticamente en equipos locales, por lo que debe instalarlos de forma manual.  La excepción es el módulo de Azure, que se instala de manera predeterminada y brinda acceso a los cmdlets para todos los servicios y actividades de Azure para Azure Automation.

Debido a que el propósito principal de la característica Hybrid Runbook Worker es administrar recursos locales, es muy probable que deba instalar los módulos que admiten estos recursos.  Puede consultar [Instalación de módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obtener información sobre cómo instalar módulos de Windows PowerShell.  Los módulos instalados deben estar en una ubicación a la que haga referencia la variable de entorno PSModulePath para que Hybrid Worker los importe automáticamente.  Para obtener más información, consulte [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Modificación de la ruta de instalación PSModulePath). 

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Linux
Para instalar y configurar una instancia de Hybrid Runbook Worker en Linux, hay que seguir un procedimiento muy sencillo para instalar y configurar el rol manualmente.  Es necesario habilitar la solución **Automation Hybrid Worker** en el área de trabajo de OMS y después ejecutar un conjunto de comandos para registrar el equipo como un trabajo y agregarlo a un grupo nuevo u otro existente. 

1.  Habilite la solución "Automation Hybrid Worker" en OMS. Esto se puede hacer de la siguiente forma:

   1. En la Galería de soluciones del [Portal de OMS](https://mms.microsoft.com), habilite la solución **Automation Hybrid Worker**.
   2. Ejecute el siguiente cmdlet:

        ```$null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```
2.  Run the following command with the proper parameters (endpoint and key can be taken from the portal from the automation account linked to the workspace used in the steps above):
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <automationsharedkey> --groupname <hybridgroupname> -e <automationendpoint>


## Removing Hybrid Runbook Worker 
You can remove one or more Hybrid Runbook Workers from a group or you can remove the group, depending on your requirements.  To remove a Hybrid Runbook Worker from an on-premises computer, perform the following steps.

1. In the Azure portal, navigate to your Automation account.  
2. From the **Settings** blade, select **Keys** and note the values for field **URL** and **Primary Access Key**.  You need this information for the next step.
3. Open a PowerShell session in Administrator mode and run the following command - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use the **-Verbose** switch for a detailed log of the removal process.

> [!NOTE]
> This does not remove the Microsoft Monitoring Agent from the computer, only the functionality and configuration of the Hybrid Runbook Worker role.  

## Remove Hybrid Worker groups
To remove a group, you first need to remove the Hybrid Runbook Worker from every computer that is a member of the group using the procedure shown earlier, and then you perform the following steps to remove the group.  

1. Open the Automation account in the Azure portal.
2. Select the **Hybrid Worker Groups** tile and in the **Hybrid Worker Groups** blade, select the group you wish to delete.  After selecting the specific group, the **Hybrid worker group** properties blade is displayed.<br> ![Hybrid Runbook Worker Group Blade](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. On the properties blade for the selected group, click **Delete**.  A message appears asking you to confirm this action, select **Yes** if you are sure you want to proceed.<br> ![Delete Group Confirmation Dialog](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> This process can take several seconds to complete and you can track its progress under **Notifications** from the menu.  

## Troubleshooting 
The Hybrid Runbook Worker depends on the Microsoft Monitoring Agent to communicate with your Automation account to register the worker, receive runbook jobs, and report status. If  registration of the worker fails, here are some possible causes for the error:  

1. The hybrid worker is behind a proxy or firewall.  
    Verify the computer has outbound access to *.azure-automation.net on port 443.  

2. The computer the hybrid worker is running on has less than the minimum hardware [requirements](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computers running the Hybrid Runbook Worker should meet the minimum hardware requirements before designating it to host this feature. Otherwise, depending on the resource utilization of other background processes and contention caused by runbooks during execution, the computer will become over utilized and cause runbook job delays or timeouts.
    Confirm the computer designated to run the Hybrid Runbook Worker feature meets the minimum hardware requirements.  If it does, monitor CPU and memory utilization to determine any correlation between the performance of Hybrid Runbook Worker processes and Windows.  If there is memory or CPU pressure, this may indicate the need to upgrade or add additional processors, or increase memory to address the resource bottleneck and resolve the error. Alternatively, select a different compute resource that can support the minimum requirements and scale when workload demands indicate an increase is necessary.
    
3. The Microsoft Monitoring Agent service is not running.  
    If the Microsoft Monitoring Agent Windows service is not running, this prevents the Hybrid Runbook Worker from communicating with Azure Automation.  Verify the agent is running by entering the following command in PowerShell: `get-service healthservice`.  If the service is stopped, enter the following command in PowerShell to start the service: `start-service healthservice`.  

4. In the **Application and Services Logs\Operations Manager** event log, you see event 4502  and EventMessage containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** with the following description:  *The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. The article KB3126513 has additional troubleshooting information for connectivity issues.*
    This can be caused by your proxy or network firewall blockking communication to Microsoft Azure.  Verify the computer has outbound access to *.azure-automation.net on ports 443.

Logs are stored locally on each hybrid worker at C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  You can check if there are any warning or error events written to the **Application and Services Logs\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** event log that would indicate a connectivity or other issue affecting onboarding of the role to Azure Automation or issue while performing normal operations.  

## Next steps
Review [run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

