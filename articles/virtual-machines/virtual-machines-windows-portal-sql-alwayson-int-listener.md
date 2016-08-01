<properties
   pageTitle="Creación de un agente de escucha para un grupo de disponibilidad AlwaysOn de SQL Server en máquinas virtuales de Azure"
   description="Instrucciones paso a paso para crear un agente de escucha en un grupo de disponibilidad AlwaysOn de SQL Server con máquinas virtuales de Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure

En este tema, se explica cómo puede crear un equilibrador de carga interno para un grupo de disponibilidad de SQL Server AlwaysOn implementado en máquinas virtuales de Azure que se ejecute en el modelo de Resource Manager. Cuando las instancias de SQL Server están implementadas en máquinas virtuales de Azure, los grupos de disponibilidad AlwaysOn necesitan un equilibrador de carga. El equilibrador de carga almacena la dirección IP del agente de escucha del grupo de disponibilidad. Si un grupo de disponibilidad abarca varias regiones, cada región necesitará su propio equilibrador de carga.

Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server AlwaysOn implementado en las máquinas virtuales de Azure con el modelo de Resource Manager. Las dos máquinas virtuales de SQL Server deben pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad AlwaysOn en Azure Resource Manager. Esta plantilla crea automáticamente el equilibrador de carga interno.

