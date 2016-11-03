<properties
   pageTitle="Varias direcciones IP para máquinas virtuales - PowerShell | Microsoft Azure"
   description="Aprenda a asignar varias direcciones IP a una máquina virtual con Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />



# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Asignación de varias direcciones IP a máquinas virtuales

Una máquina virtual (VM) de Azure puede tener una o varias interfaces de red (NIC) asociadas a ella. Cada NIC puede tener una o varias direcciones IP públicas o privadas asignadas. Si no está familiarizado con las direcciones IP en Azure, lea el artículo [Direcciones IP en Azure](virtual-network-ip-addresses-overview-arm.md) para aprender más sobre ellas. En este artículo, se explica cómo usar Azure PowerShell para asignar varias direcciones IP a una NIC en el modelo de implementación de Azure Resource Manager.

La asignación de varias direcciones IP a una NIC permite que la máquina virtual:

- Hospede varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor.
- Actúe como aplicación de red virtual, como un firewall o equilibrador de carga.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrarse para la versión preliminar, envíe un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto.
## <a name="scenario"></a>Escenario

En este artículo, asociará 3 configuraciones IP con una interfaz de red.
Las siguientes configuraciones de ejemplo se crearán y asignarán a una NIC que tendrá 3 direcciones IP privadas y una pública asignadas:

