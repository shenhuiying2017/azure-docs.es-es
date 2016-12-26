---
title: "Varias direcciones IP para máquinas virtuales - CLI de Azure | Microsoft Azure"
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con la CLI de Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Asignación de varias direcciones IP a máquinas virtuales mediante la CLI de Azure

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Una máquina virtual (VM) de Azure tiene una o varias interfaces de red (NIC) asociadas a ella. Cualquier NIC puede tener una o varias direcciones IP públicas o privadas estáticas o dinámicas asignadas. La asignación de varias direcciones IP a una VM permite las siguientes capacidades:

* Hospede varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor.
* Actúe como aplicación de red virtual, como un firewall o equilibrador de carga.
* La capacidad de agregar cualquier dirección IP privada para cualquiera de las NIC a un grupo de servidores de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end. Para más información sobre cómo equilibrar la carga de varias configuraciones de IP, lea el artículo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md) (Equilibrio de carga de varias configuraciones de IP).

Cada NIC conectada a una VM tiene una o varias configuraciones de IP asociadas. Se asigna a cada configuración una dirección IP privada estática o dinámica. Cada configuración también puede tener un recurso de dirección IP pública asociado. Un recurso de dirección IP pública tiene una dirección IP dinámica o estática asignada. Si no está familiarizado con las direcciones IP en Azure, lea el artículo [Direcciones IP en Azure](virtual-network-ip-addresses-overview-arm.md) para aprender más sobre ellas.

En este artículo, se explica cómo usar PowerShell para asignar varias direcciones IP a una VM creada a través del modelo de implementación de Azure Resource Manager. No se pueden asignar varias direcciones IP a los recursos creados mediante el modelo de implementación clásica. Para información acerca de los modelos de implementación de Azure, lea el artículo [Understand deployment models](../resource-manager-deployment-model.md) (Descripción de los modelos de implementación).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Escenario
Una VM con una sola NIC se crea y se conecta a una red virtual. La VM requiere tres diferentes IP direcciones *privadas* y dos direcciones IP *públicas*. Las direcciones IP se asignan a las siguientes configuraciones de IP:

