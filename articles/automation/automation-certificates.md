---
title: Recursos de certificados en Azure Automation | Microsoft Docs
description: "Los certificados se pueden almacenar de manera segura en Azure Automation, de manera que los runbooks o configuraciones de DSC pueden tener acceso a ellos para realizar la autenticación respecto de Azure y recursos de terceros.  Este artículo explica los detalles de los certificados y cómo trabajar con ellos en la creación de textos y de gráficos."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: b6a5ff4fa3fd0084fd910968651c6ae0fefaf2cf
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="certificate-assets-in-azure-automation"></a>Activos de certificados en Azure Automation

Los certificados se pueden almacenar de manera segura en Azure Automation de manera que los runbooks o las configuraciones de DSC pueden tener acceso a ellos mediante el uso de la actividad **Get-AzureRmAutomationCertificate** para recursos de Azure Resource Manager. Esto le permite crear runbooks y configuraciones de DSC que usan certificados para autenticación o agregarlos a Azure o a recursos de terceros.

> [!NOTE] 
> Los recursos protegidos en Azure Automation incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Azure Automation con una clave única que se genera para cada cuenta de Automation. Esta clave se cifra mediante un certificado maestro y se almacena en Azure Automation. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets de Windows PowerShell

Los cmdlets de la tabla siguiente se usan para crear y administrar variables de Automatización con Windows PowerShell. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md) que está disponible para su uso en las configuraciones de DSC y los runbooks de Automation.

|Cmdlets|Descripción|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|Recupera información sobre un certificado para utilizarlo en un runbook o en la configuración de DSC. Solo puede recuperar el certificado mismo desde la actividad Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|Crea un certificado nuevo en Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|Quita un certificado de Azure Automation.|Crea un certificado nuevo en Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|Establece las propiedades de un certificado existente, incluyendo la carga del archivo de certificado y el establecimiento de la contraseña de un .pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Carga un certificado de servicio para el servicio en la nube especificado.|


## <a name="python2-functions"></a>Funciones de Python2

La función de la tabla siguiente se usa para obtener acceso a los certificados de un runbook de Python2.

| Función | Descripción |
|:---|:---|
| automationassets.get_automation_certificate | Recupera información de un recurso de certificado. |

> [!NOTE]
> Debe importar el módulo **automationassets** al principio del runbook de Python para poder tener acceso a las funciones del recurso.


## <a name="creating-a-new-certificate"></a>Creación de un certificado nuevo

Cuando crea un certificado nuevo, debe cargar un archivo .cer o .pfx a Azure Automation. Si marca el certificado como exportable, podrá transferirlo fuera del almacén de certificados de Azure Automation. Si no es exportable, solo se puede usar para firmar dentro del runbook o la configuración de DSC.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para crear un certificado nuevo con el portal de Azure

1. En la cuenta de Automation, haga clic en el icono **Activos** para abrir la hoja **Activos**.
1. Haga clic en el icono **Certificados** para abrir la hoja **Certificados**.
1. Haga clic en **Agregar un certificado** en la parte superior del cuadro.
2. Escriba un nombre para el certificado en el cuadro **Nombre** .
2. Haga clic en **Seleccionar un archivo** en **Cargar un archivo de certificado** para buscar un archivo .cer o .pfx.  Si selecciona un archivo .pfx, especifique una contraseña y si se permitir o no su exportación.
1. Haga clic en **Crear** para guardar el recurso de certificado nuevo.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Para crear un certificado nuevo con Windows PowerShell

En el ejemplo siguiente se muestra cómo crear un nuevo certificado de Automation y marcarlo como exportable. Esta acción importa un archivo pfx ya existente.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>Uso de un certificado

Debe utilizar la actividad **Get-AutomationCertificate** para usar un certificado. No puede usar el cmdlet [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx), debido a que devuelve información sobre el activo de certificado, pero no el certificado mismo.

### <a name="textual-runbook-sample"></a>Ejemplo de runbook de texto

El código de ejemplo siguiente muestra cómo agregar un certificado a un servicio en la nube en un runbook. En este ejemplo, la contraseña se recupera a partir de una variable de automatización cifrada.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Ejemplo de runbook gráfico

Para agregar **Get-AutomationCertificate** a un runbook gráfico, haga clic con el botón derecho en el certificado en el panel Biblioteca del editor gráfico y, después, seleccione **Agregar al lienzo**.

![Adición del certificado al lienzo](media/automation-certificates/automation-certificate-add-to-canvas.png)

La imagen siguiente muestra un ejemplo de cómo usar un certificado en un runbook gráfico.  Este es el mismo ejemplo anteriormente mostrado para agregar un certificado a un servicio en la nube desde un runbook textual.

![Creación gráfica de ejemplo ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Ejemplo de Python2
En el ejemplo siguiente se muestra cómo obtener acceso a los certificados en runbooks de Python2.

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo trabajar con vínculos para controlar el flujo lógico de las actividades que su runbook está diseñado para efectuar, consulte el tema sobre los [vínculos en la creación gráfica](automation-graphical-authoring-intro.md#links-and-workflow). 
