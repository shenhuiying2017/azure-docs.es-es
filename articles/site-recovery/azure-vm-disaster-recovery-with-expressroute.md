---
title: Uso de ExpressRoute con recuperación ante desastres de máquinas virtuales de Azure | Microsoft Docs
description: Describe cómo usar Azure ExpressRoute con Azure Site Recovery para la recuperación ante desastres de máquinas virtuales de Azure.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071594"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Uso de ExpressRoute con recuperación ante desastres de máquinas virtuales de Azure

Microsoft Azure ExpressRoute le permite ampliar sus redes locales en la nube de Microsoft a través de una conexión privada que facilita un proveedor de conectividad. En este artículo se describe cómo puede usar ExpressRoute con Site Recovery para la recuperación ante desastres de máquinas virtuales de Azure.

## <a name="prerequisites"></a>requisitos previos

Antes de comenzar, asegúrese de que comprende:
-   Los [circuitos ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
-   Los [dominios de enrutamiento](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains) de ExpressRoute
-   La [arquitectura de replicación](azure-to-azure-architecture.md) de máquinas virtuales de Azure
-   La [configuración de replicación](azure-to-azure-tutorial-enable-replication.md) de máquinas virtuales de Azure
-   La [conmutación por error](azure-to-azure-tutorial-failover-failback.md) de máquinas virtuales de Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Replicación de ExpressRoute y de máquinas virtuales de Azure

Cuando se protegen máquinas virtuales de Azure con Site Recovery, los datos de replicación se envían a una cuenta de Azure Storage o a una instancia de Managed Disks de réplica en la región de Azure de destino en función de si las máquinas virtuales de Azure usan [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). De forma predeterminada, aunque los puntos de conexión de la replicación son públicos, el tráfico de replicación de las máquinas virtuales de Azure no atraviesa Internet, independientemente de la región de Azure en la que esté la red virtual de origen.

Para la recuperación ante desastres de máquinas virtuales de Azure, como los datos no sobrepasan los límites de Azure, no se necesita ExpressRoute para la replicación. Una vez que las máquinas virtuales se conmutan por error en la región de Azure de destino, puede acceder a ellas mediante el [emparejamiento privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replicación de implementaciones de Azure

En un [artículo](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity) anterior se describió una configuración simple con una red virtual de Azure conectada al centro de datos local del cliente a través de ExpressRoute. En las implementaciones empresariales habituales se dividen las cargas de trabajo entre varias redes virtuales de Azure y un centro de conectividad establece la conectividad externa con Internet y con las implementaciones locales.

En este ejemplo se describe una topología en estrella tipo hub-and-spoke que es habitual en las implementaciones empresariales:
-   La implementación está en la región **Asia Oriental de Azure** y el centro de datos local tiene una conexión con el circuito ExpressRoute a través de un servidor perimetral de un asociado en Hong Kong.
-   Las aplicaciones se implementan entre dos redes virtuales de radio: **Source VNet1** con el espacio de direcciones 10.1.0.0/24 y **Source VNet2** con el espacio de direcciones 10.2.0.0/24.
-   La red virtual de centro, **Source Hub VNet**, con un espacio de direcciones 10.10.10.0/24, actúa como equipo selector. Todas la comunicaciones entre las subredes pasa a través del centro.
-   La red virtual del centro tiene dos subredes: **NVA Subnet** con un espacio de direcciones 10.10.10.0/25 y **Gateway Subnet** con un espacio de direcciones 10.10.10.128/25.
-   La subred **NVA Subnet** tiene una aplicación virtual de red con la dirección IP 10.10.10.10.
-   La subred **Gateway Subnet** tiene una puerta de enlace de ExpressRoute conectada a una conexión de ExpressRoute que enruta el tráfico al centro de datos local del cliente mediante un dominio de enrutamiento de emparejamiento privado.
-   Cada red virtual de radio está conectada a la red virtual del centro y todo el enrutamiento en esta topología de red está controlado mediante tablas de rutas de Azure (UDR). Todo el tráfico saliente de una red virtual a la otra, o al centro de datos local, se enruta a través de la aplicación virtual de red.

![Del entorno local a Azure con ExpressRoute antes de la conmutación por error](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Emparejamiento tipo hub-and-spoke

El emparejamiento del radio al centro tiene la siguiente configuración:
-   Permitir dirección de red virtual: habilitado
-   Permitir tráfico reenviado: habilitado
-   Permitir tránsito de puerta de enlace: deshabilitado
-   Usar puertas de enlace remotas: habilitado

 ![Configuración del emparejamiento del radio al centro](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

El emparejamiento del centro al radio tiene la siguiente configuración:
-   Permitir dirección de red virtual: habilitado
-   Permitir tráfico reenviado: habilitado
-   Permitir tránsito de puerta de enlace: habilitado
-   Usar puertas de enlace remotas: deshabilitado

 ![Configuración del emparejamiento del centro al radio](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Habilitar replicación para la implementación

Para la instalación anterior, primero [configure la recuperación ante desastres](azure-to-azure-tutorial-enable-replication.md) de cada máquina virtual con Site Recovery. Site Recovery puede crear las redes virtuales de réplica (incluyendo subredes y subredes de puerta de enlace) en la región de destino y crear las asignaciones necesarias entre las redes virtuales de origen y destino. También puede crear previamente las redes y subredes en el lado de destino y usar las mismas al habilitar la replicación.

Site Recovery no replica tablas de rutas, puertas de enlace de red virtual, conexiones de puertas de enlace de red virtual, emparejamiento de redes virtuales ni ningún otro recurso o conexión de las redes. Estos y otros recursos que no forman parte del [proceso de replicación](azure-to-azure-architecture.md#replication-process) se deben crear durante la conmutación por error, o antes de esta, y conectar a los recursos pertinentes. Puede usar los potentes [planes de recuperación](site-recovery-create-recovery-plans.md) de Azure Site Recovery para automatizar la creación y conexión de recursos adicionales mediante scripts de automatización.

De forma predeterminada, el tráfico de replicación no debe sobrepasar el límite de Azure. Normalmente, las implementaciones de aplicaciones virtuales de red también definen una ruta predeterminada (0.0.0.0/0) que fuerza el tráfico de salida de Internet a través de la aplicación virtual de red. En este caso, la aplicación puede verse limitada si todo el tráfico de replicación pasa a través de la aplicación virtual de red. Lo mismo se aplica también cuando se usan las rutas predeterminadas para enrutar todo el tráfico de máquina virtual de Azure a implementaciones locales. Es recomendable [crear un punto de conexión de servicio de red virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) en la red virtual de "Storage" para que el tráfico de replicación no sobrepase el límite de Azure.

## <a name="failover-models-with-expressroute"></a>Modelos de conmutación por error con ExpressRoute

Cuando las máquinas virtuales de Azure se conmutan por error a una región diferente, la conexión de ExpressRoute existente a la red virtual de origen no se transfiere automáticamente a la red virtual de destino de la región de recuperación. Se necesita una conexión nueva para conectar ExpressRoute a la red virtual de destino.

Puede replicar máquinas virtuales de Azure en cualquier región de Azure del mismo clúster geográfico como se detalla [aquí](azure-to-azure-support-matrix.md#region-support). Si la región de Azure elegida como destino no está en la misma región geopolítica que la de origen, deberá habilitar ExpressRoute Premium si va a usar un único circuito ExpressRoute para la conectividad entre las regiones de origen y destino. Para más información, consulte [Ubicaciones de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) y [Precios de ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Dos circuitos ExpressRoute en dos ubicaciones de emparejamiento diferentes de ExpressRoute
-   Esta configuración es útil si desea asegurarse frente a errores del circuito ExpressRoute principal y ante posibles desastres regionales a gran escala que también podrían afectar a ubicaciones de emparejamiento de ExpressRoute e interrumpir el circuito ExpressRoute principal.
-   Normalmente se utiliza el circuito conectado al entorno de producción como circuito principal y el circuito secundario se usa solo como mecanismo de seguridad y normalmente tiene un ancho de banda más bajo. El ancho de banda del circuito secundario se puede aumentar en caso de desastre, cuando este circuito debe ocupar el lugar del principal.
-   Con esta configuración puede establecer conexiones desde el circuito ExpressRoute secundario a la red virtual de destino después de la conmutación por error o tener conexiones establecidas y listas para usarse en caso de declaración de desastre, lo cual reducirá el tiempo de recuperación global. Con conexiones simultáneas a la red virtual principal y a la de la región de destino, asegúrese de que el enrutamiento local usa el circuito y la conexión secundarios solo después de la conmutación por error.
-   Las redes virtuales de origen y de destino de las máquinas virtuales protegidas con Site Recovery pueden tener las mismas direcciones IP u otras distintas en la conmutación por error según sus necesidades. En ambos casos, se pueden establecer las conexiones secundarias antes de la conmutación por error.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Dos circuitos ExpressRoute en la misma ubicación de emparejamiento de ExpressRoute
-   Con esta configuración puede asegurarse frente a errores del circuito ExpressRoute principal, pero no ante posibles desastres regionales a gran escala que también podrían afectar a ubicaciones de emparejamiento de ExpressRoute. En este último caso se verían afectados los circuitos primario y secundario.
-   Las demás condiciones para las direcciones IP y las conexiones siguen siendo las mismas que en el caso anterior. Puede tener conexiones simultáneas desde el centro de datos local a la red virtual de origen con el circuito principal y a la red virtual de destino con el circuito secundario. Con conexiones simultáneas a la red virtual principal y a la de la región de destino, asegúrese de que el enrutamiento local usa el circuito y la conexión secundarios solo después de la conmutación por error.
-   Si los circuitos se crean en la misma ubicación de emparejamiento, no podrá conectarlos a la misma red virtual.

### <a name="single-expressroute-circuit"></a>Circuito ExpressRoute único
-   Esta configuración no protege frente a un desastre regional a gran escala que podría afectar a la ubicación de emparejamiento de ExpressRoute.
-   Con un único circuito ExpressRoute, no se pueden conectar simultáneamente las redes virtuales de origen y destino al circuito si se usa el mismo espacio de direcciones IP de la región de destino.
-   Cuando se utiliza el mismo espacio de direcciones IP en la región de destino, la conexión en el lado de origen se debe desconectar, y la del lado de destino se debe establecer posteriormente. Este cambio de conexión se puede establecer mediante script como parte de un plan de recuperación.
-   En un error regional, si la región primaria es inaccesible, la operación de desconexión podría producir error. Una interrupción de este tipo podría afectar a la creación de la conexión a la región de destino si se utiliza el mismo espacio de direcciones IP de la red virtual de destino.
-   Si la creación de la conexión se realiza correctamente en el destino y la región primaria se recupera posteriormente, puede hacer frente a la pérdida de paquetes si dos conexiones simultáneas intentan conectarse al mismo espacio de direcciones. Para evitar la pérdida de paquetes, se debe finalizar la conexión principal inmediatamente. Después de la conmutación por recuperación de las máquinas virtuales en la región primaria, se puede establecer de nuevo la conexión principal después de desconectar la conexión secundaria.
-   Si se utiliza un espacio de direcciones diferente en la red virtual de destino, puede conectar simultáneamente las redes virtuales de origen y de destino desde el mismo circuito ExpressRoute.

## <a name="recovering-azure-deployments"></a>Recuperación de implementaciones de Azure
Considere el modelo de conmutación por error con dos circuitos ExpressRoute diferentes en dos ubicaciones diferentes de emparejamiento, y la retención de direcciones IP privadas para las máquinas virtuales protegidas de Azure. La región de recuperación de destino de Azure es Asia Suroriental y se establece una conexión de circuito ExpressRoute secundario a través de un servidor perimetral asociado en Singapur.

Para automatizar la recuperación de toda la implementación, además de replicar máquinas virtuales y redes virtuales, se deben crear también otros recursos de red y conexiones importantes. En el caso de la anterior topología de estrella tipo hub-and-spoke, se deben tomar los siguientes pasos adicionales durante la operación de [conmutación por error](azure-to-azure-tutorial-failover-failback.md) o después de esta:
1.  Cree la puerta de enlace de Azure ExpressRoute en la red virtual del centro de la región de destino. Es necesario que la puerta de enlace de ExpressRoute conecte la red virtual del centro de destino al circuito ExpressRoute.
2.  Cree la conexión de red virtual desde la red virtual del centro de destino al circuito ExpressRoute de destino.
3.  Configure los emparejamientos de redes virtuales entre el centro de la región de destino y las redes virtuales de radio. Las propiedades de emparejamiento de la región de destino serán las mismas que aquellas de la región de origen.
4.  Configure las UDR en la red virtual de centro y en las dos redes virtuales de radio. Las propiedades de las UDR del lado de destino son las mismas que aquellas en el lado de origen si usan las mismas direcciones IP. Con distintas direcciones IP de destino, las UDR se deben modificar en consecuencia.

Los pasos anteriores se pueden establecer mediante script como parte de un [plan de recuperación](site-recovery-create-recovery-plans.md). Según los requisitos de conectividad de la aplicación y de tiempo de recuperación, se pueden completar los pasos anteriores antes de iniciar la conmutación por error.

Después de la recuperación de las máquinas virtuales y la finalización de los demás pasos de conectividad, el entorno de recuperación tiene el siguiente aspecto: ![Del entorno local a Azure con ExpressRoute después de la conmutación por error](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

[Aquí](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity) puede encontrar más información sobre un ejemplo de topología sencilla para recuperación ante desastres de máquinas virtuales de Azure con un único circuito ExpressRoute, con la misma dirección IP en las máquinas virtuales de destino.

## <a name="recovery-time-objective-rto-considerations"></a>Consideraciones sobre el objetivo de tiempo de recuperación (RTO)
Para reducir el tiempo total de recuperación de la implementación, es recomendable aprovisionar e implementar de antemano los [componentes de redes](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) adicionales de la región de destino como las puertas de enlace de la red virtual. Se asocia un pequeño tiempo de inactividad con la implementación de recursos adicionales y, este tiempo de inactividad puede afectar al tiempo de recuperación total, si este no se ha tenido en cuenta durante el planeamiento.

Es recomendable ejecutar [ejercicios de recuperación ante desastres](azure-to-azure-tutorial-dr-drill.md) con regularidad en las implementaciones protegidas. Mediante uno de estos ejercicios se valida su estrategia de replicación sin ocasionar ninguna pérdida de datos ni tiempo de inactividad, y sin afectar al entorno de producción. Ejecutar un ejercicio también evita problemas de configuración de última hora que pueden afectar negativamente al objetivo de tiempo de recuperación. Se recomienda usar una red de máquinas virtuales de Azure independiente en la conmutación por error de prueba en lugar de la red predeterminada que se configuró cuando habilitó la replicación.

Si usa un único circuito ExpressRoute, se recomienda utilizar un espacio de direcciones IP diferente para la red virtual de destino para evitar posibles problemas de establecimiento de conexión durante desastres regionales. Si no es posible usar direcciones IP diferentes para el entorno de producción recuperado, la conmutación por error de prueba del ejercicio de recuperación ante desastres se debería realizar en una red de prueba independiente con diferentes direcciones IP ya que no puede conectar dos redes virtuales con un espacio de direcciones IP que se superponga al mismo circuito ExpressRoute.

## <a name="next-steps"></a>Pasos siguientes
- Más información acerca de los [circuitos ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Más información acerca de los [dominios de enrutamiento de ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Más información acerca de las [ubicaciones de ExpressRoute](../expressroute/expressroute-locations.md).
- Más información sobre los [planes de recuperación](site-recovery-create-recovery-plans.md) para automatizar la conmutación por error de las aplicaciones.