* **IPConfig-1:** Asigna un dirección IP privada (valor predeterminado) *dinámica* y una dirección IP pública *estática*.
* **IPConfig-2:** Asigna un dirección IP privada *estática* y una dirección IP pública *estática*.
* **IPConfig-3:** Asigna un dirección IP privada *dinámica* y una dirección IP no pública.
  
    ![Varias direcciones IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Las configuraciones de IP están asociadas a la NIC cuando esta se crea y se conecta a la VM cuando se crea dicha VM. Los tipos de direcciones IP usados para el escenario tienen fines ilustrativos. Puede asignar cualquier dirección IP y tipos de asignación que requiera.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creación de una máquina virtual con varias direcciones IP

En los pasos siguientes se explica cómo crear una VM de ejemplo con varias direcciones IP, tal como se describe en el escenario. Cambie los nombres de variable los y tipos de direcciones IP según sea necesario para la implementación.

1. Instale y configure la CLI de Azure siguiendo los pasos del artículo [Instalación de la CLI de Azure](../xplat-cli-install.md) e inicie sesión en la cuenta de Azure.

2. Regístrese para la versión preliminar enviando un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto. No complete los pasos restantes:
    - Hasta después de recibir una notificación por correo electrónico de que se le acepta en la versión preliminar.
    - Sin seguir las instrucciones del correo electrónico que reciba.
3. [Cree un grupo de recursos](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) seguido de una [red virtual y una subred ](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Cambie los campos ``` --address-prefixes ``` y ```--address-prefix``` por lo siguiente para seguir el escenario exacto descrito en este artículo:

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >El artículo al que se hace referencia anteriormente utiliza Europa Occidental como la ubicación para crear recursos, pero en este artículo se usa Centro occidental de EE. UU. Realice cambios de ubicación correctamente.

4. [Cree una cuenta de almacenamiento](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) para su VM.


5. Cree la NIC y las configuraciones de IP que desee asignar a la NIC. Puede agregar, quitar o cambiar las configuraciones según sea necesario. En el escenario se describen las siguientes configuraciones:

    **IPConfig-1**

    Escriba los comandos siguientes para crear:

    - Un recurso de dirección IP pública con una dirección IP pública estática
    - Una configuración de IP con el recurso de dirección IP pública y una dirección IP privada dinámica

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Escriba los comandos siguientes para crear un nuevo recurso de dirección IP pública y una nueva configuración de IP con una dirección IP pública estática y una dirección IP privada estática:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Escriba los comandos siguientes para crear una configuración de IP con una dirección IP privada dinámica y ninguna dirección IP no pública:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Aunque este artículo asigna todas las configuraciones de IP a una NIC única, también puede asignar varias configuraciones de IP a una NIC en una VM. Para aprender a crear una VM con varias NIC, lea el artículo Implementación de máquinas virtuales con varias NIC mediante PowerShell.

6. Artículo [Creación de las máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Asegúrese de quitar la propiedad ```  --availset-name myAvailabilitySet \ ```, ya que no es necesaria para este escenario. Utilice la ubicación adecuada en función del escenario. 

    >[!WARNING] 
    > El paso 6 del artículo para crear una VM da error si el tamaño de dicha VM no se admite en la ubicación seleccionada. Ejecute el siguiente comando para obtener una lista completa de las VM de Centro Oeste de EE. UU., por ejemplo. El nombre de esta ubicación se puede cambiar en función de su escenario.
    > 
    >       azure vm sizes --location westcentralus

    Para cambiar el tamaño de VM a Estándar DS2 v2, por ejemplo, simplemente agregue la siguiente propiedad ```  --vm-size Standard_DS3_v2``` al comando ``` azure vm create ``` en el paso 6.

7. Escriba el siguiente comando para ver la NIC y las configuraciones de IP asociadas:

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>Incorporación de direcciones IP a un sistema operativo de la máquina virtual

Conéctese e inicie sesión en una máquina virtual que creó con múltiples direcciones IP privadas. Debe agregar manualmente todas las direcciones IP privadas (incluida la principal) que ha agregado a la máquina virtual. Complete los pasos siguientes para el sistema operativo de su máquina virtual:

### <a name="windows"></a>Windows

1. En un símbolo del sistema, escriba *ipconfig /all*.  Solo verá la dirección IP privada *principal* (por medio de DHCP).
2. Escriba *ncpa.cpl* en el símbolo del sistema para abrir la ventana **Conexiones de red**.
3. Abra las propiedades de la **Conexión de área local**.
4. Haga doble clic en el Protocolo de Internet versión 4 (IPv4).
5. Seleccione **Usar la siguiente dirección IP** y escriba los valores siguientes:

    * **Dirección IP**: escriba la dirección IP privada *principal* .
    * **Máscara de subred**: establezca este valor en función de su subred. Por ejemplo, si la subred es una subred /24, la máscara de subred es 255.255.255.0.
    * **Puerta de enlace predeterminada**: la primera dirección IP de la subred. Si la subred es 10.0.0.1/24, la dirección IP de la puerta de enlace es 10.0.0.0/24.
    * Haga clic en **Usar las siguientes direcciones de servidor DNS** y escriba los valores siguientes:
        * **Servidor DNS preferido:** escriba 168.63.129.16 si no usa su propio servidor DNS.  Si usa su propio servidor DNS, escriba la dirección IP de su servidor.
    * Haga clic en el botón **Avanzadas** y agregue más direcciones IP. Agregue cada una de las direcciones IP privadas secundarias indicadas en el paso 8 a la NIC con la misma subred especificada para la dirección IP principal.
    * Haga clic en **Aceptar** para cerrar la configuración de TCP/IP y en **Aceptar** otra vez para cerrar la configuración del adaptador. Se restablece la conexión RDP.
6. En un símbolo del sistema, escriba *ipconfig /all*. Se muestran todas las direcciones IP que agregó y DHCP está desactivado.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

    ```bash
    sudo -i
    ```

3. Actualice el archivo de configuración de la interfaz de red (suponiendo que sea "eth0").

    * Mantenga el elemento de línea existente para dhcp. La dirección IP principal permanece configurada que estaba.
    * Agregue una configuración para una dirección IP estática adicional con los siguientes comandos:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Debería ver un archivo .cfg.
4. Abra el archivo: vi *nombreDeArchivo*.

    Debería ver las siguientes líneas al final del archivo:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Agregue las líneas siguientes después de las que existen en este archivo:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Guarde el archivo mediante el comando siguiente:

    ```bash
    :wq
    ```

7. Restablezca la interfaz de red con el comando siguiente:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Si usa una conexión remota, ejecute ifdown e ifup en la misma línea.
    >

8. Compruebe que la dirección IP se agregue a la interfaz de red con el comando siguiente:

    ```bash
    Ip addr list eth0
    ```

    Debería ver la dirección IP que agregó en la lista.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS y otros)

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

    ```bash
    sudo -i
    ```

3. Escriba la contraseña y siga las instrucciones que aparezcan. Una vez que sea el usuario raíz, vaya a la carpeta de scripts de red con el comando siguiente:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Muestra una lista de los archivos de ifcfg relacionados con el siguiente comando:

    ```bash
    ls ifcfg-*
    ```

    Debería ver *ifcfg-eth0* como uno de los archivos.

5. Copie el archivo *ifcfg-eth0* y asígnele el nombre *ifcfg-eth0:0* con el siguiente comando:

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Edite el archivo *ifcfg-eth0:0* con el comando siguiente:

    ```bash
    vi ifcfg-eth1
    ```

7. Cambie el dispositivo al nombre adecuado en el archivo, *eth0:0* en este caso, con el siguiente comando:

    ```bash
    DEVICE=eth0:0
    ```

8. Cambie la línea *IPADDR = suDirecciónIPPrivada* para reflejar la dirección IP.
9. Guarde el archivo mediante el comando siguiente:

    ```bash
    :wq
    ```

10. Reinicie los servicios de red y asegúrese de que los cambios sean correctos con los comandos siguientes:

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    Debería ver la dirección IP que agregó, *eth0:0*, en la lista que se devuelve.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Incorporación de direcciones IP a una VM

Puede agregar más direcciones IP públicas y privadas a una NIC existente completando los pasos siguientes. Los ejemplos se crean en el [escenario](#Scenario) descrito en este artículo.

1. Abra la CLI de Azure y complete los pasos restantes de esta sección dentro de una sola sesión de CLI. Si todavía no tiene la CLI de Azure instalada y configurada, complete los pasos del artículo [Instalación de la CLI de Azure](../xplat-cli-install.md) e inicie sesión en la cuenta de Azure.

2. Regístrese para la versión preliminar enviando un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto. No complete los pasos restantes:
    - Hasta después de recibir una notificación por correo electrónico de que se le acepta en la versión preliminar.
    - Sin seguir las instrucciones del correo electrónico que reciba.


3. Complete los pasos de una de las secciones siguientes, según sus requisitos:

    **Incorporación de una dirección IP privada**
    
    Para agregar una dirección IP privada a una NIC, debe crear una configuración de IP mediante el comando siguiente.  Si desea agregar una dirección IP privada dinámica, quite ```-PrivateIpAddress 10.0.0.7``` antes de escribir el comando. Al especificar una dirección IP estática, debe ser una dirección no utilizada para la subred.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Cree tantas configuraciones como sea necesario mediante nombres de configuración únicos y direcciones IP privadas (para las configuraciones con direcciones IP estáticas).

    **Incorporación de una dirección IP pública**
    
    Se agrega una dirección IP pública mediante la asociación a una nueva configuración de IP o una configuración de IP existente. Complete los pasos de una de las secciones siguientes, según sea preciso.

    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Asociación del recurso a una nueva configuración de IP**
    
    Siempre que agregue una dirección IP pública en una nueva configuración de IP, también debe agregar una dirección IP privada, porque todas las configuraciones de IP deben tener una dirección IP privada. Puede agregar un recurso de dirección IP pública existente o crear uno nuevo. Para crear uno nuevo, escriba el comando siguiente:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    Para crear una nueva configuración de IP con una dirección IP privada dinámica y el recurso de dirección IP pública *myPublicIP3*, escriba el comando siguiente:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Asocie el recurso a una configuración de IP existente**
   . Solo se puede asociar un recurso de dirección IP pública a una configuración de IP que ya no tiene asociado uno. Puede determinar si una configuración de IP tiene una dirección IP pública asociada escribiendo el comando siguiente:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Busque una línea similar a la que aparece a continuación en la salida devuelta:
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Puesto que la columna **IP pública** para *IpConfig-3* está en blanco, ningún recurso de dirección IP pública está asociado actualmente a ella. Puede agregar un recurso de dirección IP pública existente a IpConfig-3 o escribir el siguiente comando para crear uno:

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Escriba el siguiente comando para asociar el recurso de dirección IP pública a la configuración de IP existente llamada *IPConfig-3*:
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. Vea las direcciones IP privadas y los recursos de dirección IP pública asignados a la NIC escribiendo el siguiente comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    Debería ver una salida similar a la siguiente: 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Agregue al sistema operativo de la VM las direcciones IP que agregó a la NIC siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#OsConfig) de este artículo.


<!--HONumber=Nov16_HO3-->


