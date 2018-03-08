---
title: "Compilación de configuraciones en DSC de Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo compilar configuraciones de configuración de estado deseado (DSC) para Azure Automation."
services: automation
documentationcenter: na
author: georgewallace
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 03/02/2018
ms.author: magoedte; gwallace
ms.openlocfilehash: b267f64a836851e1142475568556eebf74adf2dd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Compilación de configuraciones en DSC de Azure Automation

Puede compilar las configuraciones de configuración de estado deseado (DSC) de dos formas con Azure Automation: en Azure Portal y con Windows PowerShell. La tabla siguiente le ayudará a determinar cuándo se debe usar cada método en base a las características de cada uno:

### <a name="azure-portal"></a>Azure Portal

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

Una vez que haya elegido un método de compilación, siga estos procedimientos para comenzar a compilar.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilación de una configuración de DSC con el Portal de Azure

1. En su cuenta de Automation, haga clic en **DSC Configurations** (Configuraciones DSC).
2. Haga clic en una configuración para abrir su hoja.
3. Haga clic en **Compilar**.
4. Si la configuración no tiene parámetros, se le pedirá que confirme si desea compilarla. Si la configuración tiene parámetros, se abrirá la hoja **Compilar configuración** para que pueda proporcionar valores de parámetro. Consulte más abajo la sección [**Parámetros básicos**](#basic-parameters) para más información acerca de los parámetros.
5. La hoja **Trabajo de compilación** está abierta para que puede realizar un seguimiento de estado del trabajo de compilación y las configuraciones de nodo (documentos de configuración de MOF) que ocasiona que se colocará en el servidor de extracción de DSC de Azure Automation.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilación de una configuración DSC con Windows PowerShell

Puede usar [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) para iniciar la compilación con Windows PowerShell. El código de ejemplo siguiente inicia la compilación de una configuración DSC denominada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` devuelve un objeto de trabajo de compilación que puede usar para realizar el seguimiento de su estado. A continuación, puede usar dicho objeto de trabajo de compilación con [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) para determinar el estado del trabajo de compilación y [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) para ver sus transmisiones (salida). El código de ejemplo siguiente inicia la compilación de la configuración **SampleConfig** , espera hasta que finalice y, a continuación, muestra sus flujos.

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
La declaración de parámetro en configuraciones DSC, incluidos los tipos de parámetro y las propiedades, funciona igual que en los runbooks de Azure Automation. Vea [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md) para obtener más información acerca de los parámetros del runbook.

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

Puede compilar configuraciones DSC que usan parámetros básicos en el portal de DSC de Azure Automation o Azure PowerShell:

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

## <a name="composite-resources"></a>Recursos compuestos

Los **recursos compuestos** le permiten utilizar las configuraciones de DSC como recursos anidados dentro de una configuración. Esto le permite aplicar varias configuraciones a un solo recurso. Consulte [Recursos compuestos: uso de una configuración de DSC como un recurso](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) para más información acerca de los **recursos compuestos**

> [!NOTE]
> Para que los **recursos compuestos** se compilen correctamente, primero debe asegurarse de que los recursos de DSC de los que depende el compuesto están instalados previamente en el repositorio de módulos de cuentas de Azure Automation o no se importarán correctamente.

Para agregar un **recurso compuesto** de DSC, debe agregar el módulo del recurso a un archivo (*.zip). Vaya al repositorio de módulos en su cuenta de Azure Automation. A continuación, haga clic en el botón "Agregar un módulo".

![Adición de módulo](./media/automation-dsc-compile/add_module.png)

Desplácese al directorio donde se encuentra el archivo. Seleccione el archivo y haga clic en Aceptar.

![Selección de módulo](./media/automation-dsc-compile/select_dscresource.png)

A continuación, volverá al directorio de módulos, donde podrá supervisar el estado del **recurso compuesto** mientras se desempaqueta y se registra en Azure Automation.

![Importación del recurso compuesto](./media/automation-dsc-compile/register_composite_resource.png)

Una vez que el módulo está registrado, puede hacer clic en él para validar que los **recursos compuestos** ahora están disponibles para su uso en una configuración.

![Validación del recurso compuesto](./media/automation-dsc-compile/validate_composite_resource.png)

A continuación, puede llamar al **recurso compuesto** en su configuración de este modo:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** le permite separar las opciones estructurales de cualquier configuración específica del entorno al usar DSC de PowerShell. Consulte [Diferenciación de "Qué" y "Dónde" en DSC de PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para obtener más información sobre **ConfigurationData**.

> [!NOTE]
> Puede usar **ConfigurationData** cuando compile en DSC de Azure Automation con Azure PowerShell, pero no en Azure Portal.

En el siguiente ejemplo la configuración de DSC usa **ConfigurationData** a través de las palabras clave **$ConfigurationData** y **$AllNodes**. En este ejemplo, también necesitará el [módulo **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

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

Puede compilar la configuración de DSC anterior con PowerShell. El siguiente código de PowerShell agrega las configuraciones de dos nodos al servidor de extracción de DSC de Automatización de Azure: **ConfigurationDataSample.MyVM1** y **ConfigurationDataSample.MyVM3**:

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

Las referencias de recursos son las mismas en las configuraciones de DSC de Azure Automation y en los runbooks. Para obtener más información, consulte los temas siguientes:

* [Certificados](automation-certificates.md)
* [Conexiones](automation-connections.md)
* [Credenciales](automation-credentials.md)
* [Variables](automation-variables.md)

### <a name="credential-assets"></a>Recursos de credenciales

Mientras las configuraciones DSC en Azure Automation pueden hacer referencia a los activos de credenciales a través de **Get-AutomationPSCredential**, los activos de credenciales también se pueden pasar, si se desea, a través de los parámetros. Si una configuración toma un parámetro del tipo **PSCredential**, tendrá que pasar el nombre de cadena de un activo de credencial de Azure Automation como el valor de ese parámetro en lugar de como un objeto PSCredential. En segundo plano, se recuperará el recurso de credenciales de Azure Automation con ese nombre y se pasará a la configuración.

Mantener las credenciales seguras en configuraciones de nodo (documentos de configuración MOF) exige el cifrado de las credenciales en el archivo MOF de configuración de nodo. Azure Automation va un poco más allá y cifra todo el archivo MOF. Sin embargo, actualmente tiene que indicar a DSC de PowerShell que no importa que las credenciales tengan salida como texto sin formato durante la generación del MOF de configuración de nodo, porque PowerShell DSC desconoce que Azure Automation cifrará todo el archivo MOF después de su generación a través de un trabajo de compilación.

Puede indicar a DSC de PowerShell que es correcto que las credenciales tengan salida como texto sin formato en el MOF de configuración de nodo generado mediante [**ConfigurationData**](#configurationdata). Debe pasar `PSDscAllowPlainTextPassword = $true` a través de **ConfigurationData** para el nombre de cada bloque de nodo que aparezca en la configuración de DSC y use las credenciales.

En el ejemplo siguiente se muestra una configuración de DSC que usa un recurso de la credencial de Automation.

```powershell
Configuration CredentialSample
{
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

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

Puede compilar la configuración de DSC anterior con PowerShell. El siguiente código de PowerShell agrega las configuraciones de dos nodos al servidor de extracción de DSC de Automatización de Azure: **CredentialSample.MyVM1** y **CredentialSample.MyVM2**.

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

## <a name="importing-node-configurations"></a>Importación de configuraciones de nodo

También puede importar las configuraciones de nodo (MOF) que se hayan compilado fuera de Azure. Una ventaja de esta posibilidad es que se pueden firmar las configuraciones de nodo.
Una configuración de nodo firmada se comprueba localmente en un nodo administrado mediante el agente DSC; de este modo, se garantiza que la configuración que se va a aplicar al nodo proviene de una fuente autorizada.

> [!NOTE]
> Puede usar importar configuraciones firmadas en su cuenta de Azure Automation, pero Azure Automation no admite actualmente la compilación de configuraciones firmadas.

> [!NOTE]
> Un archivo de configuración de nodo debe ocupar más de 1 MB para poderlo importar en Azure Automation.

Puede descubrir cómo firmar configuraciones de nodo en https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importación de una configuración de nodo en Azure Portal

1. En la cuenta de Automation, haga clic en **Configuraciones de nodos de DSC** en **Administración de configuración**.

    ![Configuraciones de nodo DSC](./media/automation-dsc-compile/node-config.png)
2. En la hoja **Configuraciones de nodo DSC**, haga clic en **Agregar NodeConfiguration**.
3. En la hoja **Importar**, haga clic en el icono de carpeta situado junto al cuadro de texto **Node Configuration File** (Archivo de configuración de nodo) para buscar un archivo de configuración de nodo (MOF) en el equipo local.

    ![Buscar archivo local](./media/automation-dsc-compile/import-browse.png)
4. Escriba un nombre en el cuadro de texto **Nombre de la configuración**. Este nombre debe coincidir con el de la configuración desde la que se compiló la configuración del nodo.
5. Haga clic en **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importación de una configuración de nodo con PowerShell

Puede usar el cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) para importar una configuración de nodo en su cuenta de Automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



