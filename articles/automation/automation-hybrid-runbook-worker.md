---
title: Hybrid Runbook Worker de Azure Automation | Microsoft Docs
description: "Este artículo brinda información sobre cómo instalar y usar Trabajo híbrido de runbook, una característica de Automatización de Azure que le permite ejecutar runbooks en máquinas de su centro de datos local."
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
ms.date: 06/29/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 5cd863c3e357b67d281adb8484376295ad9099ec
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

<a id="automate-resources-in-your-data-center-with-hybrid-runbook-worker" class="xliff"></a>

# Automatización de recursos en los centros de datos con Hybrid Runbook Worker
Los runbooks de Automatización de Azure no pueden tener acceso a los recursos de su centro de datos local, debido a que se ejecutan en la nube de Azure.  La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks en máquinas ubicadas en su centro datos, para así poder administrar los recursos locales. Los runbooks se almacenan y administran en Automatización de Azure y después se entregan a una o más máquinas locales.  

Esta funcionalidad se ilustra en la imagen siguiente:<br>  

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/automation.png)

Puede designar uno o más equipos del centro de datos para que actúen como un trabajo híbrido de runbook y ejecuten runbooks desde Automatización de Azure.  Cada trabajo requiere el agente de administración de Microsoft con una conexión a Microsoft Operations Management Suite y al entorno de runbooks de Automatización de Azure.  Operations Management Suite solo se usa para instalar y mantener el agente de administración y para supervisar la funcionalidad del trabajo.  Automatización de Azure realiza la entrega de los runbooks y la instrucción para ejecutarlos.

No hay ningún requisito de firewall de entrada para admitir Trabajos híbridos de runbook. El agente que se encuentra en el equipo local inicia toda la comunicación con Automatización de Azure en la nube. Cuando se inicia un runbook, Automatización de Azure crea una instrucción que el agente recupera. El agente baja el runbook y todos los parámetros antes de ejecutarlo.  También recupera los [recursos](http://msdn.microsoft.com/library/dn939988.aspx) que usa el runbook desde Azure Automation.

> [!NOTE]
> Para administrar la configuración de los servidores que admiten el rol de Hybrid Runbook Worker con la configuración de estado deseado (DSC), debe agregarlos como nodos de DSC.  Para obtener más información sobre su incorporación para la administración con DSC, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).           
><br>
>Si habilita la [solución Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md), los equipos Windows conectados al área de trabajo OMS se configurarán automáticamente como Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución.  Sin embargo, no está registrado en ningún grupo de Hybrid Worker ya definido en la cuenta de Automation.  El equipo se puede agregar a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir los runbooks de Automation siempre que use la misma cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker.  Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.  

<a id="hybrid-runbook-worker-groups" class="xliff"></a>

## Grupos de Trabajos híbridos de runbook
Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente.  Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en Hybrid Runbook Worker, se especifica el grupo en el que se ejecuta.  Los miembros del grupo determinan qué trabajo atiende a la solicitud.  No es posible especificar un trabajo determinado.

<a id="hybrid-runbook-worker-requirements" class="xliff"></a>

## Requisitos de Hybrid Runbook Worker
Debe designar al menos un equipo local para ejecutar trabajos híbridos de Runbook.  Este equipo debe tener lo siguiente:

* Windows Server 2012 o superior
* Windows PowerShell 4.0 o posterior.  Se recomienda instalar Windows PowerShell 5.0 en los equipos para una mayor confiabilidad. Puede descargar la versión más reciente en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=50395).
* Mínimo de dos núcleos y 4 GB de RAM

Tenga en cuenta las siguientes recomendaciones para los Hybrid Worker:

* Designe varios Hybrid Worker en cada grupo para una alta disponibilidad.  
* Los Hybrid Worker pueden coexistir con servidores de runbooks de Service Management Automation o System Center Orchestrator.
* Considere la posibilidad de usar un equipo ubicado físicamente en la región de su cuenta de Automation, o cerca de ella, puesto que los datos del trabajo se envían a Azure Automation cuando se completa un trabajo.

