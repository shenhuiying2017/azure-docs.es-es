---
title: Operaciones de SAP HANA en Azure | Microsoft Docs
description: Guía de operaciones para los sistemas SAP HANA que se implementan en Azure Virtual Machines.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 959a483d293caa45180c946e92ac824fc56db084
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195046"
---
# <a name="sap-hana-on-azure-operations-guide"></a>Guía de operaciones de SAP HANA en Azure
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
> Para escenarios no de producción, use los tipos de máquinas virtuales que se enumeran en la [nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533). Para el uso de máquinas virtuales de Azure en escenarios de producción, compruebe las máquinas virtuales certificadas para SAP HANA en el documento publicado por SAP con la [lista de plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Implemente las máquinas virtuales en Azure mediante:

- Azure Portal.
- Los cmdlets de Azure PowerShell.
- La CLI de Azure.

También puede implementar una plataforma SAP HANA completamente instalada en los servicios de máquina virtual de Azure a través de la [plataforma en la nube de SAP](https://cal.sap.com/). El proceso de instalación se describe en [Implementación de SAP S/4HANA o BW/4HANA en Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h), o con la automatización publicada [aquí](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Elegir un tipo de Azure Storage
Azure proporciona dos tipos de almacenamiento adecuados para máquinas virtuales de Azure que ejecutan SAP HANA:

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure ofrece dos métodos de implementación de discos duros virtuales en Azure Standard Storage y Azure Premium Storage. Si el escenario global lo permite, aproveche las implementaciones de [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Para obtener una lista de tipos de almacenamiento y sus Acuerdos de Nivel de Servicio sobre IOPS y rendimiento del almacenamiento, revise la [documentación de Azure para Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configuración del almacenamiento para máquinas virtuales de Azure

Mientras ha adquirido dispositivos de SAP HANA para el entorno local, nunca ha tenido que tener en cuenta los subsistemas de E/S y sus capacidades, porque el proveedor del dispositivo tiene que asegurarse de que se cumplen los requisitos de almacenamiento mínimo para SAP HANA. A medida que va creando la infraestructura de Azure, debe conocer algunos de esos requisitos para poder comprender también los requisitos de configuración que se recomiendan en las secciones siguientes. O para los casos en los que va a configurar las máquinas virtuales en las que desee ejecutar SAP HANA. Algunas de las características que se piden comportan la necesidad de:

- Habilitar el volumen de lectura/escritura en /hana/log de un 250 MB/s como mínimo con tamaños de E/S de 1 MB
- Habilitar la actividad de lectura de al menos 400MB/s para /hana/data con tamaños de E/S de 16 MB y 64 MB
- Habilitar la actividad de escritura de al menos 250MB/s para /hana/data con tamaños de E/S de 16 MB y 64 MB

Debido a que la baja latencia de almacenamiento es fundamental para los sistemas DBMS, incluso estos, como SAP HANA, mantienen los datos en memoria. La ruta crítica en el almacenamiento normalmente está relacionada con las escrituras del registro de transacciones de los sistemas DBMS. Sin embargo, también las operaciones como la escritura de puntos de retorno o la carga de datos en memoria después de la recuperación tras un bloqueo pueden ser críticas. Por ello es obligatorio aprovechar Azure Premium Disks para los volúmenes /hana/data y /hana/log. Para lograr el rendimiento mínimo de /hana/log y /hana/data como desea SAP, tiene que compilar un RAID 0 utilizando MDADM o LVM en varios discos de Azure Premium Storage, y usar los volúmenes RAID como volúmenes de /hana/data y /hana/log. Como tamaños de franja para RAID 0 la recomendación es utilizar:

- 64K o 128K para /hana/data
- 32K para /hana/log

> [!NOTE]
> No es necesario configurar ningún nivel de redundancia con volúmenes RAID, ya que Azure Premium Storage y Azure Standard Storage mantienen tres imágenes de un disco duro virtual. El uso de un volumen RAID tiene como finalidad configurar volúmenes que proporcionen un rendimiento de E/S suficiente.

La acumulación de un número de discos duros virtuales de Azure bajo un RAID es acumulativo por parte de un IOPS y del rendimiento de almacenamiento. Por lo tanto, si coloca una RAID 0 en P30 x 3 discos de Azure Premium Storage, debe proporcionarle tres veces el IOPS y tres veces el rendimiento de almacenamiento de un solo disco P30 de Azure Premium Storage.

No configure Premium Storage almacenando en caché los discos usados para /hana/data y /hana/log. Todos los discos utilizados para crear estos volúmenes deben tener el almacenamiento en caché de esos discos establecido en "None".

Tenga también en cuenta el rendimiento global de E/S de la máquina virtual al elegir una máquina virtual o determinar su tamaño. El rendimiento general del almacenamiento de las máquinas virtuales está documentado en el artículo [Tamaños de máquina virtual optimizada para memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Configuración de Azure Storage con control de costo
En la tabla siguiente se muestra una configuración de tipos de VM que los clientes suelen usar para hospedar SAP HANA en máquinas virtuales de Azure. Puede haber algunos tipos de máquina virtual que no cumplen todos los criterios mínimos para SAP HANA. Pero hasta ahora parece que esas máquinas virtuales presentan un rendimiento adecuado en escenarios que no son de producción. 

> [!NOTE]
> Para escenarios de producción, compruebe si un determinado tipo de máquina virtual es compatible con SAP HANA de SAP en la [documentación de SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | /hana/data y /hana/log<br /> seccionado con LVM o MDAADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Discos recomendados para los tipos de máquinas virtuales más pequeños con un exceso de tamaño de los volúmenes de 3 x P20 con respecto a las recomendaciones de espacio según las [notas del producto sobre el almacenamiento de TDI de SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No obstante, la elección que se muestra en la tabla se realizó para proporcionar un rendimiento del disco suficiente para SAP HANA. Si necesitar realizar cambios en el volumen /hana/backup, cuyo tamaño se estableció para mantener copias de seguridad que representan el doble del volumen de memoria, puede realizar los ajustes que requiera.   
Compruebe si el rendimiento del almacenamiento para los diferentes volúmenes sugeridos se adaptará a la carga de trabajo que se va a ejecutar. Si la carga de trabajo requiere volúmenes mayores para /hana/data y /hana/log, tiene que aumentar el número de discos duros virtuales de Azure Premium Storage. Ajustar el tamaño de un volumen con más discos duros virtuales de los que se enumeran, aumentará el IOPS y el rendimiento de E/S dentro de los límites del tipo de máquina virtual de Azure. 

> [!NOTE]
> Las configuraciones anteriores NO se beneficiarían del [Acuerdo de nivel de servicio de máquinas virtuales de Azure para una máquina virtual única](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) debido a que utilizan una combinación de Azure Premium Storage y Azure Standard Storage. Sin embargo, la selección se ha elegido para optimizar los costos.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Configuración de Azure Storage para beneficiarse del cumplimiento del Acuerdo de nivel de servicios de máquina virtual única
Si desea beneficiarse del [Acuerdo de nivel de servicio de máquinas virtuales de Azure para una máquina virtual única](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), tiene que usar discos duros virtuales Azure Premium Storage exclusivamente.

> [!NOTE]
> Para escenarios de producción, compruebe si un determinado tipo de máquina virtual es compatible con SAP HANA de SAP en la [documentación de SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | /hana/data y /hana/log<br /> seccionado con LVM o MDAADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Discos recomendados para los tipos de máquinas virtuales más pequeños con un exceso de tamaño de los volúmenes de 3 x P20 con respecto a las recomendaciones de espacio según las [notas del producto sobre el almacenamiento de TDI de SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No obstante, la elección que se muestra en la tabla se realizó para proporcionar un rendimiento del disco suficiente para SAP HANA. Si necesitar realizar cambios en el volumen /hana/backup, cuyo tamaño se estableció para mantener copias de seguridad que representan el doble del volumen de memoria, puede realizar los ajustes que requiera.  
Compruebe si el rendimiento del almacenamiento para los diferentes volúmenes sugeridos se adaptará a la carga de trabajo que se va a ejecutar. Si la carga de trabajo requiere volúmenes mayores para /hana/data y /hana/log, tiene que aumentar el número de discos duros virtuales de Azure Premium Storage. Ajustar el tamaño de un volumen con más discos duros virtuales de los que se enumeran, aumentará el IOPS y el rendimiento de E/S dentro de los límites del tipo de máquina virtual de Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solución de almacenamiento con Acelerador de escritura de Azure para máquinas virtuales de serie M de Azure
El Acelerador de escritura de Azure es una funcionalidad que se implementa exclusivamente para las máquinas virtuales de la serie M. Como el nombre indica, el propósito de la funcionalidad es mejorar la latencia de E/S de las escrituras en Azure Premium Storage. Para SAP HANA, el Acelerador de escritura se supone que debe usarse solo en el volumen /hana/log. Por lo tanto, las configuraciones que se han mostrado hasta ahora tienen que cambiarse. El cambio principal es el desglose entre /hana/data y /hana/log para poder usar el Acelerador de escritura de Azure solo en el volumen de /hana/log. 

> [!IMPORTANT]
> La certificación de SAP HANA para las máquinas virtuales de serie M de Azure es exclusivamente con el Acelerador de escritura de Azure para el volumen /hana/log. Como resultado, las implementaciones de SAP HANA en un escenario de producción en máquinas virtuales de la serie M de Azure se espera que estén configuradas con el Acelerador de escritura de Azure para el volumen /hana/log.  

> [!NOTE]
> Para escenarios de producción, compruebe si un determinado tipo de máquina virtual es compatible con SAP HANA de SAP en la [documentación de SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Las configuraciones recomendadas tiene el siguiente aspecto:

| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Compruebe si el rendimiento del almacenamiento para los diferentes volúmenes sugeridos se adaptará a la carga de trabajo que se va a ejecutar. Si la carga de trabajo requiere volúmenes mayores para /hana/data y /hana/log, tiene que aumentar el número de discos duros virtuales de Azure Premium Storage. Ajustar el tamaño de un volumen con más discos duros virtuales de los que se enumeran, aumentará el IOPS y el rendimiento de E/S dentro de los límites del tipo de máquina virtual de Azure.

El Acelerador de escritura de Azure solo funciona en conjunto con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Por lo que al menos los discos de Azure Premium Storage que forman el volumen /hana/log tienen que implementarse como discos administrados.

Hay límites en los discos duros virtuales de Azure Premium Storage por máquina virtual que el Acelerador de escritura de Azure puede admitir. Los límites actuales son:

- 16 VHD para una máquina virtual M128xx
- 8 VHD para una máquina virtual M64xx

Puede encontrar instrucciones más detalladas sobre cómo habilitar el Acelerador de escritura de Azure en el artículo [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) (Acelerador de escritura).

En la misma documentación puede encontrar información más detallada y las restricciones para el Acelerador de escritura de Azure.


### <a name="set-up-azure-virtual-networks"></a>Configurar Azure Virtual Network
Si tiene conectividad de sitio a sitio a Azure por medio de VPN o de ExpressRoute, debe tener como mínimo una red de [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) conectada a través de una puerta de enlace virtual al circuito VPN o ExpressRoute. La puerta de enlace virtual reside en una subred en Azure Virtual Network. Para instalar SAP HANA, debe crear dos subredes adicionales en la red virtual. Una subred hospeda las máquinas virtuales para ejecutar las instancias de SAP HANA. La otra subred ejecuta Jumpbox o máquinas virtuales de administración para hospedar SAP HANA Studio u otro software de administración.

Al instalar las máquinas virtuales para ejecutar SAP HANA, estas necesitan lo siguiente:

- Dos NIC virtuales instaladas: una para conectarse a la subred de administración y la otra para conectarse ya sea desde la red local o desde otras redes a la instancia de SAP HANA de la máquina virtual de Azure.
- Direcciones IP privadas estáticas implementadas para ambas NIC virtuales.

Para obtener información general sobre los distintos métodos para asignar direcciones IP, consulte [Tipos de direcciones IP y métodos de asignación en Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para máquinas virtuales que ejecutan SAP HANA, debe trabajar con direcciones IP estáticas asignadas. La razón es que algunos atributos de configuración de HANA hacen referencia a direcciones IP.

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

Si se está conectando a Azure a través de Internet y no tiene un enrutador SAP para la máquina virtual con SAP HANA, tiene que instalar el componente. Instale SAProuter en una máquina virtual independiente en la subred de administración. La imagen siguiente muestra un esquema aproximado de implementación de SAP HANA sin una conexión de sitio a sitio y con SAProuter:

![Esquema de implementación aproximado para SAP HANA sin conexión de sitio a sitio y SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

Asegúrese de instalar SAPRouter en otra máquina virtual y no en la máquina virtual de JumpBox. La máquina virtual independiente debe tener una dirección IP estática. Para conectar la instancia de SAProuter a la instancia de SAProuter hospedada en SAP, póngase en contacto con SAP para obtener una dirección IP. (La instancia de SAProuter hospedada en SAP es el equivalente de la instancia de SAProuter que se instala en la máquina virtual). Utilice la dirección IP de SAP para configurar la instancia de SAProuter. En la configuración, el único puerto necesario es el puerto TCP 3299.

Para más información sobre cómo configurar y mantener conexiones de compatibilidad remota a través de SAPRouter, consulte la [documentación de SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidad con SAP HANA en máquinas virtuales nativas de Azure
Si ejecuta SUSE Linux 12 SP1 o una versión posterior, puede establecer un clúster Pacemaker con dispositivos STONITH. Puede usar los dispositivos para establecer una configuración de SAP HANA que utilice la replicación sincrónica con conmutación por error automática y replicación del sistema de HANA. Para más información acerca del procedimiento de configuración, consulte [Alta disponibilidad de SAP HANA para máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
