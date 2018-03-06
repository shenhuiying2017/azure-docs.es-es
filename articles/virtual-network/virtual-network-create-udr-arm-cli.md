---
title: "Crear una ruta definida por el usuario para enrutar el tráfico de red a través de una aplicación virtual de red: CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear una ruta definida por el usuario para invalidar el enrutamiento predeterminado de Azure enrutando el tráfico de red a través de una aplicación virtual de red."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: adfcf53f9fca0efafb538edfd65b95313dcf1559
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>Crear una ruta definida por el usuario: CLI de Azure

En este tutorial obtendrá información sobre cómo crear rutas definidas por el usuario para enrutar el tráfico entre dos subredes de la [red virtual](virtual-networks-overview.md) a través de una aplicación virtual de red. Una aplicación virtual de red es una máquina virtual que ejecuta una aplicación de red como, por ejemplo, un firewall. Para obtener más información sobre las aplicaciones virtuales de red preconfiguradas que puede implementar en una red virtual de Azure, consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Al crear subredes en una red virtual, Azure crea [rutas del sistema](virtual-networks-udr-overview.md#system-routes) predeterminadas que permiten a los recursos de todas las subredes comunicarse entre sí, tal como se muestra en la siguiente imagen:

![Rutas predeterminadas](./media/create-user-defined-route/default-routes.png)

En este tutorial podrá crear una red virtual con subredes públicas, privadas y DMZ, tal como se muestra en la imagen que aparece a continuación. Normalmente, los servidores web pueden implementarse en una subred pública, mientras que un servidor de aplicaciones o bases de datos puede implementarse en una subred privada. Puede crear una máquina virtual que actúe como una aplicación virtual de red en la subred DMZ y, de forma opcional, crear una máquina virtual en cada red que se comunique a través de la aplicación virtual de red. Todo el tráfico entre las subredes públicas y privadas se enruta a través de la aplicación, tal como se muestra en la siguiente imagen:

![Rutas definidas por el usuario](./media/create-user-defined-route/user-defined-routes.png)

En este artículo se indican los pasos para crear una ruta definida por el usuario mediante el modelo de implementación del Administrador de recursos, que es el modelo de implementación recomendado para crear rutas definidas por el usuario. Si debe crear una ruta definida por el usuario (clásica), consulte [Creación de una ruta definida por el usuario (clásica)](virtual-network-create-udr-classic-cli.md). Si no está familiarizado con los modelos de implementación de Azure, vea [Understand Azure deployment models (Descripción de los modelos de implementación de Azure)](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obtener más información sobre las rutas definidas por el usuario, consulte [Introducción a las rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Creación de rutas y aplicaciones virtuales de red

Los comandos de la CLI de Azure son los mismos, con independencia de que se ejecuten desde Windows, Linux o macOS. Pero existen diferencias de scripting entre los shells del sistema operativo. Los scripts que se muestran en los siguientes pasos requieren una instalación y ejecución de los comandos de CLI de Azure en un shell de Bash. Puede [instalar y configurar la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) en su equipo, o bien hacer clic en el botón **Probar** de cualquiera de los scripts para ejecutarlos en Azure Cloud Shell.
 
1. **Requisito previo**: cree una red virtual con dos subredes completando los pasos que se detallan en [Crear una red virtual](#create-a-virtual-network).
2. Si se ejecuta la CLI de Azure desde el equipo, inicie sesión en Azure con el comando `az login`. Si usa Cloud Shell, ya habrá iniciado sesión automáticamente.
3. Establezca algunas de las variables que se usaron a lo largo de los pasos restantes:

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. Cree una subred de *DMZ* en la red virtual que se creó en el requisito previo:

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. Cree la máquina virtual de la aplicación virtual de red (NVA). Asigne direcciones IP estáticas (públicas y privadas) a la interfaz de red que crea la CLI. Las direcciones estáticas no cambian durante la vida útil de la máquina virtual. La NVA puede ser una máquina virtual que ejecute los sistemas operativos Linux o Windows. Para crearla, copie el script de cualquiera de los sistemas operativos y péguelo en la CLI. Si crea una máquina virtual de Windows, pegue el script en un editor de texto, cambie el valor de la variable *AdminPassword* y, a continuación, pegue el texto modificado en la CLI.

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. Habilite el reenvío de direcciones IP de la interfaz de red de la NVA. Si habilita el reenvío de direcciones IP de una interfaz de red, Azure no podrá comprobar la dirección IP de origen o destino. Si no habilita esta configuración, Azure eliminará el tráfico destinado a una dirección IP que no sea la NIC que lo recibe.

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. Cree una tabla de rutas para la subred *pública*.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. De forma predeterminada, Azure enruta el tráfico entre todas las subredes de una red virtual. Cree una ruta para cambiar el enrutamiento predeterminado de Azure, de modo que el tráfico de la subred pública que va a la subred privada se enrute a través de la NVA, en lugar de ir directamente a la subred privada.

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. Asocie la tabla de rutas *myRouteTable-Public* a la subred *pública*. Al asociar una tabla de rutas a una subred, Azure enruta todo el tráfico saliente de la subred según las rutas de la tabla de ruta. Una tabla de rutas se puede asociar a varias subredes o a ninguna, mientras que una subred puede tener una tabla de rutas asociada o ninguna.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. Cree la tabla de rutas de la subred *privada*.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. Cree una ruta para enrutar el tráfico de la subred *privada* a la *pública* a través de la máquina virtual de NVA.

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. Asocie la tabla de rutas a la subred *privada*.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **Opcional:** cree una máquina virtual en las subredes pública y privada y confirme que esa comunicación entre máquinas virtuales se enrute a través de la aplicación virtual de red completando los pasos en [Validación de enrutamiento](#validate-routing).
14. **Opcional**: para eliminar los recursos que creó en este tutorial, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-resources).

## <a name="validate-routing"></a>Validar el enrutamiento

1. Si aún no lo ha hecho, complete los pasos que se detallan en [Creación de rutas y aplicación virtual de red](#create-routes-and-network-virtual-appliance).
2. Haga clic en el botón **Probar** en el cuadro que aparece a continuación y que abre Azure Cloud Shell. Cuando se le solicite, inicie sesión en Azure con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell es un shell de Bash gratuito con la interfaz de la línea de comandos de Azure preinstalada. 

    Los siguientes scripts crean dos máquinas virtuales: una en la subred *pública* y la otra en la subred *privada*. Asimismo, los scripts también habilitan el reenvío de IP de la interfaz de red en el sistema operativo de la NVA para permitir a dicho sistema operativo enrutar el tráfico a través de la interfaz de red. Una NVA de producción suele inspeccionar el tráfico antes de enrutarlo. Sin embargo, en este tutorial, la sencilla NVA se limita a enrutar el tráfico sin inspeccionarlo. 

    Haga clic en el botón **Copiar** en los scripts de **Linux** o **Windows** que aparecen a continuación y pegue el contenido de los scripts en un editor de texto. Cambie la contraseña de la variable *adminPassword* y, a continuación, pegue el script en Azure Cloud Shell. Ejecute el script del sistema operativo seleccionado al crear la aplicación virtual de red en el paso 5 de [Creación de rutas y aplicaciones virtuales de red](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Valide la comunicación entre las máquinas virtuales en las subredes pública y privada. 

    - Abra una conexión [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) o de [Escritorio remoto](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) a la dirección IP pública de la máquina virtual *myVm-Public*.
    - En un símbolo del sistema de la máquina virtual *myVm-Public*, escriba `ping myVm-Private`. Es posible que reciba respuestas porque la NVA enruta el tráfico desde la subred pública a la privada.
    - En la máquina virtual *myVm-Public*, ejecute una ruta de seguimiento entre las máquinas virtuales de las subredes pública y privada. Escriba el comando adecuado que aparece a continuación, en función del sistema operativo instalado en las máquinas virtuales de las subredes pública y privada:
        - **Windows**: en un símbolo del sistema, ejecute el comando `tracert myvm-private`.
        - **Ubuntu**: ejecute el comando `tracepath myvm-private`.
      El tráfico pasa a través de 10.0.2.4 (la NVA) antes de llegar a 10.0.1.4 (la máquina virtual de la subred privada). 
    - Complete los pasos anteriores conectándose a la máquina virtual *myVm-Private* y haciendo ping en la máquina virtual *myVm-Public*. En la ruta de seguimiento se muestra la circulación de la comunicación a través de 10.0.2.4 antes de llegar a 10.0.0.4 (la máquina virtual de la subred pública).
      
      > [!NOTE]
      > Los pasos anteriores le permiten confirmar el enrutamiento entre direcciones IP privadas de Azure. Si desea reenviar, o transmitir, el tráfico a direcciones IP públicas a través de una aplicación virtual de red:
      > - El dispositivo debe proporcionar la traducción de direcciones de red o la funcionalidad del proxy. En el caso de la traducción de direcciones de red, el dispositivo debe trasladar la dirección IP de origen a la suya propia y, después, reenviar esa solicitud a la dirección IP pública. Si el dispositivo tiene una dirección de red trasladada a la dirección de origen, o se está transmitiendo, Azure traslada la dirección IP privada de la aplicación virtual de red a una dirección IP pública. Para obtener más información acerca de los distintos métodos que Azure usa para trasladar direcciones IP privadas a direcciones IP públicas, consulte [Comprender las conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Una ruta adicional en la tabla de rutas, como el prefijo: 0.0.0.0/0, tipo de próximo salto VirtualAppliance y dirección IP del próximo salto 10.0.2.4 (en el script del ejemplo anterior).
      >
    - **Opcional**: para validar el próximo salto entre dos máquinas virtuales de Azure, use la funcionalidad de próximo salto de Azure Network Watcher. Antes de usar Network Watcher, primero debe [crear una instancia de Azure Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para la región donde desea utilizarla. En este tutorial, se usa la región de Este de EE. UU. Una vez que haya habilitado una instancia de Network Watcher para la región, escriba el siguiente comando para ver la información del próximo salto entre las máquinas virtuales de las subredes pública y privada:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       El resultado devuelve *10.0.2.4* como **nextHopIpAddress** y *VirtualAppliance* como **nextHopType**.

> [!NOTE]
> Para ilustrar los conceptos de este tutorial, las direcciones IP públicas se asignan a las máquinas virtuales de las subredes pública y privada, mientras que todo el acceso a los puertos de red está habilitado en Azure para ambas máquinas virtuales. Al crear máquinas virtuales para su uso en producción, es posible no asignar direcciones IP públicas a estas y filtrar el tráfico de red a la subred privada implementando una aplicación virtual de red frente a él o asignando un grupo de seguridad de red a las subredes, a la interfaz de red o a ambas. Para obtener más información sobre los grupos de seguridad de red, consulte [Grupos de seguridad de red](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Crear una red virtual

En este tutorial se requiere una red virtual existente con dos subredes. Haga clic en el botón **Probar** del cuadro que aparece a continuación para crear rápidamente una red virtual. Al hacer clic en el botón **Probar**, se abre [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aunque Cloud Shell ejecuta PowerShell o un shell de Bash, en esta sección, el shell de Bash se usa para crear la red virtual. El shell de Bash tiene la interfaz de la línea de comandos de Azure instalada. Cuando Cloud Shell se lo solicite, inicie sesión en Azure con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Para crear la red virtual usada en este tutorial, haga clic en el botón **Copiar** del siguiente cuadro y, a continuación, pegue el script en Azure Cloud Shell:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Para obtener más información sobre cómo usar el portal, PowerShell o una plantilla de Azure Resource Manager para crear una red virtual, consulte [Crear una red virtual](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Eliminar recursos

Cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo. En una sesión de la CLI, escriba el comando siguiente:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Cree una [aplicación virtual de red de alta disponibilidad](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Las aplicaciones virtuales de red suelen tener varias interfaces de red y direcciones IP asignadas a ellas. Obtenga más información sobre cómo [agregar interfaces de red a una máquina virtual existente](virtual-network-network-interface-vm.md#vm-add-nic) y [agregar direcciones IP a una interfaz de red existente](virtual-network-network-interface-addresses.md#add-ip-addresses). Aunque todos los tamaños de máquina virtual pueden tener al menos dos interfaces de red asociadas a ellos, cada tamaño de máquina virtual admite un número máximo de interfaces de red. Para obtener información sobre cuántas interfaces de red admite cada tamaño de máquina virtual, consulte los tamaños de máquina virtual de [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) y [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Cree una ruta definida por el usuario para forzar el tráfico de túnel local a través de una [conexión VPN de sitio a sitio](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
