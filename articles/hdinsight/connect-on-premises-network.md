---
title: "Conexión de HDInsight a la red local: Azure HDInsight | Microsoft Docs"
description: "Obtenga información sobre cómo crear un clúster de HDInsight en una instancia de Azure Virtual Network y conectarlo a la red local. Obtenga información sobre cómo usar un servidor DNS personalizado para configurar la resolución de nombres entre HDInsight y la red local."
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: larryfr
ms.openlocfilehash: 27a5d0e69ec9c47feab2b23d7c79fe2547edfc08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>Conexión de HDInsight a la red local

Obtenga información sobre cómo conectar HDInsight a la red local mediante redes virtuales de Azure y una puerta de enlace de VPN. En este documento se brinda información de planeación sobre:

* Uso de HDInsight en una instancia de Azure Virtual Network que se conecta con la red local.

* Configuración de resolución de nombres DNS entre la red virtual y la red local.

* Configuración de grupos de seguridad de red para restringir acceso a HDInsight a través de Internet.

* Puertos que HDInsight proporciona en la red virtual.

## <a name="create-the-virtual-network-configuration"></a>Creación de la configuración de la red virtual

Use los documentos siguientes para obtener información sobre cómo crear una instancia de Azure Virtual Network conectada a la red local:
    
* [Uso de Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Uso de Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Uso de la CLI de Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Configuración de la resolución de nombres

Para permitir que HDInsight y los recursos de la red combinada se comuniquen por nombre, debe realizar las acciones siguientes:

* Cree un servidor DNS personalizado en la instancia de Azure Virtual Network.

* Configure la red virtual para que use el servidor DNS personalizado en lugar de la resolución recursiva de Azure predeterminada.

* Configure el reenvío entre el servidor DNS personalizado y el servidor DNS local.

Esta configuración permite el siguiente comportamiento:

* Las solicitudes de nombres de dominio completos con el sufijo DNS __para la red virtual__ se reenvían al servidor DNS personalizado. Entonces, el servidor DNS personalizado reenvía estas solicitudes a la resolución recursiva de Azure, que devuelve la dirección IP.

* Todas las otras solicitudes se reenvían al servidor DNS local. Incluso las solicitudes de recursos de Internet, como microsoft.com, se reenvían al servidor DNS local para la resolución de nombres.

En el diagrama siguiente, las líneas verdes son solicitudes de recursos que finalizan en el sufijo DNS de la red virtual. Las líneas azules son solicitudes de recursos en la red local o en Internet.

![Diagrama de cómo se resuelven las solicitudes DNS en la configuración que se usa en este documento](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Creación de un servidor DNS personalizado

> [!IMPORTANT]
> Debe crear y configurar el servidor DNS antes de instalar HDInsight en la red virtual.

Para crear una máquina virtual Linux que usa el software DNS [Bind](https://www.isc.org/downloads/bind/), use los pasos siguientes:

> [!NOTE]
> Los pasos siguientes usan [Azure Portal](https://portal.azure.com) para crear una instancia de Azure Virtual Machine. Para conocer otras formas de crear una máquina virtual, consulte los documentos siguientes:
>
> * [Creación de una máquina virtual: CLI de Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Creación de una máquina virtual: Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. En [Azure Portal](https://portal.azure.com), seleccione __+__, __Proceso__ y __Ubuntu Server 16.04 LTS__.

    ![Creación de una máquina virtual Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. Escriba la información siguiente d la sección __Aspectos básicos__:

    * __Nombre__: nombre descriptivo que identifica esta máquina virtual. Por ejemplo, __DNSProxy__.
    * __Nombre de usuario__: nombre de la cuenta SSH.
    * __Clave pública SSH__ o __Contraseña__: método de autenticación de la cuenta SSH. Se recomienda usar claves públicas, porque son más seguras. Para más información, consulte el documento [Creación y uso de claves SSH para máquinas virtuales Linux](../virtual-machines/linux/mac-create-ssh-keys.md).
    * __Grupo de recursos__: seleccione __Usar existente__ y, luego, seleccione el grupo de recursos que contiene la red virtual que se creó anteriormente.
    * __Ubicación__: seleccione la misma ubicación que tiene la máquina virtual.

    ![Configuración básica de la máquina virtual](./media/connect-on-premises-network/vm-basics.png)

    Deje los valores predeterminados de las otras entradas y, luego, seleccione __Aceptar__.

3. En la sección __Choosing a size__ (Elegir un tamaño), seleccione el tamaño de la máquina virtual. Para este tutorial, seleccione la opción más pequeña y de menor costo. Para continuar, use el botón __Seleccionar__.

4. Escriba la información siguiente de la sección __Configuración__:

    * __Red virtual__: seleccione la red virtual que creó anteriormente.

    * __Subred__: seleccione la subred predeterminada para la máquina virtual. __No__ seleccione la subred que la puerta de enlace de VPN usa.

    * __Cuenta de almacenamiento de diagnóstico__: seleccione una cuenta de almacenamiento existente o cree una nueva.

    ![Configuración de la red virtual](./media/connect-on-premises-network/virtual-network-settings.png)

    Deje los valores predeterminados de las otras entradas y, luego, seleccione __Aceptar__ para continuar.

5. En la sección __Comprar__, seleccione el botón __Comprar__ para crear la máquina virtual.

6. En la máquina virtual que se crea, aparece la sección de __información general__. En la lista que aparece a la izquierda, seleccione __Propiedades__. Guarde los valores de __dirección IP pública__ y __dirección IP privada__. Esta información se usará en la sección siguiente.

    ![Direcciones IP pública y privada](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalación y configuración de Bind (software DNS)

1. Use SSH para conectarse a la __dirección IP pública__ de la máquina virtual. En el ejemplo siguiente, se realiza una conexión a una máquina virtual en 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Reemplace `sshuser` por la cuenta de usuario SSH que especificó cuando creó el clúster.

    > [!NOTE]
    > Existen diversas formas de obtener la utilidad `ssh`. En Linux, Unix y macOS, se proporciona como parte del sistema operativo. Si usa Windows, considere una de las opciones siguientes:
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash en Ubuntu en Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [GIT (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar Bind, use los comandos siguientes en la sesión de SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Para configurar Bind a fin de reenviar las solicitudes de resolución de nombres al servidor DNS local, use el texto siguiente como contenido del archivo `/etc/bind/named.conf.options`:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > Reemplace los valores de la sección `goodclients` por el intervalo de direcciones IP de la red virtual y la red local. En esta sección se definen las direcciones desde las cuales el servidor DNS acepta solicitudes.
    >
    > Reemplace la entrada `192.168.0.1` en la sección `forwarders` por la dirección IP del servidor DNS local. Esta entrada enruta las solicitudes DNS al servidor DNS local para la resolución.

    Para editar el archivo, use el comando siguiente:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Para guardar el archivo, use __Ctrl+X__, __Y__ y, luego, __Entrar__.

4. En la sesión de SSH, use el comando siguiente:

    ```bash
    hostname -f
    ```

    Este comando devuelve un valor similar al siguiente texto:

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    El texto `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` está en el __sufijo DNS__ de esta red virtual. Guarde este valor, porque se usará más adelante.

5. Para configurar Bind a fin de resolver nombres DNS para recursos dentro de la red virtual, use el texto siguiente como contenido del archivo `/etc/bind/named.conf.local`:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Debe reemplazar `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` por el sufijo DNS que recuperó anteriormente.

    Para editar el archivo, use el comando siguiente:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para guardar el archivo, use __Ctrl+X__, __Y__ y, luego, __Entrar__.

6. Para iniciar Bind, use el comando siguiente:

    ```bash
    sudo service bind9 restart
    ```

7. Para comprobar que Bind puede resolver los nombres de recursos en la red local, use los comandos siguientes:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Reemplace `dns.mynetwork.net` por el nombre de dominio completo (FQDN) de un recurso en la red local.
    >
    > Reemplace `10.0.0.4` por la __dirección IP interna__ del servidor DNS personalizado en la red virtual.

    La respuesta es similar al texto siguiente:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configuración de la red virtual para usar el servidor DNS personalizado

Para configurar la red virtual para que use el servidor DNS personalizado en lugar de la resolución recursiva de Azure, use los pasos siguientes:

1. En [Azure Portal](https://portal.azure.com), seleccione la red virtual y, luego, seleccione __Servidores DNS__.

2. Seleccione __Personalizar__ y escriba la __dirección IP interna__ del servidor DNS personalizado. Por último, seleccione __Guardar__.

    ![Establecimiento del servidor DNS personalizado para la red](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Configuración del servidor DNS local

En la sección anterior, configuró el servidor DNS personalizado para reenviar las solicitudes al servidor DNS local. A continuación, debe configurar el servidor DNS local para reenviar las solicitudes al servidor DNS personalizado.

Para conocer los pasos específicos sobre cómo configurar el servidor DNS, consulte la documentación correspondiente al software de servidor DNS. Busque los pasos sobre cómo configurar un __reenvío condicional__.

Un reenvío condicional solo reenvía solicitudes para un sufijo DNS específico. En este caso, debe configurar un reenvío para el sufijo DNS de la red virtual. Las solicitudes para este sufijo se deben reenviar a la dirección IP del servidor DNS personalizado. 

El texto siguiente es un ejemplo de la configuración de un reenvío condicional para el software DNS **Bind**:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Para información sobre cómo usar DNS en **Windows Server 2016**, consulte la documentación de [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone).

Una vez que configure el servidor DNS local, puede usar `nslookup` desde la red local para comprobar que es posible resolver nombres en la red virtual. El ejemplo siguiente 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

En este ejemplo se usa el servidor DNS local en 196.168.0.4 para resolver el nombre del servidor DNS personalizado. Reemplace la dirección IP por la dirección IP del servidor DNS local. Reemplace la dirección `dnsproxy` por el nombre de dominio completo del servidor DNS personalizado.

## <a name="optional-control-network-traffic"></a>Opcional: control del tráfico de red

Puede usar grupos de seguridad de red (NSG) o rutas definidas por el usuario (UDR) para controlar el tráfico de red. Los NSG permiten filtrar el tráfico de entrada y de salida, además de permitir o denegar el tráfico. Las UDR permiten controlar el flujo del tráfico entre los recursos de la red virtual, Internet y la red local.

> [!WARNING]
> HDInsight requiere acceso de entrada desde direcciones IP específicas en la nube de Azure y acceso de salida sin restricciones. Cuando use NSG o UDR para controlar el tráfico, debe realizar los pasos siguientes:
>
> 1. Encuentre las direcciones IP de la ubicación que contiene la red virtual. Para obtener una lista de las direcciones IP requeridas por ubicación, consulte [Direcciones IP requeridas](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).
>
> 2. Permitir el tráfico de entrada proveniente de las direcciones IP.
>
>    * __NSG__: permita el tráfico de __entrada__ en el puerto __443__ desde __Internet__.
>    * __UDR__: establezca el tipo __Próximo salto__ de la ruta en __Internet__.

Para un ejemplo de cómo usar Azure PowerShell o la CLI de Azure para crear NSG, consulte el documento [Extensión de HDInsight con redes virtuales de Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg).

## <a name="create-the-hdinsight-cluster"></a>Creación del clúster de HDInsight

> [!WARNING]
> Debe configurar el servidor DNS antes de instalar HDInsight en la red virtual.

Use los pasos que aparecen en el documento sobre cómo [crear un clúster de HDInsight mediante Azure Portal de un clúster](./hdinsight-hadoop-create-linux-clusters-portal.md) para crear un clúster de HDInsight.

> [!WARNING]
> * Durante la creación del clúster, debe elegir la ubicación que contiene la red virtual.
>
> * En la parte __Configuración avanzada__ de la configuración, debe seleccionar la red virtual y la subred que creó anteriormente.

## <a name="connecting-to-hdinsight"></a>Conexión a HDInsight

En la mayor parte de la documentación sobre HDInsight se supone que tiene acceso al clúster a través de Internet. Por ejemplo, que se puede conectar al clúster en https://NOMBREDECLÚSTER.azurehdinsight.net. Esta dirección usa la puerta de enlace pública, que no está disponible si ha usado NSG o UDR para restringir el acceso desde Internet.

Alguna documentación también hace referencia a `headnodehost` al conectarse al clúster desde una sesión de SSH. Esta dirección solo está disponible en los nodos de un clúster y no se puede usar en los clientes conectados a través de la red virtual.

Para conectarse directamente a HDInsight a través de la red virtual, use los pasos siguientes:

1. Para detectar los nombres de dominio completos internos de los nodos de clúster de HDInsight, use uno de los métodos siguientes:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Para determinar el puerto en que está disponible un servicio, consulte el documento [Puertos utilizados por los servicios Hadoop en HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

    > [!IMPORTANT]
    > Algunos servicios hospedados en los nodos principales solo están activos en un nodo a la vez. Si intenta acceder a un servicio en un nodo principal y se produce un error, cambie al otro nodo principal.
    >
    > Por ejemplo, Ambari solo está activo en un nodo principal a la vez. Si intenta acceder a Ambari en un nodo principal y se devuelve un error 404, es porque se ejecuta en el otro nodo principal.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo usar HDInsight en una máquina virtual, consulte [Extensión de HDInsight con redes virtuales de Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Para más información sobre las redes virtuales de Azure, consulte la [información general sobre Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Para más información sobre los grupos de seguridad de red, vea [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

* Para más información sobre las rutas definidas por el usuario, vea [Rutas definidas por el usuario y reenvío IP](../virtual-network/virtual-networks-udr-overview.md).
