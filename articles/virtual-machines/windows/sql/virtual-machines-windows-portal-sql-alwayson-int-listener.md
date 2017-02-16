---
title: "Creación del agente de escucha de grupo de disponibilidad de SQL Server - Azure Virtual Machines | Microsoft Docs"
description: Instrucciones paso a paso para crear un agente de escucha en un grupo de disponibilidad AlwaysOn para SQL Server en Azure Virtual Machines
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn en Azure
En este tema se explica cómo puede crear un equilibrador de carga interno para un grupo de disponibilidad de SQL Server AlwaysOn implementado en máquinas virtuales de Azure que se ejecute en el modelo de Resource Manager. Cuando las instancias de SQL Server están implementadas en máquinas virtuales de Azure, los grupos de disponibilidad necesitan un equilibrador de carga. El equilibrador de carga almacena la dirección IP del agente de escucha del grupo de disponibilidad. Si un grupo de disponibilidad abarca varias regiones, cada región necesitará su propio equilibrador de carga.

Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server implementado en las máquinas virtuales de Azure con el modelo de Resource Manager. Las dos máquinas virtuales de SQL Server deben pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad en Azure Resource Manager. Esta plantilla crea automáticamente el equilibrador de carga interno. 

Si lo prefiere, puede [configurar manualmente un grupo de disponibilidad](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

En este tema, es necesario que los grupos de disponibilidad ya estén configurados.  

Temas relacionados:

* [Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configuración de una conexión entre dos redes virtuales mediante Azure Resource Manager y PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

A lo largo de este documento, va a crear y configurar un equilibrador de carga en Azure Portal. Una vez hecho esto, configurará el clúster para que utilice la dirección IP del equilibrador de carga para el agente de escucha del grupo de disponibilidad.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Creación y configuración del equilibrador de carga en el Portal de Azure
En esta parte de la tarea, realizará las siguientes operaciones en Azure Portal:

1. Creación del equilibrador de carga y configuración de la dirección IP
2. Configuración del grupo back-end
3. Elaboración del sondeo 
4. Configuración de las reglas de equilibrio de carga

> [!NOTE]
> Si los servidores SQL Server se encuentran en regiones y grupos de recursos diferentes, deberá realizar estas operaciones dos veces (una por cada grupo de recursos).
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Creación del equilibrador de carga y configuración de la dirección IP
El primer paso consiste en crear el equilibrador de carga. En el Portal de Azure, abra el grupo de recursos que contiene las máquinas virtuales de SQL Server. En el grupo de recursos, haga clic en **Agregar**.

1. Busque el **equilibrador de carga**. En los resultados, seleccione el **Load Balancer**, publicado por **Microsoft**.
1. En la hoja **Load Balancer**, haga clic en **Crear**.
1. En **Create load balancer** (Crear configurador de carga), configure el equilibrador de carga tal y como se explica a continuación:

   | Configuración | Valor |
   | --- | --- |
   | **Name** |Nombre de texto que representa el equilibrador de carga; Por ejemplo, **sqlLB**. |
   | **Tipo** |**Interno** |
   | **Red virtual** |Elija la red virtual en la que se encuentran los servidores SQL Server. |
   | **Subred** |Elija la subred en la que se encuentran los servidores SQL Server. |
   | **Asignación de dirección IP** |**Estática** |
   | **Dirección IP privada** |Especifique una dirección IP disponible de la subred. Utilícela cuando cree un agente de escucha en el clúster. Más adelante en este mismo artículo, hay un script de PowerShell donde tendrá que utilizar esta dirección para la variable `$ILBIP` . |
   | **Suscripción** |Si tiene varias suscripciones, puede aparecer este campo. Seleccione la suscripción que quiere asociar con este recurso. Normalmente, será la misma suscripción que se utiliza con todos los recursos del grupo de disponibilidad. |
   | **Grupos de recursos** |Elija el grupo de recursos en el que se encuentran los servidores SQL Server. |
   | **Ubicación** |Elija la ubicación de Azure en la que se encuentran los servidores SQL Server. |

1. Haga clic en **Crear**. 

Azure crea el equilibrador de carga. El equilibrador de carga pertenece a una red, una subred, una ubicación y un grupo de recursos específicos. Cuando Azure haya completado el equilibrador de carga, compruebe su configuración. 

### <a name="2-configure-the-backend-pool"></a>2. Configuración del grupo back-end
El siguiente paso consiste en crear un grupo de direcciones de back-end. En Azure, el grupo de direcciones de back-end se denomina *grupo de back-end*. En este caso, el grupo de back-end contiene las direcciones de los dos servidores SQL Server del grupo de disponibilidad. 

1. En el grupo de recursos, haga clic en el equilibrador de carga que ha creado. 
1. En **Configuración**, haga clic en **Grupos de back-end**.
1. En **Grupos de back-end**, haga clic en **Agregar** para crear un grupo de direcciones de back-end. 
1. En **Agregar grupo back-end** under **Name**, especifique un nombre para el grupo de back-end.
1. En **Máquinas virtuales**, haga clic en **+ Agregar una máquina virtual**. 
1. En **Elegir máquinas virtuales**, haga clic en **Elegir un conjunto de disponibilidad** y especifique el conjunto de disponibilidad al que pertenecen las máquinas virtuales de SQL Server.
1. Una vez elegido el conjunto de disponibilidad, haga clic en **Elegir las máquinas virtuales**. Haga clic en las dos máquinas virtuales en las que se hospedan las instancias de SQL Server del grupo de disponibilidad. Haga clic en **Seleccionar**. 
1. Haga clic en **Aceptar** para cerrar las hojas **Elegir máquinas virtuales** y **Agregar grupo back-end**. 

Azure actualiza la configuración del grupo de direcciones de back-end. Ahora, el conjunto de disponibilidad tiene un grupo de dos servidores SQL Server.

### <a name="3-create-a-probe"></a>3. Elaboración de un sondeo
El siguiente paso consiste en crear un sondeo. Este sondeo establece el modo en que Azure va a comprobar cuál de los servidores SQL Server es el propietario actual del agente de escucha del grupo de disponibilidad. Azure analiza el servició con arreglo a la dirección IP de un puerto que estableció al crear el sondeo.

1. En la hoja **Configuración** del equilibrador de carga, haga clic en **Sondeos de estado**. 
1. En la hoja **Sondeos de estado**, haga clic en **Agregar**.
1. Configure el sondeo en la hoja **Agregar sondeo** . Utilice los valores siguientes para configurar el sondeo.

   | Configuración | Valor |
   | --- | --- |
   | **Name** |Nombre de texto que representa el sondeo. Por ejemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Puerto** |Puede usar cualquier puerto que esté disponible; Por ejemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Umbral incorrecto** |*2* |

1.  Haga clic en **Aceptar**. 

> [!NOTE]
> Asegúrese de que el puerto especificado esté abierto en el firewall de los dos servidores SQL Server. En estos dos servidores, es necesario definir una regla de entrada para el puerto TCP. Consulte [Agregar o editar regla de firewall](http://technet.microsoft.com/library/cc753558.aspx) para obtener más información. 
> 
> 

Azure crea el sondeo. Azure usa el sondeo para comprobar qué servidor SQL Server tiene el agente de escucha del grupo de disponibilidad.

### <a name="4-set-the-load-balancing-rules"></a>4. Configuración de las reglas de equilibrio de carga
Configure las reglas de equilibrio de carga. Las reglas de equilibrio de carga determinan cómo el equilibrador de carga enruta el tráfico a los servidores SQL Server. En este equilibrador de carga, habilite Direct Server Return, ya que solo uno de los dos servidores SQL Server puede ser el propietario del recurso del agente de escucha del grupo de disponibilidad simultáneamente.

1. En la hoja **Configuración** del equilibrador de carga, haga clic en **Reglas de equilibrio de carga**. 
1. En la hoja **Reglas de equilibrio de carga**, haga clic en **Agregar**.
1. Utilice la hoja **Add load balancing rules** (Agregar reglas de equilibrio de carga) para configurar la regla de equilibrio de carga. Use la configuración siguiente: 

   | Configuración | Valor |
   | --- | --- |
   | **Name** |Nombre de texto que representa las reglas de equilibrio de carga. Por ejemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Puerto** |*1433* |
   | **Puerto back-end** |*1433*. Este valor se ignorará porque la regla usa **IP flotante (Direct Server Return)**. |
   | **Sondeo** |Utilice el nombre del sondeo que creó para este equilibrador de carga. |
   | **Persistencia de la sesión** |**None** |
   | **Tiempo de espera de inactividad (minutos)** |*4* |
   | **IP flotante (Direct Server Return)** |**Enabled** |

   > [!NOTE]
   > Tal vez necesite desplazarse hacia abajo en la hoja para ver todas las opciones.
   > 

1. Haga clic en **Aceptar**. 
1. Azure configura la regla de equilibrio de carga. Ahora, el equilibrador de carga está configurado para enrutar el tráfico al servidor SQL Server que hospeda el agente de escucha del grupo de disponibilidad. 

En este punto, el grupo de recursos dispone de un equilibrador de carga que tiene conexión con las dos máquinas de SQL Server. El equilibrador de carga también contiene una dirección IP del agente de escucha del grupo de disponibilidad de SQL Server AlwaysOn, por lo que cualquier máquina puede responder a las solicitudes de los grupos de disponibilidad.

> [!NOTE]
> Si los servidores SQL Server se encuentran en dos regiones diferentes, repita los pasos en la otra región. Cada una de las regiones necesita su propio equilibrador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure el clúster para que utilice la dirección IP del equilibrador de carga.
El siguiente paso consiste en configurar el agente de escucha del clúster y conectarlo. Siga estos pasos: 

1. Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error 
2. Conexión del agente de escucha

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error
En este paso, creas manualmente el agente de escucha del grupo de disponibilidad en el Administrador de clústeres de conmutación por error y en SQL Server Management Studio (SSMS).

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>Comprobación de la configuración del agente de escucha

Si los recursos y las dependencias de clúster están configurados correctamente, tendría que ver el agente de escucha en las instancias de SQL Server Management Studio. Siga estos pasos para establecer el puerto de escucha:

1. Abra SQL Server Management Studio y conéctese a la réplica principal.
2. Vaya a **Alta disponibilidad de AlwaysOn** | **Grupos de disponibilidad** | **Agentes de escucha del grupo de disponibilidad**. 
1. Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.
1. En el cuadro **Puerto**, especifique el número de puerto del agente de escucha del grupo de disponibilidad mediante el valor $EndpointPort que ha establecido antes (1433 era el valor predeterminado) y haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad de SQL Server AlwaysOn en las máquinas virtuales de Azure que se ejecutan en el modo de Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Comprobación de la conexión con el agente de escucha
Para probar la conexión:

1. Conéctese con RDP a un servidor SQL Server que esté en la misma red virtual, pero que no sea el propietario de la réplica; por ejemplo, a otro servidor SQL Server del clúster.
2. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, el siguiente script establece una conexión **sqlcmd** con la réplica principal a través del agente de escucha utilizando la autenticación de Windows:
   
        sqlcmd -S <listenerName> -E

La conexión SQLCMD se establece automáticamente con la instancia de SQL Server en la que se hospede la réplica principal. 

## <a name="guidelines-and-limitations"></a>Pautas y limitaciones
Cuando utilice un equilibrador de carga interno, tenga en cuenta las siguientes instrucciones que se aplican al agente de escucha del grupo de disponibilidad de Azure:

* Solo se admite un agente de escucha de grupo de disponibilidad interno por cada servicio en la nube, ya que el agente de escucha se configura con arreglo al equilibrador de carga y solo hay uno interno. Sin embargo, es posible crear varios agentes de escucha externos. 
* Como solo hay un equilibrador de carga interno, el acceso al agente de escucha se realizará desde la misma red virtual.


<!--HONumber=Jan17_HO2-->


