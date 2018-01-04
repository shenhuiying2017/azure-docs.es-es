---
title: Hybrid Runbook Worker de Azure Automation en Windows | Microsoft Docs
description: "En este artículo encontrará información sobre cómo instalar un Hybrid Runbook Worker de Azure Automation que le permita ejecutar runbooks en equipos con Windows en su centro de datos local o entorno de nube."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 71c98a7e17472ae0aa7646b9e7fc745363546211
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Cómo implementar un Hybrid Runbook Worker en Windows

Los runbooks de Azure Automation no pueden tener acceso a los recursos de otras nubes o de entorno local, debido a que se ejecutan en la nube de Azure.  La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados.  

Esta funcionalidad se ilustra en la imagen siguiente:<br>

![Información general de Trabajo híbrido de runbook](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para una descripción general técnica del rol de Hybrid Runbook Worker y las consideraciones de implementación, consulte [Información general sobre la arquitectura de Automation](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Trabajos híbridos de runbook

Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente.  Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en Hybrid Runbook Worker, se especifica el grupo en el que se ejecuta.  Los miembros del grupo determinan qué trabajo atiende a la solicitud.  No es posible especificar un trabajo determinado.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Windows 

Para instalar y configurar Hybrid Runbook Worker en Windows, hay dos métodos disponibles.  El método recomendado usa un runbook de Automation a fin de automatizar completamente el proceso necesario para configurar un equipo Windows.  El segundo método es un procedimiento paso a paso para instalar y configurar el rol manualmente.  

> [!NOTE]
> Para administrar la configuración de los servidores que admiten el rol de Hybrid Runbook Worker con la configuración de estado deseado (DSC), debe agregarlos como nodos de DSC.  Para obtener más información sobre su incorporación para la administración con DSC, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).        
Si habilita la [solución Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md), los equipos Windows conectados al área de trabajo OMS se configurarán automáticamente como Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución.  Sin embargo, no está registrado en ningún grupo de Hybrid Worker ya definido en la cuenta de Automation.  El equipo se puede agregar a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir los runbooks de Automation siempre que use la misma cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker.  Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.  

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

Siga las instrucciones que se encuentran en [Conexión de equipos Windows a Log Analytics](../log-analytics/log-analytics-windows-agent.md) para instalar el agente en el equipo local.  Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

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

## <a name="troubleshooting"></a>solución de problemas 

Hybrid Runbook Worker depende de Microsoft Monitoring Agent para comunicarse con su cuenta de Automation para registrar el trabajo, recibir trabajos de runbook e informar del estado. Si se produce un error de registro del trabajo, estas son algunas de las causas posibles:  

1. Hybrid Worker está detrás de un firewall o proxy.  
    Compruebe que el equipo tenga acceso saliente a *.azure-automation.net en el puerto 443.  

2. El equipo en el que se está ejecutando Hybrid Worker no cumple los [requisitos](automation-offering-get-started.md#hybrid-runbook-worker) de hardware.  
    Los equipos que ejecutan Hybrid Runbook Worker deben cumplir los requisitos mínimos de hardware antes de indicar que hospede esta característica. De lo contrario, en función del uso de recursos de otros procesos en segundo plano y la contención provocada por Runbooks durante la ejecución, el equipo estará sobreutilizado y provocará retrasos o tiempos de espera en los trabajos de Runbook.
   Confirme que el equipo designado para ejecutar la característica Hybrid Runbook Worker cumple los requisitos mínimos de hardware.  Si es así, supervise el uso de la CPU y de memoria para determinar las posibles correlaciones entre el rendimiento de los procesos de Hybrid Runbook Worker y Windows.  Si hay presión de memoria o de la CPU, esto puede indicar que se deben actualizar o agregar procesadores adicionales, o aumentar la memoria para resolver el cuello de botella de recursos y el error. También puede seleccionar un recurso de proceso diferente que pueda admitir los requisitos mínimos y escalarse cuando las demandas de trabajo indiquen que es necesario un aumento.
    
3. No se está ejecutando el servicio Microsoft Monitoring Agent.  
    El servicio de Windows Microsoft Monitoring Agent no se está ejecutando, lo que impide que Hybrid Runbook Worker se comunique con Azure Automation.  Compruebe el agente se está ejecutando escribiendo el comando siguiente en PowerShell: `get-service healthservice`.  Si se detiene el servicio, escriba el siguiente comando de PowerShell para iniciar el servicio: `start-service healthservice`.  

4. En el registro de eventos **Registros de aplicaciones y servicios\Operations Manager**, puede ver el evento 4502 y un mensaje de evento que contiene **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** donde se indica que *el certificado presentado por el servicio <wsid>.oms.opinsights.azure.com no fue emitido por una entidad de certificación utilizada para los servicios de Microsoft. Póngase en contacto con el administrador de red para comprobar si están ejecutando un proxy que intercepta la comunicación TLS/SSL. El artículo KB3126513 incluye información adicional para la solución de problemas de conectividad.*
    Esto puede deberse a que el firewall de red o de proxy está bloqueando la comunicación con Microsoft Azure.  Compruebe que el equipo tenga acceso saliente a *.azure-automation.net en los puertos 443.

Los registros se almacenan localmente en cada Hybrid Worker en C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Puede comprobar si hay algún evento de error o advertencia en el registro de eventos **Registros de aplicaciones y servicios\Microsoft-SMA\Operations** y **Registros de aplicaciones y servicios\Operations Manager** que podría indicar que un problema de conectividad u otro problema afecta a la incorporación del rol a Azure Automation o un problema al realizar las operaciones normales.  

## <a name="next-steps"></a>Pasos siguientes

* Revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.
* Para obtener instrucciones sobre cómo quitar Hybrid Runbook Workers, vea [Quitar Hybrid Runbook Workers de Azure Automation](automation-remove-hrw.md).