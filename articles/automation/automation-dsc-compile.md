---
title: "Compilación de configuraciones en DSC de Azure Automation | Microsoft Docs"
description: "Información general sobre las dos formas de compilar las configuraciones de configuración de estado deseado (DSC): en el Portal de Azure y con Windows PowerShell. "
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 12/13/2016
ms.author: eslesar
translationtype: Human Translation
ms.sourcegitcommit: 18c6a55f2975305203bf20a040ac29bc9527a124
ms.openlocfilehash: 30c93d801c68e24b45f5fbc119724e0a18076a13

---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Compilación de configuraciones en DSC de Automatización de Azure

Puede compilar las configuraciones de configuración de estado deseado (DSC) de dos formas con la Automatización de Azure: en el Portal de Azure y con Windows PowerShell. La tabla siguiente le ayudará a determinar cuándo se debe usar cada método en base a las características de cada uno:

### <a name="azure-preview-portal"></a>Portal de vista previa de Azure

* Método más sencillo con la interfaz de usuario interactiva
* Forma para proporcionar valores de parámetro simples
* Seguimiento sencillo del estado del trabajo
* Acceso autenticado con inicio de sesión de Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Llamar desde la línea de comandos con los cmdlets de Windows PowerShell
* Poder incluirse en una solución automatizada con varios pasos
* Proporcionar valores de parámetro simples y complejos
* Realizar el seguimiento del estado del trabajo
* Responder a la necesidad del cliente para admitir los cmdlets de PowerShell
* Pasar ConfigurationData
* Compilar configuraciones que usan credenciales

Una vez que haya decidido un método de compilación, puede seguir los procedimientos correspondientes que se presentan a continuación para empezar a compilar.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilación de una configuración de DSC con el Portal de Azure

1. En su cuenta de automatización, haga clic en **Configuraciones**.
2. Haga clic en una configuración para abrir su hoja.
3. Haga clic en **Compilar**.
4. Si la configuración no tiene parámetros, se le pedirá que confirme si desea compilarla. Si la configuración tiene parámetros, se abrirá la hoja **Compilar configuración** para que pueda proporcionar valores de parámetro. Consulte más abajo la sección [**Parámetros básicos**](#basic-parameters) para más información sobre los parámetros.
5. La hoja **Trabajo de compilación** está abierta para que puede realizar un seguimiento de estado del trabajo de compilación y las configuraciones de nodo (documentos de configuración de MOF) que ocasiona que se colocará en el servidor de extracción de DSC de Automatización de Azure.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilación de una configuración DSC con Windows PowerShell

Puede usar [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) para iniciar la compilación con Windows PowerShell. El código de ejemplo siguiente inicia la compilación de una configuración DSC denominada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` devuelve un objeto de trabajo de compilación que puede usar para realizar el seguimiento de su estado. A continuación, puede usar dicho objeto de trabajo de compilación con [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) para determinar el estado del trabajo de compilación y [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) para ver sus transmisiones (salida). El código de ejemplo siguiente inicia la compilación de la configuración **SampleConfig** , espera hasta que finalice y, a continuación, muestra sus flujos.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Parámetros básicos
La declaración de parámetro en configuraciones DSC, incluidos los tipos de parámetro y las propiedades, funciona igual que en los runbooks de Automatización de Azure. Vea [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) para obtener más información acerca de los parámetros del runbook.

En el ejemplo siguiente se usan dos parámetros, **FeatureName** e **IsPresent**, para determinar los valores de las propiedades de la configuración del nodo **ParametersExample.sample**, que se ha generado durante la compilación.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Puede compilar configuraciones DSC que usan parámetros básicos en el portal de DSC de Automatización de Azure o Azure PowerShell:

### <a name="portal"></a>Portal

En el portal, puede especificar valores de parámetro después de hacer clic **Compilar**.

![texto alternativo](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell requiere parámetros de un [hashtable](http://technet.microsoft.com/library/hh847780.aspx) donde la clave coincide con el nombre del parámetro y el valor es igual al valor de parámetro.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Para obtener información acerca de cómo pasar PSCredentials como parámetros, consulte más abajo la sección <a href="#credential-assets">**Recursos de credenciales**</a> .

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** le permite separar la configuración estructural de cualquier configuración específica del entorno al usar DSC de PowerShell. Consulte [Diferenciación de "Qué" y "Dónde" en DSC de PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para obtener más información sobre **ConfigurationData**.

> [!NOTE]
> Puede usar **ConfigurationData** cuando compile en DSC de Automatización de Azure con Azure PowerShell, pero no en el Portal de Azure.

En el siguiente ejemplo la configuración de DSC usa **ConfigurationData** a través de las palabras clave **$ConfigurationData** y **$AllNodes**. Para este ejemplo también necesitará el [módulo **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Puede compilar la configuración de DSC anterior con PowerShell. PowerShell agrega las configuraciones de dos nodos al servidor de extracción de DSC de Azure Automation: **ConfigurationDataSample.MyVM1** y **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Recursos

Las referencias de recursos son las mismas en las configuraciones de DSC de Automatización de Azure y en los runbooks. Para obtener más información, consulte los temas siguientes:

* [Certificados](automation-certificates.md)
* [Conexiones](automation-connections.md)
* [Credenciales](automation-credentials.md)
* [Variables](automation-variables.md)

### <a name="credential-assets"></a>Recursos de credenciales

Mientras que las configuraciones DSC en Azure Automation pueden hacer referencia a los recursos de credenciales mediante **Get-AzureRmAutomationCredential**, los recursos de credenciales también se pueden pasar mediante parámetros, si se desea. Si una configuración toma un parámetro del tipo **PSCredential** , tendrá que pasar el nombre de cadena de un activo de credencial de Automatización de Azure como el valor de ese parámetro en lugar de como un objeto PSCredential. En segundo plano, se recuperará el recurso de credencial de Automatización de Azure con ese nombre y se pasará a la configuración.

Mantener las credenciales seguras en configuraciones de nodo (documentos de configuración MOF) exige el cifrado de las credenciales en el archivo MOF de configuración de nodo. Automatización de Azure va un poco más allá y cifra todo el archivo MOF. Sin embargo, actualmente tiene que indicar a DSC de PowerShell que no importa que las credenciales tengan salida como texto sin formato durante la generación del MOF de configuración de nodo, porque PowerShell DSC desconoce que Automatización de Azure cifrará todo el archivo MOF después de su generación a través de un trabajo de compilación.

Puede indicar a DSC de PowerShell que es correcto que las credenciales tengan salida como texto sin formato en el MOF de configuración de nodo generado mediante [**ConfigurationData**](#configurationdata). Debe pasar `PSDscAllowPlainTextPassword = $true` a través de **ConfigurationData** para el nombre de cada bloque de nodo que aparezca en la configuración de DSC y use las credenciales.

En el ejemplo siguiente se muestra una configuración de DSC que usa un recurso de la credencial de Automatización.

```powershell
Configuration CredentialSample
{
    $Cred = Get-AzureRmAutomationCredential -Name "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Puede compilar la configuración de DSC anterior con PowerShell. PowerShell agrega las configuraciones de dos nodos al servidor de extracción de DSC de Azure Automation: **CredentialSample.MyVM1** y **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```



<!--HONumber=Dec16_HO2-->


