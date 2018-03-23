---
title: 'Creación de una instancia de Azure Virtual Network con varias subredes: portal | Microsoft Docs'
description: Obtenga información sobre cómo crear una red virtual con varias subredes mediante Azure Portal.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Creación de una red virtual con varias subredes mediante Azure Portal

Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen en Internet y entre ellos de forma privada. La creación de varias subredes en una red virtual permite segmentar la red para filtrar o controlar el flujo de tráfico entre las subredes. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual
> * Creación de una subred
> * Probar la comunicación de red entre máquinas virtuales

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-virtual-network"></a>Crear una red virtual

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. Tal como se muestra en la siguiente imagen, escriba *myVirtualNetwork* en el campo **Nombre**, *10.0.0.0/16* en **Espacio de direcciones**, **myResourceGroup** en **Grupo de recursos**, *Public* en el **Nombre de la subred**, 10.0.0.0/24 en el **Intervalo de direcciones** de la subred, seleccione un valor en **Ubicación** y **Suscripción**, acepte los valores predeterminados restantes y luego seleccione **Crear**:

    ![Crear una red virtual](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    El **Espacio de direcciones** e **Intervalo de direcciones** se especifican en la notación CIDR. El **Espacio de direcciones** especificado incluye las direcciones IP 10.0.0.0 a 10.0.255.254. El **Intervalo de direcciones** especificado para una subred debe estar dentro del **espacio de direcciones** definido para la red virtual. Azure DHCP asigna direcciones IP de un intervalo de direcciones de subred a los recursos implementados en una subred. Azure solo asigna las direcciones 10.0.0.4-10.0.0.254 a los recursos implementados en la subred **Public**, porque Azure reserva las cuatro primeras direcciones (10.0.0.0-10.0.0.3 para la subred, en este ejemplo) y la última dirección (10.0.0.255 para la subred, en este ejemplo) en cada subred.

## <a name="create-a-subnet"></a>Creación de una subred

1. En el cuadro **Search resources, services, and docs** (Buscar recursos, servicios y documentos) en la parte superior del portal, comience a escribir *myVirtualNetwork*. Cuando aparezca la opción **myVirtualNetwork** en los resultados de la búsqueda, selecciónela.
2. Seleccione **Subredes** y, luego, seleccione **+ Subred**, tal como se muestra en la imagen siguiente:

     ![Incorporación de una subred](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. En el cuadro **Agregar subred** que aparece, escriba *Privado* en el **Nombre**, escriba *10.0.1.0/24* en el **Intervalo de direcciones** y seleccione **Aceptar**.  Un intervalo de direcciones de subred no puede superponerse con los intervalos de direcciones de otras subredes dentro de una red virtual. 

Antes de implementar las redes virtuales de Azure y las subredes para usarlas en producción, es aconsejable que se familiarice con las [consideraciones ](manage-virtual-network.md#create-a-virtual-network) del espacio de direcciones y los [límites de red virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Una vez implementados los recursos en las subredes, puede que algunos cambios en la red virtual y la subred, como el de los intervalos de direcciones, requieran la reimplementación de los recursos de Azure en las subredes.

## <a name="test-network-communication"></a>Prueba de la comunicación de red

Una red virtual permite que varios tipos de recursos de Azure se comuniquen en Internet y entre ellos de forma privada. Un tipo de recurso que se puede implementar en una red virtual es una máquina virtual. Cree dos máquinas virtuales en la red virtual de modo que pueda probar la comunicación de red entre ellas e Internet en un paso posterior.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. Puede seleccionar otro sistema operativo, pero en los pasos restantes se supone que seleccionó **Windows Server 2016 Datacenter**. 
3. Seleccione o escriba la información siguiente para **Aspectos básicos** y luego seleccione **Aceptar**:
    - **Nombre**: *myVmWeb*
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione *myResourceGroup*.
    - **Ubicación**: seleccione *Este de EE. UU*.

    El **nombre de usuario** y la **contraseña** que escriba se usarán en un paso posterior. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). La **ubicación** y la **suscripción** que se seleccionen deben ser la misma ubicación y suscripción en que se encuentra la red virtual. No es necesario que seleccione el mismo grupo de recursos en que se creó la red virtual pero el mismo grupo de recursos se selecciona para este tutorial.
4. Seleccione un tamaño de máquina virtual en **Elegir un tamaño**.
5. Seleccione o escriba la información siguiente para **Configuración** y luego seleccione **Aceptar**:
    - **Red virtual**: asegúrese de que esté seleccionada la opción **myVirtualNetwork**. Si no es así, seleccione **Red virtual** y, luego, seleccione **myVirtualNetwork** en **Elegir red virtual**.
    - **Subred**: asegúrese de que esté seleccionada la opción **Pública**. Si no es así, seleccione **Subred** y, luego, seleccione **Pública** en **Elegir subred**, tal como se muestra en la imagen siguiente:
    
        ![Configuración de máquina virtual](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual.
7. Vuelva a completar los pasos del 1 al 6, pero escriba *myVmMgmt* en el campo **Nombre** de la máquina virtual y seleccione **Privada** en la **Subred**.

La creación de las máquinas virtuales tarda algunos minutos. No siga con los pasos restantes hasta que se creen ambas máquinas virtuales.

Las máquinas virtuales creadas en este artículo tiene una [interfaz de red](virtual-network-network-interface.md) con una dirección IP que se le asigna dinámicamente. Después de implementar la máquina virtual, puede [agregar varias direcciones IP públicas y privadas o cambiar el método de asignación de direcciones IP a estático](virtual-network-network-interface-addresses.md#add-ip-addresses). También puede [agregar interfaces de red](virtual-network-network-interface-vm.md#vm-add-nic), hasta el límite admitido por el [tamaño de máquina virtual](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seleccionó al crear una máquina virtual. Además, puede [habilitar la virtualización de E/S de raíz única (SR-IOV)](create-vm-accelerated-networking-powershell.md) en una máquina virtual, pero solo al crear una máquina virtual con un tamaño que admita la funcionalidad.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicación entre máquinas virtuales y con Internet

1. En el cuadro *Búsqueda* que se encuentra en la parte superior del portal, comience a escribir *myVmMgmt*. Seleccione **myVmMgmt** cuando aparezca en los resultados de la búsqueda.
2. Cree una conexión a Escritorio remoto a la máquina virtual *myVmMgmt* mediante la selección de **Conectar**, tal como se indica en la imagen siguiente:

    ![Conexión a la máquina virtual](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.
4. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**.
5. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión.
6. En un paso posterior, se usa ping para comunicarse con la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*. Ping usa ICMP, que, de forma predeterminada, se deniega a través del Firewall de Windows. Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde un símbolo del sistema:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Aunque en este artículo se usa ping, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones de producción.
7. Por motivos de seguridad, es habitual limitar el número de máquinas virtuales a las que se pueden conectar de forma remota en una red virtual. En este tutorial, la máquina virtual *myVmMgmt* se usa para administrar la máquina virtual *myVmWeb* en la red virtual. Para establecer una conexión a Escritorio remoto a la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt*, escriba el comando siguiente en un símbolo del sistema:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Para comunicarse con la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*, escriba el siguiente comando desde un símbolo del sistema:

    ```
    ping myvmmgmt
    ```

    Recibirá una salida similar al ejemplo siguiente:
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Puede ver que la dirección de la máquina virtual *myVmMgmt* es 10.0.1.4. 10.0.1.4 era la primera dirección IP disponible en el intervalo de direcciones de la subred *Private* que implementó la máquina virtual *myVmMgmt* en un paso anterior.  Verá que el nombre de dominio completo de la máquina virtual es *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Aunque la parte *dar5p44cif3ulfq00wxznl3i3f* del nombre de dominio es diferente en su máquina virtual, el resto es igual. 

    De forma predeterminada, todas las máquinas virtuales de Azure usan el servicio Azure DNS predeterminado. Todas las máquinas virtuales de una red virtual pueden resolver los nombres de las demás máquinas virtuales de la misma red virtual mediante el servicio Azure DNS predeterminado. En lugar de usar el servicio DNS predeterminado de Azure, puede usar su propio servidor DNS o la funcionalidad de dominio privado del servicio Azure DNS. Para más información, consulte [Resolución de nombre mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) o [Uso de Azure DNS para dominios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Para instalar Internet Information Services (IIS) para Windows Server en la máquina virtual *myVmWeb*, escriba el siguiente comando desde una sesión de PowerShell:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. Una vez completada la instalación de IIS, desconecte la sesión de Escritorio remoto de *myVmWeb*, lo cual le deja en la sesión de Escritorio remoto de *myVmMgmt*. Abra un explorador web y vaya a http://myvmweb. Verá la página principal de IIS.
11. Desconecte la sesión de Escritorio remoto de *myVmMgmt*.
12. Busque la dirección IP pública de la máquina virtual *myVmWeb*. Cuando Azure creó la máquina virtual *myVmWeb*, también se creó un recurso de dirección IP pública con el nombre *myVmWeb* y se asignó a la máquina virtual. Puede ver que 52.170.5.92 se asignó para **Dirección IP pública** a la máquina virtual *myVmMgmt* en la imagen del paso 2. Para encontrar la dirección IP pública asignada a la máquina virtual *myVmWeb*, busque *myVmWeb* en el cuadro de búsqueda del portal y, luego, selecciónelo cuando aparezca en los resultados de la búsqueda.

    Aunque no es necesario que una máquina virtual tenga asignada una dirección IP pública, de manera predeterminada, Azure asigna una a cada máquina virtual que cree. Para comunicarse con una máquina virtual desde Internet, se debe asignar una dirección IP pública a la máquina virtual. Todas las máquinas virtuales pueden establecer una comunicación saliente con Internet, tanto si se les asigna una dirección IP pública como si no. Para más información sobre las conexiones a Internet salientes en Azure, consulte [Conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. En su propio equipo, vaya a la dirección IP pública de la máquina virtual *myVmWeb*. Se produce un error al intentar ver la página de bienvenida de IIS desde su propio equipo. El motivo es que cuando se implementaron las máquinas virtuales, Azure creó un grupo de seguridad de red para cada máquina virtual, de forma predeterminada. 

     Un grupo de seguridad de red contiene reglas de seguridad que permiten o deniegan el tráfico de red entrante y saliente por dirección IP y puerto. El grupo de seguridad de red predeterminado que creó Azure permite la comunicación a través de todos los puertos entre los recursos de la misma red virtual. En las máquinas virtuales Windows, el grupo de seguridad de red predeterminado deniega todo el tráfico entrante desde Internet a través de todos los puertos, excepto el puerto TCP 3389 (RDP). Como resultado, de forma predeterminada, también puede conectarse directamente a la máquina virtual *myVmWeb* mediante RDP desde Internet, aunque puede que no quiera que el puerto 3389 se abra a un servidor web. Dado que la comunicación en la exploración web tiene lugar en el puerto 80, se produce un error en la comunicación desde Internet porque no hay ninguna regla en el grupo de seguridad de red predeterminado que permita el tráfico en ese puerto.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene: 

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a implementar una red virtual con varias subredes. También aprendió que, al crear una máquina virtual Windows, Azure crea una interfaz de red que se asocia a la máquina virtual, y crea un grupo de seguridad de red que solo permite el tráfico en el puerto 3389, desde Internet. Avance al siguiente tutorial para aprender a filtrar el tráfico de red que va a las subredes, en lugar de a máquinas virtuales individuales.

> [!div class="nextstepaction"]
> [Filtrado del tráfico de red que va a las subredes](./virtual-networks-create-nsg-arm-pportal.md)
