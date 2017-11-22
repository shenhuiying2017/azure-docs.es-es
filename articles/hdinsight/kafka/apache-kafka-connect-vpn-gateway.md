---
title: "Conexión a Kafka mediante redes virtuales - Azure HDInsight | Microsoft Docs"
description: "Aprenda a conectarse directamente a Kafka en HDInsight mediante una instancia de Azure Virtual Network. Aprenda cómo conectarse a Kafka desde clientes de desarrollo mediante una puerta de enlace de red privada virtual o desde clientes de la red local mediante un dispositivo de puerta de enlace de red privada virtual."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 2b55de4de6bb94be78649112161211346090b23a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Conexión a Kafka en HDInsight mediante una instancia de Azure Virtual Network

Aprenda a conectarse directamente a Kafka en HDInsight mediante una instancia de Azure Virtual Network. En este documento se proporciona información sobre la conexión a Kafka mediante las siguientes configuraciones:

* Desde recursos de una red local. Esta conexión se establece mediante un dispositivo de red privada virtual (software o hardware) en la red local.
* Desde un entorno de desarrollo con un cliente de software de red privada virtual.

## <a name="architecture-and-planning"></a>Arquitectura y planeación

HDInsight no permite la conexión directa a Kafka a través de la red pública de Internet. Los clientes de Kafka (productores y consumidores) deben usar uno de los siguientes métodos de conexión:

* Ejecutar el cliente en la misma red virtual que Kafka en HDInsight. Esta configuración se usa en el documento [Introducción a Apache Kafka en HDInsight](apache-kafka-get-started.md). El cliente se ejecuta directamente en los nodos del clúster de HDInsight o en otra máquina virtual de la misma red.

