---
title: "Ventaja para uso híbrido de Azure para Windows Server | Microsoft Docs"
description: "Descubra cómo maximizar las ventajas de Software Assurance de Windows para incorporar licencias locales a Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: 245bffbc208ce67d990a63e744c42dc671686b4b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Ventaja para uso híbrido de Azure para Windows Server
Para los clientes con Software Assurance, la ventaja para uso híbrido de Azure para Windows Server le permite usar las licencias de Windows Server locales y ejecutar máquinas virtuales de Windows en Azure a bajo costo. Puede usar la ventaja para uso híbrido de Azure para Windows Server para implementar nuevas máquinas virtuales desde imágenes personalizadas de Windows o cualquier imagen de Windows Server de una plataforma de Azure compatible. En este artículo se recorren los pasos necesarios para implementar nuevas máquinas virtuales con la Ventaja híbrida de Azure para Windows Server y para actualizar las máquinas virtuales en funcionamiento existentes. Para obtener más información acerca de los ahorros de costos y la concesión de licencias de la ventaja para uso híbrido para Azure para Windows Server, vea la [página de concesión de licencias de la ventaja para uso híbrido de Azure para Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Las imágenes de Windows Server [HUB] heredadas que se publicaron para clientes con el Contrato Enterprise en Azure Marketplace se han retirado a partir de 9/11/2017. Utilice la edición estándar de Windows Server con la opción "Ahorrar dinero" en el portal de Ventaja para uso híbrido de Azure para Windows Server. Para obtener más información, consulte este [artículo](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions).
>

> [!NOTE]
> El uso de la Ventaja híbrida de Azure para Windows Server con máquinas virtuales en las que se cobra por el software adicional, como SQL Server o cualquiera de las imágenes de Marketplace de terceros, se encuentra en proceso de implementación. Si recibe un error 409, por ejemplo: No se permite cambiar la propiedad "LicenseType", significa que intenta convertir o implementar una nueva máquina virtual Windows Server que tiene un coste por software adicional, que no se admite en esa región. Lo mismo ocurre si intenta que la opción de configuración del portal realice la conversión y no puede verla para esa máquina virtual.
>


> [!NOTE]
> Para las máquinas virtuales clásicas, solo se admite la implementación de una nueva máquina virtual desde imágenes personalizadas locales. Para aprovechar las ventajas de las funcionalidades admitidas en este artículo, primero debe migrar las máquinas virtuales clásicas al modelo de Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Formas de usar la ventaja para uso híbrido de Azure para Windows Server
Hay varias maneras de utilizar máquinas virtuales de Windows con la ventaja para uso híbrido de Azure:

1. Puede implementar máquinas virtuales desde una de las [imágenes de Windows Server en Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview) proporcionadas.
2. Puede [cargar una máquina virtual personalizada](#upload-a-windows-vhd) e [implementar mediante una plantilla de Resource Manager](#deploy-a-vm-via-resource-manager) o [Azure PowerShell](#detailed-powershell-deployment-walkthrough).
3. Puede cambiar y convertir la máquina virtual existente entre la ejecución con la Ventaja híbrida de Azure o el pago del coste adicional por Windows Server
4. También puede implementar un nuevo conjunto de escalado de máquinas virtuales con la ventaja para uso híbrido de Azure para Windows Server.

> [!NOTE]
> No se admite la conversión de un conjunto de escalado de máquinas virtuales existente para usar la Ventaja híbrida de Azure para Windows Server.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Implementación de una máquina virtual desde una imagen de Windows Server Marketplace
Todas las imágenes de Windows Server que están disponibles desde Azure Marketplace se habilitan con la ventaja para uso híbrido de Azure para Windows Server. Por ejemplo, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, y Windows Server 2008SP1, entre otros. Puede usar estas imágenes para implementar máquinas virtuales directamente desde Azure Portal, plantillas de Resource Manager, Azure PowerShell u otros SDK.

Puede implementar estas imágenes directamente desde Azure Portal. Para su uso en plantillas de Resource Manager y con Azure PowerShell, consulte la lista de imágenes de la siguiente manera:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Puede seguir los pasos en [Creación de una máquina virtual Windows con PowerShell](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) y pasar LicenseType = "Windows_Server". Esta opción le permite usar la licencia de Windows Server existente en Azure.

### <a name="portal"></a>Portal
Puede seguir los pasos en [Creación de una máquina virtual Windows desde Azure Portal](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) y seleccionar la opción para usar la licencia de Windows Server existente.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Conversión de una máquina virtual existente con la Ventaja híbrida de Azure para Windows Server
Si tiene una máquina virtual existente que le gustaría convertir para aprovechar la Ventaja híbrida de Azure para Windows Server, puede actualizar el tipo de licencia de dicha máquina del modo siguiente:

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Conversión para usar la Ventaja híbrida de Azure para Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Conversión de vuelta a pago por uso
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portal
En la hoja de la máquina virtual del portal, puede actualizar la máquina virtual para usar la Ventaja híbrida de Azure; para ello, seleccione la opción "Configuración" y cambie a la opción "Ventaja híbrida de Azure".

> [!NOTE]
> Si no ve la opción para activar o desactivar la "Ventaja híbrida de Azure" en "Configuración", esto se debe a que la conversión aún no es compatible con el tipo de máquina virtual seleccionado (por ejemplo, una máquina virtual creada a partir de una imagen personalizada o de una imagen que tiene software de pago adicional, como SQL Server o software de terceros de Azure Marketplace).
>

## <a name="upload-a-windows-server-vhd"></a>Carga de un VHD de Windows Server
Para implementar una máquina virtual de Windows Server en Azure, primero debe crear un VHD que contenga la compilación de Windows base. Este VHD debe haberse preparado adecuadamente mediante Sysprep antes de cargarlo en Azure. También puede obtener más información tanto [sobre los requisitos de los discos duros virtuales y el proceso Sysprep](upload-generalized-managed.md) como sobre la [compatibilidad de Sysprep con los roles de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Cree una copia de seguridad de la VM antes de ejecutar Sysprep. 

Cuando haya preparado el disco duro virtual, cárguelo en su cuenta de Azure Storage de la siguiente forma:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server y Dynamics también pueden utilizar una concesión de licencias de Software Assurance. Aun así, es preciso preparar la imagen de Windows Server, para lo que hay que instalar los componentes de la aplicación y proporcionar las claves de licencia según corresponda y, luego, cargar la imagen del disco en Azure. Revise la documentación adecuada para ejecutar Sysprep con su aplicación, como [Consideraciones acerca de la instalación de SQL Server con SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) o [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) [Creación de una imagen de referencia de SharePoint Server 2016 (Sysprep)].
>
>

También puede obtener más detalles sobre la [carga del disco duro virtual en un proceso de Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account).

## <a name="deploy-a-vm-via-resource-manager-template"></a>Implementación de una máquina virtual a través de una plantilla de Resource Manager
En las plantillas de Resource Manager, se debe especificar un parámetro `licenseType` adicional. En [Creación de plantillas de Azure Resource Manager](../../resource-group-authoring-templates.md), puede encontrar más información al respecto. Una vez que haya cargado el VHD en Azure, edite la plantilla de Resource Manager para incluir el tipo de licencia como parte del proveedor de procesos e impleméntela de la forma habitual:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Implementación de una máquina virtual a través del inicio rápido de PowerShell
Cuando se implementa una máquina virtual de Windows Server mediante PowerShell, se dispone de un parámetro adicional `-LicenseType`. Cuando el disco duro virtual esté cargado en Azure, cree una máquina virtual mediante `New-AzureRmVM` y especifique el tipo de concesión de licencias de la siguiente forma:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Puede leer una guía más descriptiva sobre los distintos pasos en [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Comprobación de que la máquina virtual está utilizando la ventaja de licencia
Cuando haya implementado la máquina virtual mediante PowerShell, la plantilla de Resource Manager o el portal, puede comprobar el tipo de licencia con `Get-AzureRmVM` , como se indica a continuación:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

La salida es similar a la del ejemplo siguiente para Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Este resultado se contrasta con la siguiente máquina virtual implementada sin licencias para la ventaja para uso híbrido de Azure para Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Enumeración de todas las ventajas para uso híbrido de Azure para máquinas virtuales de Windows Server en una suscripción

Para ver y contar todas las máquinas virtuales implementadas con la ventaja para uso híbrido de Azure para Windows Server, puede ejecutar el siguiente comando desde su suscripción:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implementación de un nuevo conjunto de escalado de máquinas virtuales con la ventaja para uso híbrido de Azure para Windows Server
En las plantillas de Resource Manager del conjunto de escalado de máquinas virtuales se debe especificar un parámetro `licenseType` adicional. En [Creación de plantillas de Azure Resource Manager](../../resource-group-authoring-templates.md), puede encontrar más información al respecto. Edite la plantilla de Resource Manager para incluir la propiedad licenseType como parte de virtualMachineProfile del conjunto de escalado e implemente la plantilla como normal (vea el ejemplo siguiente en el que se utiliza la imagen de Windows Server 2016):


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
También puede [crear e implementar un conjunto de escalado de máquinas virtuales](#https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) y establecer la propiedad LicenseType.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre [cómo ahorrar dinero con la Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Aprenda más sobre la [concesión de licencias para la Ventaja híbrida de Azure en esta guía detallada](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit).

Obtenga más información sobre el [uso de plantillas de Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Obtenga más información sobre [Azure Hybrid Benefit for Windows Server and Azure Site Recovery make migrating applications to Azure even more cost-effective](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/) (la Ventaja híbrida de Azure para Windows Server y Azure Site Recovery rentabilizan aún más la migración de aplicaciones a Azure).

Obtenga más información sobre [Implementación de Windows 10 en Azure con derechos de hospedaje multiinquilino](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment).

Obtenga más información sobre [Preguntas más frecuentes](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/).
