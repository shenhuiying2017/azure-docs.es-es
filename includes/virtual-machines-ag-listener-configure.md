El agente de escucha del grupo de disponibilidad es un nombre de red y una dirección IP en los que escucha el grupo de disponibilidad de SQL Server. Para crear el agente de escucha del grupo de disponibilidad, haga lo siguiente:

1. <a name="getnet"></a>Obtenga el nombre del recurso de red del clúster.

    a. Use RDP para conectarse a la máquina virtual de Azure que hospeda la réplica principal. 

    b. Abra el Administrador de clústeres de conmutación por error.

    c. Seleccione el nodo **Redes** y anota el nombre de red del clúster. Use este nombre en la variable `$ClusterNetworkName` del script de PowerShell. En la siguiente imagen el nombre de red del clúster es **Cluster Network 1**:

   ![Nombre de red del clúster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Agregue el punto de acceso cliente  
    El punto de acceso cliente es el nombre de red que las aplicaciones utilizan para conectarse a las bases de datos en un grupo de disponibilidad. Cree el punto de acceso cliente en el Administrador de clústeres de conmutación por error.

    a. Amplía el nombre del clúster y haz clic en **Funciones**.

    b. En el panel **Roles**, haga clic con el botón derecho en el nombre del grupo de disponibilidad y después seleccione **Agregar recurso** > **Punto de acceso cliente**.

   ![Punto de acceso cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. En el cuadro **Nombre**, cree un nombre para este nuevo agente de escucha. 
   Las aplicaciones utilizarán el nombre del nuevo agente de escucha como nombre de red para conectarse a las bases de datos del grupo de disponibilidad de SQL Server.
   
    d. Para terminar de crear el agente de escucha, haga clic en **Siguiente** dos veces y, a continuación, en **Finalizar**. No pongas el agente de escucha o el recurso en línea en este momento.

3. <a name="congroup"></a>Configure el recurso IP para el grupo de disponibilidad.

    a. Haga clic en la pestaña **Recursos** y expanda el punto de acceso cliente que acaba de crear.  
    El punto de acceso cliente está desconectado.

   ![Punto de acceso cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Haga clic con el botón derecho en el recurso de IP y, después, en Propiedades. Tenga en cuenta el nombre de la dirección IP y úselo en la variable `$IPResourceName` en el script de PowerShell.

    c. En **Dirección IP**, haga clic en **Dirección IP estática**. Establezca la dirección IP como la misma dirección que usó cuando estableció la dirección del equilibrador de carga en Azure Portal.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Haga que el recurso del grupo de disponibilidad de SQL Server dependa del punto de acceso cliente.

    a. En el Administrador de clústeres de conmutación por error, haga clic en **Roles** y, después, en el grupo de disponibilidad.

    b. En la pestaña **Recursos**, en **Otros recursos**, haga clic con el botón derecho en el grupo de recursos de disponibilidad y después haga clic en **Propiedades**. 

    c. En la pestaña Dependencias, agregue el nombre del recurso de punto de acceso cliente (el agente de escucha).

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Haga clic en **Aceptar**.

5. <a name="listname"></a>Haga que el recurso de punto de acceso cliente dependa de la dirección IP.

    a. En el Administrador de clústeres de conmutación por error, haga clic en **Roles** y, después, en el grupo de disponibilidad. 

    b. En la pestaña **Recursos**, haga clic en el recurso de punto de acceso cliente bajo **Nombre del servidor** y después haga clic en **Propiedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Haz clic en la pestaña **Dependencias** . Compruebe que la dirección IP es una dependencia. Si no es así, establezca una dependencia en la dirección IP. Si aparecen varios recursos, comprueba que las direcciones IP tienen dependencias OR, y no AND. Haga clic en **Aceptar**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Haga clic con el botón derecho en el nombre del agente de escucha y luego haga clic en **Poner en línea**. 

    >[!TIP]
    >Puede validar que las dependencias estén configuradas correctamente. En el Administrador de clústeres de conmutación por error, vaya a Roles, haga clic con el botón derecho en el grupo de disponibilidad, haga clic en **Más acciones** y, después, haga clic en **Mostrar informe de dependencias**. Cuando las dependencias estén configuradas correctamente, el grupo de disponibilidad depende en el nombre de red y el nombre de red depende de la dirección IP. 


6. <a name="setparam"></a>Establezca los parámetros de clúster en PowerShell.
    
    a. Copie el siguiente script de PowerShell en una de las instancias de SQL Server. Actualice las variables para su entorno.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Ejecute el script de PowerShell en uno de los nodos del clúster para establecer los parámetros del clúster.  

    > [!NOTE]
    > Si las instancias de SQL Server se encuentran en distintas regiones, debe ejecutar el script de PowerShell dos veces. La primera vez, utilice `$ILBIP` y `$ProbePort` en la primera región. La segunda vez, `$ILBIP` y `$ProbePort` en la segunda región. El nombre de red del clúster y el nombre del recurso IP del clúster son iguales. 
