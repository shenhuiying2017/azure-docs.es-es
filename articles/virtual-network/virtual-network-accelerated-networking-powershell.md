---
title: "Accelerated Networking para una máquina virtual | Microsoft Docs"
description: "Aprenda a configurar Accelerated Networking para una máquina virtual de Azure con PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a0b63599-c23b-40b5-a8ab-23af8b07dded
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 979ff03320063fc02366c738a10f028bcecba76b


---
# <a name="accelerated-networking-for-a-virtual-machine"></a>Accelerated Networking para una máquina virtual
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

Accelerated Networking habilita Virtualización de E/S de raíz única (SR-IOV) en una máquina virtual (VM), lo que mejora significativamente el rendimiento de la red. Esta ruta de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para su uso con las cargas de trabajo de red más exigentes en tipos de máquina virtual compatibles. En este artículo se explica cómo usar Azure PowerShell para configurar Accelerated Networking en el modelo de implementación de Azure Resource Manager. También puede crear una máquina virtual con Accelerated Networking con Azure Portal. Para información, haga clic en la casilla Azure Portal en la parte superior de este artículo.

En la siguiente imagen, se muestra la comunicación entre dos máquinas virtuales (VM) con y sin Accelerated Networking:

![De comparación](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sin Accelerated Networking, todo el tráfico de red dentro y fuera de la máquina virtual debe atravesar el host y el conmutador virtual. El conmutador virtual se encarga de toda la aplicación de directivas, como grupos de seguridad de red, listas de control de acceso, aislamiento y otros servicios virtualizados de red, al tráfico de red. Para más información, lea el artículo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualización de red de Hyper-V y conmutador virtual).

Con Accelerated Networking, el tráfico de red llega a la tarjeta de red (NIC) y se reenvía después a la máquina virtual. Todas las directivas de red que el conmutador virtual aplica sin Accelerated Networking se descargan y aplican en el hardware. La aplicación de directivas en hardware permite que la NIC reenvíe el tráfico de red directamente a la máquina virtual, pasando por alto el host y el conmutador virtual, al mismo tiempo que se mantienen todas las directivas aplicadas en el host.

Las ventajas de Accelerated Networking solo se aplican a la máquina virtual donde esté habilitada. Para obtener resultados óptimos, lo ideal es habilitar esta característica en al menos dos máquinas virtuales conectadas a la misma red virtual.  Al comunicarse entre redes virtuales o conectarse de forma local, esta característica tiene un efecto mínimo sobre la latencia total.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Ventajas
* **Menor latencia/Más paquetes por segundo (pps):** al quitarse el conmutador virtual de la ruta de acceso de datos, se elimina el tiempo que los paquetes pasan en el host para el procesamiento de las directivas y se aumenta el número de paquetes que se pueden procesar dentro de la máquina virtual.
* **Inestabilidad reducida:** el procesamiento del conmutador virtual depende de la cantidad de directivas que deben aplicarse y la carga de trabajo de la CPU que se encarga del procesamiento. Al descargarse la aplicación de directivas en el hardware, se elimina esa variabilidad, ya que los paquetes se entregan directamente a la máquina virtual y se elimina el host de la comunicación de la máquina virtual, así como todas las interrupciones de software y los cambios de contexto.
* **Disminución de la utilización de la CPU:** el pasar por alto el conmutador virtual en el host conlleva una disminución de la utilización de la CPU para procesar el tráfico de red.

## <a name="limitations"></a>Limitaciones
Cuando se utiliza esta funcionalidad, existen las siguientes limitaciones:

