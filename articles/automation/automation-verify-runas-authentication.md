---
title: "Validación de la configuración de la cuenta de Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo confirmar que la configuración de la cuenta de Automation es correcta."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 55f5d5524019ac63565e5ddd1f47dbdd65f05065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Comprobación de la autenticación con la cuenta de ejecución de Azure Automation
Una vez que se haya creado correctamente una cuenta de Automation, puede realizar una prueba sencilla para confirmar que puede autenticarse correctamente en Azure Resource Manager o en el modelo de implementación clásica de Azure mediante la cuenta de ejecución de Automation recién creada o actualizada.    

## <a name="automation-run-as-authentication"></a>Autenticación con la cuenta de ejecución de Automation
Utilice el código de ejemplo que aparece a continuación para [crear un runbook de PowerShell](automation-creating-importing-runbook.md) para comprobar la autenticación mediante la cuenta de ejecución y también, en sus runbooks personalizados, para autenticar y administrar los recursos de Resource Manager con la cuenta de Automation.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Add-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Observe que el cmdlet usado para autenticarse en el Runbook - **Add-AzureRmAccount**usa el conjunto de parámetros *ServicePrincipalCertificate* .  Se autentica mediante el certificado de la entidad de servicio, no las credenciales.  

Cuando [ejecuta el runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar la cuenta de ejecución, se crea un [trabajo de Runbook](automation-runbook-execution.md), se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo**. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.

Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .  En la hoja **Salida**, debería ver que se ha autenticado correctamente y ha devuelto una lista de todos los recursos de todos los grupos de recursos de la suscripción.  

No olvide quitar el bloque de código que comienza con el comentario `#Get all ARM resources from all resource groups` cuando reutilice el código para sus runbooks.

## <a name="classic-run-as-authentication"></a>Autenticación con la cuenta de ejecución del modelo de implementación clásica de Azure
Utilice el código de ejemplo que aparece a continuación para [crear un runbook de PowerShell](automation-creating-importing-runbook.md) para comprobar la autenticación mediante la cuenta de ejecución clásica y también, en sus runbooks personalizados, para autenticar y administrar los recursos del modelo de implementación clásica.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Cuando [ejecuta el runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar la cuenta de ejecución, se crea un [trabajo de Runbook](automation-runbook-execution.md), se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo**. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.

Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .  En la hoja **Salida**, debería ver que se ha autenticado correctamente y ha devuelto una lista de todas las máquinas virtuales de Azure ordenadas por VMName que están implementadas en la suscripción.  

No olvide quitar el cmdlet **Get-AzureVM** cuando reutilice el código para sus runbooks.

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).
* Para más información sobre la creación gráfica, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).
