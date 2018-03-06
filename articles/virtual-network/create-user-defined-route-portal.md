---
title: "Creación de una ruta definida por el usuario para enrutar el tráfico de red a través de una aplicación virtual de red - Azure Portal | Microsoft Docs"
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
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Creación de una ruta definida por el usuario - Azure Portal

En este tutorial, obtenga información sobre cómo crear rutas definidas por el usuario para enrutar el tráfico entre dos subredes de la [red virtual](virtual-networks-overview.md) a través de una aplicación virtual de red. Una aplicación virtual de red es una máquina virtual que ejecuta una aplicación de red como, por ejemplo, un firewall. Para obtener más información sobre las aplicaciones virtuales de red preconfiguradas que puede implementar en una red virtual de Azure, consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Al crear subredes en una red virtual, Azure crea [rutas del sistema](virtual-networks-udr-overview.md#system-routes) predeterminadas que permiten a los recursos de todas las subredes comunicarse entre sí, tal como se muestra en la siguiente imagen:

![Rutas predeterminadas](./media/create-user-defined-route/default-routes.png)

En este tutorial podrá crear una red virtual con subredes públicas, privadas y DMZ, tal como se muestra en la imagen que aparece a continuación. Normalmente, los servidores web pueden implementarse en una subred pública, mientras que un servidor de aplicaciones o bases de datos puede implementarse en una subred privada. Puede crear una máquina virtual que actúe como una aplicación virtual de red en la subred DMZ y, de forma opcional, crear una máquina virtual en cada red que se comunique a través de la aplicación virtual de red. Todo el tráfico entre las subredes públicas y privadas se enruta a través de la aplicación, tal como se muestra en la siguiente imagen:

![Rutas definidas por el usuario](./media/create-user-defined-route/user-defined-routes.png)

En este artículo se indican los pasos para crear una ruta definida por el usuario mediante el modelo de implementación del Administrador de recursos, que es el modelo de implementación recomendado para crear rutas definidas por el usuario. Si debe crear una ruta definida por el usuario (clásica), consulte [Creación de una ruta definida por el usuario (clásica)](virtual-network-create-udr-classic-ps.md). Si no está familiarizado con los modelos de implementación de Azure, vea [Understand Azure deployment models (Descripción de los modelos de implementación de Azure)](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obtener más información sobre las rutas definidas por el usuario, consulte [Introducción a las rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Creación de rutas y aplicación virtual de red

1. **Requisito previo**: cree una red virtual con dos subredes completando los pasos en [Crear una red virtual](#create-a-virtual-network).
2. Una vez que haya creado la red virtual, en un explorador de Internet, vaya a [Azure Portal](https://portal.azure.com). Inicie sesión con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myResourceGroup*. Haga clic en **myResourceGroup** cuando aparezca en los resultados de la búsqueda. El grupo de recursos se creó como parte del requisito previo.
4. Haga clic en **myVnet**, tal como se muestra en la siguiente imagen:

    ![Configuración de la interfaz de red](./media/create-user-defined-route/virtual-network.png)

5. Cree una subred para la aplicación virtual de red:
 
    - En **myVnet**, haga clic en **Subredes** de **CONFIGURACIÓN**, a la izquierda.
    - Haga clic en **+ Subred**, como se muestra en la siguiente imagen:

        ![Subredes](./media/create-user-defined-route/subnets.png) 
    - Escriba los siguientes valores en **Agregar subred** y, a continuación, haga clic en **Aceptar**:

        |Configuración|Valor|
        |-----|-----|
        |Nombre|DMZ|
        |Intervalo de direcciones (bloque CIDR)|10.0.2.0/24|

6. Cree una máquina virtual de la aplicación virtual de red:

    - En el portal, a la izquierda, haga clic en **+ Nuevo**, **Compute** y **Windows Server 2016 Datacenter** o **Ubuntu Server 16.04 LTS**.
    - Escriba los siguientes valores en la hoja **Datos básicos** que aparece y, a continuación, haga clic en **Aceptar**.

        |Configuración|Valor|
        |---|---|
        |Nombre|myVm-Nva|
        |Nombre de usuario|azureuser|
        |Contraseña y Confirmar contraseña|Una contraseña de su elección|
        |La suscripción|Seleccione su suscripción.|
        |Grupos de recursos|Haga clic en **Usar existente** y, a continuación, seleccione **myResourceGroup**|
        |Ubicación|Este de EE. UU.|
    - En la hoja **Elegir un tamaño** que aparece, haga clic en **DS1_V2 Standard** y en **Seleccionar**.
    - En la hoja **Configuración** que aparece, haga clic en **Red virtual**. Haga clic en **myVnet** en la hoja **Elegir red virtual** que aparece.
    - En la hoja **Configuración**, haga clic en **Subred**. Haga clic en **DMZ**, en la hoja **Elegir subred** que aparece. 
    - Deje los valores predeterminados para las opciones de configuración restantes y haga clic en **Aceptar**.
    - Haga clic en **Crear**, en la hoja **Crear** que aparece. La implementación de la máquina virtual tarda algunos minutos.

    > [!NOTE]
    > Además de crear la máquina virtual, Azure Portal crea una dirección IP pública y la asigna a la máquina virtual, de forma predeterminada. Si implementara la máquina virtual en un entorno de producción, puede que decidiera no asignar una dirección IP pública a la máquina virtual. En su lugar, podría tener acceso a la aplicación virtual de red en otras máquinas virtuales de la red virtual. Para más información sobre las direcciones IP públicas, consulte [Administrar una dirección IP pública](virtual-network-public-ip-address.md).

7. Asigne una dirección IP privada estática y habilite el reenvío IP para la interfaz de red creada por el portal en el paso anterior. 
    - En el cuadro **Buscar recursos** en la parte superior de la página, escriba *myVm-Nva*.
    - Haga clic en **myVm-Nva** cuando aparezca en los resultados de la búsqueda.
    - Haga clic en **Redes** de **CONFIGURACIÓN**, a la izquierda.
    - Haga clic en el nombre de la interfaz de red, en **myVm-Nva - Interfaces de red**. El nombre es **myvm-nva***X*, donde *X* es un número asignado por el portal.
    - Haga clic en **Configuraciones de IP** de **CONFIGURACIÓN** de la interfaz de red, como se muestra en la siguiente imagen:

        ![Configuración de la interfaz de red](./media/create-user-defined-route/network-interface-settings.png)
        
    - Haga clic en **Habilitado** para la configuración de **reenvío IP** y, a continuación, haga clic en **Guardar**. El reenvío IP deberá habilitarse para cada interfaz de red que reciba tráfico no dirigido a una dirección IP asignada a este. Al habilitarse el reenvío IP, se deshabilita la comprobación de origen o destino de Azure de la interfaz de red.
    - Haga clic en **ipconfig1** en la lista de configuraciones de IP.
    - Haga clic en **Estático** para **Asignación** de la dirección IP privada de **ipconfig1**, como se muestra en la siguiente imagen:

        ![Configuración de IP](./media/create-user-defined-route/ip-configuration.png)
    - Como se muestra en la imagen anterior, escriba *10.0.2.4* en **Dirección IP** en la **Configuración de dirección IP privada**. La asignación de una dirección IP estática a la interfaz de red garantiza que la dirección no cambie durante la vida útil de la máquina virtual a la que se asocia la interfaz de red. La dirección escrita no se asigna actualmente a otro recurso de la subred DMZ donde está la interfaz de red. 
    - Para guardar la configuración, haga clic en **Guardar** de **ipconfig1**. No cierre el cuadro ipconfig1 hasta que reciba una notificación en el portal indicando que la interfaz de red está guardada.
 
8. Cree dos tablas de ruta. Las tablas de ruta contienen cero o más rutas:

    - En el portal, a la izquierda, haga clic en **+Nuevo** > **Redes** > **Tabla de ruta**.
    - Escriba los siguientes valores en **Crear una tabla de ruta** y, a continuación, haga clic en **Crear**:

        |Configuración|Valor|
        |---|---|
        |Nombre|myRouteTable-Public|
        |La suscripción|Seleccione su suscripción.|
        |Grupos de recursos|Seleccione **Usar existente** y, a continuación, **myResourceGroup**|
        |Ubicación|Este de EE. UU.|
    
    - Vuelva a completar los subpasos anteriores del paso 8, pero asigne el nombre *myRouteTable-Private* a la tabla de ruta.
9. Agregue rutas a la tabla de ruta *myRouteTable-Public* y asocie esta a la subred *Pública*:

    - En el cuadro **Buscar recursos** en la parte superior del portal, escriba *myRouteTable-Public*. Haga clic en **myRouteTable-Public** cuando aparezca en los resultados de la búsqueda.
    - En **myRouteTable-Public**, haga clic en **Rutas** en la lista de **CONFIGURACIÓN**.
    - Haga clic en **+ Agregar** en **myRouteTable-Public - Rutas**.
    -  De forma predeterminada, Azure enruta el tráfico entre todas las subredes de una red virtual. Cree una ruta para cambiar el enrutamiento predeterminado de Azure, de modo que el tráfico de la subred *Pública* se enrute a través de la NVA, en lugar de directamente a la subred *Privada*. Escriba los siguientes valores en la hoja **Agregar ruta** que aparece y, a continuación, haga clic en **Aceptar**:

        |Configuración|Valor|Explicación|
        |---|---|---|
        |Nombre de ruta|ToPrivateSubnet|Esta ruta dirige el tráfico a la subred Privada, a través de la aplicación virtual de red.|
        |Prefijo de dirección|10.0.1.0/24| Todo el tráfico enviado a cualquier dirección con este prefijo de dirección (10.0.1.0 - 10.0.1.254) se envía a la aplicación virtual de red.|
        |Tipo de próximo salto| Seleccione **Aplicación virtual**||
        |Siguiente dirección de salto|10.0.2.4| La dirección IP privada estática de la interfaz de red asociada a la aplicación virtual de red. El único tipo de salto para el que puede especificar una dirección es **Aplicación virtual**.|

    - En **myRouteTable-Public**, haga clic en **Subredes** de **Configuración**. 
    - Haga clic en **+ Asociar** en **myRouteTable-Public - Subredes**.
    - Haga clic en **Red virtual** en **Asociar subred** y, a continuación, haga clic en **myVnet**.
    - Haga clic en **Subred** en **Asociar subred** y, a continuación, haga clic en **Pública** en **Elegir subred**. 
    - Para guardar la configuración, haga clic en **Aceptar** en **Asociar subred**. Una subred puede tener una tabla de ruta asociada a ella o ninguna.
10. Vuelva a completar el paso 9, buscando **myRouteTable-Private**, creando una ruta con la siguiente configuración y, a continuación, asociando la tabla de ruta a la subred **Privada** de la red virtual **myVnet**:

    |Configuración|Valor|Explicación|
    |---|---|---|
    |Nombre de ruta|ToPublicSubnet|Esta ruta dirige el tráfico a la subred pública, a través de la aplicación virtual de red.|
    |Prefijo de dirección|10.0.0.0/24| Todo el tráfico enviado a cualquier dirección con este prefijo de dirección (10.0.0.0 - 10.0.1.254) se envía a la aplicación virtual de red.|
    |Tipo de próximo salto| Seleccione **Aplicación virtual**||
    |Siguiente dirección de salto|10.0.2.4||

    El tráfico de red entre los recursos de las subredes Privada y Pública fluye a través de la aplicación virtual de red. 
11. **Opcional:** la creación de una máquina virtual en las subredes Pública y Privada y la validación de esa comunicación entre las máquinas virtuales se enrutan a través de la aplicación virtual de red completando los pasos en [Validación de enrutamiento](#validate-routing). 
12. **Opcional**: elimine los recursos que cree en este tutorial. Para ello, complete los pasos que aparecen en la sección [Eliminar recursos](#delete-resources).

## <a name="validate-routing"></a>Validación de enrutamiento

1. Si aún no lo ha hecho, complete los pasos que se detallan en [Creación de rutas y aplicación virtual de red](#create-routes-and-network-virtual-appliance).
2. Haga clic en el botón **Probar** en el cuadro que aparece a continuación y que abre Azure Cloud Shell. Cuando se le solicite, inicie sesión en Azure con la [cuenta de Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si no tiene una cuenta de Azure, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell es un shell de Bash gratuito con la interfaz de la línea de comandos de Azure preinstalada. 

    Los siguientes scripts crean dos máquinas virtuales: una en la subred *pública* y la otra en la subred *privada*. Asimismo, los scripts también habilitan el reenvío de IP de la interfaz de red en el sistema operativo de la NVA para permitir a dicho sistema operativo enrutar el tráfico a través de la interfaz de red. Una NVA de producción suele inspeccionar el tráfico antes de enrutarlo. Sin embargo, en este tutorial, la sencilla NVA se limita a enrutar el tráfico sin inspeccionarlo. 

    Haga clic en el botón **Copiar** en los scripts de **Linux** o **Windows** que aparecen a continuación y pegue el contenido de los scripts en un editor de texto. Cambie la contraseña de la variable *adminPassword* y, a continuación, pegue el script en Azure Cloud Shell. Ejecute el script del sistema operativo seleccionado al crear la aplicación virtual de red en el paso 6 de [Creación de rutas y aplicación virtual de red](#create-routes-and-network-virtual-appliance). 

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
      > Los pasos anteriores le permiten confirmar el enrutamiento entre direcciones IP privadas de Azure. Si desea reenviar, o transmitir, el tráfico a direcciones IP públicas a través de un dispositivo virtual de red:
      > - El dispositivo debe proporcionar la traducción de direcciones de red o la funcionalidad del proxy. Si existe la traducción de direcciones de red, el dispositivo debe traducir la dirección IP de origen a la suya propia y, después, reenviar esa solicitud a la dirección IP pública. Si el dispositivo tiene una dirección de red traducida a la dirección de origen, o se está transmitiendo, Azure traduce la dirección IP privada del dispositivo virtual de red a una dirección IP pública. Para más información acerca de los distintos métodos que Azure usa para traducir direcciones IP privadas a direcciones IP públicas, consulte [Comprender las conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Es necesaria una ruta adicional en la tabla de rutas, como prefijo: 0.0.0.0/0, tipo del próximo salto VirtualAppliance y dirección IP del próximo salto 10.0.2.4 (en el script del ejemplo anterior).
      >
    - **Opcionalmente**: para validar el próximo salto entre dos máquinas virtuales de Azure, use la funcionalidad de próximo salto de Azure Network Watcher. Antes de usar Network Watcher, primero debe [crear una instancia de Azure Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para la región donde desea utilizarla. En este tutorial, se usa la región de Este de EE. UU. Una vez que haya habilitado una instancia de Network Watcher para la región, escriba el siguiente comando para ver la información del próximo salto entre las máquinas virtuales de las subredes pública y privada:
     
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

Cuando termine este tutorial, es posible que quiera eliminar los recursos que ha creado para no incurrir en gastos de uso. Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo. Con el portal abierto, complete los siguientes pasos:

1. En el cuadro de búsqueda del portal, escriba **myResourceGroup**. En los resultados de la búsqueda, haga clic en **myResourceGroup**.
2. En la hoja **myResourceGroup**, haga clic en el icono **Eliminar**.
3. Para confirmar la eliminación, en el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba **myResourceGroup** y, luego, haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- Cree una [aplicación virtual de red de alta disponibilidad](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Las aplicaciones virtuales de red suelen tener varias interfaces de red y direcciones IP asignadas a ellas. Obtenga más información sobre cómo [agregar interfaces de red a una máquina virtual existente](virtual-network-network-interface-vm.md#vm-add-nic) y [agregar direcciones IP a una interfaz de red existente](virtual-network-network-interface-addresses.md#add-ip-addresses). Aunque todos los tamaños de máquina virtual pueden tener al menos dos interfaces de red asociadas a ellos, cada tamaño de máquina virtual admite un número máximo de interfaces de red. Para obtener información sobre cuántas interfaces de red admite cada tamaño de máquina virtual, consulte los tamaños de máquina virtual de [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) y [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Cree una ruta definida por el usuario para forzar el tráfico de túnel local a través de una [conexión VPN de sitio a sitio](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