* **Creación de interfaz de red:** Accelerated Networking solo se puede habilitar para una nueva interfaz de red.  No es posible para una interfaz de red existente.
* **Creación de máquina virtual:** una interfaz de red con Accelerated Networking habilitada solo se puede asociar a una máquina virtual cuando esta se crea. La interfaz de red no puede asociarse a una máquina virtual existente.
* **Regiones:** solo se ofrece en las regiones Centro occidental de EE. UU. y Europa Occidental de Azure. El conjunto de regiones se expandirá en el futuro.
* **Sistema operativo compatible:** Microsoft Windows Server 2012 R2 y Windows Server 2016 Technical Preview 5. Próximamente se agregará compatibilidad con Linux y Windows Server 2012.
* **Tamaño de máquina virtual:** Standard_D15_v2 y Standard_DS15_v2 son los únicos tamaños de instancia de máquina virtual admitidos. Para más información, consulte el artículo sobre los [tamaños de máquina virtual Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . El conjunto de tamaños de instancia de máquina virtual compatibles se ampliará en el futuro.

Los cambios en estas limitaciones se anunciarán a través de la página con [actualizaciones para redes virtuales de Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Creación de una máquina virtual Windows con Accelerated Networking
1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección en una única sesión de PowerShell. Si todavía no tiene PowerShell instalado y configurado, complete los pasos del artículo [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) .
2. Para registrarse para la versión preliminar, envíe un correo electrónico a [Accelerated Networking Subscriptions](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto. No complete los pasos restantes hasta después de recibir una notificación por correo electrónico de que se le acepta en la versión preliminar.
3. Registre la funcionalidad con su suscripción mediante los siguientes comandos:
   
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
4. Reemplace *westcentralus* por el nombre de otra ubicación compatible con esta funcionalidad que aparezca en la sección [Limitaciones](#limitations) este artículo (si lo desea). Escriba el siguiente comando para establecer una variable para la ubicación:
   
        $locName = "westcentralus"
5. Reemplace *RG1* por un nombre para el grupo de recursos que contendrá la nueva interfaz de red y escriba los siguientes comandos para crearla:
   
        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
6. Cambie *VM1-NIC1* al nombre que desee para la interfaz de red y escriba el siguiente comando:
   
        $NICName = "VM1-NIC1"
7. La interfaz de red debe estar conectada a una subred dentro de una instancia de Azure Virtual Network (VNet) existente en la misma ubicación y [suscripción](../azure-glossary-cloud-terminology.md#subscription) que la interfaz de red. Obtenga más información sobre las redes virtuales en el artículo [Información general sobre redes virtuales](virtual-networks-overview.md) si no está familiarizado con ellas. Para crear una red virtual, complete los pasos del artículo sobre cómo [crear una red virtual](virtual-networks-create-vnet-arm-ps.md) . Cambie los "valores" de los siguientes elementos $Variables al nombre de la red virtual y la subred a las que desee conectar la interfaz de red.
   
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
   
    Si no conoce el nombre de una red virtual existente en la ubicación que eligió en el paso 3, escriba los siguientes comandos:
   
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
   
    Si la lista que se devuelve está vacía, debe crear una red virtual en la ubicación. Para crear una red virtual, complete los pasos del artículo sobre cómo [crear una red virtual](virtual-networks-create-vnet-arm-ps.md) .
   
    Para obtener el nombre de las subredes de la red virtual, escriba los comandos siguientes y reemplace *Subnet1* arriba por el nombre de una subred:
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
8. Escriba los comandos siguientes para recuperar la red virtual y la subred, y asignarlas a variables.
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
9. Identifique un recurso de dirección IP pública existente que pueda asociarse a la interfaz de red para que pueda conectarse a él a través de Internet. Si no desea acceder a la máquina virtual con la interfaz de red a través de Internet, puede omitir este paso. Sin una dirección IP pública, debe conectarse a la máquina virtual desde otra máquina virtual conectada a la misma red virtual. 
   
    Cambie *PIP1* al nombre de un recurso de dirección IP pública existente que se encuentre en la ubicación donde esté creando la interfaz de red y que no esté asociado actualmente a otra interfaz de red. Si es necesario, cambie $rgName al nombre del grupo de recursos donde se encuentra el recurso de dirección IP pública y escriba el siguiente comando:
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName
   
    Escriba los siguientes comandos si desconoce el nombre de un recurso de dirección IP pública existente:
   
        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration
   
    Si la columna **IPConfiguration** no muestra ningún valor en la salida devuelta, el recurso de dirección IP pública no está asociado con una interfaz de red existente y se puede usar. Si la lista está vacía o no hay ningún recurso de dirección IP pública disponible, puede crear uno con el comando New-AzureRmPublicIPAddress.
   
   > [!NOTE]
   > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
   > 
   > 
10. Si decide no agregar un recurso de dirección IP pública a la interfaz, quite *-PublicIPAddress $PIP1* del final del comando que sigue. Cree la interfaz de red con Accelerated Networking mediante el siguiente comando:
    
        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 
11. Asigne la interfaz de red a una máquina virtual al crear está siguiendo las instrucciones de los pasos 3 y 6 del artículo sobre cómo [crear una máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . En el paso 6-2, reemplace *Standard_A1* por uno de los tamaños de máquina virtual que aparecen en la sección [Limitaciones](#limitations) de este artículo.
    
    > [!NOTE]
    > Si cambió el *nombre* de las variables $locName, $rgName o $nic en este artículo, el paso 6 del artículo sobre cómo crear una máquina virtual producirá un error. Sin embargo, sí puede cambiar los *valores* de las variables.
    > 
    > 
12. Una vez creada la máquina virtual, descargue el [controlador de Accelerated Networking](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conéctese a la máquina virtual y ejecute el instalador del controlador dentro de la máquina virtual.
13. Haga clic con el botón derecho en el botón Windows y haga clic en **Administrador de dispositivos**. Compruebe que el **adaptador Ethernet de función virtual ConnectX-3 de Mellanox** aparezca bajo la opción **Red** expandida, como se muestra en la siguiente imagen:
    
    ![Administrador de dispositivos](./media/virtual-network-accelerated-networking-powershell/image2.png)




<!--HONumber=Dec16_HO1-->


