---
title: "Ejecución de runbooks en Azure Automation Hybrid Runbook Worker | Microsoft Docs"
description: "Este artículo proporciona información acerca de cómo ejecutar runbooks en máquinas de un centro de datos local o en el proveedor de la nube con el rol Hybrid Runbook Worker."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: magoedte
ms.openlocfilehash: d73bb33b4b330df803e140145ed63319af4a6733
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Ejecución de runbooks en Hybrid Runbook Worker 
No hay ninguna diferencia en la estructura de runbooks que se ejecutan en Azure Automation y los que se ejecutan en Hybrid Runbook Worker. Los runbooks que usa con cada uno de ellos probablemente sean muy distintos entre sí, debido a que los runbooks destinados a Hybrid Runbook Worker normalmente administran los recursos en el propio equipo local donde se implementan, mientras que los runbooks en Azure Automation suelen hacerlo en la nube de Azure.

Puede editar un runbook para Trabajo híbrido de runbook en Azure Automation, pero es probable que tenga dificultades si intenta probar el runbook en el editor.  Es posible que los módulos de PowerShell que tienen acceso a los recursos locales no estén instalados en el entorno de Azure Automation; si este es el caso, la prueba presentará errores.  Si instala los módulos necesarios, se ejecutará el runbook, pero no podrá tener acceso a los recursos locales para una prueba completa.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Inicio de un runbook en Hybrid Runbook Worker
[Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md) describe los distintos métodos para iniciar un runbook.  Trabajo híbrido de runbook agrega una opción **Ejecutar en** donde puede especificar el nombre de un grupo de Trabajos híbridos de runbook.  Si se especifica un grupo, los trabajos de ese grupo recuperan y ejecutan el runbook.  Si no se especifica esta opción, se ejecuta en Azure Automation de la manera normal.

Cuando inicie un runbook en Azure Portal, verá una opción **Ejecutar en** donde puede seleccionar **Azure** o **Hybrid Worker**.  Si selecciona **Trabajo híbrido**, puede seleccionar el grupo en una lista desplegable.

