---
title: 'Conexión de redes virtuales con emparejamiento de redes virtuales: Azure Portal | Microsoft Docs'
description: Aprenda a conectar redes virtuales con el emparejamiento de redes virtuales.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Conexión de redes virtuales con emparejamiento de redes virtuales usando Azure Portal

Puede conectar redes virtuales entre sí con el emparejamiento de redes virtuales. Una vez que las redes virtuales están emparejadas, los recursos de ambas se pueden comunicar entre sí con el mismo ancho de banda y la misma latencia que si estuvieran en la misma red virtual. En este artículo se describe la creación y el emparejamiento de dos redes virtuales. Aprenderá a:

> [!div class="checklist"]
> * Crear dos redes virtuales
> * Crear un emparejamiento entre redes virtuales
> * Realizar un emparejamiento de prueba

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-virtual-networks"></a>Creación de redes virtuales

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. Tal como se muestra en la siguiente imagen, escriba *myVirtualNetwork1* en el campo **Nombre**, *10.0.0.0/16* para **Espacio de direcciones**, **myResourceGroup** en **Grupo de recursos**, *Subnet1* en el **Nombre** de la subred, 10.0.0.0/24 en el **Intervalo de direcciones** de la subred, seleccione un valor en **Ubicación** y **Suscripción**, acepte los valores predeterminados restantes y luego seleccione **Crear**:

    ![Crear una red virtual](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Complete de nuevo los pasos del 1 al 3, con los cambios siguientes:
    - **Nombre**: *myVirtualNetwork2*
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione **myResourceGroup**.
    - **Espacio de direcciones**: *10.1.0.0/16*
    - **Intervalo de direcciones de subred**: *10.1.0.0/24*

    El prefijo de dirección para la red virtual *myVirtualNetwork2* no se superpone con el espacio de direcciones de la red virtual *myVirtualNetwork1*. No se pueden emparejar redes virtuales con espacios de direcciones superpuestos.

## <a name="peer-virtual-networks"></a>Emparejamiento de redes virtuales

1. En el cuadro Búsqueda que se encuentra en la parte superior de Azure Portal, escriba *MyVirtualNetwork1*. Cuando la opción **myVirtualNetwork1** aparezca en los resultados de la búsqueda, selecciónela.
2. En **CONFIGURACIÓN**, seleccione **Emparejamientos** y, luego, seleccione **+Agregar**, como se muestra en la imagen siguiente:

    ![Creación de emparejamiento](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Escriba o seleccione la información que se muestra en la siguiente imagen, a continuación seleccione **Aceptar**. Para seleccionar la red virtual *myVirtualNetwork2*, seleccione **Red virtual**, a continuación, seleccione *myVirtualNetwork2*.

    ![Configuración de emparejamiento](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    El **ESTADO DE EMPAREJAMIENTO** es *Iniciado*, tal y como se muestra en la siguiente imagen:

    ![Estado de emparejamiento](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Si no ve el estado, actualice el explorador.

4. Busque la red virtual *myVirtualNetwork2*. Cuando la encuentre en los resultados de búsqueda, selecciónela.
5. Complete de nuevo los pasos del 1 al 3, con los cambios siguientes y luego seleccione **Aceptar**:
    - **Nombre**: *myVirtualNetwork2-myVirtualNetwork1*
    - **Red virtual**: *myVirtualNetwork1*

    El **ESTADO DE EMPAREJAMIENTO** es *Conectado*. Azure también cambia el estado de emparejamiento del emparejamiento *myVirtualNetwork2 myVirtualNetwork1* de *Iniciado* a *Conectado.* El emparejamiento de red virtual no se habrá establecido correctamente hasta que el estado de emparejamiento de ambas redes virtuales no sea *Conectado.* 

Los emparejamientos se realizan entre dos redes virtuales, pero no son transitivos. Así, por ejemplo, si también desea emparejar *myVirtualNetwork2* con *myVirtualNetwork3*, tendrá que crear un emparejamiento adicional entre las redes virtuales *myVirtualNetwork2* y *myVirtualNetwork3*. Aunque *myVirtualNetwork1* esté emparejada con *myVirtualNetwork2*, los recursos de *myVirtualNetwork1* solo pueden acceder a los recursos de  *myVirtualNetwork3* si *myVirtualNetwork1* también se empareja con *myVirtualNetwork3*. 

Antes de emparejar redes virtuales de producción, se recomienda familiarizarse en detalle con la [introducción al emparejamiento](virtual-network-peering-overview.md), la [administración del emparejamiento](virtual-network-manage-peering.md) y los [límites de red virtual ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Aunque en este artículo se explica un emparejamiento entre dos redes virtuales en la misma suscripción y ubicación, también puede emparejar redes virtuales en [diferentes regiones](#register) y [diferentes suscripciones de Azure](create-peering-different-subscriptions.md#portal). También puede crear [diseños de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con emparejamiento.

## <a name="test-peering"></a>Realizar un emparejamiento de prueba

Para probar la comunicación de red entre las máquinas virtuales en diferentes redes virtuales mediante un emparejamiento, implemente una máquina virtual en cada subred y establezca la comunicación entre ellas. 

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree una máquina virtual en cada red virtual para poder validar la comunicación entre ellas posteriormente.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. Puede seleccionar otro sistema operativo, pero en los pasos restantes se supone que seleccionó **Windows Server 2016 Datacenter**. 
3. Seleccione o escriba la información siguiente para **Aspectos básicos** y luego seleccione **Aceptar**:
    - **Nombre**: *myVm1*
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione *myResourceGroup*.
    - **Ubicación**: seleccione *Este de EE. UU*.

    El **nombre de usuario** y la **contraseña** que escriba se usarán en un paso posterior. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). La **ubicación** y la **suscripción** que se seleccionen deben ser la misma ubicación y suscripción en que se encuentra la red virtual. Aunque en este artículo se selecciona el mismo grupo de recursos en el que se creó la red virtual, no es necesario seleccionar el mismo grupo.
4. Seleccione un tamaño de máquina virtual en **Elegir un tamaño**.
5. Seleccione o escriba la información siguiente para **Configuración** y luego seleccione **Aceptar**:
    - **Red virtual**: asegúrese de que esté seleccionada la opción **myVirtualNetwork1**. Si no es así, seleccione **Red virtual** y, luego, seleccione **myVirtualNetwork1** en **Elegir red virtual**.
    - **Subred**: asegúrese de que esté seleccionada la opción **Subnet1**. Si no es así, seleccione **Subred** y, luego, seleccione **Subnet1** en **Elegir subred**, tal como se muestra en la imagen siguiente:
    
        ![Configuración de máquina virtual](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual.
7. Vuelva a completar los pasos del 1 al 6, pero escriba *myVm2* en el campo **Nombre** de la máquina virtual y seleccione *myVirtualNetwork2* en la **Red virtual**.

Azure ha asignado *10.0.0.4* como la dirección IP privada de la máquina virtual *myVm1* y 10.1.0.4 a la máquina virtual *myVm2*, porque eran las primeras direcciones IP disponibles en *Subnet1* de las redes virtuales *myVirtualNetwork1* y *myVirtualNetwork2*, respectivamente.

La creación de las máquinas virtuales tarda algunos minutos. No siga con los pasos restantes hasta que se creen ambas máquinas virtuales.

### <a name="test-virtual-machine-communication"></a>Prueba de comunicación de la máquina virtual

1. En el cuadro *Búsqueda* que se encuentra en la parte superior del portal, escriba *myVm1*. Seleccione **MyVm1** cuando aparezca en los resultados de búsqueda.
2. Cree una conexión a Escritorio remoto a la máquina virtual *myVm1* seleccionando **Conectar**, tal como se indica en la imagen siguiente:

    ![Conexión a la máquina virtual](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.
4. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**.
5. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión.
6. En un paso posterior, se usa ping para comunicarse con la máquina virtual *myVm2* desde la máquina virtual *myVmWeb*. Ping usa ICMP, que, de forma predeterminada, se deniega a través del Firewall de Windows. Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde un símbolo del sistema:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Aunque en este artículo se usa ping para hacer una prueba, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones de producción.

7. Para conectar con la máquina virtual *myVm2*, escriba el siguiente comando desde el símbolo del sistema en la máquina virtual *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Puesto que ha habilitado ping en *myVm1*, ahora puede hacer ping a él por dirección IP:

    ```
    ping 10.0.0.4
    ```
    
    Recibirá cuatro respuestas. Si hace ping por el nombre de la máquina virtual (*myVm1*), en lugar de su dirección IP, el ping falla, porque *myVm1* es un nombre de host desconocido. La resolución de nombres predeterminada de Azure funciona entre máquinas virtuales de la misma red virtual, pero no entre máquinas virtuales de diferentes redes virtuales. Para resolver nombres en varias redes virtuales, tiene que [implementar su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) o usar [dominios privados de Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Desconecte las sesiones RDP para ambos *myVm1* y *myVm2*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene: 

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

**<a name="register"></a>Registro para la versión preliminar del emparejamiento de red virtual global**

El emparejamiento de redes virtuales en las mismas regiones tiene disponibilidad general. El emparejamiento de redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. Consulte las [actualizaciones de redes virtuales](https://azure.microsoft.com/updates/?product=virtual-network) para ver las regiones disponibles. Para emparejar redes virtuales en diferentes regiones, primero tiene que registrarse para la versión preliminar. No se puede registrar con el portal, pero puede registrar usando [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) o [CLI de Azure](tutorial-connect-virtual-networks-cli.md#register). Si intenta emparejar redes virtuales en diferentes regiones antes de registrarse para la funcionalidad, el emparejamiento produce un error.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a conectar dos redes con el emparejamiento de redes virtuales.

Continúe para conectar su equipo a una red virtual mediante VPN e interactuar con recursos en una red virtual, o en redes virtuales emparejadas.

> [!div class="nextstepaction"]
> [Conexión de un equipo a una red virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
