---
title: "Conexión de una red virtual de Azure a otra red virtual: modelo clásico | Microsoft Docs"
description: "Cómo conectar redes virtuales de Azure simultáneamente mediante PowerShell y el Portal de Azure clásico."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7413827f-233d-4c7c-a133-9c99cf031833
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eadb1f29da69e7f6fcc2c7c19ba67f4e3072c346
ms.openlocfilehash: 863b308125a7a9ae2b9d9104d150bd2474c064b3


---
# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configuración de una conexión de red virtual a red virtual para el modelo de implementación clásico
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Clásico - Portal clásico](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Este artículo le guía por los pasos necesarios para crear y conectar redes virtuales mediante el modelo de implementación clásica (también conocido como Service Management). Los pasos siguientes utilizan el Portal de Azure clásico para crear redes virtuales y puertas de enlace, así como PowerShell para configurar la conexión de red virtual a red virtual. No puede configurar la conexión en el Portal.

![Diagrama de conectividad VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para conexiones de red virtual a red virtual
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los modelos de implementación disponibles actualmente y los métodos para las configuraciones de red virtual a red virtual. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca de conexiones de red virtual a red virtual
La conexión de una red virtual a otra (de red virtual a red virtual) es parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. 

Las redes virtuales que se conecten pueden estar en suscripciones y regiones distintas. Puede combinar la comunicación entre redes virtuales con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales.

### <a name="why-connect-virtual-networks"></a>¿Por qué debería conectarse a redes virtuales?
Puede que desee conectar redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**
  
  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con Azure Load Balancer y Microsoft, o con una tecnología de agrupación en clústeres de otros fabricantes, puede configurar cargas de trabajo de alta disponibilidad y redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de varios niveles con límite de aislamiento sólido**
  
  * En la misma región se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí, con un aislamiento sólido y una comunicación entre niveles segura.
* **Comunicación entre suscripciones y entre organizaciones en Azure**
  
  * Si tiene varias suscripciones a Azure, puede conectar cargas de trabajo de distintas suscripciones simultáneamente entre redes virtuales de forma segura.
  * Asimismo, tanto las empresas como los proveedores de servicios pueden habilitar la comunicación entre organizaciones con tecnología VPN segura en Azure.

### <a name="vnet-to-vnet-considerations-for-classic-vnets"></a>Consideraciones de red virtual a red virtual para redes virtuales clásicas
* Las redes virtuales pueden estar en la misma suscripción o en suscripciones distintas.
* Las redes virtuales pueden estar en la misma región de Azure o en regiones distintas (ubicaciones).
* Un servicio en la nube o un punto de conexión de equilibrio de carga no puede abarcar varias redes virtuales, aunque estas estén conectadas entre sí.
* La conexión simultánea de varias redes virtuales no requiere dispositivos VPN locales.
* VNet a VNet admite la conexión a redes virtuales de Azure. No admite la conexión de máquinas virtuales o servicios en la nube que no estén implementados en una red virtual.
* Red virtual a red virtual requiere puertas de enlace de enrutamiento dinámico. No se admiten puertas de enlace de enrutamiento estático de Azure.
* La conectividad de red virtual se puede usar de forma simultánea con VPN de varios sitios. Existe un máximo de 10 túneles de VPN para una puerta de enlace de VPN de red virtual conectada a otras redes virtuales o sitios locales.
* Los espacios de direcciones de las redes virtuales y de los sitios de red local no se pueden solapar. Los espacios de direcciones solapados provocarán un error al crear redes virtuales o al cargar archivos de configuración netcfg.
* No se admiten los túneles redundantes entre un par de redes virtuales.
* Todos los túneles de VPN para la red virtual, incluidas las VPN de punto a sitio (P2S), comparten el ancho de banda disponible para la puerta de enlace de VPN y el mismo Acuerdo de Nivel de Servicio de tiempo de actividad de puerta de enlace de VPN de Azure.
* El tráfico VNet a VNet viaja a través de la red troncal de Azure.

## <a name="a-namestep1astep-1---plan-your-ip-address-ranges"></a><a name="step1"></a>Paso 1: Planeamiento de los intervalos de direcciones IP
Es importante decidir los intervalos que usará para configurar las redes virtuales. Para esta configuración, debe asegurarse de que ninguno de los intervalos de red virtual se superpongan entre sí o con cualquiera de las redes locales a las que se conectan.

En la tabla siguiente se muestra un ejemplo de cómo definir las redes virtuales. Use los intervalos solo como referencia. Escriba los intervalos para las redes virtuales. Necesitará esta información en pasos posteriores.

**Configuración de ejemplo**

| Red virtual | Espacio de direcciones | Region | Se conecta a un sitio de red local |
|:--- |:--- |:--- |:--- |
| VNet1 |VNet1 (10.1.0.0/16) |Oeste de EE. UU. |VNet2Local (10.2.0.0/16) |
| VNet2 |VNet2 (10.2.0.0/16) |Este de Japón |VNet1Local (10.1.0.0/16) |

## <a name="step-2---create-vnet1"></a>Paso 2: Creación de VNet1
En este paso, se crea VNet1. Cuando se utiliza cualquiera de los ejemplos, asegúrese de sustituir sus propios valores. Si ya existe la red virtual, no es necesario realizar este paso. No obstante, debe comprobar que los intervalos de direcciones IP no se superpongan con los intervalos de la segunda red virtual o con cualquiera de las demás redes virtuales a las que desea conectarse.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com). En este artículo, se usa el Portal clásico porque algunos de los valores de configuración necesarios no están aún disponibles en el Portal de Azure.
2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo** > **Servicios de red** > **Red virtual** > **Creación personalizada** para iniciar el asistente para configuración. Mientras navega por el asistente, agregue los valores especificados para cada página.

### <a name="virtual-network-details"></a>Detalles de red virtual
En la página Detalles de la red virtual, escriba la siguiente información:

  ![Detalles de red virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

* **Nombre** : nombre de la red virtual. Por ejemplo, VNet1.
* **Ubicación** : al crear una red virtual, la debe asociar a una ubicación de Azure (región). Por ejemplo, Por ejemplo, si desea que las máquinas virtuales que implemente en la red virtual se encuentren físicamente en Oeste de EE.UU., seleccione esa ubicación. No se puede cambiar la ubicación asociada a la red virtual después de crearla.

### <a name="dns-servers-and-vpn-connectivity"></a>Servidores DNS y conectividad VPN
En la página sobre servidores DNS y conectividad VPN, especifique la información siguiente y, a continuación, haga clic en la flecha siguiente en la parte inferior derecha.

  ![Servidores DNS y conectividad VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

* **Servidores DNS** : escriba el nombre y la dirección IP del servidor DNS o seleccione un servidor DNS previamente registrado de la lista desplegable. Mediante este valor no se crea un servidor DNS. Le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual. Si desea tener resolución de nombres entre las redes virtuales, debe configurar su propio servidor DNS, en vez de usar la resolución de nombres que proporciona Azure.
* No seleccione ninguna de las casillas para conectividad P2S o S2S. Haga clic en la flecha de la parte inferior derecha para pasar a la pantalla siguiente.

### <a name="virtual-network-address-spaces"></a>Espacios de direcciones de la red virtual
En la página Espacios de direcciones de la red virtual, especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual. 

Si va a crear una máquina virtual que también va a tener una conexión a la red local, es especialmente importante seleccionar un intervalo que no se superponga con ninguno de los intervalos utilizados para la red local. En este caso, necesita coordinarse con el administrador de red. Es posible que el administrador de red necesite definir un intervalo de direcciones IP desde el espacio de direcciones de la red local para que los use en la red virtual.

  ![Página Espacios de direcciones de la red virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

* **Espacio de direcciones** : incluidas Dirección IP de inicio y Recuento de direcciones. Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local. En este ejemplo, se usa 10.1.0.0/16 para VNet1.
* **Agregar subred** : incluidas Dirección IP de inicio y Recuento de direcciones. No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tendrán DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.

**Haga clic en la marca de verificación** de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando finalice, verá "Creado" listado bajo Estado en la página Redes.

## <a name="step-3---create-vnet2"></a>Paso 3: Creación de VNet2
A continuación, repita los pasos anteriores para crear otra red virtual. En pasos posteriores, conectará las dos redes virtuales. Puede hacer referencia a la [configuración de ejemplo](#step1) del paso 1. Si ya existe la red virtual, no es necesario realizar este paso. No obstante, debe comprobar que los intervalos de direcciones IP no se superpongan con los de las demás redes virtuales o redes locales a las que quiere conectarse.

## <a name="step-4---add-the-local-network-sites"></a>Paso 4: Adición de sitios de redes locales
Cuando se crea una configuración de red virtual a red virtual, debe configurar sitios de red local, que se muestran en la página **Redes locales** del portal. Azure usa la configuración especificada en cada sitio de red local para determinar cómo enrutar el tráfico entre las redes virtuales. Puede determinar el nombre que quiere utilizar para hacer referencia a cada sitio de red local. Es mejor utilizar un nombre descriptivo, ya que seleccionará el valor en una lista desplegable en pasos posteriores.

Por ejemplo, VNet1 se conecta a un sitio de red local que cree con el nombre "VNet2Local". La configuración de VNet2Local contiene los prefijos de dirección para VNet2 y una dirección IP pública para la puerta de enlace de VNet2. VNet2 se conecta a un sitio de red local que ha creado con el nombre "VNet1Local" que contiene los prefijos de dirección para VNet1 y la dirección IP pública para la puerta de enlace de VNet1.

### <a name="a-namelocalnetaadd-the-local-network-site-vnet1local"></a><a name="localnet"></a>Adición del sitio de red local VNet1
1. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo** > **Network Services** > **Red virtual** > **Agregar red local**.
2. En la página **Especifique los datos de la red local**, en **Nombre**, escriba el nombre que quiera usar para representar la red a la que quiere conectarse. En este ejemplo, puede utilizar "VNet1Local" para hacer referencia a los intervalos de direcciones IP y a la puerta de enlace para VNet1.
3. En **Dirección IP del dispositivo VPN (opcional)**, escriba cualquier dirección IP pública válida. Normalmente, se usaría la dirección IP externa real para un dispositivo VPN. Para las configuraciones de red virtual a red virtual, utilice la dirección IP pública asignada a la puerta de enlace para la red virtual. Sin embargo, dado que aún no ha creado la puerta de enlace, puede especificar cualquier dirección IP pública válida como marcador de posición. No lo deje en blanco: no es opcional para esta configuración. En un paso posterior, volverá a estos parámetros y los configurará con las direcciones IP de puerta de enlace correspondientes que genera Azure. Haga clic en la flecha para avanzar a la siguiente pantalla.
4. En la página **Especifique el espacio de direcciones**, indique el intervalo de direcciones IP y el recuento de direcciones para VNet1. Se debe corresponder exactamente con el intervalo configurado para VNet1. Azure usa los intervalos de direcciones IP que especifique para enrutar el tráfico destinado a VNet1. Haga clic en la marca de verificación para crear la red local.

### <a name="add-the-local-network-site-vnet2local"></a>Adición del sitio de red local VNet2Local
Utilice los pasos anteriores para crear el sitio de red local "VNet2Local". Puede hacer referencia a los valores de la [configuración de ejemplo](#step1) del paso 1, si fuera necesario.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configuración de las redes virtuales para que señalen a una red local
Cada red virtual debe señalar a la red local correspondiente a la que se desea redirigir el tráfico. 

**Para VNet1**

1. Desplácese a la página **Configurar** para la red virtual **VNet1**. 
2. En Conectividad de sitio a sitio, seleccione "Conectar con la red local" y elija **VNet2Local** como red local en el menú desplegable. 
3. Guarde la configuración.

**Para VNet2**

1. Desplácese a la página **Configurar** para la red virtual **VNet2**. 
2. En Conectividad de sitio a sitio, seleccione "Conectar con la red local" y elija **VNet1Local** como red local en el menú desplegable. 
3. Guarde la configuración.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Paso 5: Configuración de una puerta de enlace para cada red virtual
Configure una puerta de enlace de enrutamiento dinámico para cada red virtual. Esta configuración no es compatible con las puertas de enlace de enrutamiento estático. Si usa redes virtuales que se habían configurado previamente y que aún tienen las puertas de enlace de enrutamiento dinámico, no es necesario realizar este paso. Si las puertas de enlace son de enrutamiento estático, debe eliminarlas y volver a crearlas como puertas de enlace de enrutamiento dinámico. Si elimina una puerta de enlace, se libera la dirección IP pública asignada y debe retroceder y volver a configurar cualquiera de las redes locales y dispositivos VPN con la nueva dirección IP pública para la nueva puerta de enlace.

1. En la página **Redes**, compruebe que la columna de estado de la red virtual sea **Creada**.
2. En la columna **Nombre** , haga clic en el nombre de la red virtual. En este ejemplo, se usa VNet1.
3. En la página **Panel** , observe que esta red virtual aún no tiene configurada una puerta de enlace. Verá que este estado cambia a medida que avance por los pasos para configurar la puerta de enlace.
4. En la parte inferior de la página, haga clic en **Crear puerta de enlace** y **Enrutamiento dinámico**. Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en Sí.
   
      ![Tipo de puerta de enlace](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  
5. Una vez creada la puerta de enlace, verá que el gráfico de la puerta de enlace de la página cambia a amarillo e indica "Creando puerta de enlace". La creación de la puerta de enlace suele tardar unos 30 minutos.
6. Repita los mismos pasos para VNet2. No es necesario completar la primera puerta de enlace de red virtual para empezar a crear la puerta de enlace de la otra.
7. Cuando el estado de la puerta de enlace cambia a "Conectando", la dirección IP pública para cada puerta de enlace está visible en el panel. Anote la dirección IP que corresponde a cada red virtual, teniendo cuidado para no mezclarlas. Estas son las direcciones IP que se utilizan al editar las direcciones IP de marcador de posición para el dispositivo VPN de cada red local.

## <a name="step-6---edit-the-local-network"></a>Paso 6 : Editar la red local
1. En la página **Redes locales**, haga clic en el nombre de la red local que quiere editar y, a continuación, elija **Editar** en la parte inferior de la página. En **Dirección IP del dispositivo VPN**, escriba la dirección IP de la puerta de enlace correspondiente a la red virtual. Por ejemplo, para VNet1Local, escriba la dirección IP de puerta de enlace asignada a VNet1. A continuación, haga clic en la flecha de la parte inferior de la página.
2. En la página **Especifique el espacio de direcciones** , haga clic en la marca de verificación de la parte inferior derecha sin hacer cambios.

## <a name="step-7---create-the-vpn-connection"></a>Paso 7: Creación de la conexión VPN
Una vez completados todos los pasos anteriores, defina las claves compartidas previamente de IPsec/IKE y cree la conexión. Esta serie de pasos usa PowerShell y no se puede configurar en el Portal. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar los cmdlets de Azure PowerShell. Asegúrese de descargar la versión más reciente de los cmdlets de Service Management (SM). 

1. Abra Windows PowerShell e inicie sesión.
   
        Add-AzureAccount
2. Seleccione la suscripción en la que residen las redes virtuales.
   
        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"
3. Cree las conexiones. En los ejemplos, verá que la clave compartida es exactamente la misma. Siempre debe coincidir con la clave compartida.

    Conexión VNet1 a VNet2

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    Conexión VNet2 a VNet1

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Espere a que se inicialicen las conexiones. Una vez inicializada la puerta de enlace, esta es similar a la que aparece en la ilustración siguiente.
   
    ![Estado de la puerta de enlace: conectada](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  
   
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Pasos siguientes
Puede agregar máquinas virtuales a las redes virtuales. Consulte la [documentación sobre máquinas virtuales](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.




<!--HONumber=Jan17_HO4-->


