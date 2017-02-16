---
title: "Varias direcciones IP para máquinas virtuales - PowerShell | Microsoft Docs"
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con PowerShell | Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 11e490ee3b2d2f216168e827154125807a61a73f
ms.openlocfilehash: 39b45b276c50922878e9918b225f6fa399d42edf


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Asignación de varias direcciones IP a máquinas virtuales mediante PowerShell

> [!div class="op_single_selector"]
> * [Portal](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Una máquina virtual (VM) de Azure tiene una o varias interfaces de red (NIC) asociadas a ella. Una NIC puede tener una o varias direcciones IP públicas o privadas estáticas o dinámicas asignadas. La asignación de varias direcciones IP a una VM permite las siguientes capacidades:

* Hospede varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor.
* Actúe como aplicación de red virtual, como un firewall o equilibrador de carga.
* La capacidad de agregar cualquier dirección IP privada para cualquiera de las NIC a un grupo de servidores de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end. Para más información sobre cómo equilibrar la carga de varias configuraciones de IP, lea el artículo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md) (Equilibrio de carga de varias configuraciones de IP).

Cada NIC conectada a una VM tiene una o varias configuraciones de IP asociadas. Se asigna a cada configuración una dirección IP privada estática o dinámica. Cada configuración también puede tener un recurso de dirección IP pública asociado. Un recurso de dirección IP pública tiene una dirección IP dinámica o estática asignada. Para más información sobre las direcciones IP en Azure, lea el artículo sobre [direcciones IP en Azure](virtual-network-ip-addresses-overview-arm.md).

En este artículo, se explica cómo usar PowerShell para asignar varias direcciones IP a una VM creada a través del modelo de implementación de Azure Resource Manager. No se pueden asignar varias direcciones IP a los recursos creados mediante el modelo de implementación clásica. Para información acerca de los modelos de implementación de Azure, lea el artículo [Understand deployment models](../resource-manager-deployment-model.md) (Descripción de los modelos de implementación).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Escenario
Se crea una máquina virtual con una sola NIC y se conecta a una red virtual. La máquina virtual requiere tres direcciones IP *privadas* y dos direcciones IP *públicas*, todas diferentes. Las direcciones IP se asignan a las siguientes configuraciones de IP:

* **IPConfig-1:** Asigna un dirección IP privada (valor predeterminado) *dinámica* y una dirección IP pública *estática*.
* **IPConfig-2:** asigna un dirección IP privada *estática* y una dirección IP pública *estática*.
* **IPConfig-3:** asigna un dirección IP privada *dinámica* y una dirección IP no pública.
  
    ![Varias direcciones IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Las configuraciones de IP se asocian a la NIC cuando esta se crea, y la NIC se conecta a la máquina virtual cuando esta se crea. Los tipos de direcciones IP usados para el escenario tienen fines ilustrativos. Puede asignar cualquier dirección IP y tipos de asignación que requiera.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creación de una máquina virtual con varias direcciones IP

En los pasos siguientes se explica cómo crear una VM de ejemplo con varias direcciones IP, tal como se describe en el escenario. Cambie los nombres de variable los y tipos de direcciones IP según sea necesario para la implementación.

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección en una única sesión de PowerShell. Si todavía no tiene PowerShell instalado y configurado, complete los pasos del artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) .
2. Regístrese para la versión preliminar enviando un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto. No complete los pasos restantes:
    - Hasta después de recibir una notificación por correo electrónico de que se le acepta en la versión preliminar.
    - Sin seguir las instrucciones del correo electrónico que reciba.
3. Complete los pasos 1-4 del artículo [Creación de una máquina virtual de Windows con Resource Manager y PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md). No complete el paso 5 (creación del recurso de dirección IP pública y la interfaz de red). Si cambia los nombres de las variables utilizadas en ese artículo, cambie también los nombres de las variables de los pasos restantes. Para crear una máquina virtual Linux, seleccione un sistema operativo Linux en lugar de Windows.
4. Cree una variable para almacenar el objeto de subred creado en el paso 4 (crear una red virtual) del artículo Creación de una máquina virtual de Windows con Resource Manager y PowerShell escribiendo el comando siguiente:

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
5. Defina las configuraciones de IP que desea asignar a la NIC. Puede agregar, quitar o cambiar las configuraciones según sea necesario. En el escenario se describen las siguientes configuraciones:

    **IPConfig-1**

    Escriba los comandos siguientes para crear:
    - Un recurso de dirección IP pública con una dirección IP pública estática
    - Una configuración de IP con el recurso de dirección IP pública y una dirección IP privada dinámica

    ```powershell
    $myPublicIp1     = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    Tenga en cuenta el modificador `-Primary` en el comando anterior. Cuando se asignan varias configuraciones de dirección IP a una NIC, se debe asignar una como la *principal*.

    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **IPConfig-2**

    Cambie el valor de la variable **$IPAddress** que sigue a una dirección disponible válida, disponible en la subred creada. Para comprobar si la dirección 10.0.0.5 está disponible en la subred, escriba el comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`. Si la dirección está disponible, la salida devuelve *True*. Si no está disponible, la salida devuelve *False* y una lista de direcciones que están disponibles. Escriba los comandos siguientes para crear un nuevo recurso de dirección IP pública y una nueva configuración de IP con una dirección IP pública estática y una dirección IP privada estática:
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = 10.0.0.5
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    Escriba los comandos siguientes para crear una configuración de IP con una dirección IP privada dinámica y ninguna dirección IP no pública:

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
6. Cree la NIC con las configuraciones de IP definidas en el paso anterior escribiendo el siguiente comando:

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > Aunque este artículo asigna todas las configuraciones de IP a una NIC única, también puede asignar varias configuraciones de IP a una NIC en una VM. Para aprender a crear una VM con varias NIC, lea el artículo [Implementación de máquinas virtuales con varias NIC mediante PowerShell](virtual-network-deploy-multinic-arm-ps.md).

