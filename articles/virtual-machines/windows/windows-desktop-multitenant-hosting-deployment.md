---
title: "Implementación de Windows 10 en Azure con derechos de hospedaje multiinquilino"
description: "Descubra cómo maximizar las ventajas de Software Assurance de Windows para incorporar licencias locales a Azure"
services: virtual-machines-windows
documentationcenter: 
author: xujing
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 8/20/2017
ms.author: xujing
ms.openlocfilehash: e3209abd17c7ba3e39a67f834be69f113c27a021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Implementación de Windows 10 en Azure con derechos de hospedaje multiinquilino 
Para los clientes con Windows 10 Enterprise E3/E5 por usuario o con acceso a escritorios virtuales de Windows por usuario (licencias de suscripción de usuarios o licencias de suscripción de usuario de complemento), los derechos de hospedaje multiinquilino de Windows 10 le permiten llevar sus licencias de Windows 10 a la nube y ejecutar máquinas virtuales de Windows 10 en Azure sin pagar por otra licencia. Para más información, consulte [Multitenant Hosting for Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx) (Hospedaje multiinquilino para Windows 10).

> [!NOTE]
> En este artículo se explica cómo implementar la ventaja de las licencias con las imágenes de Windows 10 Desktop. Puede consultar el siguiente artículo para más información sobre las [Ventajas de uso híbrido de Azure para imágenes de Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implementación de la imagen de Windows 10 desde Azure Marketplace 
Para las implementaciones de plantilla de Powershell, CLI y Azure Resource Manager, la imagen de Windows 10 puede encontrarse con el siguiente nombre de editor, oferta o sku.

| SO  |      PublisherName      |  Oferta | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Carga del disco duro virtual de Windows 10 en Azure
Si va a cargar un disco duro virtual generalizado de Windows 10, tenga en cuenta que Windows 10 no tiene una cuenta de administrador integrada habilitada de forma predeterminada. Para habilitar la cuenta de administrador integrada, incluya el siguiente comando como parte de la extensión de script personalizada.

```powershell
Net user <username> /active:yes
```

El siguiente fragmento de código de PowerShell sirve para marcar todas las cuentas de administrador como activas, incluida la del administrador integrado. Este ejemplo es útil si desconoce el nombre de usuario del administrador integrado.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para obtener más información: 
* [Carga del disco duro virtual en Azure](upload-generalized-managed.md)
* [Preparación de un disco duro virtual de Windows para cargarlo en Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implementación de Windows 10 con derechos de hospedaje multiinquilino
Asegúrese de que tiene [la versión de Azure PowerShell más reciente instalada y configurada](/powershell/azure/overview). Cuando haya preparado el disco duro virtual, cárguelo en su cuenta de Almacenamiento de Azure mediante el cmdlet `Add-AzureRmVhd` de la siguiente forma:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementación mediante la plantilla de Azure Resource Manager**: dentro de las plantillas de Resource Manager, se puede especificar un parámetro adicional para `licenseType`. En [Creación de plantillas de Azure Resource Manager](../../resource-group-authoring-templates.md), puede encontrar más información al respecto. Una vez que haya cargado el VHD en Azure, edite la plantilla de Resource Manager para incluir el tipo de licencia como parte del proveedor de procesos e impleméntela de la forma habitual:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Implementación mediante PowerShell**: cuando se implementa una máquina virtual de Windows Server mediante PowerShell, se dispone de un parámetro adicional para `-LicenseType`. Cuando el disco duro virtual esté cargado en Azure, cree una máquina virtual mediante `New-AzureRmVM` y especifique el tipo de concesión de licencias de la siguiente forma:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Comprobación de que la máquina virtual está utilizando la ventaja de licencia
Cuando haya implementado la máquina virtual mediante el método de implementación de Resource Manager o PowerShell, compruebe el tipo de licencia con `Get-AzureRmVM` , como se indica a continuación:
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

La salida es similar a la del ejemplo siguiente para Windows 10 con el tipo correcto de licencia:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Esta salida contrasta con la siguiente máquina virtual implementada sin la licencia de ventaja de uso híbrido de Azure, como una implementada directamente desde la galería de Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Información adicional sobre la unión con Azure AD
>[!NOTE]
>Azure aprovisiona todas las máquinas virtuales Windows con la cuenta de administrador integrada, que no se puede usar para unirse con AAD. Por ejemplo, *Configuración > Cuenta > Obtener acceso a trabajo o escuela > +Conectar* no funcionará. Debe crear e iniciar sesión como una segunda cuenta de administrador para unirse de forma manual a Azure AD. También puede configurar Azure AD mediante un paquete de aprovisionamiento. Use el vínculo de la sección *Pasos siguientes* para más información.
>
>

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre la [configuración de VDA para Windows 10](https://docs.microsoft.com/en-us/windows/deployment/vda-subscription-activation).
- Obtenga más información sobre el [hospedaje multiinquilino para Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


