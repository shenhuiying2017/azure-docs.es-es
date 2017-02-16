---
title: "Varias direcciones IP para máquinas virtuales - Portal | Microsoft Docs"
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con Azure Portal."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 716046d5e23de015da42f25f8a1f674de228efac
ms.openlocfilehash: a04192ffde57ae38d901a78a74724a89f43caedb


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Asignación de varias direcciones IP a máquinas virtuales
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)


Una máquina virtual (VM) de Azure puede tener una o varias interfaces de red (NIC) asociadas a ella. Cualquier NIC puede tener una o varias direcciones IP públicas o privadas asignadas. Si no está familiarizado con las direcciones IP en Azure, lea el artículo [Direcciones IP en Azure](virtual-network-ip-addresses-overview-arm.md) para aprender más sobre ellas. En este artículo, se explica cómo usar Azure Portal para asignar varias direcciones IP a una VM en el modelo de implementación de Azure Resource Manager.

La asignación de varias direcciones IP a una VM permite las siguientes capacidades:

* Hospede varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor.
* Actúe como aplicación de red virtual, como un firewall o equilibrador de carga.
* La capacidad de agregar cualquier dirección IP privada para cualquiera de las NIC a un grupo de servidores de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrarse para la versión preliminar, envíe un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto.

## <a name="scenario"></a>Escenario
En este artículo, asociará 3 configuraciones IP con una interfaz de red.
Las siguientes configuraciones de ejemplo se crearán y asignarán a una NIC que tendrá 3 direcciones IP privadas y una pública asignadas:

