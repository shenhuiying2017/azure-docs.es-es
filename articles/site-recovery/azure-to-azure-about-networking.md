---
title: "Acerca de las redes en Azure para la recuperación ante desastres de Azure mediante Azure Site Recovery | Microsoft Docs"
description: "Proporciona información general de las redes para la replicación de máquinas virtuales de Azure mediante Azure Site Recovery."
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: sujayt
ms.openlocfilehash: 5ce85761df4e0ad62c22a829f67464a3145fd827
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Acerca de las redes en Azure para la replicación de Azure

>[!NOTE]
> La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.

En este artículo se proporciona la guía de redes cuando se van a replicar y recuperar máquinas virtuales de Azure de una región a otra, mediante [Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Antes de comenzar

Obtenga información sobre cómo Site Recovery proporciona recuperación ante desastres para [este escenario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infraestructura de red típica

El siguiente diagrama representa el entorno de Azure típico para aplicaciones que se ejecutan en máquinas virtuales de Azure:

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Si usa Azure ExpressRoute o una conexión VPN de su red local a Azure, el entorno tendrá este aspecto:

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, los clientes protegen sus redes mediante firewalls y grupos de seguridad de red (NSG). Los firewalls usan la inclusión en lista blanca basada en direcciones URL o IP para controlar la conectividad de red. Los NSG proporcionan reglas que usan intervalos de direcciones IP para controlar la conectividad de red.

>[!IMPORTANT]
> Site Recovery no admite el uso de un proxy autenticado para controlar la conectividad de red y la replicación no se podrá habilitar.


## <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en la dirección URL para controlar la conectividad de salida, admita estas direcciones URL de Site Recovery:


**URL** | **Detalles**  
--- | ---
* .blob.core.windows.net | Se requiere para que los datos se puedan escribir en la cuenta de almacenamiento de la caché en la región de origen de la máquina virtual.
login.microsoftonline.com | Se requiere para la autorización y la autenticación de las direcciones URL del servicio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Se requiere para la comunicación del servicio Site Recovery desde la máquina virtual.
*.servicebus.windows.net | Se requiere para que se puedan escribir datos de supervisión y diagnóstico de Site Recovery desde la máquina virtual.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

Si utiliza un firewall basado en IP, proxy o reglas NSG para controlar la conectividad saliente, estos intervalos IP tienen que permitirse.

- Todos los intervalos de dirección IP que correspondan a la ubicación de origen.
    - Puede descargar los [intervalos de direcciones IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Es necesario que permita estas direcciones para que los datos se puedan escribir en la cuenta de almacenamiento en caché, desde la máquina virtual.
- Todos los intervalos de direcciones IP que corresponden a los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.
    - Si se agregan en el futuro nuevas direcciones a los intervalos de Office 365, deberá crear nuevas reglas de NSG.
- Direcciones IP de puntos de conexión del servicio Site Recovery. Se encuentran disponibles en un [archivo XML](https://aka.ms/site-recovery-public-ips) y dependen de su ubicación de destino.
-  Puede [descargar y usar este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702), para crear automáticamente las reglas necesarias en el NSG. 
- Se recomienda crear las reglas de NSG necesarias en un grupo NSG de NSG de prueba y comprobar que no haya ningún problema antes de crear las reglas en un grupo de NSG de producción.
- Para crear el número necesario de reglas de NSG, asegúrese de que su suscripción esté incluida en la lista de permitidos. Si desea aumentar el límite de reglas de NSG de su suscripción, póngase en contacto con el soporte técnico de Azure.

Los intervalos de direcciones IP son los siguientes:

>
   **Destino** | **IP de Site Recovery** |  **IP de supervisión de Site Recovery**
   --- | --- | ---
   Asia oriental | 52.175.17.132 | 13.94.47.61
   Sudeste asiático | 52.187.58.193 | 13.76.179.223
   India Central | 52.172.187.37 | 104.211.98.185
   Sur de la India | 52.172.46.220 | 104.211.224.190
   Centro-Norte de EE. UU | 23.96.195.247 | 168.62.249.226
   Europa del Norte | 40.69.212.238 | 52.169.18.8
   Europa occidental | 52.166.13.64 | 40.68.93.145
   Este de EE. UU | 13.82.88.226 | 104.45.147.24
   Oeste de EE. UU | 40.83.179.48 | 104.40.26.199
   Centro-Sur de EE. UU | 13.84.148.14 | 104.210.146.250
   Central EE. UU: | 40.69.144.231 | 52.165.34.144
   Este de EE. UU. 2 | 52.184.158.163 | 40.79.44.59
   Este de Japón | 52.185.150.140 | 138.91.1.105
   Oeste de Japón | 52.175.146.69 | 138.91.17.38
   Sur de Brasil | 191.234.185.172 | 23.97.97.36
   Australia Oriental | 104.210.113.114 | 191.239.64.144
   Sudeste de Australia | 13.70.159.158 | 191.239.160.45
   Centro de Canadá | 52.228.36.192 | 40.85.226.62
   Este de Canadá | 52.229.125.98 | 40.86.225.142
   Centro occidental de EE.UU | 52.161.20.168 | 13.78.149.209
   Oeste de EE. UU 2 | 52.183.45.166 | 13.66.228.204
   Oeste de Reino Unido | 51.141.3.203 | 51.141.14.113
   Sur del Reino Unido 2 | 51.140.43.158 | 51.140.189.52
   Sur del Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte del Reino Unido | 51.142.209.167 | 13.87.102.68
   Corea Central | 52.231.28.253 | 52.231.32.85
   Corea del Sur | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>Configuración de NSG de ejemplo

En este ejemplo se muestra cómo configurar reglas de NSG para la replicación de una máquina virtual. 

- Si usa reglas de NSG para controlar la conectividad de salida, utilice reglas para permitir el tráfico HTTPS de salida en todos los intervalos de direcciones IP necesarios.
- En el ejemplo se presume que la ubicación de origen de máquina virtual es "Este de EE. UU." y la ubicación de destino es "Centro de EE. UU".

### <a name="nsg-rules---east-us"></a>Reglas de NSG: este de EE. UU.

1. Cree reglas que se correspondan con [intervalos de direcciones IP del este de EE. UU](https://www.microsoft.com/download/confirmation.aspx?id=41653). Esto es necesario para que los datos se puedan escribir en la cuenta de almacenamiento en caché desde la máquina virtual.
2. Cree reglas para todos los intervalos de direcciones IP que se correspondan con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.
3. Cree reglas que se correspondan con la ubicación de destino:

   **Ubicación** | **Dirección IP de Site Recovery** |  **Dirección IP de supervisión de Site Recovery**
    --- | --- | ---
   Central EE. UU: | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reglas de NSG: centro de EE. UU. 

Estas reglas son necesarias para que la replicación se pueda habilitar de la región de destino a la región de origen con posterioridad a la conmutación por error:

* Reglas que se corresponden con [intervalos IP del centro de EE. UU](https://www.microsoft.com/download/confirmation.aspx?id=41653). Son necesarias para que los datos se puedan escribir en la cuenta de almacenamiento en caché desde la máquina virtual.

* Reglas para todos los intervalos IP que se corresponden con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.

* Reglas que se corresponden con la ubicación de origen:
    - Este de EE. UU
    - Dirección IP de Site Recovery: 13.82.88.226
    - Dirección IP de supervisión de Site Recovery: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Si tiene una conexión ExpressRoute o VPN entre el entorno local y la ubicación de Azure, siga las directrices descritas en esta sección.

### <a name="forced-tunneling"></a>Tunelización forzada

Normalmente, puede definir una ruta predeterminada (0.0.0.0/0) que fuerza el tráfico de salida de Internet a través de la ubicación local. Esto no se recomienda. El tráfico de replicación y la comunicación con el servicio de Site Recovery no deben salirse del límite de Azure. La solución consiste en agregar rutas definidas por el usuario (UDR) para [estos intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) de forma que el tráfico de replicación no pase el entorno local.

### <a name="connectivity"></a>Conectividad 

Para las conexiones que tienen lugar entre la ubicación de destino y la ubicación local, siga estas instrucciones:
- Si la aplicación necesita conectarse a las máquinas locales o si hay clientes que de conectan a la aplicación desde el entorno local mediante VPN/ExpressRoute, asegúrese de tener al menos una [conexión de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre la región de Azure de destino y el centro de datos local.

- Si espera un flujo de tráfico denso entre la región de Azure de destino y el centro de datos local, debe crear otra [conexión ExpressRoute](../expressroute/expressroute-introduction.md) entre ellos.

- Si desea conservar las direcciones IP de las máquinas virtuales después de que realizan una conmutación por error, mantenga la conexión de sitio a sitio o de ExpressRoute de la región de destino en un estado desconectado. De esta forma se asegura de que no hay ningún conflicto entre los intervalos IP de la región de origen y los de la región de destino.

### <a name="expressroute-configuration"></a>Configuración de ExpressRoute
Siga estos procedimientos recomendados para la configuración de ExpressRoute:

- Debe crear un circuito ExpressRoute en las regiones de origen y de destino. Luego, debe crear una conexión entre:
  - La red virtual de origen y el circuito ExpressRoute.
  - La red virtual de destino y el circuito ExpressRoute.

- Como parte del estándar de ExpressRoute, puede crear circuitos en la misma región geopolítica. Para crear circuitos ExpressRoute en distintas regiones geopolíticas, se requiere Azure ExpressRoute Premium, lo que implica un costo incremental. (Si ya usa ExpressRoute Premium, no existen costos adicionales). Para más información, consulte el [documento de ubicaciones de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) y el artículo sobre [precios de ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- Se recomienda usar diferentes intervalos IP en las regiones de origen y destino. El circuito ExpressRoute no podrá conectarse con dos redes virtuales de Azure de los mismos intervalos IP al mismo tiempo.

- Puede crear redes virtuales con los mismos intervalos IP en ambas regiones y luego crear circuitos ExpressRoute en ambas regiones. En el caso de un evento de conmutación por error, desconecte el circuito de la red virtual de origen y conéctelo en la red virtual de destino.

 >[!IMPORTANT]
 > Si la región principal está completamente inactiva, la operación de desconexión podría dar error, lo que impedirá que la red virtual de destino obtenga conectividad de ExpressRoute.

## <a name="next-steps"></a>pasos siguientes
Comience a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](site-recovery-azure-to-azure.md).
