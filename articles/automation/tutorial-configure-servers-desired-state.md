---
title: "Configuración de servidores en un estado deseado y administración de desviaciones con Azure Automation | Microsoft Docs"
description: "Tutorial: administración de configuraciones de servidor con DSC de Azure Automation"
services: automation
documentationcenter: automation
author: georgewallace
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: c510b2244dfa85b12ed08cad9dbab75067ebe41a
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configuración de servidores en un estado deseado y administración de desviaciones

Configuración de estado deseado (DSC) de Azure Automation permite especificar configuraciones para los servidores y asegurarse de que dichos servidores se encuentran en el estado especificado a lo largo del tiempo.



> [!div class="checklist"]
> * Incorporación de una máquina virtual para su administración por DSC de Azure Automation
> * Carga de una configuración en Azure Automation
> * Compilación de una configuración en una configuración de nodo
> * Asignación de una configuración de nodo a un nodo administrado
> * Comprobación del estado de cumplimiento de un nodo administrado

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesitará:

* Una cuenta de Azure Automation Para obtener instrucciones sobre cómo crear una cuenta de ejecución de Azure Automation, consulte el artículo sobre las [cuentas de ejecución de Azure](automation-sec-configure-azure-runas-account.md).
* Una máquina virtual de Azure Resource Manager (no clásico) ejecuta Windows Server 2008 R2, o cualquier versión posterior. Para obtener instrucciones sobre la creación de una máquina virtual, consulte [Creación de la primera máquina virtual de Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Módulo Azure PowerShell, versión 3.6 o posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Familiaridad con DSC. Para obtener información sobre DSC, consulte [Información general sobre la configuración de estado deseado de Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Creación y carga de una configuración en Azure Automation

Para este tutorial, se usará una configuración DSC simple que se asegura de que IIS está instalado en la máquina virtual.

Para más información sobre las configuraciones DSC, consulte [Configuraciones DSC](https://docs.microsoft.com/powershell/dsc/configurations).

En un editor de texto, escriba lo siguiente y guárdelo localmente como `TestConfig.ps1`.

```powershell
configuration TestConfig {

   Node WebServer {

      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Llame al cmdlet `Import-AzureRmAutomationDscConfiguration` para cargar la configuración en la cuenta de Automation:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilación de una configuración en una configuración de nodo

Una configuración DSC se debe compilar en una configuración de nodo antes de que se pueda asignar a un nodo.

Para más información sobre la compilación de configuraciones, consulte [Configuraciones DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Llame al cmdlet `Start-AzureRmAutomationDscCompilationJob` para compilar la configuración `TestConfig` en una configuración de nodo:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Esto crea una configuración de nodo llamada `TestConfig.WebServer` en su cuenta de Automation.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Registro de una máquina virtual para su administración mediante DSC

DSC de Azure Automation se puede usar para administrar máquinas virtuales de Azure (tanto el enfoque clásico como el de Resource Manager), máquinas virtuales locales, máquinas de Linux, máquinas virtuales de AWS y máquinas físicas locales. En este tema, se explicará cómo registrar solo máquinas virtuales de Azure Resource Manager.
Para más información sobre el registro de otros tipos de máquinas, consulte [Incorporación de máquinas para su administración mediante DSC de Azure Automation](automation-dsc-onboarding.md).

Llame al cmdlet `Register-AzureRmAutomationDscNode` para registrar la máquina virtual con DSC de Azure Automation.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

Esto registra la máquina virtual especificada como un nodo de DSC en la cuenta de Azure Automation.

### <a name="specify-configuration-mode-settings"></a>Especificación de opciones del modo de configuración

Al registrar una máquina virtual como un nodo administrado, también puede especificar propiedades de la configuración.
Por ejemplo, puede especificar que el estado de la máquina se aplicará una sola vez (DSC no intenta aplicar la configuración después de la comprobación inicial) mediante la especificación de `ApplyOnly` como el valor de la propiedad **ConfigurationMode**:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

También puede especificar la frecuencia con la que DSC comprueba el estado de configuración mediante el uso de la propiedad **ConfigurationModeFrequencyMins**:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Para más información acerca de cómo establecer propiedades de configuración para un nodo administrado, consulte [AzureRmAutomationDscNode registrar](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Para más información sobre las opciones de configuración de DSC, consulte [Configuración del administrador de configuración local](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Asignación de una configuración de nodo a un nodo administrado

Ahora se puede asignar la configuración de nodo compilada a la máquina virtual que desea configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Esto asigna la configuración de nodo llamada `TestConfig.WebServer` al nodo DSC registrado llamado `DscVm`.
De forma predeterminada, se comprueba el cumplimiento de la configuración del nodo DSC cada 30 minutos.
Para obtener información sobre cómo cambiar el intervalo de comprobación de cumplimiento, consulte [Configuración del administrador de configuración Local](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Comprobación del estado de cumplimiento de un nodo administrado

Puede obtener informes sobre el estado de cumplimiento de un nodo de DSC mediante una llamada al cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>pasos siguientes

* Para obtener información sobre cómo incorporar nodos para que se administren con DSC de Azure Automation, consulte [Incorporación de máquinas para administrarlas con DSC de Azure Automation](automation-dsc-onboarding.md).
* Para más información acerca del uso de Azure Portal para la utilización de DSC de Automation, consulte [Introducción a DSC de Azure Automation](automation-dsc-getting-started.md)
* Para obtener información acerca de la compilación de configuraciones de DSC para que poder asignarlas a los nodos de destino, vea [Compilación de configuraciones en DSC de Azure Automation](automation-dsc-compile.md).
* Para ver la referencia de cmdlets de PowerShell para DSC de Azure Automation, consulte [Cmdlets de DSC de Azure Automation](/powershell/module/azurerm.automation/#automation).
* Para obtener información de precios, consulte [Precios de DSC de Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
* Para ver un ejemplo del uso de DSC de Azure Automation en una canalización de implementación continua, consulte [Implementación continua en máquinas virtuales IaaS con DSC de Automation y Chocolatey](automation-dsc-cd-chocolatey.md)