<a id="configure-proxy-and-firewall-settings" class="xliff"></a>

### Configuración del proxy y el firewall
Para que la instancia de Hybrid Runbook Worker local se conecte al servicio Microsoft Operations Management Suite (OMS) y se registre en él, debe tener acceso al número de puerto y a las direcciones URL que se describen a continuación.  Y esto aparte de los [puertos y las direcciones URL necesarios para que Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agents.md#network) se conecte a OMS. Si se usa un servidor proxy para realizar la comunicación entre el agente y el servicio de OMS, debe asegurarse de que es posible tener acceso a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, deberá configurarlo para que permita el acceso.

A continuación se indican los puertos y las direcciones URL que son necesarios para que Hybrid Runbook Worker se comunique con Automatización.

* Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet
* URL global: *.azure-automation.net

Si tiene una cuenta de Automatización definida para una región específica y quiere restringir la comunicación con ese dentro de datos regional, en la tabla siguiente se proporciona el registro DNS para cada región.

| **Región** | **Registro DNS** |
| --- | --- |
| Centro-Sur de EE. UU |scus-jobruntimedata-prod-su1.azure-automation.net |
| Este de EE. UU. 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centro occidental de EE.UU. | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Europa del Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Centro de Canadá |cc-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste de Asia |sea-jobruntimedata-prod-su1.azure-automation.net |
| India Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Este de Japón |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste de Australia |ase-jobruntimedata-prod-su1.azure-automation.net |
| Sur del Reino Unido 2 | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gobierno de EE. UU. - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Para obtener una lista de direcciones IP en lugar de nombres, descargue y revise el archivo XML [Intervalos de direcciones IP de los centros de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653) desde el Centro de descarga de Microsoft. 

> [!NOTE]
> Este archivo contiene los intervalos de direcciones IP (incluidos los intervalos de Compute, SQL y Storage) utilizados en los centros de datos de Microsoft Azure. Semanalmente, se publica un archivo actualizado que refleja los intervalos implementados actualmente y los próximos cambios en los intervalos de direcciones IP. Los nuevos intervalos que aparecen en el archivo no se utilizarán en los centros de datos durante al menos una semana. Descargue el nuevo archivo XML cada semana y realizar los cambios necesarios en su sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Express Route observarán que este archivo se usa para actualizar la publicidad de BGP del espacio de Azure en la primera semana de cada mes. 
> 

<a id="installing-hybrid-runbook-worker" class="xliff"></a>

## Instalación de Trabajo híbrido de runbook

Para instalar y configurar Hybrid Runbook Worker, hay dos métodos disponibles.  El método recomendado usa un runbook de Automation a fin de automatizar completamente el proceso necesario para configurar un equipo Windows.  El segundo método es un procedimiento paso a paso para instalar y configurar el rol manualmente.  

<a id="automated-deployment" class="xliff"></a>

### Implementación automatizada

Realice los pasos siguientes para automatizar la instalación y configuración del rol Hybrid Worker.  

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

<a id="manual-deployment" class="xliff"></a>

### Implementación manual 
Realice los dos primeros pasos una vez para su entorno de Automatización y después repita los pasos restantes en cada equipo de trabajo.

<a id="1-create-operations-management-suite-workspace" class="xliff"></a>

#### 1. Creación del área de trabajo de Operations Management Suite
Si todavía no tiene un área de trabajo de Operations Management Suite, puede crear uno siguiendo las instrucciones que aparecen en [Administración del área de trabajo](../log-analytics/log-analytics-manage-access.md). Si cuenta con un área de trabajo existente, puede usarla.

<a id="2-add-automation-solution-to-operations-management-suite-workspace" class="xliff"></a>

#### 2. Adición de soluciones de Automatización de área de trabajo de Operations Management Suite
Las soluciones agregan funcionalidad a Operations Management Suite.  La solución de Automatización agrega funcionalidad a Automatización de Azure, incluida la compatibilidad con Hybrid Runbook Worker.  Cuando se agrega la solución al área de trabajo, se insertan automáticamente los componentes de trabajo al equipo del agente que va a instalar en el paso siguiente.

Siga las instrucciones de [Para agregar una solución mediante la Galería de soluciones](../log-analytics/log-analytics-add-solutions.md) para agregar la solución de **Automatización** al área de trabajo de Operations Management Suite.

<a id="3-install-the-microsoft-monitoring-agent" class="xliff"></a>

#### 3. Instalación de Microsoft Monitoring Agent
El agente de Microsoft Monitoring Agent conecta los equipos a Operations Management Suite.  Cuando instala el agente en el equipo local y se conecte al espacio de trabajo, se descargarán automáticamente los componentes necesarios para Hybrid Runbook Worker.

Siga las instrucciones que se encuentran en [Conexión de equipos Windows a Log Analytics](../log-analytics/log-analytics-windows-agents.md) para instalar el agente en el equipo local.  Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

Cuando el agente se conecta correctamente a Operations Management Suite, se enumerará en la pestaña **Orígenes conectados** del panel **Configuración** de Operations Management Suite.  Puede comprobar que el agente ha descargado correctamente la solución Automation cuando tiene una carpeta llamada **AzureAutomationFiles** en C:\Program Files\Microsoft Monitoring Agent\Agent.  Para confirmar la versión de Hybrid Runbook Worker, puede ir a C:\Archivos de programa\Microsoft Monitoring Agent\Agent\AzureAutomation\ y examinar la subcarpeta \\*version*.   

<a id="4-install-the-runbook-environment-and-connect-to-azure-automation" class="xliff"></a>

#### 4. Instalación del entorno de runbook y conexión con Automatización de Azure
Cuando agrega un agente a Operations Management Suite, la solución Automation inserta el módulo **HybridRegistration** de PowerShell, que contiene el cmdlet **Add-HybridRunbookWorker**.  Este cmdlet se usa para instalar el entorno de runbook en el equipo y registrarlo con Automatización de Azure.

Abra una sesión de PowerShell en modo de Administrador y ejecute el comando siguientes para importar el módulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

A continuación, ejecute el cmdlet **Add-HybridRunbookWorker** con la siguiente sintaxis:

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Puede obtener la información necesaria para este cmdlet en la hoja **Administrar claves** del Portal de Azure.  Para abrir esta hoja, seleccione la opción **Claves** en la hoja **Configuración** de su cuenta de Automation.

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name** es el nombre del grupo de Trabajos híbridos de runbook. Si este grupo ya existe en la cuenta de automatización, se le agregará el equipo actual.  Si todavía no existe, se agregará.
* **EndPoint** es el campo **URL** de la hoja **Administrar claves**.
* **Token** es la **Clave de acceso primaria** en la hoja **Administrar claves**.  

Use el modificador **-Verbose** con **Add-HybridRunbookWorker** para recibir información detallada sobre la instalación.

<a id="5-install-powershell-modules" class="xliff"></a>

#### 5. Instalación de módulos PowerShell
Los runbooks pueden usar cualquiera de las actividades y los cmdlets definidos en los módulos instalados en el entorno de Automatización de Azure.  Estos módulos no se implementan automáticamente en equipos locales, por lo que debe instalarlos de forma manual.  La excepción es el módulo de Azure, que se instala de manera predeterminada y brinda acceso a los cmdlets para todos los servicios y actividades de Azure para Azure Automation.

Debido a que el propósito principal de la característica Hybrid Runbook Worker es administrar recursos locales, es muy probable que deba instalar los módulos que admiten estos recursos.  Puede consultar [Instalación de módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obtener información sobre cómo instalar módulos de Windows PowerShell.  Los módulos instalados deben estar en una ubicación a la que haga referencia la variable de entorno PSModulePath para que Hybrid Worker los importe automáticamente.  Para obtener más información, consulte [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Modificación de la ruta de instalación PSModulePath). 

<a id="removing-hybrid-runbook-worker" class="xliff"></a>

## Eliminación de Hybrid Runbook Worker 
Puede quitar uno o más Hybrid Runbook Worker de un grupo o puede quitar el grupo, dependiendo de sus requisitos.  Para quitar un Hybrid Runbook Worker de un equipo local, realice los pasos siguientes.

1. En Azure Portal, vaya a su cuenta de Automation.  
2. En la hoja **Configuración**, seleccione **Claves** y anote los valores de **URL** y **Primary Access Key** (Clave de acceso principal).  Esta información la necesita para el siguiente paso.
3. Abra una sesión de PowerShell en modo Administrador y ejecute el comando siguiente: `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use el modificador **-Verbose** para ver un registro detallado del proceso de eliminación.

> [!NOTE]
> Con esto no se quita Microsoft Monitoring Agent del equipo, sino solo la funcionalidad y la configuración del rol Hybrid Runbook Worker.  

<a id="remove-hybrid-worker-groups" class="xliff"></a>

## Eliminación de grupos de Hybrid Worker
Para quitar un grupo, primero debe quitar el Hybrid Runbook Worker de todos los equipos que sean miembros del grupo mediante el procedimiento mostrado anteriormente y luego realizar los pasos siguientes para quitar el grupo.  

1. Abra la cuenta de Automatización en el Portal de Azure.
2. Seleccione el icono **Grupos de Hybrid Worker** y en la hoja **Grupos de Hybrid Worker**, seleccione el grupo que quiere eliminar.  Tras seleccionar el grupo específico, se muestra la hoja de propiedades de **Grupo de Hybrid Worker**.<br> ![Hoja Grupo de Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. En la hoja de propiedades del grupo seleccionado, haga clic en **Eliminar**.  Aparecerá un mensaje solicitándole que confirme esta acción. Si está seguro de que quiere continuar, seleccione **Sí**.<br> ![Cuadro de diálogo de confirmación de eliminación de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Este proceso puede tardar varios segundos en completarse y puede realizar el seguimiento de su progreso en **Notificaciones** en el menú.  

<a id="starting-runbooks-on-hybrid-runbook-worker" class="xliff"></a>

## Inicio de runbooks en Trabajo híbrido de runbook
[Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) describe los distintos métodos para iniciar un runbook.  Trabajo híbrido de runbook agrega una opción **Ejecutar en** donde puede especificar el nombre de un grupo de Trabajos híbridos de runbook.  Si se especifica un grupo, los trabajos de ese grupo recuperan y ejecutan el runbook.  Si no se especifica esta opción, se ejecuta en Automatización de Azure de la manera normal.

Cuando inicie un runbook en Azure Portal, verá una opción **Ejecutar en** donde puede seleccionar **Azure** o **Hybrid Worker**.  Si selecciona **Trabajo híbrido**, puede seleccionar el grupo en una lista desplegable.

Use el parámetro **RunOn**.  Puede usar el comando siguiente para iniciar un runbook llamado Test-Runbook en un grupo de Hybrid Runbook Worker denominado MyHybridGroup con Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> El parámetro **RunOn** se agregó al cmdlet **Start-AzureAutomationRunbook** en la versión 0.9.1 de Microsoft Azure PowerShell.  Si tiene instalada una versión anterior, deberá [descargar la versión más reciente](https://azure.microsoft.com/downloads/) .  Solo necesita instalar esta versión en una estación de trabajo donde inicia el runbook desde Windows PowerShell.  No es necesario que la instale en el equipo de trabajo, a menos que tenga la intención de iniciar runbooks desde ese equipo.  Actualmente no puede iniciar un runbook en un Trabajo híbrido de runbook desde otro runbook, debido a que esto requeriría que la versión más reciente de PowerShell de Azure esté instalada en su cuenta de Automatización.  La versión más reciente se actualiza automáticamente en Azure Automation y pronto se insertará de manera automática en los trabajos.
>
>

<a id="runbook-permissions" class="xliff"></a>

## Permisos de runbooks
Los runbooks que se ejecutan en una instancia de Hybrid Runbook Worker no pueden usar el mismo método que se usa normalmente para los runbooks que se autentican en los recursos de Azure, debido a que tendrán acceso a recursos fuera de Azure.  El runbook puede proporcionar su propia autenticación a los recursos locales, o puede especificar una cuenta RunAs para proporcionar un contexto de usuario para todos los runbooks.

<a id="runbook-authentication" class="xliff"></a>

### Autenticación de runbook
De forma predeterminada, los Runbooks se ejecutarán en el contexto de la cuenta de sistema local en el equipo local, por lo que deben proporcionar su propia autenticación a los recursos a los que tienen acceso.  

Puede usar los recursos [redencial](http://msdn.microsoft.com/library/dn940015.aspx) y [ertificado](http://msdn.microsoft.com/library/dn940013.aspx) en el Runbook con cmdlets que le permitan especificar las credenciales, para que así pueda autenticarse en distintos recursos.  El ejemplo siguiente muestra una porción de un runbook que reinicia un equipo.  Recupera las credenciales desde un recurso de Credencial y el nombre del equipo desde un recurso de Variable y, a continuación, usa estos valores con el cmdlet Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

También puede aprovechar [InlineScript](automation-powershell-workflow.md#inlinescript), que le permite ejecutar bloques de código en otro equipo con credenciales especificadas por el [parámetro común PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

<a id="runas-account" class="xliff"></a>

### Cuenta RunAs
En lugar de hacer que los runbooks proporcionen su propia autenticación a los recursos locales, puede especificar una cuenta **RunAs** para un grupo de Hybrid Worker.  Especifique un [recurso de credencial](automation-credentials.md) que tenga acceso a los recursos locales. Todos los runbooks se ejecutan con estas credenciales si se ejecutan en una instancia de Hybrid Runbook Worker del grupo.  

El nombre de usuario de la credencial debe tener uno de los siguientes formatos:

* dominio\nombre de usuario
* username@domain
* nombre de usuario (para cuentas locales en el equipo local)

Utilice el procedimiento siguiente para especificar una cuenta RunAs de un grupo de Hybrid Worker:

1. Cree un [recurso de credencial](automation-credentials.md) con acceso a los recursos locales.
2. Abra la cuenta de Automatización en el Portal de Azure.
3. Seleccione el icono **Grupos de Hybrid Worker** y luego seleccione el grupo.
4. Seleccione **Toda la configuración** y luego **Configuración del grupo de Hybrid Worker**.
5. Cambie **Ejecutar como** de **Predeterminado** a **Personalizado**.
6. Seleccione la credencial y haga clic en **Guardar**.

<a id="automation-run-as-account" class="xliff"></a>

### Cuenta de ejecución de Automation
Como parte del proceso de compilación automatizado para implementar recursos en Azure, es posible que sea necesario interrogar los sistemas locales para admitir una tarea o conjunto de pasos en la secuencia de implementación.  A fin de admitir la autenticación en Azure mediante la cuenta de ejecución, debe instalar el certificado de la cuenta de ejecución.  

El siguiente runbook de PowerShell, *Export-RunAsCertificateToHybridWorker*, exporta el certificado de ejecución desde la cuenta de Azure Automation y lo descarga e importa en el almacén de certificados de la máquina local de un Hybrid Worker conectado a la misma cuenta.  Una vez completado dicho paso, comprueba que el trabajo puede autenticarse correctamente en Azure con la cuenta de ejecución.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Guarde el runbook *Export-RunAsCertificateToHybridWorker* en el equipo con una extensión `.ps1`.  Impórtelo en la cuenta de Automation y edite el runbook, cambiando el valor de la variable `$Password` con su propia contraseña.  Publique y, a continuación, ejecute el runbook dirigido al grupo de Hybrid Worker que ejecuta y autentica runbooks con la cuenta de ejecución.  La transmisión del trabajo informa sobre el intento de importar el certificado en el almacén de la máquina local y sigue con varias líneas dependiendo del número de cuentas de Automation definidas en la suscripción y de si la autenticación se realiza correctamente.  

<a id="creating-runbooks-for-hybrid-runbook-worker" class="xliff"></a>

## Creación de runbooks para Trabajo híbrido de runbook
No hay ninguna diferencia en la estructura de runbooks que se ejecutan en Automatización de Azure y los que se ejecutan en un Trabajo híbrido de runbook. Los runbooks que usa con cada uno de ellos probablemente sean muy distintos entre sí, debido a que los runbooks para Hybrid Runbook Worker normalmente administran los recursos locales de su centro de datos, mientras que los runbooks en Azure Automation normalmente administran recursos en la nube de Azure.

Puede editar un runbook para Trabajo híbrido de runbook en Automatización de Azure, pero es probable que tenga dificultades si intenta probar el runbook en el editor.  Es posible que los módulos de PowerShell que tienen acceso a los recursos locales no estén instalados en el entorno de Automatización de Azure; si este es el caso, la prueba presentará errores.  Si instala los módulos necesarios, se ejecutará el runbook, pero no podrá tener acceso a los recursos locales para una prueba completa.

<a id="troubleshooting-runbooks-on-hybrid-runbook-worker" class="xliff"></a>

## Solución de problemas de runbooks en Hybrid Runbook Worker
[La salida y los mensajes de runbooks](automation-runbook-output-and-messages.md) se envían a Automatización de Azure desde los trabajos híbridos igual que los trabajos de runbook que se ejecutan en la nube.  También puede habilitar los flujos Detallado y Progreso de la misma manera que haría para otros runbooks.  

Los registros se almacenan localmente en cada Hybrid Worker en C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.

Si sus runbooks no están finalizando correctamente y el resumen del trabajo muestra el estado **Suspendido**, consulte el artículo de solución de problemas [Hybrid Runbook Worker: un trabajo de Runbook finaliza con el estado Suspendido](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

<a id="relationship-to-service-management-automation" class="xliff"></a>

## Relación con Service Management Automation
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx)permite ejecutar los mismos Runbooks compatibles con Azure Automation en su centro de datos local. SMA normalmente se implementa junto con Windows Azure Pack, dado que Windows Azure Pack contiene una interfaz gráfica para la administración de SMA. A diferencia de Azure Automation, SMA requiere una instalación local que incluye servidores web para hospedar la API, una base de datos para que contenga la configuración de SMA y de los Runbooks y Runbook Workers para ejecutar los trabajos de Runbook. Automatización de Azure proporciona estos servicios en la nube y solo requiere que mantenga los Trabajos híbridos de runbook en su entorno local.

Si es un usuario existente de SMA, puede mover los runbooks a Automatización de Azure para usarlos con Trabajo híbrido de runbook sin cambios, suponiendo que realizan su propia autenticación a recursos, tal como se describe en [Creación de runbooks para Trabajo híbrido de runbook](#creating-runbooks-for-hybrid-runbook-worker).  Los runbooks en SMA se ejecutan en el contexto de la cuenta de servicio en el servidor de trabajo, lo que puede proporciona esa autenticación para los runbooks.

Puede usar los criterios siguientes para determinar si Automatización de Azure con Trabajo híbrido de runbook o Service Management Automation es más adecuado para sus requisitos.

* SMA requiere la instalación local de sus componentes subyacentes que están conectados a Windows Azure Pack, en caso de que sea necesaria una interfaz gráfica de administración. Se requieren más recursos locales con costos de mantenimiento más elevados que Azure Automation, que solo necesita un agente instalado en Runbook Workers locales. Operations Management Suite administra los agentes, con lo que disminuyen los costos de mantenimiento.
* Azure Automation almacena sus Runbooks en la nube y los entrega a Hybrid Runbook Worker locales. Si su directiva de seguridad no permite este comportamiento, deberá usar SMA.
* SMA se incluye con System Center; y por tanto, requiere una licencia de System Center 2012 R2. Azure Automation se basa en un modelo de suscripción en niveles.
* Presenta características avanzadas, como Runbooks gráficos que no están disponibles en SMA.

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
* Para más información sobre los distintos métodos que se pueden utilizar para iniciar un runbook, consulte [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md).  
* Para entender los diferentes procedimientos para trabajar con runbooks de PowerShell y de flujo de trabajo de PowerShell en Automatización de Azure mediante el editor de texto, consulte [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md)