7. Complete el paso 6 [Creación de una máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md) del artículo. 

    > [!WARNING]
    > El paso 6 Creación de una máquina virtual del artículo da error si:
    > - Cambió la variable denominada $myNIC a otra cosa en el paso 6 de este artículo.
    > - No se han completado los pasos anteriores de este artículo y del artículo Creación de una máquina virtual.
    >
8. Escriba el siguiente comando para ver las direcciones IP privadas y los recursos de dirección IP pública asignados a la NIC:

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. Agregue al sistema operativo de la máquina virtual las direcciones IP privadas siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo. No agregue las direcciones IP públicas al sistema operativo.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Incorporación de direcciones IP a una VM

Puede agregar direcciones IP públicas y privadas a una NIC completando los pasos siguientes. En los ejemplos de las secciones siguientes se da por sentado que ya tiene una máquina virtual con las tres configuraciones de IP descritas en el [escenario](#Scenario) de este artículo, pero no es necesario que lo haga.

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección en una única sesión de PowerShell. Si todavía no tiene PowerShell instalado y configurado, complete los pasos del artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) .
2. Regístrese para la versión preliminar enviando un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto. No complete los pasos restantes:
    - Hasta después de recibir una notificación por correo electrónico de que se le acepta en la versión preliminar.
    - Sin seguir las instrucciones del correo electrónico que reciba.
3. Cambie los "valores" de los elementos $Variables siguientes al nombre de la NIC a la que desee agregar una dirección IP y al grupo de recursos y la ubicación donde existe la NIC:

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    Si no conoce el nombre de la NIC que desea cambiar, escriba los siguientes comandos y cambie los valores de las anteriores variables:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
4. Cree una variable y establézcala en la NIC existente con el siguiente comando:

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
5. En los siguientes comandos, cambie *myVNet* y *mySubnet* a los nombres de la red virtual y la subred a las que está conectada la NIC. Escriba los comandos para recuperar los objetos de red virtual y subred a las que está conectada la NIC:

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    Si no conoce el nombre de red virtual o la subred de a la que está conectada la NIC, escriba el siguiente comando:
    ```powershell
    $mynic.IpConfigurations
    ```
    Busque texto similar al siguiente texto en la salida devuelta:

        Subnet   : {
                     "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"

    En esta salida, *myVnet* es la red virtual y *mySubnet* es la subred a la que está conectada la NIC.

6. Complete los pasos de una de las secciones siguientes, según sus requisitos:

    **Incorporación de una dirección IP privada**

    Para agregar una dirección IP privada a una NIC, debe crear una configuración de IP. El siguiente comando crea una configuración con una dirección IP estática de 10.0.0.7. Si desea agregar una dirección IP privada dinámica, quite `-PrivateIpAddress 10.0.0.7` antes de escribir el comando. Al especificar una dirección IP estática, debe ser una dirección no utilizada para la subred. Se recomienda que pruebe en primer lugar la dirección para asegurarse de que está disponible escribiendo el comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet`. Si la dirección IP está disponible, la salida devuelve *True*. Si no está disponible, la salida devuelve *False* y una lista de direcciones que están disponibles.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```

    Cree tantas configuraciones como sea necesario mediante nombres de configuración únicos y direcciones IP privadas (para las configuraciones con direcciones IP estáticas).

    Agregue al sistema operativo de la máquina virtual la dirección IP privada siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo.

    **Incorporación de una dirección IP pública**

    Se agrega una dirección IP pública mediante la asociación de un recurso de dirección IP pública a una nueva configuración de IP o una configuración de IP existente. Complete los pasos de una de las secciones siguientes, según sea preciso.

    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Asociación del recurso de dirección IP pública a una nueva configuración de IP**
    
    Siempre que agregue una dirección IP pública en una nueva configuración de IP, también debe agregar una dirección IP privada, porque todas las configuraciones de IP deben tener una dirección IP privada. Puede agregar un recurso de dirección IP pública existente o crear uno nuevo. Para crear uno nuevo, escriba el comando siguiente:
    
    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    Para crear una nueva configuración de IP con una dirección IP privada dinámica y el recurso de dirección IP pública *myPublicIp3*, escriba el comando siguiente:

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

    **Asociación del recurso de dirección IP pública a una configuración de IP existente**

    Solo se puede asociar un recurso de dirección IP pública a una configuración de IP que ya no tiene asociado uno. Puede determinar si una configuración de IP tiene una dirección IP pública asociada escribiendo el comando siguiente:

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

    Busque una línea similar a la que aparece a continuación en la salida devuelta:

        Name       PrivateIpAddress PublicIpAddress                                           Primary
        ----       ---------------- ---------------                                           -------
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False

    Puesto que la columna **PublicIpAddress** para *IpConfig-3* está en blanco, ningún recurso de dirección IP pública está asociado actualmente a ella. Puede agregar un recurso de dirección IP pública existente a IpConfig-3 o escribir el siguiente comando para crear uno:

    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    Escriba el siguiente comando para asociar el recurso de dirección IP pública a la configuración de IP existente llamada *IpConfig-3*:
    
    ```powershell
    Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

7. Establezca la NIC con la nueva configuración de IP escribiendo el siguiente comando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

8. Vea las direcciones IP privadas y los recursos de dirección IP pública asignados a la NIC escribiendo el siguiente comando:

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. Agregue al sistema operativo de la máquina virtual la dirección IP privada siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo. No agregue la dirección IP pública al sistema operativo.vo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]


<!--HONumber=Dec16_HO1-->


