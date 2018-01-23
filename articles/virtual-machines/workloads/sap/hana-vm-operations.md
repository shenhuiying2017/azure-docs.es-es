---
title: Operaciones de SAP HANA en Azure | Microsoft Docs
description: "Guía de operaciones para los sistemas SAP HANA que se implementan en Azure Virtual Machines."
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
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA en la guía de operaciones de Azure
En este documento se proporcionan instrucciones para el funcionamiento de los sistemas SAP HANA que se implementaron en Azure Virtual Machines (VM) nativas. Este documento no pretende reemplazar ninguna documentación estándar de SAP, incluido el contenido siguiente:

- [Guía de administración de SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guías de instalación de SAP](https://service.sap.com/instguides)
- [Notas de SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>requisitos previos
Para utilizar esta guía, necesita un conocimiento básico de los siguientes componentes de Azure:

- [Máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes de azure y Azure Virtual Network](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para obtener más información acerca de SAP NetWeaver y otros componentes de SAP en Azure, consulte la sección [SAP en Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) de la [documentación de Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Consideraciones básicas sobre la configuración
En las siguientes secciones se describen las consideraciones de configuración básicas para implementar sistemas SAP HANA en máquinas virtuales de Azure.

### <a name="connect-into-azure-virtual-machines"></a>Conectarse a Azure Virtual Machines
Como se documenta en la [guía de planeamiento de Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), existen dos métodos básicos para conectarse a máquinas virtuales de Azure:

- Conéctese a través de Internet y de los puntos de conexión públicos en una máquina virtual de Jump o en la máquina virtual que ejecuta SAP HANA.
- Conéctese a través de una [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o de Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

La conectividad de sitio a sitio a través de VPN o ExpressRoute es necesaria para los escenarios de producción. Este tipo de conexión también es necesario para escenarios de no producción que se incorporan en escenarios de producción cuando se usa el software SAP. La imagen siguiente muestra un ejemplo de conectividad entre sitios:

![Conectividad entre sitios](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Elegir tipos de máquina virtual de Azure
Los tipos de máquina virtual de Azure que pueden utilizarse para escenarios de producción se muestran en la [documentación de SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). En el caso de los escenarios que no son de producción, existe una variedad más amplia de tipos de máquinas virtuales de Azure disponible.

>[!NOTE]
>Para escenarios no de producción, use los tipos de máquinas virtuales que se enumeran en la [nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Implemente las máquinas virtuales en Azure mediante:

- Azure Portal.
- Los cmdlets de Azure PowerShell.
- La CLI de Azure.

También puede implementar una plataforma SAP HANA completamente instalada en los servicios de máquina virtual de Azure a través de la [plataforma en la nube de SAP](https://cal.sap.com/). El proceso de instalación se describe en [Implementación de SAP S/4HANA o BW/4HANA en Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choose-azure-storage-type"></a>Elegir un tipo de Azure Storage
Azure proporciona dos tipos de almacenamiento adecuados para máquinas virtuales de Azure que ejecutan SAP HANA:

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure ofrece dos métodos de implementación de discos duros virtuales en Azure Standard Storage y Azure Premium Storage. Si el escenario global lo permite, aproveche las implementaciones de [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Para obtener una lista de tipos de almacenamiento y sus SLA, revise la [documentación de Azure para Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Se recomienda usar Azure Premium Disks para los volúmenes /hana/data y /hana/log. Puede crear un RAID de LVM en varios discos de Premium Storage y usar esos volúmenes de RAID como volúmenes /hana/data y /hana/log.

En la tabla siguiente se muestra una configuración de tipos de VM que los clientes suelen usar para hospedar SAP HANA en máquinas virtuales de Azure:

| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | /hana/data y /hana/log<br /> seccionado con LVM o MDAADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GB | 384 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 GB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 GB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 GB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 GB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> Discos recomendados para los tipos de máquinas virtuales más pequeños con un exceso de tamaño de los volúmenes de 3 x P20 con respecto a las recomendaciones de espacio según las [notas del producto sobre el almacenamiento de TDI de SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No obstante, la elección que se muestra en la tabla se realizó para proporcionar un rendimiento del disco suficiente para SAP HANA. Si necesita menos rendimiento de E/S, puede ajustar la elección de discos Premium Storage para /hana/data y /hana/log. Los mismo es válido para definir el tamaño del volumen /hana/backup, que se estableció para mantener copias de seguridad que representaran el doble del volumen de memoria. Si necesita menos espacio, se puede ajustar. Tenga también en cuenta el rendimiento global de E/S de la máquina virtual al elegir una máquina virtual o determinar su tamaño. El rendimiento general de las máquinas virtuales está documentado en el artículo [Tamaños de máquina virtual optimizada para memoria](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).  

> [!NOTE]
> Si quiere aprovechar el [Contrato de nivel de servicio para Virtual Machines de Microsoft Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) debe cambiar todos los discos duros virtuales que aparecen como Standard Storage (Sxx) a Premium Storage (Pxx). 


### <a name="set-up-azure-virtual-networks"></a>Configurar Azure Virtual Network
Si tiene conectividad de sitio a sitio a Azure por medio de VPN o de ExpressRoute, debe tener como mínimo una red de [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) conectada a través de una puerta de enlace virtual al circuito VPN o ExpressRoute. La puerta de enlace virtual reside en una subred en Azure Virtual Network. Para instalar SAP HANA, debe crear dos subredes adicionales en la red virtual. Una subred hospeda las máquinas virtuales para ejecutar las instancias de SAP HANA. La otra subred ejecuta Jumpbox o máquinas virtuales de administración para hospedar SAP HANA Studio u otro software de administración.

Al instalar las máquinas virtuales para ejecutar SAP HANA, estas necesitan lo siguiente:

- Dos NIC virtuales instaladas: una para conectarse a la subred de administración y la otra para conectarse ya sea desde la red local o desde otras redes a la instancia de SAP HANA de la máquina virtual de Azure.
- Direcciones IP privadas estáticas implementadas para ambas NIC virtuales.

Para obtener información general sobre los distintos métodos para asignar direcciones IP, consulte [Tipos de direcciones IP y métodos de asignación en Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Los [grupos de seguridad de red (NSG) de Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se usan para dirigir el tráfico que se enruta a la instancia de SAP HANA o al Jumpbox. Los NSG se asocian a la subred de SAP HANA y a la subred de administración.

La siguiente imagen muestra un resumen de un esquema de implementación aproximado para SAP HANA:

![Esquema de implementación aproximado de SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Para implementar SAP HANA en Azure sin una conexión de sitio a sitio, acceda a la instancia de SAP HANA mediante una dirección IP pública. La dirección IP debe asignarse a la máquina virtual de Azure que ejecuta su máquina virtual de Jumpbox. En este escenario básico, la implementación depende de los servicios DNS integrados en Azure para resolver los nombres de host. En una implementación más compleja en la que se usan direcciones IP de acceso público, los servicios DNS integrados en Azure son especialmente importantes. Use los NSG de Azure para limitar los puertos abiertos o los intervalos de direcciones IP que puedan conectarse a subredes de Azure con recursos que tengan direcciones IP de acceso público. La imagen siguiente muestra un esquema aproximado de implementación de SAP HANA sin una conexión de sitio a sitio:
  
![Esquema de implementación aproximado de SAP HANA sin una conexión de sitio a sitio](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operaciones de implementación de SAP HANA en máquinas virtuales de Azure
En las siguientes secciones se describen algunas de las operaciones relacionadas con la implementación de sistemas SAP HANA en máquinas virtuales de Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operaciones de copia de seguridad y restauración en máquinas virtuales Azure
En los documentos siguientes se describe cómo realizar la copia de seguridad y la restauración de una implementación de SAP HANA:

- [SAP HANA: información general sobre copias de seguridad](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Azure Backup de SAP HANA en el nivel de archivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Iniciar y reiniciar máquinas virtuales que contienen SAP HANA
Una característica fundamental de la nube pública de Azure es que solo se le cobra por los minutos de computación. Por ejemplo, cuando se apaga una máquina virtual que ejecuta SAP HANA, se le facturan solo los costos de almacenamiento durante ese tiempo. Otra característica está disponible cuando se especifican las direcciones IP estáticas de las máquinas virtuales de la implementación inicial. Cuando se reinicia una máquina virtual con SAP HANA, lo hace con sus direcciones IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Usar SAPRouter para la compatibilidad remota de SAP
Si tiene una conexión de sitio a sitio entre sus ubicaciones locales y Azure, y ejecuta componentes de SAP, es probable que ya está ejecutando SAProuter. En este caso, realice las siguientes acciones para obtener compatibilidad remota:

- Mantener la dirección IP estática y privada de la máquina virtual que hospeda SAP HANA en la configuración de SAProuter.
- Configurar el NSG de la subred que hospeda la máquina virtual de HANA para permitir el tráfico a través del puerto TCP/IP 3299.

Si se está conectando a Azure a través de Internet y no tiene un enrutador SAP para la máquina virtual con SAP HANA, debe instalar el componente. Instale SAProuter en una máquina virtual independiente en la subred de administración. La imagen siguiente muestra un esquema aproximado de implementación de SAP HANA sin una conexión de sitio a sitio y con SAProuter:

![Esquema de implementación aproximado para SAP HANA sin conexión de sitio a sitio y SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

Asegúrese de instalar SAPRouter en otra máquina virtual y no en la máquina virtual de JumpBox. La máquina virtual independiente debe tener una dirección IP estática. Para conectar la instancia de SAProuter a la instancia de SAProuter hospedada en SAP, póngase en contacto con SAP para obtener una dirección IP. (La instancia de SAProuter hospedada en SAP es el equivalente de la instancia de SAProuter que se instala en la máquina virtual). Utilice la dirección IP de SAP para configurar la instancia de SAProuter. En la configuración, el único puerto necesario es el puerto TCP 3299.

Para más información sobre cómo configurar y mantener conexiones de compatibilidad remota a través de SAPRouter, consulte la [documentación de SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidad con SAP HANA en máquinas virtuales nativas de Azure
Si ejecuta SUSE Linux 12 SP1 o una versión posterior, puede establecer un clúster Pacemaker con dispositivos STONITH. Puede usar los dispositivos para establecer una configuración de SAP HANA que utilice la replicación sincrónica con conmutación por error automática y replicación del sistema de HANA. Para más información, consulte [Alta disponibilidad de SAP HANA en Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).
