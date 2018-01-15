---
title: Recursos de credenciales en Azure Automation | Microsoft Docs
description: "Los recursos de credenciales en Azure Automation contienen credenciales de seguridad que se pueden usar para realizar la autenticación a los recursos a los que tiene acceso el runbook o configuración de DSC. En este artículo se describe cómo crear recursos de credenciales y usarlos en un runbook o una configuración de DSC."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 3209bf73-c208-425e-82b6-df49860546dd
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: bwren
ms.openlocfilehash: ac253fda413718ded815c9a990ae61473a5d8870
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="credential-assets-in-azure-automation"></a>Recursos de credenciales en Azure Automation
Un recurso de credencial de Automation contiene un objeto [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) que contiene credenciales de seguridad, como un nombre de usuario y una contraseña. Los runbooks y las configuraciones de DSC pueden usar cmdlets que aceptan un objeto PSCredential para autenticación, o bien pueden extraer el nombre de usuario y la contraseña el objeto PSCredential para proporcionarlos a alguna aplicación o servicio que requiere autenticación. Las propiedades de una credencial se almacenan de manera segura en Azure Automation y es posible tener acceso a ellas en el runbook o la configuración de DSC con la actividad [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) .

> [!NOTE]
> Los recursos protegidos en Azure Automation incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Azure Automation con una clave única que se genera para cada cuenta de Automation. Esta clave se cifra mediante un certificado maestro y se almacena en Azure Automation. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.  

## <a name="azure-classic-powershell-cmdlets"></a>Cmdlets de PowerShell de Azure Classic
Los cmdlets de la tabla siguiente se usan para crear y administrar recursos de credenciales de Automatización con Windows PowerShell.  Se incluyen como parte del [módulo Azure PowerShell](/powershell/azure/overview) que está disponible para su uso en las configuraciones de DSC y los runbooks de Automation.

| Cmdlets | DESCRIPCIÓN |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Recupera información acerca de un recurso de credencial. Solo puede recuperar la propia credencial desde la actividad **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Crea una nueva credencial de Automation. |
| [Remove- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Quita una credencial de Automation. |
| [Set- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Establece las propiedades para una credencial de Automatización existente. |

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets de AzureRM PowerShell
En AzureRM, los cmdlets de la tabla siguiente se usan para crear y administrar recursos de credenciales de automatización con Windows PowerShell.  Se incluyen como parte del [módulo de AzureRM Automation](/powershell/azure/overview) que está disponible para su uso en las configuraciones de DSC y los runbooks denominados "Automation".

| Cmdlets | DESCRIPCIÓN |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Recupera información acerca de un recurso de credencial.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Crea una nueva credencial de Automation. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Quita una credencial de Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Establece las propiedades para una credencial de Automatización existente. |

## <a name="runbook-activities"></a>Actividades de runbook
Las actividades de la tabla siguiente se usan para tener acceso a las credenciales de un runbook y configuraciones de DSC.

| Actividades | DESCRIPCIÓN |
|:--- |:--- |
| Get-AutomationPSCredential |Obtiene una credencial para usarla en un runbook o una configuración de DSC. Devuelve un objeto [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) . |

> [!NOTE]
> Debe evitar el uso de variables en el parámetro –Name de Get-AutomationPSCredential, debido a que esto podría complicar la detección de las dependencias entre runbooks o configuraciones de DSC, y recursos de credenciales en tiempo de diseño.

## <a name="python2-functions"></a>Funciones de Python2
La función de la tabla siguiente se usa para obtener acceso a las credenciales de un runbook de Python2.

| Función | DESCRIPCIÓN |
|:---|:---|
| automationassets.get_automation_credential | Recupera información acerca de un recurso de credencial. |

> [!NOTE]
> Debe importar el módulo "automationassets" en la parte superior del runbook de Python para poder tener acceso a las funciones del recurso.

## <a name="creating-a-new-credential-asset"></a>Creación de un nuevo recurso de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para crear un nuevo recurso de credencial con el Portal de Azure
1. En la cuenta de automatización, haga clic en la parte **Recursos** para abrir la hoja **Recursos**.
2. Haga clic en la parte de **Credenciales** para abrir la hoja **Credenciales**.
3. Haga clic en **Agregar una credencial** en la parte superior de la hoja.
4. Complete el formulario y haga clic en **Crear** para guardar la nueva credencial.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para crear un nuevo recurso de credencial con Windows PowerShell
Los comandos de ejemplo siguientes muestran cómo crear una nueva credencial de automatización. Primero se crea un objeto PSCredential con el nombre y la contraseña y, a continuación, se usa para crear el recurso de credencial. De manera alternativa, podría usar el cmdlet **Get-Credential** para que se le solicite escribir un nombre y una contraseña.

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## <a name="using-a-powershell-credential"></a>Uso de una credencial de PowerShell
Puede recuperar un activo de credencial en un runbook o una configuración de DSC con la actividad **Get-AutomationPSCredential** . Esto devuelve un [objeto PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que puede usar con una actividad o cmdlet que requiere un parámetro PSCredential. También puede recuperar las propiedades del objeto de credencial para usarlas individualmente. El objeto tiene una propiedad para el nombre de usuario y la contraseña segura, o bien puede usar el método **GetNetworkCredential** para devolver un objeto [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) que proporcionará una versión no protegida de la contraseña.

### <a name="textual-runbook-sample"></a>Ejemplo de runbook de texto
Los comandos de ejemplo siguientes muestran cómo usar una credencial de PowerShell en un runbook. En este ejemplo, se recupera la contraseña y su nombre de usuario y contraseña se asignan a variables.

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### <a name="graphical-runbook-sample"></a>Ejemplo de runbook gráfico
Para agregar una actividad **Get-AutomationPSCredential** a un runbook gráfico, haga clic con el botón derecho en la credencial en el panel Biblioteca del editor gráfico y, después, seleccione **Agregar a lienzo**.

![Agregar credencial a lienzo](media/automation-credentials/credential-add-canvas.png)

La imagen siguiente muestra un ejemplo de cómo usar una credencial en un runbook gráfico.  En este caso, se usa para proporcionar la autenticación de un runbook a los recursos de Azure, tal como se describe en [Autenticación de Runbooks con Administración de servicios de Azure y Resource Manager](automation-create-aduser-account.md).  La primera actividad recupera la credencial que tiene acceso a la suscripción de Azure.  A continuación, la actividad **Add-AzureAccount** usa esta credencial para proporcionar autenticación para cualquier actividad que venga después.  Aquí se encuentra un [vínculo de canalización](automation-graphical-authoring-intro.md#links-and-workflow) , debido a que **Get-AutomationPSCredential** espera un solo objeto.  

![Agregar credencial a lienzo](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Uso de una credencial de PowerShell en DSC
Mientras las configuraciones DSC en Azure Automation pueden hacer referencia a los activos de credenciales a través de **Get-AutomationPSCredential**, los activos de credenciales también se pueden pasar, si se desea, a través de los parámetros. Para obtener más información, consulte [Compilación de configuraciones en DSC de Azure Automation](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Uso de credenciales en Python2
En el ejemplo siguiente se muestra un acceso a credenciales en runbooks de Python2.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a credential
    cred = automationassets.get_automation_credential("credtest")
    print cred["username"]
    print cred["password"]

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de los vínculos en creación gráfica, consulte [Vínculos en Creación gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
* Para entender los distintos métodos de autenticación con la automatización, vea [Seguridad de Azure Automation](automation-security-overview.md)
* Para empezar a trabajar con runbooks gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
* Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md) 
* Para empezar a trabajar con runbooks de Python2, vea [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Mi primer runbook de Python2). 