* IPConfig-1: una dirección IP privada dinámica (predeterminada) y una dirección IP pública del recurso de dirección IP pública llamado PIP1.
* IPConfig-2: una dirección IP privada estática y ninguna dirección IP pública.
* IPConfig-3: una dirección IP privada dinámica y ninguna dirección IP pública.

    ![Texto alternativo de imagen](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Este escenario supone que tiene un grupo de recursos llamado *RG1*, dentro del cual se encuentra una red virtual denominada *VNet1* y una subred llamada *Subnet1*. Además, supone que tiene una máquina virtual llamada *VM1*, una interfaz de red llamada *VM1-NIC1* asociada a ella y una dirección IP pública denominada *PIP1*.

[Este artículo](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lo guiará en el proceso de creación de los recursos mencionados anteriormente en caso de que todavía no los haya creado.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creación de una máquina virtual con varias direcciones IP
Para crear configuraciones de varias direcciones IP basadas en el escenario anterior por medio del portal de vista previa de Azure, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Seleccione la interfaz de red a la que desea agregar las configuraciones de IP: **Máquinas virtuales** > **VM1** > **Interfaces de red** > **VM1 NIC1**
3. En la hoja **Interfaz de red** hoja, seleccione **Configuraciones de IP**. Verá una lista de las configuraciones de IP existentes.
4. Para asociar **PIP1** a **ipconfig1**, en la hoja **Configuraciones de IP**, seleccione **ipconfig1**. En la hoja **ipconfig1**, en **Dirección IP pública**, seleccione **Habilitada**.
5. En la pestaña **Dirección IP**, seleccione **Configurar valores obligatorios** y luego seleccione **PIP1** o la dirección IP pública que desea asociar a esta configuración IP primaria y después haga clic en Guardar. También puede crear una nueva dirección IP pública para asociar aquí. A continuación verá la notificación Guardando interfaz de red que, una vez finalizada, verá **PIP1** asociada a **ipconfig1**.

    ![Texto alternativo de imagen](media\\virtual-network-multiple-ip-addresses-portal\\01-portal.PNG)

    >[!NOTE] 
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

1. Después, para agregar una configuración de IP, en la sección **Configuraciones de IP** de la interfaz de red, haga clic en **+Agregar**.

   > [!NOTE]
   > Puede asignar hasta 250 direcciones IP privadas a una NIC. Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager) .
   >
   >
2. En la hoja **Agregar configuración IP**, asigne un nombre a la configuración de IP. Aquí es **IPConfig 2**. Seleccione **Estática** para **Asignación** y escriba la dirección IP que desea. En este escenario, 10.0.0.5. A continuación, haga clic en **Aceptar**. Una vez guardada la configuración, verá la configuración de IP en la lista.

    ![Texto alternativo de imagen](media\\virtual-network-multiple-ip-addresses-portal\\02-portal.PNG)
3. A continuación, agregue la tercera configuración de IP, seleccionando **+Agregar** en la hoja **Configuraciones de IP** y rellene los detalles necesarios como se muestra a continuación. Después seleccione **Aceptar**.

    ![Texto alternativo de imagen](media\\virtual-network-multiple-ip-addresses-portal\\03-portal.PNG)

    Tenga en cuenta que IPConfig-2 e IPConfig-3 también se pueden asociar a una dirección IP pública seleccionando **Habilitada** en la sección **Dirección IP pública** de la hoja **Agregar configuración IP**. Se puede crear una dirección IP pública nueva o una ya creada se puede asociarse a la configuración de IP.
4. Agregue manualmente todas las direcciones IP privadas (incluida la principal) a la configuración TCP/IP en el sistema operativo tal y como se muestra a continuación. Para agregar manualmente las direcciones IP debe conectarse a la VM y luego seguir los pasos descritos a continuación.

**Windows**

1. En un símbolo del sistema, escriba *ipconfig /all*.  Solo verá la dirección IP privada *principal* (por medio de DHCP).
2. Luego, escriba *ncpa.cpl* en la ventana del símbolo del sistema del comando. De este modo se abrirá una ventana nueva.
3. Abra las propiedades de la **Conexión de área local**.
4. Haga doble clic en el Protocolo de Internet versión 4 (IPv4)
5. Seleccione **Usar la siguiente dirección IP** y escriba los valores siguientes:

   * **Dirección IP**: escriba la dirección IP privada *principal* .
   * **Máscara de subred**: establezca este valor en función de su subred. Por ejemplo, si la subred es una subred /24, la máscara de subred es 255.255.255.0.
   * **Puerta de enlace predeterminada**: la primera dirección IP de la subred. Si la subred es 10.0.0.1/24, la dirección IP de la puerta de enlace es 10.0.0.0/24.
   * Haga clic en **Usar las siguientes direcciones de servidor DNS** y escriba los valores siguientes:
     * **Servidor DNS preferido:** escriba 168.63.129.16 si no usa su propio servidor DNS.  Si usa uno propio, escriba su dirección IP.
   * Haga clic en el botón **Avanzadas** y agregue más direcciones IP. Agregue cada una de las direcciones IP privadas secundarias indicadas en el paso 8 a la NIC con la misma subred especificada para la dirección IP principal.
   * Haga clic en **Aceptar** para cerrar la configuración de TCP/IP y en **Aceptar** otra vez para cerrar la configuración del adaptador. Con esto se restablecerá la conexión RDP.
6. En un símbolo del sistema, escriba *ipconfig /all*. Se muestran todas las direcciones IP que agregó y DHCP está desactivado.

**Linux (Ubuntu)**

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, cámbielo mediante el siguiente comando:

            sudo -i
3. Actualice el archivo de configuración de la interfaz de red (suponiendo que sea "eth0").

   * Mantenga el elemento de línea existente para dhcp. Esto configurará la dirección IP principal como solía ser antes.
   * Agregue una configuración para una dirección IP estática adicional con los siguientes comandos:

               cd /etc/network/interfaces.d/
               ls

       Debería ver un archivo .cfg.
4. Abra el archivo: vi *nombreDeArchivo*.

    Debería ver las siguientes líneas al final del archivo:

            auto eth0
            iface eth0 inet dhcp
5. Agregue las líneas siguientes después de las que existen en este archivo:

            iface eth0 inet static
            address <your private IP address here>
6. Guarde el archivo mediante el comando siguiente:

            :wq
7. Restablezca la interfaz de red con el comando siguiente:

           sudo ifdown eth0 && sudo ifup eth0

   > [!IMPORTANT]
   > Si usa una conexión remota, ejecute ifdown e ifup en la misma línea.
   >
   >
8. Compruebe que la dirección IP se agregue a la interfaz de red con el comando siguiente:

            ip addr list eth0

    Debería ver la dirección IP que agregó en la lista.

**Linux (Redhat, CentOS y otros)**

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, cámbielo mediante el siguiente comando:

            sudo -i
3. Escriba la contraseña y siga las instrucciones que aparezcan. Una vez que sea el usuario raíz, vaya a la carpeta de scripts de red con el comando siguiente:

            cd /etc/sysconfig/network-scripts
4. Muestra una lista de los archivos de ifcfg relacionados con el siguiente comando:

            ls ifcfg-*

    Debería ver *ifcfg-eth0* como uno de los archivos.
5. Copie el archivo *ifcfg-eth0* y asígnele el nombre *ifcfg-eth0:0* con el siguiente comando:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Edite el archivo *ifcfg-eth0:0* con el comando siguiente:

            vi ifcfg-eth1
7. Cambie el dispositivo al nombre adecuado en el archivo, *eth0:0* en este caso, con el siguiente comando:

            DEVICE=eth0:0
8. Cambie la línea *IPADDR = suDirecciónIPPrivada* para reflejar la dirección IP.
9. Guarde el archivo mediante el comando siguiente:

            :wq
10. Reinicie los servicios de red y asegúrese de que los cambios sean correctos con los comandos siguientes:

            /etc/init.d/network restart
            Ipconfig

    Debería ver la dirección IP que agregó, *eth0:0*, en la lista que se devuelve.

## <a name="a-nameaddaadd-ip-addresses-to-an-existing-vm"></a><a name="add"></a>Incorporación de direcciones IP a una máquina virtual existente
Complete los pasos siguientes para agregar direcciones IP adicionales a una NIC existente:

1. Desde un explorador, vaya a http://portal.azure.com y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Seleccione la interfaz de red a la que desea agregar las configuraciones de IP en la sección **Interfaces de red**.
3. En la hoja **Interfaz de red** hoja, seleccione **Configuraciones de IP**. Verá una lista de las configuraciones de IP existentes.
4. Después, para agregar una configuración de IP, en la sección Configuraciones de IP de la interfaz de red, haga clic en **+Agregar**.
5. En la hoja Agregar configuración IP, asigne un nombre a la configuración de IP. Seleccione el tipo de dirección IP que le gustaría (Estática o Dinámica) en **Asignación**. En **Dirección IP pública**, seleccione **Habilitada** si desea asociar la configuración de IP a una dirección IP pública. Si no hace clic en **Deshabilitado**. Seleccione **Habilitado** para poder asociar una dirección de IP pública existente a la configuración. Luego haga clic en Aceptar. Una vez guardada la configuración, verá la configuración de IP en la lista.



<!--HONumber=Nov16_HO3-->


