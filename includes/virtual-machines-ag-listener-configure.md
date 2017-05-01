El agente de escucha del grupo de disponibilidad es un nombre de red y una dirección IP en los que escucha el grupo de disponibilidad de SQL Server. Para crear el agente de escucha del grupo de disponibilidad, siga estos pasos:

1. [Obtener el nombre del recurso de red del clúster](#getnet).

1. [Agregar el punto de acceso cliente](#addcap).

1. [Configurar el recurso IP para el grupo de disponibilidad](#congroup).

1. [Hacer que el recurso del grupo de disponibilidad de SQL Server dependa del punto de acceso cliente](#dependencyGroup).

1. [Hacer que el recurso de punto de acceso cliente dependa de la dirección IP](#listname).

1. [Establecer los parámetros de clúster en PowerShell](#setparam).

En las siguientes secciones, se proporcionan instrucciones detalladas de cada uno de estos pasos. 

#### <a name="getnet"></a>Obtener el nombre del recurso de red del clúster

1. Use RDP para conectarse a la máquina virtual de Azure que hospeda la réplica principal. 

1. Abra el Administrador de clústeres de conmutación por error.

1. Seleccione el nodo **Redes** y anota el nombre de red del clúster. Use este nombre en la variable `$ClusterNetworkName` del script de PowerShell.

   En la siguiente imagen el nombre de red del clúster es **Cluster Network 1**:

   ![Nombre de red del clúster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

#### <a name="addcap"></a>Agregar el punto de acceso cliente

El punto de acceso cliente es el nombre de red que las aplicaciones utilizan para conectarse a las bases de datos en un grupo de disponibilidad. Cree el punto de acceso cliente en el Administrador de clústeres de conmutación por error. 

1. Amplía el nombre del clúster y haz clic en **Funciones**.

1. En el panel **Roles**, haga clic en el nombre del grupo de disponibilidad y seleccione **Agregar recurso** > **Punto de acceso cliente**.

   ![Punto de acceso cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

1. En el cuadro **Nombre**, cree un nombre para este nuevo agente de escucha. 

   Las aplicaciones utilizarán el nombre del nuevo agente de escucha como nombre de red para conectarse a las bases de datos del grupo de disponibilidad de SQL Server.
   
   Para terminar de crear el agente de escucha, haga clic en **Siguiente** dos veces y, a continuación, en **Finalizar**. No pongas el agente de escucha o el recurso en línea en este momento.
   
#### <a name="congroup"></a>Configurar el recurso IP para el grupo de disponibilidad

1. Haga clic en la pestaña **Recursos** y expanda el punto de acceso cliente que acaba de crear. El punto de acceso cliente está desconectado.

   ![Punto de acceso cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

1. Haga clic con el botón derecho en el recurso de IP y, después, en Propiedades. Anote el nombre de la dirección IP. Use este nombre en la variable `$IPResourceName` del script de PowerShell.

1. En **Dirección IP**, haga clic en **Dirección IP estática**. Establezca la dirección IP en la misma dirección que usó cuando estableció la dirección del equilibrador de carga en Azure Portal.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

<!-----------------------I don't see this option on server 2016
1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
------------------------->

#### <a name = "dependencyGroup"></a>Hacer que el recurso del grupo de disponibilidad de SQL Server dependa del punto de acceso cliente

1. En el Administrador de clústeres de conmutación por error, haga clic en **Roles** y en el grupo de disponibilidad.

1. En la pestaña **Recursos**, haga clic en el grupo de recursos de disponibilidad bajo **Nombre del servidor** y haga clic en **Propiedades**. 

1. En la pestaña de dependencias, agregue el recurso de nombre. Este recurso es el punto de acceso cliente. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

1. Haga clic en **Aceptar**.

#### <a name="listname"></a>Hacer que el recurso de punto de acceso cliente dependa de la dirección IP

1. En el Administrador de clústeres de conmutación por error, haga clic en **Roles** y en el grupo de disponibilidad. 

1. En la pestaña **Recursos**, haga clic en el recurso de punto de acceso cliente bajo **Nombre del servidor** y haga clic en **Propiedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

1. Haz clic en la pestaña **Dependencias** . Establezca una dependencia en el nombre del recurso del agente de escucha. Si aparecen varios recursos, comprueba que las direcciones IP tienen dependencias OR, y no AND. Haga clic en **Aceptar**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

1. Haz clic en el nombre del agente de escucha y luego haz clic en **Poner en línea**. 

#### <a name="setparam"></a>Establecer los parámetros de clúster en PowerShell

1. Copie el siguiente script de PowerShell en uno de los servidores SQL Server. Actualice las variables para su entorno.     
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

2. Ejecute el script de PowerShell en uno de los nodos del clúster para establecer los parámetros del clúster.  

> [!NOTE]
> Si los servidores SQL Server se encuentran en distintas regiones, debe ejecutar el script de PowerShell dos veces. La primera vez, utilice `$ILBIP` y `$ProbePort` en la primera región. La segunda vez, utilice `$ILBIP` y `$ProbePort` en la segunda región. El nombre de red del clúster y el nombre del recurso IP del clúster son iguales. 


