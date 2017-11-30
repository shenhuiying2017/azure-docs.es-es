---
title: "Guía de redes de Azure Site Recovery para la replicación de máquinas virtuales de Azure a Azure | Microsoft Docs"
description: "Guía de redes para la replicación de máquinas virtuales de Azure"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: a2ea66f2ed3815d0375001571e64dad338f16e3e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Guía de redes para la replicación de máquinas virtuales de Azure

>[!NOTE]
> La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.

En este artículo se detalla la guía de redes para Azure Site Recovery cuando se van a replicar y recuperar máquinas virtuales de Azure de una región a otra. Para más información sobre los requisitos de Azure Site Recovery, consulte los [requisitos previos](site-recovery-support-matrix-azure-to-azure.md).

## <a name="site-recovery-architecture"></a>Arquitectura de Site Recovery

Site Recovery ofrece una manera sencilla de replicar las aplicaciones que se ejecutan en máquinas virtuales de Azure a otra región de Azure. De esta forma, se podrán recuperar en caso de una interrupción en la región primaria. Aprenda más sobre [este escenario y la arquitectura de Site Recovery](concepts-azure-to-azure-architecture.md).

## <a name="your-network-infrastructure"></a>La infraestructura

El siguiente diagrama representa el entorno de Azure típico para una aplicación que se ejecuta en máquinas virtuales de Azure:

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Si usa Azure ExpressRoute o una conexión VPN de una red local a Azure, el entorno tendrá este aspecto:

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, los clientes protegen sus redes mediante firewalls o grupos de seguridad de red (NSG). Para controlar la conectividad de red, los firewalls pueden usar la inclusión en lista de direcciones IP o URL permitidas. Los NSG permiten reglas para usar intervalos de IP en el control de la conectividad de red.

>[!IMPORTANT]
> Si va a usar un proxy autenticado para controlar la conectividad de red, no se admite, y la replicación de Site Recovery no se podrá habilitar.

En las secciones siguientes se describen los cambios en la conectividad de red saliente que son necesarios en las máquinas virtuales de Azure para que funcione Site Recovery.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Conectividad saliente para direcciones URL de Azure Site Recovery

Si va a usar un proxy de firewall basado en direcciones URL para controlar la conectividad saliente, asegúrese de incluir en la lista de permitidos estas direcciones URL de servicio de Azure Site Recovery que son necesarias:


**URL** | **Propósito**  
--- | ---
*.blob.core.windows.net | Se requiere para que los datos se puedan escribir en la cuenta de almacenamiento de la caché en la región de origen de la máquina virtual.
login.microsoftonline.com | Se requiere para la autorización y la autenticación de las direcciones URL del servicio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Se requiere para la comunicación del servicio Site Recovery desde la máquina virtual.
*.servicebus.windows.net | Se requiere para que se puedan escribir datos de supervisión y diagnóstico de Site Recovery desde la máquina virtual.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Conectividad saliente para intervalos IP de Azure Site Recovery