Use el parámetro **RunOn**.  Puede usar el comando siguiente para iniciar un runbook llamado Test-Runbook en un grupo de Hybrid Runbook Worker denominado MyHybridGroup con Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> El parámetro **RunOn** se agregó al cmdlet **Start-AzureAutomationRunbook** en la versión 0.9.1 de Microsoft Azure PowerShell.  Si tiene instalada una versión anterior, deberá [descargar la versión más reciente](https://azure.microsoft.com/downloads/) .  Solo necesita instalar esta versión en una estación de trabajo donde inicia el runbook desde Windows PowerShell.  No es necesario que la instale en el equipo de trabajo, a menos que tenga la intención de iniciar runbooks desde ese equipo.  Actualmente no puede iniciar un runbook en un Trabajo híbrido de runbook desde otro runbook, debido a que esto requeriría que la versión más reciente de PowerShell de Azure esté instalada en su cuenta de Automation.  La versión más reciente se actualiza automáticamente en Azure Automation y pronto se insertará de manera automática en los trabajos.
>
>

## <a name="runbook-permissions"></a>Permisos de runbooks
Los runbooks que se ejecutan en una instancia de Hybrid Runbook Worker no pueden usar el mismo método que se usa normalmente para los runbooks que se autentican en los recursos de Azure, debido a que tendrán acceso a recursos fuera de Azure.  El runbook puede proporcionar su propia autenticación a los recursos locales, o puede especificar una cuenta RunAs para proporcionar un contexto de usuario para todos los runbooks.

### <a name="runbook-authentication"></a>Autenticación de runbook
De forma predeterminada, los Runbooks se ejecutarán en el contexto de la cuenta de sistema local en el equipo local, por lo que deben proporcionar su propia autenticación a los recursos a los que tienen acceso.  

Puede usar los recursos [redencial](http://msdn.microsoft.com/library/dn940015.aspx) y [ertificado](http://msdn.microsoft.com/library/dn940013.aspx) en el Runbook con cmdlets que le permitan especificar las credenciales, para que así pueda autenticarse en distintos recursos.  El ejemplo siguiente muestra una porción de un runbook que reinicia un equipo.  Recupera las credenciales desde un recurso de Credencial y el nombre del equipo desde un recurso de Variable y, a continuación, usa estos valores con el cmdlet Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

También puede aprovechar [InlineScript](automation-powershell-workflow.md#inlinescript), que le permite ejecutar bloques de código en otro equipo con credenciales especificadas por el [parámetro común PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Cuenta RunAs
En lugar de hacer que los runbooks proporcionen su propia autenticación a los recursos locales, puede especificar una cuenta **RunAs** para un grupo de Hybrid Worker.  Especifique un [recurso de credencial](automation-credentials.md) que tenga acceso a los recursos locales. Todos los runbooks se ejecutan con estas credenciales si se ejecutan en una instancia de Hybrid Runbook Worker del grupo.  

El nombre de usuario de la credencial debe tener uno de los siguientes formatos:

* dominio\nombre de usuario
* username@domain
* nombre de usuario (para cuentas locales en el equipo local)

Utilice el procedimiento siguiente para especificar una cuenta RunAs de un grupo de Hybrid Worker:

1. Cree un [recurso de credencial](automation-credentials.md) con acceso a los recursos locales.
2. Abra la cuenta de Automation en Azure Portal.
3. Seleccione el icono **Grupos de Hybrid Worker** y luego seleccione el grupo.
4. Seleccione **Toda la configuración** y luego **Configuración del grupo de Hybrid Worker**.
5. Cambie **Ejecutar como** de **Predeterminado** a **Personalizado**.
6. Seleccione la credencial y haga clic en **Guardar**.

### <a name="automation-run-as-account"></a>Cuenta de ejecución de Automation
Como parte del proceso de compilación automatizado para implementar recursos en Azure, es posible que sea necesario acceder a los sistemas locales para admitir una tarea o conjunto de pasos en la secuencia de implementación.  A fin de admitir la autenticación en Azure mediante la cuenta de ejecución, debe instalar el certificado de la cuenta de ejecución.  

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
    Get-AzureRmAutomationAccount | Select-Object AutomationAccountName

Guarde el runbook *Export-RunAsCertificateToHybridWorker* en el equipo con una extensión `.ps1`.  Impórtelo en la cuenta de Automation y edite el runbook, cambiando el valor de la variable `$Password` con su propia contraseña.  Publique y, a continuación, ejecute el runbook dirigido al grupo de Hybrid Worker que ejecuta y autentica runbooks con la cuenta de ejecución.  La transmisión del trabajo informa sobre el intento de importar el certificado en el almacén de la máquina local y sigue con varias líneas dependiendo del número de cuentas de Automation definidas en la suscripción y de si la autenticación se realiza correctamente.  

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Solución de problemas de runbooks en Hybrid Runbook Worker
Los registros se almacenan localmente en cada Hybrid Worker en C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Hybrid Worker también registra los errores y eventos en el registro de eventos de Windows, en **Registros de aplicaciones y servicios\Microsoft-SMA\Operational**.  Los eventos relacionados con los runbooks ejecutados en el trabajo de runbook se escriben en **Registros de aplicaciones y servicios\Microsoft-Automation\Operational**.  El registro **Microsoft SMA** incluye muchos más eventos relacionados con los procesos de runbook insertados en el trabajo de runbook y el procesamiento del runbook.  Aunque el registro de eventos **Microsoft-Automation** no tenga muchos eventos con detalles relacionados con la solución de problemas de ejecución de un runbook, al menos encontrará los resultados del trabajo de runbook.  

[La salida y los mensajes de runbooks](automation-runbook-output-and-messages.md) se envían a Azure Automation desde los trabajos híbridos igual que los trabajos de runbook que se ejecutan en la nube.  También puede habilitar los flujos Detallado y Progreso de la misma manera que haría para otros runbooks.  

Si sus runbooks no están finalizando correctamente y el resumen del trabajo muestra el estado **Suspendido**, consulte el artículo de solución de problemas [Hybrid Runbook Worker: un trabajo de Runbook finaliza con el estado Suspendido](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).   

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre los distintos métodos que se pueden utilizar para iniciar un runbook, consulte [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md).  
* Para entender los diferentes procedimientos para trabajar con runbooks de PowerShell y de flujo de trabajo de PowerShell en Azure Automation mediante el editor de texto, consulte [Edición de runbooks de texto en Azure Automation](automation-edit-textual-runbook.md)
