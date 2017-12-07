---
title: Operaciones de SAP HANA en Azure | Microsoft Docs
description: "Operaciones de SAP HANA en máquinas virtuales nativas de Azure"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA en la guía de operaciones de Azure
En esta guía se proporcionan instrucciones para el funcionamiento de los sistemas SAP HANA que se implementaron en Azure Virtual Machines. Este documento no pretende reemplazar ninguna de las documentaciones estándar de SAP. Puede encontrar las guías y notas de SAP en las ubicaciones siguientes:

- [Guía de administración de SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guías de instalación de SAP](https://service.sap.com/instguides)
- [Nota de SAP](https://sservice.sap.com/notes)

Como requisito previo, debe tener un conocimiento básico sobre los distintos componentes de Azure de:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes y redes virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Puede encontrar documentación adicional sobre SAP NetWeaver y otros componentes de SAP en Azure en la sección [SAP en Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) de la [documentación de Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Consideraciones básicas sobre la configuración
### <a name="connecting-into-azure"></a>Conexión a Azure
Tal como se documenta en [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver][planning-guide], existen dos métodos básicos para conectarse a Azure Virtual Machines. 

- Conexión a través de Internet y puntos de conexión públicos en una máquina virtual de Jump o en la máquina virtual que ejecuta SAP HANA
- Conexión a través de una [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

En el caso de escenarios de producción o donde los escenarios que no son de producción se incorporan a los escenarios de producción junto con el software de SAP, necesita tener una conectividad de sitio a sitio a través de VPN o de ExpressRoute, como se muestra en este gráfico:

![Conectividad entre sitios](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Elección de los tipos de máquina virtual de Azure
Puede buscar [aquí](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) los tipos de máquinas virtuales de Azure que se pueden usar para los escenarios de producción. En el caso de los escenarios que no son de producción, puede elegir una variedad más amplia de máquinas virtuales nativas de Azure. Sin embargo, debe restringirse a los tipos de máquina virtual que se indican en la [nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533). La implementación de esas máquinas virtuales en Azure se puede lograr a través de:

- Azure Portal
- Cmdlets de Azure Powershell
- CLI de Azure

También puede implementar la plataforma SAP HANA completamente instalada en los servicios de máquina virtual de Azure a través de [SAP Cloud platform](https://cal.sap.com/), tal como se documenta [aquí](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Elección del almacenamiento de Azure
Azure proporciona dos tipos principales de almacenamiento adecuados para las máquinas virtuales de Azure que ejecutan SAP HANA

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- 
            [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure ofrece dos métodos de implementación de discos duros virtuales en Azure Standard Storage y Azure Premium Storage. Si el escenario global lo permite, se recomienda usar las implementaciones de [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Para los tipos de almacenamiento exactos y los contratos de nivel de servicio relacionados con esos tipos de almacenamiento, consulte [esta documentación](https://azure.microsoft.com/pricing/details/managed-disks/)

Se recomienda usar Azure Premium Disks para los volúmenes /hana/data y /hana/log. Se admite la creación de un RAID de LVM en varios discos de Premium Storage y use esos volúmenes de RAID como volúmenes /hana/data y /hana/log.

Una configuración posible para los distintos tipos de máquinas virtuales comunes que los clientes hasta ahora usan para hospedar SAP HANA en las máquinas virtuales de Azure sería similar a:

| SKU de la máquina virtual | RAM | /hana/data y /hana/log<br /> seccionado con LVM o MDAADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Redes de Azure
Si tiene conectividad de sitio a sitio de VPM o ExpressRoute en Azure, como mínimo debería tener una [red virtual de Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) que se conecta a través de una puerta de enlace virtual al circuito VPN o ExpressRoute. La puerta de enlace virtual reside en una subred de la red virtual de Azure. Para instalar HANA, podría crear otras dos subredes dentro de la red virtual. Una subred que hospeda las máquinas virtuales que ejecutan las instancias de SAP HANA y otra subred que ejecuta máquinas virtuales JumpBox o de administración ocasionales que pueden hospedar SAP HANA Studio u otro software de administración.
Cuando se instalan las máquinas virtuales que deben ejecutar HANA, deben tener:

- Dos NIC virtuales instaladas, de las cuales una se conecta a la subred de administración y la otra se usa para conectarse ya sea desde el entorno local o desde otras redes a la instancia de SAP HANA en la máquina virtual.
- Direcciones IP privadas estáticas implementadas para ambas NIC virtuales

Puede encontrar información general sobre las distintas posibilidades de la asignación de direcciones IP [aquí](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Los [grupos de seguridad de red de Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) asociados con la subred de HANA y la subred de administración dirigen el enrutamiento de tráfico que va directamente a la instancia de SAP HANA o a la instancia de JumpBox.

En general, el esquema de implementación aproximado tendría un aspecto similar al siguiente:

![Esquema de implementación aproximado de SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Si solo implementa SAP HANA en Azure sin conectividad de sitio a sitio (VPN o ExpressRoute en Azure), podría acceder a la instancia de SAP HANA mediante una dirección IP pública asignada a la máquina virtual de Azure que ejecuta la máquina virtual de JumpBox. En un caso simple, solo se basa en los servicios DNS integrados de Azure para resolver los nombres de host. En especial cuando usa direcciones IP públicas, desea usar los grupos de seguridad de red de Azure para limitar los puertos abiertos o los intervalos de direcciones IP que tienen permitido conectarse en las subredes de Azure que ejecutan los activos con direcciones IP públicas. El esquema de una implementación de ese tipo podría tener el aspecto siguiente:
  
![Esquema de implementación aproximado para SAP HANA sin conexión de sitio a sitio](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Operaciones
### <a name="backup-and-restore-operations-on-azure-vms"></a>Operaciones de copia de seguridad y restauración en máquinas virtuales Azure
Las posibilidades de copia de seguridad y restauración de SAP HANA aparecen en estos documentos:

- [SAP HANA: información general sobre copias de seguridad](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA: copia de seguridad en el nivel de archivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA: banco de pruebas de instantánea de almacenamiento](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Inicio y reinicio de máquinas virtuales que contienen SAP HANA
Uno de los puntos fuertes de la nube pública de Azure es el hecho de que solo se cobran los minutos de proceso que se usan. Esto significa que si se apaga una máquina virtual que ejecuta SAP HANA, los costos de almacenamiento solo se facturan durante ese tiempo. Cuando vuelva a iniciar la máquina virtual con SAP HANA, la máquina virtual se volverá a iniciar y tendrá las mismas direcciones IP (si realizó la implementación con direcciones IP estáticas). 


### <a name="saprouter-enabling-sap-remote-support"></a>Habilitación de SAPRouter con la compatibilidad remota de SAP
Si tiene una conexión de sitio a sitio entre las ubicaciones locales y Azure y ya ejecuta los componentes de SAP, es muy probable que ya esté ejecutando SAPRouter. En este caso, no necesita hacer nada con las instancias de SAP HANA que implemente en Azure, excepto mantener las direcciones IP privadas y estáticas de la máquina virtual que hospeda HANA en la configuración de SAPRouter y adaptar el grupo de seguridad de red de la subred que hospeda la máquina virtual de HANA (se permite el tráfico a través del puerto ICP/IP 3299).

Si implementa SAP HANA y se conecta a Azure a través de Internet y no tiene instalado un enrutador de SAP en la red virtual que ejecuta una máquina virtual con SAP HANA, debe instalar SAPRouter en una máquina virtual en la subred de administración, tal como se muestra a continuación:


![Esquema de implementación aproximado para SAP HANA sin conexión de sitio a sitio y SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

Debe instalar SAPRouter en otra máquina virtual y no en la máquina virtual de JumpBox. La máquina virtual independiente necesita una dirección IP estática. Para conectar SAPRouter con la instancia de SAPRouter que SAP hospeda (equivalente a la instancia de SAPRouter que instala en la máquina virtual), debe contactarse con SAP para obtener una dirección IP que necesita para configurar la instancia de SAPRouter. El único puerto necesario es el puerto TCP 3299.
Para más información sobre cómo configurar y mantener conexiones de compatibilidad remota a través de SAPRouter, compruebe este [origen de SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidad con SAP HANA en máquinas virtuales nativas de Azure
La ejecución de SUSE Linux 12 SP1 y versiones más recientes le permite establecer un clúster de Pacemaker con dispositivos STONITH para establecer una configuración de SAP HANA que usa la replicación sincrónica con replicación y conmutación automática del sistema de HANA. El procedimiento de configuración se describe en el artículo [Alta disponibilidad de SAP HANA en Azure Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










