---
title: "Conexión a Kafka en HDInsight mediante redes virtuales - Azure | Microsoft Docs"
description: "Obtenga información sobre cómo conectarse a Kafka de manera remota en HDInsight mediante el cliente kafka-python. La configuración que se indica en este documento usa HDInsight dentro de una instancia de Azure Virtual Network. El cliente remoto se conecta a la red virtual mediante una puerta de enlace de VPN de punto a sitio."
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
ms.date: 04/18/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9489fdc3c5388a7510bc4411b4abb05fa72fbf4f
ms.contentlocale: es-es
ms.lasthandoff: 05/08/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Conexión a Kafka en HDInsight (versión preliminar) mediante una instancia de Azure Virtual Network

Obtenga información sobre cómo conectarse a Kafka en HDInsight mediante redes virtuales de Azure. Los clientes de Kafka (productores y consumidores) pueden ejecutarse directamente en HDInsight o en sistemas remotos. Los clientes remotos deben conectarse a Kafka en HDInsight mediante una instancia de Azure Virtual Network. Use la información que se entrega en este documento para entender cómo los clientes remotos se pueden conectar a HDInsight mediante las redes virtuales de Azure.

> [!IMPORTANT]
> Varias de las configuraciones que se describen en este documento se pueden usar con clientes Windows, macOS o Linux. Sin embargo, el ejemplo de punto a sitio que se incluye solo proporciona un cliente VPN para Windows.
>
> El ejemplo también usa un cliente Python ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) para comprobar la comunicación con Kafka en HDInsight.

## <a name="architecture-and-planning"></a>Arquitectura y planeación

Los clústeres de HDInsight están protegidos dentro de una instancia de Azure Virtual Network y solo permiten el tráfico SSH y HTTPS entrante. El tráfico llega a través de una puerta de enlace pública que no enruta el tráfico desde los clientes Kafka. Para tener acceso a Kafka desde un cliente remoto, debe crear una instancia de Azure Virtual Network que proporcione una puerta de enlace de red privada virtual (VPN). Una vez que haya configurado la puerta de enlace y la red virtual, instale HDInsight en la red virtual y conéctelo a ella a través de la puerta de enlace de VPN.