* Conectar una red privada, como la red local, a la red virtual. Esta configuración permite a los clientes de la red local trabajar directamente con Kafka. Para habilitar esta configuración, realice las siguientes tareas:

    1. Cree una red virtual.
    2. Cree una puerta de enlace de red privada virtual que use una configuración de sitio a sitio. La configuración usada en este documento conecta a un dispositivo de puerta de enlace de red privada virtual en la red local.
    3. Cree un servidor DNS en la red virtual.
    4. Configure el reenvío entre el servidor DNS de cada red.
    5. Instale Kafka en HDInsight en la red virtual.

    Para más información, vea la sección [Conexión a Kafka desde una red local](#on-premises). 

* Conectar equipos individuales a la red virtual mediante una puerta de enlace de red privada virtual y un cliente de red privada virtual. Para habilitar esta configuración, realice las siguientes tareas:

    1. Cree una red virtual.
    2. Cree una puerta de enlace de red privada virtual que use una configuración de punto a sitio. Esta configuración proporciona un cliente VPN que se puede instalar en los clientes Windows.
    3. Instale Kafka en HDInsight en la red virtual.
    4. Configurar Kafka para anunciar direcciones IP. Esta configuración permite al cliente conectarse mediante direcciones IP en lugar de nombres de dominio.
    5. Descargue y use el cliente de VPN en el sistema de desarrollo.

    Para más información, vea la sección [Conexión a Kafka con un cliente VPN](#vpnclient).

    > [!WARNING]
    > Esta configuración solo se recomienda para fines de desarrollo debido a las limitaciones siguientes:
    >
    > * Cada cliente debe conectarse con un cliente de software de VPN. Azure solo proporciona un cliente basado en Windows.
    > * El cliente VPN no pasa las solicitudes de resolución de nombres a la red virtual, por lo que debe usar direcciones IP para comunicarse con Kafka. La comunicación IP exige configuración adicional en el clúster de Kafka.

Para más información sobre cómo usar HDInsight en una máquina virtual, consulte [Extensión de HDInsight con redes virtuales de Azure](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Conexión a Kafka desde una red local

Para crear un clúster de Kafka que se comunique con la red local, siga los pasos del documento [Connect HDInsight to your on-premises network (Conexión de HDInsight a la red local)](./../connect-on-premises-network.md).

> [!IMPORTANT]
> Al crear el clúster de HDInsight, seleccione el tipo de clúster __Kafka__.

Estos pasos crean la siguiente configuración:

* Azure Virtual Network
* Puerta de enlace de VPN de sitio a sitio
* Cuenta de Azure Storage (usada por HDInsight)
* Kafka en HDInsight

Para comprobar que un cliente de Kafka puede conectarse al clúster desde el entorno local, siga los pasos de la sección [Ejemplo: cliente de Python](#python-client).

## <a id="vpnclient"></a> Conexión a Kafka con un cliente VPN

Use los pasos de esta sección para crear la configuración siguiente:

* Azure Virtual Network
* Puerta de enlace de VPN de punto a sitio
* Cuenta de Azure Storage (usada por HDInsight)
* Kafka en HDInsight

1. Siga los pasos del documento [Working with self-signed certificates for Point-to-site connections (Funcionamiento de los certificados autofirmados para conexiones de punto a sitio)](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md). Este documento crea los certificados necesarios para la puerta de enlace.

2. Abra un símbolo del sistema de PowerShell y use el código siguiente para iniciar sesión en la suscripción de Azure:

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Use el código siguiente para crear variables que contengan la información de configuración:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Use el código siguiente para crear la red virtual y el grupo de recursos de Azure:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > Este proceso puede tardar varios minutos en completarse.

5. Use el código siguiente para crear el contenedor de blobs y la cuenta de Azure Storage:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Use el código siguiente para crear el clúster de HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Este proceso tarda unos 15 minutos en completarse.

8. Use el cmdlet siguiente para recuperar la dirección URL del cliente de VPN de Windows para la red virtual:

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Para descargar el cliente de VPN de Windows, use el identificador URI devuelto en el explorador web.

### <a name="configure-kafka-for-ip-advertising"></a>Configuración de Kafka para anunciar direcciones IP

De manera predeterminada, Zookeeper devuelve el nombre de dominio de los agentes de Kafka a los clientes. Esta configuración no funciona con el cliente de software de VPN, ya que no puede usar la resolución de nombres para entidades de la red virtual. Para esta configuración, use los pasos siguientes para configurar Kafka con el fin de anunciar direcciones IP en lugar de nombres de dominio:

1. En el explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplace __CLUSTERNAME__ por el nombre del clúster de Kafka en HDInsight.

    Cuando se le solicite, use el nombre de usuario y la contraseña HTTPS para el clúster. Aparece la interfaz de usuario web de Ambari para el clúster.

2. Para ver información sobre Kafka, seleccione __Kafka__ en la lista de la izquierda.

    ![Lista de servicios donde Kafka aparece resaltado](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Para ver la configuración de Kafka, seleccione __Configs__ (Configuraciones) en la parte superior central.

    ![Vínculos de configuraciones de Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Para encontrar la configuración __kafka-env__, escriba `kafka-env` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

    ![Configuración de Kafka para kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Para configurar Kafka y anunciar direcciones IP, agregue el texto siguiente en la parte inferior del campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar la interfaz en que escucha Kafka, escriba `listeners` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

7. Para configurar Kafka para que escuche en todas las interfaces de red, cambie el valor del campo __listeners__ (agentes de escucha) a `PLAINTEXT://0.0.0.0:9092`.

8. Use el botón __Guardar__ para guardar los cambios en la configuración. Escriba un mensaje de texto para describir los cambios. Seleccione __Aceptar__ una vez que se guarden los cambios.

    ![Botón Guardar configuración](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Para evitar errores al reiniciar Kafka, use el botón __Acciones de servicio__ y seleccione __Activar el modo de mantenimiento__. Seleccione Aceptar para completar esta operación.

    ![Acciones de servicio, en que Activar mantenimiento aparece resaltado](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Para reiniciar Kafka, use el botón __Reiniciar__ y seleccione __Restart All Affected__ (Reiniciar todos los elementos afectados). Confirme el reinicio y use el botón __Aceptar__ una vez que se complete la operación.

    ![Botón Reiniciar con la opción Restart All Affected resaltada](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Para deshabilitar el modo de mantenimiento, use el botón __Acciones de servicio__ y seleccione __Desactivar el modo de mantenimiento__. Seleccione **Aceptar** para completar esta operación.

### <a name="connect-to-the-vpn-gateway"></a>Conexión a la puerta de enlace de VPN

Para conectarse a la puerta de enlace de VPN desde un __cliente Windows__, use la sección __Conexión a Azure__ del documento [Configuración de una conexión de punto a sitio](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#clientcertificate).

## <a id="python-client"></a> Ejemplo: cliente de Python

Para validar la conectividad con Kafka, use los pasos siguientes para crear y ejecutar un productor y un consumidor:

1. Use uno de los métodos siguientes para recuperar el nombre de dominio completo (FQDN) y las direcciones IP de los nodos del clúster de Kafka:

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

    En este script se supone que `$resourceGroupName` es el nombre del grupo de recursos de Azure que contiene la red virtual.

    Guarde la información devuelta para su uso en los pasos siguientes.

2. Use el código siguiente para instalar el cliente [kafka-python](http://kafka-python.readthedocs.io/):

        pip install kafka-python

3. Para enviar datos a Kafka, use el código Python siguiente:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Sustituya las entradas `'kafka_broker'` por las direcciones devueltas en el paso 1 de esta sección:

    * Si usa un __cliente de software de VPN__, sustituya las entradas `kafka_broker` por la dirección IP de los nodos de trabajo.

    * Si tiene __habilitada la resolución de nombres a través de un servidor DNS personalizado__, sustituya las entradas `kafka_broker` por el FQDN de los nodos de trabajo.

    > [!NOTE]
    > Este código envía la cadena `test message` al tema `testtopic`. La configuración predeterminada de Kafka en HDInsight es crear el tema si no existe.

4. Para recuperar los mensajes de Kafka, use el código Python siguiente:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Sustituya las entradas `'kafka_broker'` por las direcciones devueltas en el paso 1 de esta sección:

    * Si usa un __cliente de software de VPN__, sustituya las entradas `kafka_broker` por la dirección IP de los nodos de trabajo.

    * Si tiene __habilitada la resolución de nombres a través de un servidor DNS personalizado__, sustituya las entradas `kafka_broker` por el FQDN de los nodos de trabajo.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo usar HDInsight con una red virtual, vea el documento [Extensión de las funcionalidades de HDInsight con Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md).

Para más información sobre cómo crear una instancia de Azure Virtual Network con puerta de enlace de VPN de punto a sitio, consulte los documentos siguientes:

* [Configuración de una conexión de punto a sitio mediante Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configuración de una conexión de punto a sitio mediante Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Para más información sobre cómo trabajar con Kafka en HDInsight, consulte los documentos siguientes:

* [Introducción a Kafka en HDInsight](apache-kafka-get-started.md)
* [Uso de creación de reflejos con Kafka en HDInsight](apache-kafka-mirroring.md)
