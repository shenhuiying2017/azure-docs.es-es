---
title: "Filtrado de tráfico de red con grupos de seguridad de aplicaciones y de red de Azure (versión preliminar) | Microsoft Docs"
description: "Aprenda a crear grupos de seguridad de aplicaciones y de red (versión preliminar) para restringir el tipo de tráfico de red tanto dentro como fuera de las máquinas virtuales."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrado de tráfico de red con la red de Azure y grupos de seguridad de aplicaciones (versión preliminar)

En este tutorial aprenderá a crear grupos de seguridad de red para filtrar el tráfico de la red hacia y desde grupos de máquinas virtuales con grupos de seguridad de aplicaciones. Para saber más sobre los grupos de seguridad de red y los grupos de seguridad de aplicaciones, vea [Network Security](security-overview.md) (Seguridad de red). 

En las secciones siguientes se incluyen los pasos que se pueden seguir para crear grupos de seguridad de red mediante la interfaz de la línea de comandos de Azure ([CLI de Azure](#azure-cli)) y [Azure PowerShell](#powershell). El resultado es el mismo, independientemente de la herramienta que use para crear los grupos de seguridad de red. Haga clic en un vínculo de la herramienta para ir a esa sección del tutorial. 

En este artículo se indican los pasos para crear grupos de seguridad de red mediante el modelo de implementación del Administrador de recursos, que es el que se recomienda para crear redes virtuales. Si necesita crear un grupo de seguridad de red (clásico), vea [Creación de grupos de seguridad de red (clásicos) en PowerShell](virtual-networks-create-nsg-classic-ps.md). Si no está familiarizado con los modelos de implementación de Azure, vea [Understand Azure deployment models (Descripción de los modelos de implementación de Azure)](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> En este tutorial se usan las características de grupo de seguridad de red que actualmente se encuentran en la versión preliminar. Las características de la versión preliminar no tienen el mismo nivel de disponibilidad y confiabilidad que las características de la versión general. Si quiere implementar los grupos de seguridad de red usando solamente características de la versión general, vea [Creación de grupos de seguridad de red con Azure Portal](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Azure CLI

Los comandos de la CLI de Azure son los mismos, con independencia de que se ejecuten desde Windows, Linux o macOS. Pero existen diferencias de scripting entre los shells del sistema operativo. Los scripts de los pasos siguientes se ejecutan en un shell de Bash. 

1. [Instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Asegúrese de que utiliza la versión 2.0.18 o posterior de la CLI de Azure escribiendo el comando `az --version`. Si no la está usando, instale la versión más reciente.
3. Inicie sesión en Azure con el comando `az login`.
4. Regístrese para obtener la versión preliminar mediante estos comandos:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > El registro puede tardar hasta una hora en completarse. No continúe con los pasos restantes hasta que aparezca *Registrado* en **Estado** en la salida devuelta por el comando anterior. Si continúa antes de estar registrado, se producirá un error en los pasos restantes.

6. Ejecute el siguiente script de Bash para crear un grupo de recursos:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Cree tres grupos de seguridad de aplicaciones, uno para cada tipo de servidor:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Cree un grupo de seguridad de red:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Cree reglas de seguridad en los grupos de seguridad de red (NSG) y configure los grupos de seguridad de la aplicación como el destino:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Cree una red virtual: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Asocie el grupo de seguridad de red a la subred de la red virtual:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Cree tres interfaces de red, una para cada tipo de servidor: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Solo se aplica a la interfaz de red la regla de seguridad correspondiente que ha creado en el paso 9, en función del grupo de seguridad de aplicaciones al que pertenece la interfaz de red. Por ejemplo, solo la regla *AppRule* es eficaz para *myNic2*, porque la interfaz de red pertenece al grupo de seguridad de aplicaciones *AppServers* y la regla especifica el grupo de seguridad de aplicaciones *AppServers* como su destino. Las reglas *WebRule* y *DatabaseRule* no se aplican a *myNic2*, porque la interfaz de red no pertenece a los grupos de seguridad de aplicaciones *WebServers* y *DatabaseServers*. Tanto la regla *WebRule* como la regla *AppRule* son efectivas para *myNic1*; sin embargo, la interfaz de red de *myNic1* es miembro de los grupos de seguridad de aplicaciones *WebServers* y *AppServers* y las reglas especifican los grupos de seguridad de aplicaciones *WebServers* y *AppServers* como sus destinos. 

13. Cree una máquina virtual para cada tipo de servidor, conectando la interfaz de red correspondiente a cada máquina virtual. En este ejemplo se crean máquinas virtuales de Windows, pero puede cambiar *win2016datacenter* a *UbuntuLTS* para crear máquinas virtuales de Linux, si lo prefiere.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Opcional**: elimine los recursos que cree en este tutorial. Para ello, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instale y configure [PowerShell](/powershell/azure/install-azurerm-ps).
2. Asegúrese de que tiene instalada la versión 4.4.0 o posterior del módulo AzureRM. Puede comprobar qué versión tiene instalada actualmente mediante el comando `Get-Module -ListAvailable AzureRM`. Si necesita instalar o actualizar una versión, instale la más reciente del módulo AzureRM desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. En una sesión de PowerShell, inicie sesión en Azure con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) mediante el comando `login-azurermaccount`.
4. Regístrese para obtener la versión preliminar mediante estos comandos:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > El registro puede tardar hasta una hora en completarse. No continúe con los pasos restantes hasta que aparezca *Registrado* en **RegistrationState** en la salida devuelta por el comando anterior. Si continúa antes de estar registrado, se producirá un error en los pasos restantes.
        
6. Cree un grupo de recursos:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Cree tres grupos de seguridad de aplicaciones, uno para cada tipo de servidor:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Cree reglas de seguridad para cada tipo de servidor.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Cree un grupo de seguridad de red:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Cree una configuración de subred y asocie a ella el grupo de seguridad de red:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Cree una red virtual: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Cree tres interfaces de red, una para cada tipo de servidor. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Solo se aplica a la interfaz de red la regla de seguridad correspondiente que ha creado en el paso 8, en función del grupo de seguridad de aplicaciones al que pertenece la interfaz de red. Por ejemplo, solo la regla *AppRule* es eficaz para *myNic2*, porque la interfaz de red pertenece al grupo de seguridad de aplicaciones *AppServers* y la regla especifica el grupo de seguridad de aplicaciones *AppServers* como su destino. Las reglas *WebRule* y *DatabaseRule* no se aplican a *myNic2*, porque la interfaz de red no pertenece a los grupos de seguridad de aplicaciones *WebServers* y *DatabaseServers*. Tanto la regla *WebRule* como la regla *AppRule* son efectivas para *myNic1*; sin embargo, la interfaz de red de *myNic1* es miembro de los grupos de seguridad de aplicaciones *WebServers* y *AppServers* y las reglas especifican los grupos de seguridad de aplicaciones *WebServers* y *AppServers* como sus destinos. 

13. Cree una máquina virtual para cada tipo de servidor, conectando la interfaz de red correspondiente a cada máquina virtual. En este ejemplo se crean máquinas virtuales de Windows, pero si quiere crear máquinas virtuales de Linux, antes de ejecutar el script puede cambiar *-Windows* por *- Linux*, *MicrosoftWindowsServer* por *Canonical*, *WindowsServer* por *UbuntuServer* y *2016-Datacenter* por *14.04.2-LTS*.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Opcional**: elimine los recursos que cree en este tutorial. Para ello, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Eliminar un adaptador de red de ASG
Una vez que se quita una interfaz de red de un grupo de seguridad de aplicaciones, ninguna de las reglas que especifican el grupo de seguridad de aplicaciones se aplica a la interfaz de red que se va a quitar.

### <a name="azure-cli"></a>Azure CLI

Para quitar *myNic3* de todos los grupos de seguridad de aplicaciones, escriba el siguiente comando:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Para quitar *myNic3* de todos los grupos de seguridad de aplicaciones, escriba el siguiente comando:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Eliminación de recursos

Cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

### <a name="delete-portal"></a>Azure Portal

1. En el cuadro de búsqueda del portal, escriba **myResourceGroup**. En los resultados de la búsqueda, haga clic en **myResourceGroup**.
2. En la hoja **myResourceGroup**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroup** y, luego, haga clic en **Eliminar**.

### <a name="delete-cli"></a>Azure CLI

En una sesión de la CLI, escriba el comando siguiente:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

En una sesión de PowerShell, escriba el comando siguiente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