- IPConfig-1: una dirección IP privada dinámica (predeterminada) y una dirección IP pública del recurso de dirección IP pública llamado PIP1.
- IPConfig-2: una dirección IP privada estática y ninguna dirección IP pública.
- IPConfig-3: una dirección IP privada dinámica y ninguna dirección IP pública.

    ![Texto alternativo de imagen](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Este escenario supone que tiene un grupo de recursos llamado *RG1*, dentro del cual se encuentra una red virtual denominada *VNet1* y una subred llamada *Subnet1*. Además, supone que tiene una máquina virtual llamada *VM1*, una interfaz de red llamada *VM1-NIC1* asociada a ella y una dirección IP pública denominada *PIP1*.

[Este artículo](./virtual-machines/virtual-machines-windows-ps-create.md ) lo guiará en el proceso de creación de los recursos mencionados anteriormente en caso de que todavía no los haya creado.

## <a name="a-name-createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creación de una máquina virtual con varias direcciones IP

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección en una única sesión de PowerShell. Si todavía no tiene PowerShell instalado y configurado, complete los pasos del artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

2. Cambie los "valores" de los siguientes elementos $Variables a la [ubicación](https://azure.microsoft.com/regions) de Azure donde está la red virtual, el nombre del [grupo de recursos](../resource-group-overview.md#resource-groups), la red virtual dentro del grupo de recursos, la subred a la que desea conectar la tarjeta NIC y el nombre de esta.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Si no conoce el nombre de una ubicación de Azure o un grupo de recursos existentes, escriba los siguientes comandos:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>La NIC debe estar conectada a una subred dentro de una instancia de Azure Virtual Network (red virtual). Los 3 componentes, es decir, la tarjeta NIC, la subred y la red virtual, deben existir en la misma región y [suscripción](../azure-glossary-cloud-terminology.md#subscription).  Si desconoce las redes virtuales, lea el artículo [Información general sobre redes virtuales](virtual-networks-overview.md) para aprender más sobre ellas o lea el artículo sobre cómo [crear una máquina virtual](virtual-networks-create-vnet-arm-ps.md) para aprender a crear una.

    Si no conoce el nombre de una red virtual existente, escriba el siguiente comando y reemplace *VNet1* en la variable anterior por el nombre de una red virtual:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Si la lista que se devuelve está vacía, debe crear una red virtual. Para aprender a hacerlo, consulte el artículo sobre cómo [crear una red virtual](virtual-networks-create-vnet-arm-ps.md) .

    Escriba los comandos siguientes para obtener el nombre de las subredes existentes de la red virtual y reemplace *Subnet1* arriba por el nombre de una subred:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Escriba el siguiente comando para recuperar la subred y asignarla a una variable.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Defina las configuraciones de dirección IP que desea asignar a la NIC. Cada configuración puede tener una dirección IP privada estática o dinámica y un recurso de dirección IP pública asociado con una dirección estática o dinámica.

    Agregue o quite las configuraciones siguientes que desee, según la cantidad de direcciones IP que desee asociar a la NIC y la configuración que desee establecer.

    **IPConfig-1**

    Cambie el valor *PIP1* al nombre de un recurso de dirección IP pública existente que se encuentre en la ubicación donde esté creando la NIC y que no esté asociado actualmente a otra NIC. Cambie *RG1* al nombre del grupo de recursos donde exista el recurso de dirección IP pública. Cambie *IPConfig-1* al nombre que desee asignar a la primera configuración de dirección IP. Escriba los siguientes comandos:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Observe el modificador *-Primary* . Cuando se asignan varias configuraciones de dirección IP a una NIC, se debe asignar una como la *principal*. Si desconoce el nombre de un recurso de dirección IP pública existente, escriba el siguiente comando:

        Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration

    Si la columna **IPConfiguration** no muestra ningún valor en la salida devuelta, el recurso de dirección IP pública no está asociado con una NIC existente y se puede usar. Si la lista está vacía o no hay ningún recurso de dirección IP pública disponible, puede crear uno con el comando **New-AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Cambie *IPConfig-2* al nombre que desee asignar a la segunda configuración de dirección IP y cambie *10.0.0.5* a una dirección IP válida sin usar para la subred a la que va a asignar la NIC. Escriba los siguientes comandos:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Escriba el siguiente comando si no conoce el intervalo de direcciones IP asignado a la subred:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Cambie *IPConfig-3* al nombre que desee asignar a la tercera configuración de dirección IP y escriba los siguientes comandos:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Puede asignar hasta 250 direcciones IP privadas a una NIC. Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Cree la NIC con las configuraciones de dirección IP definidas en el paso anterior.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Asocie la NIC al crear una máquina virtual según los pasos del artículo sobre cómo [crear una máquina virtual](../virtual-machines/virtual-machines-windows-ps-create.md) . Aunque el artículo crea una máquina virtual que se ejecuta en Windows Server, los pasos son los mismos para una máquina virtual Linux, excepto que se selecciona un sistema operativo diferente. Complete los pasos del 1 al 3 del artículo. Pase por alto los pasos 4 y 5, y complete el paso 6 en ese artículo.

    >[AZURE.WARNING] El paso 6 en el artículo producirá un error si cambia la variable denominada $nic a un valor diferente al que aparece en el paso 6 de ese artículo o si no se han completado los pasos anteriores del presente artículo.

8. Vea las direcciones IP privadas que Azure DHCP asignó a la NIC y el recurso de dirección IP pública asignado a la NIC escribiendo el siguiente comando:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Agregue manualmente todas las direcciones IP privadas secundarias (direcciones IP con *False* en la columna **Primary** en la salida del paso anterior) a la configuración de TCP/IP en el sistema operativo. La dirección IP privada asignada a *IPConfig-1* en el paso 5 se asigna automáticamente al sistema operativo por medio de Azure DHCP, porque tiene la configuración *Principal*.


**Windows**

1. En un símbolo del sistema, escriba *ipconfig /all*.  Solo verá la dirección IP privada *principal* (por medio de DHCP).
2. Luego, escriba *ncpa.cpl* en la ventana del símbolo del sistema del comando. De este modo se abrirá una ventana nueva.
3. Abra las propiedades de la **Conexión de área local**.
4. Haga doble clic en el Protocolo de Internet versión 4 (IPv4)
5. Seleccione **Usar la siguiente dirección IP** y escriba los valores siguientes:
    - **Dirección IP**: escriba la dirección IP privada *principal* .
    - **Máscara de subred**: establezca este valor en función de su subred. Por ejemplo, si la subred es una subred /24, la máscara de subred es 255.255.255.0.
    - **Puerta de enlace predeterminada**: la primera dirección IP de la subred. Si la subred es 10.0.0.1/24, la dirección IP de la puerta de enlace es 10.0.0.0/24.
    - Haga clic en **Usar las siguientes direcciones de servidor DNS** y escriba los valores siguientes:
        - **Servidor DNS preferido:** escriba 168.63.129.16 si no usa su propio servidor DNS.  Si usa uno propio, escriba su dirección IP.
    - Haga clic en el botón **Avanzadas** y agregue más direcciones IP. Agregue cada una de las direcciones IP privadas secundarias indicadas en el paso 8 a la NIC con la misma subred especificada para la dirección IP principal.
    - Haga clic en **Aceptar** para cerrar la configuración de TCP/IP y en **Aceptar** otra vez para cerrar la configuración del adaptador. Con esto se restablecerá la conexión RDP.

6. En un símbolo del sistema, escriba *ipconfig /all*. Se muestran todas las direcciones IP que agregó y DHCP está desactivado.


**Linux (Ubuntu)**

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, cámbielo mediante el siguiente comando:

            sudo -i
3. Actualice el archivo de configuración de la interfaz de red (suponiendo que sea "eth0").
    - Mantenga el elemento de línea existente para dhcp. Esto configurará la dirección IP principal como solía ser antes.
    - Agregue una configuración para una dirección IP estática adicional con los siguientes comandos:

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
7.  Restablezca la interfaz de red con el comando siguiente:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Si usa una conexión remota, ejecute ifdown e ifup en la misma línea.
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

1. Abra un símbolo del sistema de PowerShell y complete los pasos restantes de esta sección en una única sesión de PowerShell. Si todavía no tiene PowerShell instalado y configurado, complete los pasos del artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

2. Cambie los "valores" de los elementos $Variables siguientes al nombre de la NIC a la que desee agregar direcciones IP y al grupo de recursos y la ubicación donde existe la NIC:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Si no conoce el nombre de la NIC que desea cambiar, escriba los siguientes comandos y cambie los valores de las anteriores variables:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Cree una variable y establézcala en la NIC existente con el siguiente comando:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Recupere el identificador de la subred a la esté conectada la NIC completando el [paso 3](#subnet) de la sección Creación de una máquina virtual con varias direcciones IP de este artículo.

5. Cree las configuraciones de dirección IP que desee agregar a la red siguiendo las instrucciones del [paso 5](#ipconfigs) de la sección Creación de una máquina virtual con varias direcciones IP de este artículo.

6. Cambie *$IPConfigName4* al nombre de la configuración de dirección IP que creó en el paso anterior. Para agregar la configuración, escriba el siguiente comando:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Para establecer la NIC con la configuración de dirección IP, escriba el siguiente comando:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Agregue al sistema operativo de la máquina virtual las direcciones IP que agregó a la NIC siguiendo las instrucciones del [paso 9](#os) de la sección Creación de una máquina virtual con varias direcciones IP de este artículo.



<!--HONumber=Oct16_HO2-->


