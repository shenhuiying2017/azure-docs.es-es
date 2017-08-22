---
title: "Planeamiento de redes de VMware para la  replicación de Azure con Site Recovery | Microsoft Docs"
description: "Este artículo describe el planeamiento de las redes necesarias al replicar máquinas virtuales de Azure con Azure Site Recovery"
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
ms.date: 08/01/2017
ms.author: sujayt
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.contentlocale: es-es
ms.lasthandoff: 08/02/2017

---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>Paso 3: Plan sobre las redes para la replicación de máquinas virtuales de Azure

Después de comprobar los [requisitos previos para la implementación](azure-to-azure-walkthrough-prerequisites.md),lea este artículo para entender las consideraciones de la red cuando se replican y recuperan máquinas virtuales de Azure (VM) de una región de Azure en otra, mediante el servicio Azure Site Recovery. 

- Cuando termine el artículo, debe tener una idea clara de cómo configurar el acceso de salida para las máquinas virtuales de Azure que desee replicar y cómo conectarse desde su sitio local a las máquinas virtuales.
- Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
> La replicación de máquinas virtuales de Azure con Site Recovery está actualmente en versión preliminar.



## <a name="network-overview"></a>Información general sobre redes

Normalmente se encuentran las máquinas virtuales de Azure en una red/subred virtual de Azure y no hay una conexión desde su sitio local a Azure mediante Azure ExpressRoute o una conexión VPN.

Normalmente, los clientes protegen sus redes mediante firewalls o grupos de seguridad de red (NSG). Firewalls puede usar listas basadas en IP o en URL para controlar la conectividad de la red. NSG usan reglas basadas en intervalos IP. 


Para que Site Recovery funcione de la forma esperada, debe realizar algunos cambios en la conectividad de red de salida desde las máquinas virtuales que desee replicar. Site Recovery no admite el uso de un servidor proxy de autenticación para la conectividad de la red de control. Si tiene un servidor proxy de autenticación, no se puede habilitar la replicación. 


El siguiente diagrama representa el entorno típico para una aplicación que se ejecuta en máquinas virtuales de Azure.