![Diagrama de HDInsight dentro de una instancia de Azure Virtual Network con un cliente conectado a través de VPN](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

En la lista siguiente se muestra información sobre el proceso de usar Kafka en HDInsight con una red virtual:

1. Cree una red virtual. Para información específica sobre cómo usar HDInsight con redes virtuales de Azure, consulte el documento sobre la [extensión de HDInsight mediante una instancia de Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

2. (Opcional) Cree una máquina virtual de Azure dentro de la red virtual e instale ahí un servidor DNS personalizado. Este servidor DNS se usa para habilitar la resolución de nombres para clientes remotos en una configuración de sitio a sitio o de red virtual a red virtual. Para más información, consulte el documento sobre [resolución de nombres para máquinas virtuales y servicios en la nube](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

3. Cree una puerta de enlace de VPN para la red virtual. Para más información sobre las configuraciones de puerta de enlace de VPN, consulte el documento de [información sobre VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

4. Cree HDInsight dentro de la red virtual. Si configuró un servidor DNS personalizado para la red, HDInsight se configura automáticamente para usarlo.

5. (Opcional) Si no usó un servidor DNS personalizado y no tiene resolución de nombres entre los clientes y la red virtual, debe configurar Kafka para publicidad de direcciones IP. Para más información, consulte la sección [Configuración de Kafka para anunciar direcciones IP](#configure-kafka-for-ip-advertising) de este documento.

## <a name="create-using-powershell"></a>Creación mediante PowerShell

Los pasos de esta sección crean la configuración siguiente con [Azure PowerShell](/powershell/azure/overview):

* Red virtual
* Puerta de enlace de VPN de punto a sitio
* Cuenta de Azure Storage (usada por HDInsight)
* Kafka en HDInsight

1. Siga los pasos que aparecen en el documento [Funcionamiento de los certificados autofirmados para conexiones de punto a sitio](../vpn-gateway/vpn-gateway-certificates-point-to-site.md) para crear los certificados que se necesitan para la puerta de enlace.

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
    $hdiVersion = "3.5"
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
  > Este proceso tarda unos 20 minutos en completarse.

8. Use el cmdlet siguiente para recuperar la dirección URL del cliente de VPN de Windows para la red virtual:

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Para descargar el cliente de VPN de Windows, use el identificador URI devuelto en el explorador web.

## <a name="configure-kafka-for-ip-advertising"></a>Configuración de Kafka para anunciar direcciones IP

De manera predeterminada, Zookeeper devuelve el nombre de dominio de los agentes de Kafka a los clientes. Esta configuración no funciona para el cliente de VPN, por lo que no puede usar la resolución de nombres para entidades de la red virtual. Use los pasos siguientes para configurar Kafka en HDInsight y anunciar direcciones IP en lugar de nombres de dominio:

1. En el explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplace __CLUSTERNAME__ por el nombre del clúster de Kafka en HDInsight.

    Cuando se le solicite, use el nombre de usuario y la contraseña HTTPS para el clúster. Aparece la interfaz de usuario web de Ambari para el clúster.

2. Para ver información sobre Kafka, seleccione __Kafka__ en la lista de la izquierda.

    ![Lista de servicios donde Kafka aparece resaltado](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Para ver la configuración de Kafka, seleccione __Configs__ (Configuraciones) en la parte superior central.

    ![Vínculos de configuraciones de Kafka](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Para encontrar la configuración __kafka-env__, escriba `kafka-env` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

    ![Configuración de Kafka para kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Para configurar Kafka y anunciar direcciones IP, agregue el texto siguiente en la parte inferior del campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar la interfaz en que escucha Kafka, escriba `listeners` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

7. Para configurar Kafka para que escuche en todas las interfaces de red, cambie el valor del campo __listeners__ (agentes de escucha) a `PLAINTEXT://0.0.0.0:92092`.

8. Use el botón __Guardar__ para guardar los cambios en la configuración. Escriba un mensaje de texto para describir los cambios. Seleccione __Aceptar__ una vez que se guarden los cambios.

    ![Botón Guardar configuración](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Para evitar errores al reiniciar Kafka, use el botón __Acciones de servicio__ y seleccione __Activar el modo de mantenimiento__. Seleccione Aceptar para completar esta operación.

    ![Acciones de servicio, en que Activar mantenimiento aparece resaltado](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Para reiniciar Kafka, use el botón __Reiniciar__ y seleccione __Restart All Affected__ (Reiniciar todos los elementos afectados). Confirme el reinicio y use el botón __Aceptar__ una vez que se complete la operación.

    ![Botón Reiniciar con la opción Restart All Affected resaltada](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. Para deshabilitar el modo de mantenimiento, use el botón __Acciones de servicio__ y seleccione __Desactivar el modo de mantenimiento__. Seleccione **Aceptar** para completar esta operación.

## <a name="connect-to-the-vpn-gateway"></a>Conexión a la puerta de enlace de VPN

Para conectarse a la puerta de enlace de VPN desde un __cliente Windows__, use la sección __Conexión a Azure__ del documento [Configuración de una conexión de punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameclientcertificatea7---install-an-exported-client-certificate).

## <a name="remote-kafka-client"></a>Cliente Kafka remoto

Para conectarse a Kafka desde el equipo cliente, debe usar la dirección IP de los agentes de Kafka o los nodos de Zookeeper (cualquiera sea la opción que requiere el cliente). Use los pasos siguientes para recuperar la dirección IP de los agentes de Kafka y, luego, úselos desde una aplicación Python

1. Use el script siguiente para recuperar las direcciones IP de los nodos del clúster:

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    En este script se supone que `$resourceGroupName` es el nombre del grupo de recursos de Azure que contiene la red virtual. La salida del script es similar al texto siguiente:

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Si el cliente Kafka usa nodos de Zookeeper en lugar de agentes de Kafka, reemplace `*workernode*` por `*zookeepernode*` en el script de PowerShell.

    > [!WARNING]
    > Si escala el clúster o los nodos presentan un error y es necesario reemplazarlos, es posible que las direcciones IP cambien. Actualmente, no hay direcciones IP específicas asignadas previamente para los nodos de un clúster de HDInsight.

2. Use el código siguiente para instalar el cliente [kafka-python](http://kafka-python.readthedocs.io/):

        pip install kafka-python

3. Para enviar datos a Kafka, use el código Python siguiente:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Reemplace las entradas `'ip_address'` por las direcciones devueltas del paso 1 de esta sección.

    > [!NOTE]
    > Este código envía la cadena `test message` al tema `testtopic`. La configuración predeterminada de Kafka en HDInsight es crear el tema si no existe.

4. Para recuperar los mensajes de Kafka, use el código Python siguiente:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Reemplace las entradas `'ip_address'` por las direcciones devueltas del paso 1 de esta sección. La salida contiene el mensaje de prueba que se envió al productor en el paso anterior.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas para conectarse a la red virtual o para conectarse a HDInsight a través de la red, consulte el documento [Solución de problemas de las conexiones y la puerta de enlace de Virtual Network](../network-watcher/network-watcher-troubleshoot-manage-powershell.md) para instrucciones.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo crear una instancia de Azure Virtual Network con puerta de enlace de VPN de punto a sitio, consulte los documentos siguientes:

* [Configuración de una conexión de punto a sitio mediante Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configuración de una conexión de punto a sitio mediante Azure PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Para más información sobre cómo trabajar con Kafka en HDInsight, consulte los documentos siguientes:

* [Introducción a Kafka en HDInsight](hdinsight-apache-kafka-get-started.md)
* [Uso de creación de reflejos con Kafka en HDInsight](hdinsight-apache-kafka-mirroring.md)