>[!NOTE]
> Para crear automáticamente las reglas de NSG necesarias en el grupo de seguridad de red, puede [descargar y usar este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

>[!IMPORTANT]
> * Se recomienda que cree las reglas de NSG necesarias en un grupo de seguridad de red de prueba y que compruebe que no haya ningún problema antes de crear las reglas en un grupo de seguridad de red de producción.
> * Para crear el número necesario de reglas de NSG, asegúrese de que su suscripción esté incluida en la lista de permitidos. Si desea aumentar el límite de reglas de NSG de su suscripción, póngase en contacto con el soporte técnico.

Si va a usar un proxy de firewall basado en IP o reglas de NSG para controlar la conectividad saliente, es necesario incluir en lista banca los siguientes intervalos IP, según las ubicaciones de origen y destino de las máquinas virtuales:

- Todos los intervalos IP que correspondan a la ubicación de origen. (Puede descargar los [intervalos IP](https://www.microsoft.com/download/confirmation.aspx?id=41653)). Es necesaria la inclusión en la lista de permitidos para que los datos se puedan escribir en la cuenta de almacenamiento en caché de la máquina virtual.

- Todos los intervalos IP que corresponden a los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.

    >[!NOTE]
    > Si se agregan en el futuro nuevas direcciones IP a los intervalos IP de Office 365 IP, deberá crear nuevas reglas de NSG.

- Las direcciones IP de punto de conexión del servicio de Site Recovery ([disponibles en un archivo XML](https://aka.ms/site-recovery-public-ips)), que dependen de la ubicación de destino:

   **Ubicación de destino** | **Direcciones IP del servicio de Site Recovery** |  **IP de supervisión de Site Recovery**
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

## <a name="sample-nsg-configuration"></a>Configuración de NSG de ejemplo
En esta sección se explican los pasos para configurar reglas de NSG de forma que la replicación de Site Recovery pueda funcionar en una máquina virtual. Si usa reglas de NSG para controlar la conectividad de salida, utilice reglas para permitir el tráfico HTTPS de salida en todos los intervalos IP necesarios.

>[!Note]
> Para crear automáticamente las reglas de NSG necesarias en el grupo de seguridad de red, puede [descargar y usar este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

Por ejemplo, si la ubicación de origen de la máquina virtual es "Este de EE. UU." y la ubicación del destino de la replicación es "Centro de EE. UU.", siga los pasos descritos en las dos secciones siguientes.

>[!IMPORTANT]
> * Se recomienda que cree las reglas de NSG necesarias en un grupo de seguridad de red de prueba y que compruebe que no haya ningún problema antes de crear las reglas en un grupo de seguridad de red de producción.
> * Para crear el número necesario de reglas de NSG, asegúrese de que su suscripción esté incluida en la lista de permitidos. Si desea aumentar el límite de reglas de NSG de su suscripción, póngase en contacto con el soporte técnico.

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>Reglas de NSG en el grupo de seguridad de red de Este de EE. UU.

* Cree reglas que se correspondan con [intervalos de IP del este de EE. UU](https://www.microsoft.com/download/confirmation.aspx?id=41653). Esto es necesario para que los datos se puedan escribir en la cuenta de almacenamiento en caché desde la máquina virtual.

* Cree reglas para todos los intervalos IP que se correspondan con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.

* Cree reglas que se correspondan con la ubicación de destino:

   **Ubicación** | **Direcciones IP del servicio de Site Recovery** |  **IP de supervisión de Site Recovery**
    --- | --- | ---
   Central EE. UU.: | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>Reglas de NSG en el grupo de seguridad de red de Centro de EE. UU.

Estas reglas son necesarias para que la replicación se pueda habilitar de la región de destino a la región de origen con posterioridad a la conmutación por error:

* Reglas que se corresponden con [intervalos IP del centro de EE. UU](https://www.microsoft.com/download/confirmation.aspx?id=41653). Son necesarias para que los datos se puedan escribir en la cuenta de almacenamiento en caché desde la máquina virtual.

* Reglas para todos los intervalos IP que se corresponden con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.

* Reglas que se corresponden con la ubicación de origen:

   **Ubicación** | **Direcciones IP del servicio de Site Recovery** |  **IP de supervisión de Site Recovery**
    --- | --- | ---
   Este de EE. UU. | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Directrices para la configuración de ExpressRoute o VPN existente de Azure en el entorno local

Si tiene una conexión ExpressRoute o VPN entre el entorno local y la ubicación de origen en Azure, siga las directrices descritas en esta sección.

### <a name="forced-tunneling-configuration"></a>Configuración de la tunelización forzada

Una configuración de cliente común consiste en definir una ruta predeterminada (0.0.0.0/0) que fuerza a que el tráfico saliente de Internet pase por la ubicación local. Esto no se recomienda. El tráfico de replicación y la comunicación con el servicio de Site Recovery no deben salirse del límite de Azure. La solución consiste en agregar rutas definidas por el usuario (UDR) para [estos intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) de forma que el tráfico de replicación no pase el entorno local.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>Conectividad entre la ubicación de destino y local

Para las conexiones que tienen lugar entre la ubicación de destino y la ubicación local, siga estas instrucciones:
- Si la aplicación necesita conectarse a las máquinas locales o si hay clientes que de conectan a la aplicación desde el entorno local mediante VPN/ExpressRoute, asegúrese de tener al menos una [conexión de sitio a sitio](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre la región de Azure de destino y el centro de datos local.

- Si espera un flujo de tráfico denso entre la región de Azure de destino y el centro de datos local, debe crear otra [conexión ExpressRoute](../../expressroute/expressroute-introduction.md) entre ellos.

- Si desea conservar las direcciones IP de las máquinas virtuales después de que realizan una conmutación por error, mantenga la conexión de sitio a sitio o de ExpressRoute de la región de destino en un estado desconectado. De esta forma se asegura de que no hay ningún conflicto entre los intervalos IP de la región de origen y los de la región de destino.

### <a name="best-practices-for-expressroute-configuration"></a>Procedimientos recomendados para la configuración de ExpressRoute
Siga estos procedimientos recomendados para la configuración de ExpressRoute:

- Debe crear un circuito ExpressRoute en las regiones de origen y de destino. Luego, debe crear una conexión entre:
  - La red virtual de origen y el circuito ExpressRoute.
  - La red virtual de destino y el circuito ExpressRoute.

- Como parte del estándar de ExpressRoute, puede crear circuitos en la misma región geopolítica. Para crear circuitos ExpressRoute en distintas regiones geopolíticas, se requiere Azure ExpressRoute Premium, lo que implica un costo incremental. (Si ya usa ExpressRoute Premium, no existen costos adicionales). Para más información, consulte el [documento de ubicaciones de ExpressRoute](../../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) y el artículo sobre [precios de ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- Se recomienda usar diferentes intervalos IP en las regiones de origen y destino. El circuito ExpressRoute no podrá conectarse con dos redes virtuales de Azure de los mismos intervalos IP al mismo tiempo.

- Puede crear redes virtuales con los mismos intervalos IP en ambas regiones y luego crear circuitos ExpressRoute en ambas regiones. En el caso de un evento de conmutación por error, desconecte el circuito de la red virtual de origen y conéctelo en la red virtual de destino.

 >[!IMPORTANT]
 > Si la región principal está completamente inactiva, la operación de desconexión podría dar error, lo que impedirá que la red virtual de destino obtenga conectividad de ExpressRoute.

## <a name="next-steps"></a>Pasos siguientes
Comience a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](azure-to-azure-quickstart.md).