Si lo prefiere, puede [configurar manualmente un grupo de disponibilidad AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

En este tema, es necesario que los grupos de disponibilidad ya estén configurados.

Temas relacionados:

 - [Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
 
 - [Configuración de una conexión entre dos redes virtuales mediante Azure Resource Manager y PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## Pasos

A lo largo de este documento, va a crear y configurar un equilibrador de carga en el Portal de Azure. Una vez hecho esto, configurará el clúster para que utilice la dirección IP del equilibrador de carga en el agente de escucha del grupo de disponibilidad AlwaysOn.

## Creación y configuración del equilibrador de carga en el Portal de Azure

En esta parte de la tarea, realizará las siguientes operaciones en el Portal de Azure:

1. Creación del equilibrador de carga y configuración de la dirección IP

1. Configuración del grupo back-end

1. Elaboración del sondeo

1. Configuración de las reglas de equilibrio de carga

>[AZURE.NOTE] Si los servidores SQL Server se encuentran en regiones y grupos de recursos diferentes, deberá realizar estas operaciones dos veces (una por cada grupo de recursos).

## 1\. Creación del equilibrador de carga y configuración de la dirección IP

El primer paso consiste en crear el equilibrador de carga. En el Portal de Azure, abra el grupo de recursos que contiene las máquinas virtuales de SQL Server. En el grupo de recursos, haga clic en **Agregar**.

- Busque el **equilibrador de carga**. En los resultados, seleccione el **Equilibrador de carga**, publicado por **Microsoft**.

- En la hoja **Equilibrador de carga**, haga clic en **Crear**.

- En **Create load balancer** (Crear configurador de carga), configure el equilibrador de carga tal y como se explica a continuación:

| Configuración | Valor |
| ----- | ----- |
| **Name** | Nombre de texto que representa el equilibrador de carga; por ejemplo, **sqlLB**. |
| **Esquema** | **Interno** |
| **Red virtual** | Elija la red virtual en la que se encuentran los servidores SQL Server. |
| **Subred** | Elija la subred en la que se encuentran los servidores SQL Server. |
| **Suscripción** | Si tiene varias suscripciones, puede aparecer este campo. Seleccione la suscripción que quiere asociar con este recurso. Normalmente, será la misma suscripción que se utiliza con todos los recursos del grupo de disponibilidad. |
| **Grupos de recursos** | Elija el grupo de recursos en el que se encuentran los servidores SQL Server. | 
| **Ubicación** | Elija la ubicación de Azure en la que se encuentran los servidores SQL Server. |

- Haga clic en **Crear**.

Azure crea el equilibrador de carga que configuró en los pasos anteriores. El equilibrador de carga pertenece a una red, una subred, una ubicación y un grupo de recursos específicos. Cuando Azure haya completado el equilibrador de carga, compruebe su configuración.

Ahora, configure la dirección IP del equilibrador de carga.

- En la hoja **Configuración** del equilibrador de carga, haga clic en **Dirección IP**. En la hoja **Dirección IP**, se indica que se trata de un equilibrador de carga privado ubicado en la misma red virtual que los servidores SQL Server.

- Configure las opciones siguientes:

| Configuración | Valor |
| ----- | ----- |
| **Subred** | Elija la subred en la que se encuentran los servidores SQL Server. |
| **Asignación** | **Estática** |
| **Dirección IP** | Escriba una dirección IP virtual de la subred que no esté en uso. |

- Guarde la configuración

Ahora, el equilibrador de carga tiene una dirección IP. Guarde esta dirección. Tendrá que utilizarla cuando cree un agente de escucha en el clúster. Más adelante en este mismo artículo, hay un script de PowerShell donde tendrá que utilizar esta dirección para la variable `$ILBIP`.



## 2\. Configuración del grupo back-end

El siguiente paso consiste en crear un grupo de direcciones de back-end. En Azure, el grupo de direcciones de back-end se denomina *grupo de back-end*. En este caso, el grupo de back-end contiene las direcciones de los dos servidores SQL Server del grupo de disponibilidad.

- En el grupo de recursos, haga clic en el equilibrador de carga que ha creado.

- En **Configuración**, haga clic en **Grupos de back-end**.

- En **Grupos de direcciones de back-end**, haga clic en **Agregar** para crear un grupo de direcciones de back-end.

- En **Agregar grupo back-end**, en la opción **Nombre**, especifique un nombre para el grupo de back-end.

- En **Máquinas virtuales**, haga clic en **+ Agregar una máquina virtual**.

- En **Elegir máquinas virtuales**, haga clic en **Elegir un conjunto de disponibilidad** y especifique el conjunto de disponibilidad al que pertenecen las máquinas virtuales de SQL Server.

- Una vez elegido el conjunto de disponibilidad, haga clic en **Elegir las máquinas virtuales**. Haga clic en las dos máquinas virtuales en las que se hospedan las instancias de SQL Server del grupo de disponibilidad. Haga clic en **Seleccionar**.

- Haga clic en **Aceptar** para cerrar las hojas **Elegir máquinas virtuales** y **Agregar grupo back-end**.

Azure actualiza la configuración del grupo de direcciones de back-end. Ahora, el conjunto de disponibilidad tiene un grupo de dos servidores SQL Server.

## 3\. Elaboración de un sondeo

El siguiente paso consiste en crear un sondeo. Este sondeo establece el modo en que Azure va a comprobar cuál de los servidores SQL Server es el propietario actual del agente de escucha del grupo de disponibilidad. Azure analizará el servició con arreglo a la dirección IP de un puerto que estableció al crear el sondeo.

- En la hoja **Configuración** del equilibrador de carga, haga clic en **Sondeos**.

- En la hoja **Sondeos**, haga clic en **Agregar**.

- Configure el sondeo en la hoja **Agregar sondeo**. Utilice los valores siguientes para configurar el sondeo.

| Configuración | Valor |
| ----- | ----- |
| **Name** | Nombre de texto que representa el sondeo. Por ejemplo, **SQLAlwaysOnEndPointProbe**. |
| **Protocolo** | **TCP** |
| **Puerto** | Puede usar cualquier puerto que esté disponible; por ejemplo, *59999*. |
| **Intervalo** | *5* | 
| **Umbral incorrecto** | *2* | 

- Haga clic en **Aceptar**.

>[AZURE.NOTE] Asegúrese de que el puerto especificado esté abierto en el firewall de los dos servidores SQL Server. En estos dos servidores, es necesario definir una regla de entrada para el puerto TCP. Consulte [Agregar o editar regla de firewall](http://technet.microsoft.com/library/cc753558.aspx) para más información.

Azure crea el sondeo. Azure usará el sondeo para comprobar qué servidor SQL Server tiene el agente de escucha del grupo de disponibilidad.

## 4\. Configuración de las reglas de equilibrio de carga

Configure las reglas de equilibrio de carga. Las reglas de equilibrio de carga determinan cómo el equilibrador de carga enruta el tráfico a los servidores SQL Server. En este equilibrador de carga, podrá habilitar Direct Server Return, ya que solo uno de los dos servidores SQL Server puede ser el propietario del recurso del agente de escucha del grupo de disponibilidad.

- En la hoja **Configuración** del equilibrador de carga, haga clic en **Reglas de equilibrio de carga**.

- En la hoja **Reglas de equilibrio de carga**, haga clic en **Agregar**.

- Utilice la hoja **Add load balancing rules** (Agregar reglas de equilibrio de carga) para configurar la regla de equilibrio de carga. Use la configuración siguiente:

| Configuración | Valor |
| ----- | ----- |
| **Name** | Nombre de texto que representa las reglas de equilibrio de carga. Por ejemplo, **SQLAlwaysOnEndPointListener**. |
| **Protocolo** | **TCP** |
| **Puerto** | *1433* |
| **Puerto back-end** | *1433*. Tenga en cuenta que esta opción estará deshabilitada porque la regla usa **IP flotante (Direct Server Return)**. |
| **Sondeo** | Utilice el nombre del sondeo que creó para este equilibrador de carga. |
| **Session persistance (Persistencia de sesión)** | **None** | 
| **Tiempo de espera de inactividad (minutos)** | *4* | 
| **IP flotante (Direct Server Return)** | **Enabled** | 

 >[AZURE.NOTE] Tal vez necesite desplazarse hacia abajo en la hoja para ver todas las opciones.

- Haga clic en **Aceptar**.

- Azure configura la regla de equilibrio de carga. Ahora, el equilibrador de carga está configurado para enrutar el tráfico al servidor SQL Server que hospeda el agente de escucha del grupo de disponibilidad.

En este punto, el grupo de recursos dispone de un equilibrador de carga que tiene conexión con las dos máquinas de SQL Server. El equilibrador de carga también contiene una dirección IP del agente de escucha del grupo de disponibilidad de SQL Server AlwaysOn, por lo que cualquier máquina puede responder a las solicitudes de los grupos de disponibilidad.

>[AZURE.NOTE] Si los servidores SQL Server se encuentran en dos regiones diferentes, repita los pasos en la otra región. Cada una de las regiones necesita su propio equilibrador de carga.

## Configure el clúster para que utilice la dirección IP del equilibrador de carga. 

El siguiente paso consiste en configurar el agente de escucha del clúster y conectarlo. Para ello, siga estos pasos:

1. Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error

1. Conexión del agente de escucha

## 1\. Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error

En este paso, creas manualmente el agente de escucha del grupo de disponibilidad en el Administrador de clústeres de conmutación por error y en SQL Server Management Studio (SSMS).

- Use RDP para conectarse a la máquina virtual de Azure que hospeda la réplica principal.

- Abra el Administrador de clústeres de conmutación por error.

- Seleccione el nodo **Redes** y anota el nombre de red del clúster. Este nombre se utilizará en la variable `$ClusterNetworkName` del script de PowerShell.

- Amplía el nombre del clúster y haz clic en **Funciones**.

- En el panel **Roles**, haz clic en el nombre del grupo de disponibilidad y después selecciona **Agregar recurso** > **Punto de acceso cliente**.

- En la casilla **Nombre**, crea un nombre para este nuevo agente de escucha, después haz clic en **Siguiente** dos veces y haz clic en **Finalizar**. No pongas el agente de escucha o el recurso en línea en este momento.

 >[AZURE.NOTE] Las aplicaciones utilizarán el nombre del nuevo agente de escucha como nombre de red para conectarse a las bases de datos del grupo de disponibilidad de SQL Server.

- Haz clic en la pestaña **Recursos** y después amplía el punto de acceso de cliente que acabas de crear. Haga clic con el botón derecho en el recurso de IP y, después, en Propiedades. Anote el nombre de la dirección IP. Este es el nombre que va a utilizar en la variable `$IPResourceName` del script de PowerShell.

- En **Dirección IP**, haga clic en **Dirección IP estática** y especifique la misma dirección que utilizó al definir la dirección IP del equilibrador de carga en el Portal de Azure. Habilite NetBIOS para esta dirección y haga clic en **Aceptar**. Repite este paso en cada recurso de IP si tu solución abarca varias redes virtuales de Azure.

- En el nodo de clúster donde actualmente se hospeda la réplica principal, abra una instancia de PowerShell ISE con privilegios elevados y pegue los siguientes comandos en un nuevo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Actualice las variables y ejecute el script de PowerShell para configurar la dirección IP y el puerto del nuevo agente de escucha.

 >[AZURE.NOTE] Si los servidores SQL Server se encuentran en distintas regiones, debe ejecutar el script de PowerShell dos veces. La primera vez, utilice el nombre de red del clúster, el nombre del recurso IP del clúster y la dirección IP del equilibrador de carga correspondientes al primer grupo de recursos. La segunda vez, utilice el nombre de red del clúster, el nombre del recurso IP del clúster y la dirección IP del equilibrador de carga correspondientes al segundo grupo de recursos.

Ahora, el clúster dispone de un recurso de agente de escucha del grupo de disponibilidad.

## 2\. Conexión del agente de escucha

Una vez que el recurso de agente de escucha del grupo de disponibilidad está configurado, puede ponerlo en línea. De este modo, las aplicaciones podrán conectarse a las bases de datos del grupo de disponibilidad con el agente de escucha.

- Desplázate atrás al Administrador de clústeres de conmutación por error. Amplia las **Funciones** y después resalta tu grupo de disponibilidad. En la pestaña **Recursos**, haga clic con el botón derecho en el nombre del agente de escucha y después haga clic en **Propiedades**.

- Haz clic en la pestaña **Dependencias**. Si aparecen varios recursos, comprueba que las direcciones IP tienen dependencias OR, y no AND. Haga clic en **Aceptar**.

- Haz clic en el nombre del agente de escucha y luego haz clic en **Poner en línea**.


- Una vez que el agente de escucha está en línea, en la pestaña de **Recursos**, haz clic con el botón derecho en el grupo de disponibilidad y haz clic en **Propiedades**.

- Crea una dependencia en el recurso del nombre del agente de escucha (no en el nombre de los recursos de dirección IP). Haga clic en **Aceptar**.


- Abra SQL Server Management Studio y conéctese a la réplica principal.


- Vaya a **Alta disponibilidad de AlwaysOn** | **Grupos de disponibilidad** | **Agentes de escucha del grupo de disponibilidad**.


- Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.


- En el cuadro **Puerto**, especifique el número de puerto del agente de escucha del grupo de disponibilidad utilizando el valor de $EndpointPort que estableció anteriormente (1433 era el valor predeterminado) y después haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad de SQL Server AlwaysOn en las máquinas virtuales de Azure que se ejecuta en el modo de Resource Manager.

## Comprobación de la conexión con el agente de escucha

Para probar la conexión:

1. Conéctese con RDP a un servidor SQL Server que esté en la misma red virtual, pero que no sea el propietario de la réplica; por ejemplo, a otro servidor SQL Server del clúster.

1. Compruebe la conexión con la utilidad **sqlcmd**. Por ejemplo, el siguiente script establece una conexión **sqlcmd** con la réplica principal a través del agente de escucha utilizando la autenticación de Windows:

        sqlcmd -S <listenerName> -E

La conexión SQLCMD se establece automáticamente con cualquiera de las instancias de SQL Server en las que se hospeda la réplica principal.

## Pautas y limitaciones

Cuando utilice un equilibrador de carga interno, tenga en cuenta las siguientes instrucciones que se aplican al agente de escucha del grupo de disponibilidad de Azure:

- Solo se admite un agente de escucha de grupo de disponibilidad interno por cada servicio en la nube, ya que el agente de escucha se configura con arreglo al equilibrador y solo hay un equilibrador de carga interno. Sin embargo, es posible crear varios agentes de escucha externos.

- Como solo hay un equilibrador de carga interno, el acceso al agente de escucha se realizará desde la misma red virtual.
 

<!---HONumber=AcomDC_0720_2016-->