![Entorno de cliente](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Entorno de máquina virtual de Azure**

También podría tener una conexión a la configuración de Azure desde el sitio local mediante una conexión VPN o Azure ExpressRoute. 

![Entorno de cliente](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Conexión a local a Azure**



## <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en la dirección URL para controlar la conectividad de salida, asegúrese de que admite estas direcciones URL usadas por Site Recovery

**URL** | **Detalles**  
--- | ---
*.blob.core.windows.net | Permite que los datos se puedan escribir desde la máquina virtual a la cuenta de almacenamiento de caché en la región de origen.
login.microsoftonline.com | Proporciona autorización y autenticación de las direcciones URL del servicio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Permite la comunicación con el servicio Site Recovery de la máquina virtual.
*.servicebus.windows.net | Se requiere para que se puedan escribir datos de supervisión y diagnóstico de Site Recovery desde la máquina virtual.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

- Puede crear automáticamente las reglas necesarias en el NSG al descargar y ejecutar [este script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).
- Se recomienda crear las reglas de NSG necesarias en un grupo NSG de NSG de prueba y comprobar que no haya ningún problema antes de crear las reglas en un grupo de NSG de producción.
- Para crear el número necesario de reglas de NSG, asegúrese de que su suscripción esté incluida en la lista de permitidos. Si desea aumentar el límite de reglas de NSG de su suscripción, póngase en contacto con el soporte técnico.
Si va a usar un proxy de firewall basado en IP o reglas de NSG para controlar la conectividad saliente, es necesario incluir en lista banca los siguientes intervalos IP, según las ubicaciones de origen y destino de las máquinas virtuales:

    - Todos los intervalos de dirección IP que correspondan a la ubicación de origen. Descargar los [intervalos](https://www.microsoft.com/download/confirmation.aspx?id=41653).) Es necesaria la inclusión en la lista de permitidos para que los datos se puedan escribir en la cuenta de almacenamiento en caché de la máquina virtual.
    - Todos los intervalos IP que corresponden a los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365. Si se agregan nuevas direcciones IP a los intervalos IP de Office 365, deberá crear nuevas reglas de NSG.
-     Las direcciones IP de punto de conexión del servicio de Site Recovery ([disponibles en un archivo XML](https://aka.ms/site-recovery-public-ips)), que dependen de la ubicación de destino: 

   **Ubicación de destino** | **Direcciones IP del servicio de Site Recovery** |  **IP de supervisión de Site Recovery**
   --- | --- | ---
   Asia oriental | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   Sudeste asiático | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   India Central | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   Sur de la India | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   Centro-Norte de EE. UU | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   Europa del Norte | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   Europa occidental | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   Este de EE. UU | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   Oeste de EE. UU | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   Centro-Sur de EE. UU | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   Central EE. UU: | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   Este de EE. UU. 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   Este de Japón | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   Oeste de Japón | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   Sur de Brasil | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   Australia Oriental | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   Sudeste de Australia | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   Centro de Canadá | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   Este de Canadá | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   Centro occidental de EE.UU | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   Oeste de EE. UU 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   Oeste de Reino Unido | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   Sur del Reino Unido 2 | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>Configuración de NSG de ejemplo

Esta sección muestra cómo configurar las reglas NSG, por lo que las replicaciones funcionan para una máquina virtual. Si usa reglas de NSG para controlar la conectividad de salida, utilice reglas para permitir el tráfico HTTPS de salida en todos los intervalos IP necesarios.

En este ejemplo, la ubicación de origen de la máquina virtual es el este de EE. UU. La ubicación de destino de replicación es el centro de EE. UU.


### <a name="example"></a>Ejemplo

#### <a name="east-us"></a>Este de EE. UU.

1. Cree reglas que se correspondan con [intervalos de IP del este de EE. UU](https://www.microsoft.com/download/confirmation.aspx?id=41653). Esto es necesario para que los datos se puedan escribir en la cuenta de almacenamiento en caché desde la máquina virtual.
2. Cree reglas para todos los intervalos IP que se correspondan con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.
3. Cree reglas que se correspondan con la ubicación de destino:

   **Ubicación** | **Direcciones IP del servicio de Site Recovery** |  **IP de supervisión de Site Recovery**
    --- | --- | ---
   Central EE. UU.: | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>Central EE. UU.:

Estas reglas son necesarias para que la replicación se pueda habilitar de la región de destino a la región de origen después de la conmutación por error.

1. Reglas que se corresponden con [intervalos IP del centro de EE. UU](https://www.microsoft.com/download/confirmation.aspx?id=41653).
2. Cree reglas para todos los intervalos IP que se correspondan con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.
3. Reglas que se corresponden con la ubicación de origen:

   **Ubicación** | **Direcciones IP del servicio de Site Recovery** |  **IP de supervisión de Site Recovery**
    --- | --- | ---
   Este de EE. UU. | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>Conexión existente a local

Si tiene una conexión ExpressRoute o VPN entre el entorno local y la ubicación de origen en Azure, siga las siguientes directrices:

**Configuración** | **Detalles**
--- | ---
**Tunelización forzada** | Normalmente, una ruta predeterminada (0.0.0.0/0) fuerza el tráfico de salida de internet a través de la ubicación local. Esta opción no se recomienda. El tráfico de replicación y las comunicaciones de Site Recovery deben permanecer dentro de Azure.<br/><br/> La solución consiste en agregar rutas definidas por el usuario (UDR) para [estos intervalos IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) de forma que el tráfico no pase el entorno local.
**Conectividad** | Si las aplicaciones necesitan conectarse a máquinas locales o los clientes en local se conectan a la aplicación en local y a través de VPN o ExpressRoute, asegúrese de que tiene al menos una [conexión de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre la región de Azure de destino y el sitio local.<br/><br/> Si los volúmenes de tráfico son altos entre la región de Azure de destino y el sitio local, cree otra [conexión ExpressRoute](../expressroute/expressroute-introduction.md) entre la región de destino y local.<br/><br/> Si desea conservar las direcciones IP de las máquinas virtuales después de que realicen una conmutación por error, mantenga la conexión de sitio a sitio o de ExpressRoute de la región de destino en un estado desconectado. Esto garantiza que no habrá conflictos de intervalo entre los intervalos de direcciones IP de origen y de destino.
**ExpressRoute** | Debe crear un circuito ExpressRoute en las regiones de origen y de destino.<br/><br/> Crear una conexión entre la red de origen y el circuito de ExpressRoute y entre la red de destino y el circuito.<br/><br/> Se recomienda usar diferentes intervalos IP en las regiones de origen y destino. El circuito no podrá conectarse al mismo tiempo a más de una red de Azure con los mismos intervalos IP.<br/><br/> Puede crear redes virtuales con los mismos intervalos IP en ambas regiones y luego crear circuitos ExpressRoute en ambas regiones. En el caso de conmutación por error, desconecte el circuito de la red virtual de origen y conéctelo en la red virtual de destino.<br/><br/> Si la región primaria está completamente inactiva, la operación de desconexión podría dar error. En este caso, la red virtual de destino no obtendrá la conectividad de ExpressRoute.
**ExpressRoute Premium** | Puede crear circuitos en la misma región geopolítica.<br/><br/> Para crear circuitos en distintas regiones geopolíticas, necesita Azure ExpressRoute Premium.<br/><br/> Con Premium, el costo es progresivo. Si ya está usándolo, no hay ningún costo adicional.<br/><br/> Obtenga más información sobre [ubicaciones](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) y [precios](https://azure.microsoft.com/pricing/details/expressroute/).



## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 4: Creación de un almacén](azure-to-azure-walkthrough-vault.md)


