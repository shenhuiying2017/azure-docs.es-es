---
title: Paso de un objeto JSON a un runbook de Azure Automation | Microsoft Docs
description: "Procedimiento para pasar parámetros a un runbook como un objeto JSON"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: powershell, runbook, json, azure automation
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: eac0e95a46731b9d396ea0590e629d61ca6a7d70
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Paso de un objeto JSON a un runbook de Azure Automation

Puede ser útil almacenar los datos que desea pasar a un runbook en un archivo JSON.
Por ejemplo, podría crear un archivo JSON que contiene todos los parámetros que desea pasar a un runbook.
Para hacerlo, debe convertir el archivo JSON en una cadena y luego convertir esta cadena en un objeto de PowerShell antes de pasar el contenido al runbook.

En este ejemplo, se creará un script de PowerShell que llamará a [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar un runbook de PowerShell, pasando el contenido del archivo JSON al runbook.
El runbook de PowerShell inicia una máquina virtual de Azure y se obtienen los parámetros de la máquina virtual desde el archivo JSON que se pasó.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o <a href="/pricing/free-account/" target="_blank">[registrarse para obtener una cuenta gratis](https://azure.microsoft.com/free/).
* [Cuenta de Automatización](automation-sec-configure-azure-runas-account.md) para contener el Runbook y autenticarse en recursos de Azure.  Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Detendremos e iniciaremos esta máquina, por lo que no debería ser una máquina virtual de producción.
* Azure Powershell instalado en una máquina local. Consulte [Instalación y configuración de Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) para información sobre cómo obtener Azure PowerShell.

## <a name="create-the-json-file"></a>Creación del archivo JSON

Escriba el texto siguiente en un archivo de texto y guárdelo como `test.json` en algún lugar del equipo local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Creación del runbook

Cree un runbook de PowerShell denominado "Test-Json" en Azure Automation.
Para saber cómo crear un runbook de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).

Para aceptar los datos de JSON, el runbook debe tomar un objeto como parámetro de entrada.

De ese modo, el runbook puede usar las propiedades definidas en el archivo JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Guarde y publique este runbook en su cuenta de Automation.

## <a name="call-the-runbook-from-powershell"></a>Llamada al runbook desde PowerShell

Ahora puede llamar al runbook desde la máquina local mediante Azure PowerShell.
Ejecute los comandos de PowerShell siguientes:

1. Inicie sesión en Azure:
   ```powershell
   Login-AzureRmAccount
   ```
    Se le pedirá que escriba las credenciales de Azure.
1. Obtenga el contenido del archivo JSON y conviértalo en una cadena:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` es la ruta de acceso donde se guardó el archivo JSON.
1. Convierta el contenido de la cadena de `$json` en un objeto de PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Cree una tabla hash para los parámetros de `Start-AzureRmAutomstionRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Tenga en cuenta que se establece el valor de `Parameters` en el objeto de PowerShell que contiene los valores desde el archivo JSON. 
1. Inicio del runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

El runbook usa los valores del archivo JSON para iniciar una máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de la edición de runbooks de PowerShell y flujo de trabajo de PowerShell, consulte [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md) 
* Para información sobre cómo crear e importar runbooks, consulte [Creación o importación de un runbook en Azure Automation](automation-creating-importing-runbook.md)


