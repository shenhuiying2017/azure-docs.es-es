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
ms.date: 05/01/2017
ms.author: mikeray
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 9998c6ac27b9dc06b71edb4531aebeeb53fefcce
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn en Azure
En este tema se explica cómo puede crear un equilibrador de carga interno para un grupo de disponibilidad de SQL Server AlwaysOn en máquinas virtuales de Azure que se ejecutan con Azure Resource Manager. Cuando las instancias de SQL Server están implementadas en máquinas virtuales de Azure, los grupos de disponibilidad necesitan un equilibrador de carga. El equilibrador de carga almacena la dirección IP del agente de escucha del grupo de disponibilidad. Si un grupo de disponibilidad abarca varias regiones, cada región necesitará su propio equilibrador de carga.

Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server implementado en máquinas virtuales de Azure según el modo de Resource Manager. Las dos máquinas virtuales de SQL Server deben pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad en Azure Resource Manager. Esta plantilla crea automáticamente el equilibrador de carga interno. 

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

### <a name="verify-the-configuration-of-the-listener"></a>Comprobación de la configuración del agente de escucha

Si los recursos y las dependencias de clúster están configurados correctamente, tendría que ver el agente de escucha en las instancias de SQL Server Management Studio. Siga estos pasos para establecer el puerto de escucha:

1. Abra SQL Server Management Studio y conéctese a la réplica principal.
2. Vaya a **Alta disponibilidad de AlwaysOn** | **Grupos de disponibilidad** | **Agentes de escucha del grupo de disponibilidad**. 
1. Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.
1. En el cuadro **Puerto**, especifique el número de puerto del agente de escucha del grupo de disponibilidad mediante el valor $EndpointPort que ha establecido antes (1433 era el valor predeterminado) y haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad en máquinas virtuales de Azure que se ejecutan con el modelo de Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Comprobación de la conexión con el agente de escucha
Para probar la conexión:

1. Conéctese con RDP a un servidor SQL Server que esté en la misma red virtual, pero que no sea el propietario de la réplica; por ejemplo, a otro servidor SQL Server del clúster.
2. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, el siguiente script establece una conexión **sqlcmd** con la réplica principal a través del agente de escucha utilizando la autenticación de Windows:
   
        sqlcmd -S <listenerName> -E

La conexión SQLCMD se establece automáticamente con la instancia de SQL Server en la que se hospede la réplica principal. 

## <a name="create-an-ip-address---for-an-additional-availability-group"></a>Creación de una dirección IP para un grupo de disponibilidad adicional

Cada grupo de disponibilidad usa un agente de escucha independiente. Cada agente de escucha tiene su propia dirección IP. Use el mismo equilibrador de carga para contener la dirección IP de los agentes de escucha adicionales. Después de crear un nuevo grupo de disponibilidad, agregue la dirección IP al equilibrador de carga y luego configure el agente de escucha.

Para agregar una dirección IP a un equilibrador de carga con el portal de Azure, siga estos pasos:

1. En el portal de Azure, abra el grupo de recursos que contiene el equilibrador de carga y haga clic en el equilibrador de carga. 
2. En **Configuración**, haga clic en **Grupo de direcciones IP de front-end**. Haga clic en **+ Agregar**. 
3. En **Agregar dirección IP de front-end**, asigne un nombre para el front-end. 
4. Compruebe que la **red Virtual** y la **subred** sean las mismas que en las instancias de SQL Server.
5. Establezca la dirección IP del agente de escucha. 
   
   >[!TIP]
   >Puede configurar la dirección IP como estática y escribir una dirección que no se use actualmente en la subred. También puede configurar la dirección IP como dinámica y guardar el nuevo grupo de direcciones IP de front-end. Al hacerlo, el portal de Azure asignará automáticamente una dirección IP disponible al grupo. Luego puede volver a abrir el grupo de direcciones IP de front-end y cambiar la asignación a estática. 

   Guarde la dirección IP del agente de escucha. 

6. Agregue un sondeo de estado. Use la configuración siguiente:

   |Configuración |Valor
   |:-----|:----
   |**Name** |Un nombre para identificar el sondeo.
   |**Protocolo** |TCP
   |**Puerto** |Un puerto TCP sin usar. Debe estar disponible en todas las máquinas virtuales. No se puede usar para ningún otro fin. Dos agentes de escucha no pueden usar el mismo puerto de sondeo. 
   |**Intervalo** |Cantidad de tiempo entre los intentos de sondeo. Use el valor predeterminado (5).
   |**Umbral incorrecto** |El número de umbrales consecutivos que deben generar un error antes de que se considere que una máquina virtual tiene un estado incorrecto.

   Haga clic en **Aceptar** para guardar el sondeo. 

7. Cree una nueva regla de equilibrio de carga. Haga clic en **Reglas de equilibrio de carga** y luego haga clic en **+ Agregar**.
8. Configure la nueva regla de equilibrio de carga con los siguientes valores:

   |Configuración |Valor
   |:-----|:----
   |**Name** |Un nombre para identificar la regla de equilibrio de carga. 
   |**Frontend IP address** (Dirección IP de front-end) |Elija la dirección IP que creó. 
   |**Protocolo** |TCP
   |**Puerto** |Use el puerto que usan las instancias de SQL Server. Una instancia predeterminada usa el puerto 1433, a menos que lo haya modificado. 
   |**Puerto back-end** |Use el mismo valor que en **Puerto**.
   |**Grupo de back-end** |El grupo que contiene las máquinas virtuales con las instancias de SQL Server. 
   |**Sondeo de estado** |Elija el sondeo que creó.
   |**Persistencia de la sesión** |None
   |**Tiempo de espera de inactividad (minutos)** |Valor predeterminado (4)
   |**IP flotante (Direct Server Return)** | habilitado

### <a name="configure-the-availability-group-go-use-the-new-ip-address"></a>Configuración del grupo de disponibilidad para usar la nueva dirección IP

Para terminar de configurar el clúster, repita los pasos realizados cuando creó el primer grupo de disponibilidad. Es decir, configure el [clúster para usar la nueva dirección IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Después de haber agregado una dirección IP para el agente de escucha, puede configurar el grupo de disponibilidad adicional. 

1. Compruebe que el puerto de sondeo de la nueva dirección IP esté abierto en ambas máquinas virtuales SQL Server. 

2. [En el Administrador de clústeres, agregue el punto de acceso de cliente](#addcap).

3. [Configurar el recurso IP para el grupo de disponibilidad](#congroup).

   >[!IMPORTANT]
   >Cuando cree la dirección IP, use la dirección IP que agregó al equilibrador de carga.  

4. [Hacer que el recurso del grupo de disponibilidad de SQL Server dependa del punto de acceso cliente](#dependencyGroup).

5. [Hacer que el recurso de punto de acceso cliente dependa de la dirección IP](#listname).
 
5. [Establezca los parámetros de clúster en PowerShell](#setparam).

Después de configurar el grupo de disponibilidad para usar la nueva dirección IP, configure la conexión al agente de escucha. 

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de un grupo de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines en regiones distintas](virtual-machines-windows-portal-sql-availability-group-dr.md)

