---
title: "Creación de una máquina virtual Windows de SQL Server con Azure PowerShell | Microsoft Docs"
description: "Este tutorial muestra cómo crear una máquina virtual Windows de SQL Server 2017 con Azure PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 9af08fd46314ff102eff95e0832f7ce96bc161d6
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Creación de una máquina virtual Windows de SQL Server con Azure PowerShell

Esta guía de inicio rápido le ayuda a crear una máquina virtual de SQL Server con Azure PowerShell.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar los pasos de esta guía, se requiere la versión 3.6 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="configure-powershell"></a>Configuración de PowerShell

1. Abra PowerShell y establezca el acceso a su cuenta de Azure, para lo que debe ejecutar el comando **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Debería ver una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

1. Defina una variable con un nombre único de grupo de recursos. Para simplificar el resto de la guía de inicio rápido, el resto de los comandos usan este nombre como base de otros nombres de recursos.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Defina la ubicación de una región de Azure de destino para todos los recursos de máquina virtual.

   ```PowerShell
   $Location = "East US"
   ```

1. Cree el grupo de recursos.

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Configuración de la red

1. Cree una red virtual, una subred, una dirección IP pública. Estos recursos se utilizan para proporcionar conectividad de red a la máquina virtual y conectarse a Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Cree un grupo de seguridad de red. Configure reglas para permitir conexiones de SQL Server y de escritorio remoto (RDP).

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Cree la interfaz de red.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Creación de la máquina virtual con SQL

1. Defina sus credenciales para iniciar sesión en la máquina virtual. El nombre de usuario es "azureadmin". Asegúrese de cambiar la contraseña antes de ejecutar el comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Cree un objeto de configuración de máquina virtual y, a continuación, cree la máquina virtual. El siguiente comando crea una máquina virtual de SQL Server 2017 Developer Edition en Windows Server 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Se tarda unos minutos en crearla.

## <a name="install-the-sql-iaas-agent"></a>Instalación del Agente de IaaS de SQL Server

Para obtener las características de máquina virtual SQL e integración del portal, debe instalar la [Extensión del Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para instalar al agente en la nueva máquina virtual, ejecute el comando siguiente después de crearlo.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Escritorio remoto en la máquina virtual

1. Use el comando siguiente para recuperar la dirección IP pública para la nueva máquina virtual.

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. A continuación, tome la dirección IP devuelta y pásela como parámetro de línea de comandos a **mstsc** para iniciar una sesión de Escritorio remoto en la nueva máquina virtual.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Cuando se le pidan credenciales, elija introducir las de una cuenta diferente. Escriba el nombre de usuario con una barra diagonal inversa; por ejemplo, `\azureadmin` y la contraseña que ha definido anteriormente en esta guía de inicio rápido.

## <a name="connect-to-sql-server"></a>Conexión con SQL Server

1. Después de iniciar sesión en la sesión de Escritorio remoto, inicie **SQL Server Management Studio 2017** desde el menú Inicio.

1. En el cuadro de diálogo **Conectar con el servidor**, mantenga los valores predeterminados. El nombre de servidor es el de la máquina virtual. La autenticación se establece en **Autenticación de Windows**. Haga clic en **Conectar**.

Ahora está conectado a SQL Server localmente. Si desea conectarse de forma remota, deberá [configurar la conectividad](virtual-machines-windows-sql-connect.md) desde el portal o manualmente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no necesita que la máquina virtual se ejecute continuamente, puede detenerla cuando no se esté usando y así evitar cargos innecesarios. El siguiente comando detiene la máquina virtual, pero la deja disponible para usarla en el futuro.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

También puede eliminar de forma definitiva todos los recursos asociados a la máquina virtual con el comando **Remove-AzureRmResourceGroup**. Como esta acción también elimina la máquina virtual definitivamente, use este comando con cuidado.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una máquina virtual de SQL Server 2017 con Azure PowerShell. Para más información sobre la migración de los datos a la nueva instancia de SQL Server, consulte el artículo siguiente.

> [!div class="nextstepaction"]
> [Migración de una base de datos a una máquina virtual SQL](virtual-machines-windows-migrate-sql.md)