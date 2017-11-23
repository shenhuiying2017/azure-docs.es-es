---
title: "Creación de una máquina virtual Windows con zonas mediante Azure Portal | Microsoft Docs"
description: "Creación de una máquina virtual Windows en una zona de disponibilidad con Azure Portal"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 4d48aff7d29def9fa54438a11885b4ff4fba54cc
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Creación de una máquina virtual Windows en una zona de disponibilidad con Azure Portal

En este artículo se describe el uso de Azure Portal para crear una máquina virtual en una zona de disponibilidad de Azure. Una [zona de disponibilidad](../../availability-zones/az-overview.md) es una zona separada físicamente en una región de Azure. Use zonas de disponibilidad para proteger sus datos y aplicaciones de la improbable pérdida o error de todo un centro de datos.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]


## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-virtual-machine"></a>Create virtual machine

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**. 

3. Escriba la información de la máquina virtual. El nombre de usuario y la contraseña que especifique aquí se usarán para iniciar sesión en la máquina virtual. Cuando haya terminado, haga clic en **Aceptar**.

    ![Especificación de la información básica de la máquina virtual en la hoja del Portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Seleccione un tamaño para la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). Tenga cuidado al seleccionar uno de los tamaños admitidos en la versión preliminar de zonas de disponibilidad, como *DS1_v2 Standard*. 

    ![Captura de pantalla que muestra los tamaños de máquina virtual](./media/create-portal-availability-zone/create-linux-vm-portal-sizes.png)  

5. En **Configuración** > **Alta disponibilidad**, seleccione una de las zonas numeradas en la lista desplegable **Zona de disponibilidad**, mantenga el resto de valores predeterminados y haga clic en **Aceptar**.

    ![Seleccione una zona de disponibilidad.](./media/create-portal-availability-zone/create-linux-vm-portal-availability-zone.png)

6. En la página de resumen, haga clic en **Comprar** para iniciar la implementación de la máquina virtual.

7. La máquina virtual se anclará al panel de Azure Portal. Una vez completada la implementación, se abrirá automáticamente el resumen de la máquina virtual.


## <a name="zone-for-ip-address-and-managed-disk"></a>Zona de dirección IP y disco administrado

Cuando la máquina virtual se implementa en una zona de disponibilidad, la dirección IP y los recursos del disco administrado se implementan en la misma zona de disponibilidad. Puede confirmar la configuración de zona con Azure PowerShell. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

En los ejemplos siguientes se obtiene información sobre los recursos de un grupo de recursos denominado *myResourceGroup*. Sustituya el nombre del grupo de recursos que usó para crear la máquina virtual.

Busque la zona de la dirección IP pública con [Get-AzureRmPublicIpAddress](/en-us/powershell/module/azurerm.network/get-azurermpublicipaddress):

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup
```
El valor `Zones` de la salida muestra que la dirección IP pública está en la misma zona de disponibilidad que la máquina virtual:

```powershell
Name                     : myVM-ip
ResourceGroupName        : myResourceGroup
Location                 : eastus2
Id                       : /subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/danlep0911/providers/Micr
                           osoft.Network/publicIPAddresses/myVM-ip
Etag                     : W/"b67e14c0-7e8a-4d12-91c5-da2a5dfad132"
ResourceGuid             : 314bf57d-9b25-4474-9282-db3561d536aa
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.68.16.25
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVM11842/ipConfigurations/ipconfig1"
                           }
DnsSettings              : null
Zones                    : {2}
```


También se crea el recurso de disco administrado para la máquina virtual en la misma zona de disponibilidad. Puede comprobarlo con [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk):

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

La salida muestra que el disco administrado está en la misma zona de disponibilidad que la máquina virtual:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a crear una máquina virtual en una zona de disponibilidad. Aprenda más sobre las [regiones y la disponibilidad](regions-and-availability.md) de las máquinas virtuales de Azure.
