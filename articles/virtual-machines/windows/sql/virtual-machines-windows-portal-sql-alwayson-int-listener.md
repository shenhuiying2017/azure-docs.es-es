---
title: "Creación del agente de escucha de grupo de disponibilidad de SQL Server en Azure Virtual Machines | Microsoft Docs"
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
ms.openlocfilehash: 09fed7e785708d4afe64905de973becc188181d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configuración de un equilibrador de carga para un grupo de disponibilidad AlwaysOn en Azure
En este artículo se explica cómo puede crear un equilibrador de carga para un grupo de disponibilidad de SQL Server AlwaysOn en instancias de Azure Virtual Machines que se ejecutan con Azure Resource Manager. Cuando las instancias de SQL Server están implementadas en máquinas virtuales de Azure, los grupos de disponibilidad necesitan un equilibrador de carga. El equilibrador de carga almacena la dirección IP del agente de escucha del grupo de disponibilidad. Si un grupo de disponibilidad abarca varias regiones, cada región necesitará su propio equilibrador de carga.

Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server implementado en instancias de Azure Virtual Machines que se estén ejecutando con Resource Manager. Las dos máquinas virtuales de SQL Server deben pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad en Resource Manager. Esta plantilla crea automáticamente un equilibrador de carga interno. 

Si lo prefiere, puede [configurar manualmente un grupo de disponibilidad](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

En este artículo, es necesario que los grupos de disponibilidad ya estén configurados.  

Temas relacionados:

* [Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configuración de una conexión entre dos redes virtuales mediante Azure Resource Manager y PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

A lo largo de este artículo, va a crear y configurar un equilibrador de carga en Azure Portal. Una vez hecho esto, configurará el clúster para que utilice la dirección IP del equilibrador de carga para el agente de escucha del grupo de disponibilidad.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Creación y configuración del equilibrador de carga en el Portal de Azure
En esta parte de la tarea, haga lo siguiente:

1. En Azure Portal, cree el equilibrador de carga y configure la dirección IP.
2. Configure el grupo back-end.
3. Cree el sondeo. 
4. Configure las reglas de equilibrio de carga.

> [!NOTE]
> Si las instancias de SQL Server se encuentran en varios grupos de recursos y regiones, realice cada paso dos veces, una vez en cada grupo de recursos.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Paso 1: Creación del equilibrador de carga y configuración de la dirección IP
En primer lugar, cree el equilibrador de carga. 

1. En el Portal de Azure, abra el grupo de recursos que contiene las máquinas virtuales de SQL Server. 

2. En el grupo de recursos, haga clic en **Agregar**.

3. Busque el **equilibrador de carga** y, a continuación, en los resultados de la búsqueda, seleccione el **equilibrador de carga** publicado por **Microsoft**.

4. En la hoja **Load Balancer**, haga clic en **Crear**.

5. En el cuadro de diálogo **Crear equilibrador de carga**, configure el equilibrador de carga tal y como se explica a continuación:

   | Configuración | Valor |
   | --- | --- |
   | **Name** |Nombre de texto que representa el equilibrador de carga; Por ejemplo, **sqlLB**. |
   | **Tipo** |**Interno**: la mayoría de las implementaciones usa un equilibrador de carga interno que permite a las aplicaciones dentro de la misma red virtual conectarse al grupo de disponibilidad.  </br> **Externo**: permite que las aplicaciones se conecten al grupo de disponibilidad a través de una conexión a Internet pública. |
   | **Red virtual** |Seleccione la red virtual en la que se encuentran las instancias de SQL Server. |
   | **Subred** |Seleccione la subred en la que se encuentran las instancias de SQL Server. |
   | **Asignación de dirección IP** |**Estática** |
   | **Dirección IP privada** |Especifique una dirección IP disponible de la subred. Utilícela cuando cree un agente de escucha en el clúster. Más adelante en este mismo artículo, hay un script de PowerShell en el que tendrá que utilizar esta dirección para la variable `$ILBIP`. |
   | **Suscripción** |Si tiene varias suscripciones, puede aparecer este campo. Seleccione la suscripción que quiere asociar con este recurso. Normalmente, será la misma suscripción que se utiliza con todos los recursos del grupo de disponibilidad. |
   | **Grupos de recursos** |Seleccione el grupo de recursos en el que se encuentran las instancias de SQL Server. |
   | **Ubicación** |Seleccione la ubicación de Azure en la que se encuentran las instancias de SQL Server. |

6. Haga clic en **Crear**. 

Azure crea el equilibrador de carga. El equilibrador de carga pertenece a una red, una subred, una ubicación y un grupo de recursos específicos. Cuando Azure haya completado la tarea, compruebe la configuración del equilibrador de carga en Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Paso 2: Configuración del grupo back-end
En Azure, el grupo de direcciones de back-end se denomina *grupo de back-end*. En este caso, el grupo de back-end contiene las direcciones de las dos instancias de SQL Server del grupo de disponibilidad. 

1. En el grupo de recursos, haga clic en el equilibrador de carga que ha creado. 

2. En **Configuración**, haga clic en **Grupos de back-end**.

3. En **Grupos de back-end**, haga clic en **Agregar** para crear un grupo de direcciones de back-end. 

4. En **Agregar grupo back-end** en **Nombre**, especifique un nombre para el grupo de back-end.

5. En **Máquinas virtuales**, haga clic en **+ Agregar una máquina virtual**. 

6. En **Elegir máquinas virtuales**, haga clic en **Elegir un conjunto de disponibilidad** y especifique el conjunto de disponibilidad al que pertenecen las máquinas virtuales de SQL Server.

7. Después de elegir el conjunto de disponibilidad, haga clic en **Elegir máquinas virtuales**, seleccione las dos máquinas virtuales que hospedan las instancias de SQL Server en el grupo de disponibilidad y, finalmente, haga clic en **Seleccionar**. 

8. Haga clic en **Aceptar** para cerrar las hojas **Elegir máquinas virtuales** y **Agregar grupo back-end**. 

Azure actualiza la configuración del grupo de direcciones de back-end. Ahora, el conjunto de disponibilidad tiene un grupo de dos instancias de SQL Server.

### <a name="step-3-create-a-probe"></a>Paso 3: Elaboración de un sondeo
Este sondeo establece el modo en que Azure va a comprobar cuál de las instancias de SQL Server es el propietario actual del agente de escucha del grupo de disponibilidad. Azure analiza el servició con arreglo a la dirección IP de un puerto que estableció al crear el sondeo.

1. En la hoja **Configuración** del equilibrador de carga, haga clic en **Sondeos de estado**. 

2. En la hoja **Sondeos de estado**, haga clic en **Agregar**.

3. Configure el sondeo en la hoja **Agregar sondeo** . Utilice los valores siguientes para configurar el sondeo.

   | Configuración | Valor |
   | --- | --- |
   | **Name** |Nombre de texto que representa el sondeo. Por ejemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Puerto** |Puede usar cualquier puerto que esté disponible. Por ejemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Umbral incorrecto** |*2* |

4.  Haga clic en **Aceptar**. 

> [!NOTE]
> Asegúrese de que el puerto especificado esté abierto en el firewall de las dos instancias de SQL Server. En estas dos instancias, es necesario definir una regla de entrada para el puerto TCP. Consulte [Agregar o editar regla de firewall](http://technet.microsoft.com/library/cc753558.aspx) para obtener más información. 
> 
> 

Azure crea el sondeo y, a continuación, lo usa para comprobar qué instancia de SQL Server tiene el agente de escucha del grupo de disponibilidad.

### <a name="step-4-set-the-load-balancing-rules"></a>Paso 4: Configuración de las reglas de equilibrio de carga
Las reglas de equilibrio de carga determinan cómo el equilibrador de carga enruta el tráfico a las instancias de SQL Server. En este equilibrador de carga, habilite Direct Server Return, ya que solo una de las dos instancias de SQL Server puede ser el propietario del recurso del agente de escucha del grupo de disponibilidad simultáneamente.

1. En la hoja **Configuración** del equilibrador de carga, haga clic en **Reglas de equilibrio de carga**. 

2. En la hoja **Reglas de equilibrio de carga**, haga clic en **Agregar**.

3. Utilice la hoja **Add load balancing rules** (Agregar reglas de equilibrio de carga) para configurar la regla de equilibrio de carga. Use la configuración siguiente: 

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

4. Haga clic en **Aceptar**. 
5. Azure configura la regla de equilibrio de carga. Ahora, el equilibrador de carga está configurado para enrutar el tráfico a la instancia de SQL Server que hospeda el agente de escucha del grupo de disponibilidad. 

En este punto, el grupo de recursos dispone de un equilibrador de carga que se conecta con las dos máquinas de SQL Server. El equilibrador de carga también contiene una dirección IP del agente de escucha del grupo de disponibilidad de SQL Server AlwaysOn, por lo que cualquier máquina puede responder a las solicitudes de los grupos de disponibilidad.

> [!NOTE]
> Si las instancias de SQL Server se encuentran en dos regiones diferentes, repita los pasos en la otra región. Cada una de las regiones necesita su propio equilibrador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure el clúster para que utilice la dirección IP del equilibrador de carga.
El siguiente paso consiste en configurar el agente de escucha del clúster y conectarlo. Haga lo siguiente: 

1. Cree el agente de escucha del grupo de disponibilidad en el clúster de conmutación por error. 

2. Conecte el agente de escucha.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Paso 5: Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error
En este paso, se crea manualmente el agente de escucha del grupo de disponibilidad en el Administrador de clústeres de conmutación por error y SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Comprobación de la configuración del agente de escucha

Si los recursos y las dependencias de clúster están configurados correctamente, tendría que ver el agente de escucha en SQL Server Management Studio. Haga lo siguiente para establecer el puerto de escucha:

1. Abra SQL Server Management Studio y conéctese a la réplica principal.

2. Vaya a **Alta disponibilidad de AlwaysOn** > **Grupos de disponibilidad** > **Agentes de escucha del grupo de disponibilidad**.  
    Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. 

3. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.

4. En el cuadro **Puerto**, especifique el número de puerto del agente de escucha del grupo de disponibilidad mediante el valor $EndpointPort que ha establecido antes (1433 era el valor predeterminado) y haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad en máquinas virtuales de Azure que se ejecutan con el modelo de Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Comprobación de la conexión con el agente de escucha
Pruebe la conexión haciendo lo siguiente:

1. Conéctese con RDP a una instancia de SQL Server que esté en la misma red virtual, pero que no sea la propietaria de la réplica. Este servidor puede ser otra instancia de SQL Server del clúster.

2. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, el siguiente script establece una conexión **sqlcmd** con la réplica principal a través del agente de escucha utilizando la autenticación de Windows:
   
        sqlcmd -S <listenerName> -E

La conexión SQLCMD se establece automáticamente con la instancia de SQL Server en la que se hospede la réplica principal. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Creación de una dirección IP para un grupo de disponibilidad adicional

Cada grupo de disponibilidad usa un agente de escucha independiente. Cada agente de escucha tiene su propia dirección IP. Use el mismo equilibrador de carga para contener la dirección IP de los agentes de escucha adicionales. Después de crear un grupo de disponibilidad, agregue la dirección IP al equilibrador de carga y luego configure el agente de escucha.

Para agregar una dirección IP a un equilibrador de carga con el portal de Azure, haga lo siguiente:

1. En Azure Portal, abra el grupo de recursos que contiene el equilibrador de carga y haga clic en él. 

2. En **Configuración**, haga clic en **Grupo de direcciones IP de front-end** y, después, en **Agregar**. 

3. En **Agregar dirección IP de front-end**, asigne un nombre para el front-end. 

4. Compruebe que la **red Virtual** y la **subred** sean las mismas que en las instancias de SQL Server.

5. Establezca la dirección IP del agente de escucha. 
   
   >[!TIP]
   >Puede configurar la dirección IP como estática y escribir una dirección que no se use actualmente en la subred. También puede configurar la dirección IP como dinámica y guardar el nuevo grupo de direcciones IP de front-end. Al hacerlo, Azure Portal asigna automáticamente una dirección IP disponible al grupo. Luego puede volver a abrir el grupo de direcciones IP de front-end y cambiar la asignación a estática. 

6. Guarde la dirección IP del agente de escucha. 

7. Agregue un sondeo de estado mediante las siguientes opciones:

   |Configuración |Valor
   |:-----|:----
   |**Name** |Un nombre para identificar el sondeo.
   |**Protocolo** |TCP
   |**Puerto** |Un puerto TCP sin usar, que debe estar disponible en todas las máquinas virtuales. No se puede usar para ningún otro fin. Dos agentes de escucha no pueden usar el mismo puerto de sondeo. 
   |**Intervalo** |Cantidad de tiempo entre los intentos de sondeo. Use el valor predeterminado (5).
   |**Umbral incorrecto** |El número de umbrales consecutivos que deben generar un error antes de que se considere que una máquina virtual tiene un estado incorrecto.

8. Haga clic en **Aceptar** para guardar el sondeo. 

9. Cree una regla de equilibrio de carga. Haga clic en **Reglas de equilibrio de carga** y luego haga clic en **Agregar**.

10. Configure la nueva regla de equilibrio de carga con los siguientes valores:

   |Configuración |Valor
   |:-----|:----
   |**Name** |Un nombre para identificar la regla de equilibrio de carga. 
   |**Frontend IP address** (Dirección IP de front-end) |Seleccione la dirección IP que creó. 
   |**Protocolo** |TCP
   |**Puerto** |Use el puerto que usan las instancias de SQL Server. Una instancia predeterminada usa el puerto 1433, a menos que lo haya modificado. 
   |**Puerto back-end** |Use el mismo valor que en **Puerto**.
   |**Grupo de back-end** |El grupo que contiene las máquinas virtuales con las instancias de SQL Server. 
   |**Sondeo de estado** |Elija el sondeo que creó.
   |**Persistencia de la sesión** |None
   |**Tiempo de espera de inactividad (minutos)** |Valor predeterminado (4)
   |**IP flotante (Direct Server Return)** | habilitado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configuración del grupo de disponibilidad para usar la nueva dirección IP

Para terminar de configurar el clúster, repita los pasos realizados cuando creó el primer grupo de disponibilidad. Es decir, configure el [clúster para usar la nueva dirección IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Después de haber agregado una dirección IP para el agente de escucha, configure el grupo de disponibilidad adicional. Para ello, haga lo siguiente: 

1. Compruebe que el puerto de sondeo de la nueva dirección IP esté abierto en ambas máquinas virtuales SQL Server. 

2. [En el Administrador de clústeres, agregue el punto de acceso cliente](#addcap).

3. [Configurar el recurso IP para el grupo de disponibilidad](#congroup).

   >[!IMPORTANT]
   >Cuando cree la dirección IP, use la dirección IP que agregó al equilibrador de carga.  

4. [Haga que el recurso del grupo de disponibilidad de SQL Server dependa del punto de acceso cliente](#dependencyGroup).

5. [Hacer que el recurso de punto de acceso cliente dependa de la dirección IP](#listname).
 
6. [Establezca los parámetros de clúster en PowerShell](#setparam).

Después de configurar el grupo de disponibilidad para usar la nueva dirección IP, configure la conexión al agente de escucha. 

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de un grupo de disponibilidad de SQL Server AlwaysOn en Azure Virtual Machines en regiones distintas](virtual-machines-windows-portal-sql-availability-group-dr.md)
