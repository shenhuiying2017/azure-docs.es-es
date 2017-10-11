---
title: Azure Backup de SAP HANA en el nivel de archivo | Microsoft Docs
description: "Existen dos posibilidades de copia de seguridad principales para SAP HANA en Azure Virtual Machines. Este artículo trata sobre Azure Backup de SAP HANA en el nivel de archivo"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Azure Backup de SAP HANA en el nivel de archivo

## <a name="introduction"></a>Introducción

Esto forma parte de una serie de tres partes de artículos relacionados sobre copia de seguridad de SAP HANA. [Guía de copia de seguridad de SAP HANA en Azure Virtual Machines](./sap-hana-backup-guide.md) proporciona una introducción e información sobre los pasos iniciales, y [Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento](./sap-hana-backup-storage-snapshots.md) trata la opción de copia de seguridad basada en instantáneas de almacenamiento.

Puede examinar los tamaños de máquina virtual de Azure para comprobar que GS5 permite 64 discos de datos asociados. Para los sistemas de SAP HANA de gran tamaño, puede haberse tomado ya un número significativo de discos para archivos de registro y datos, posiblemente junto con el RAID del software para ofrecer un rendimiento óptimo de E/S del disco. La pregunta es: ¿Cuándo almacenar archivos de copia de seguridad de SAP HANA que puedan llenar los discos de datos asociados con el paso del tiempo? Vea [Tamaños de las máquinas virtuales Linux en Azure](../../linux/sizes.md) para las tablas de tamaño de máquina virtual de Azure.

En este momento no hay ninguna integración de copia de seguridad de SAP HANA disponible con el servicio Azure Backup. Es el método estándar de administración de la copia de seguridad/restauración en el nivel de archivo es con una copia de seguridad basada en archivos a través de SAP HANA Studio o a través de instrucciones SQL de SAP HANA. Vea [SAP HANA SQL y referencia de vistas del sistema](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) para obtener más información.

![Esta ilustración muestra el cuadro de diálogo del elemento de menú de copia de seguridad en SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Esta ilustración muestra el cuadro de diálogo del elemento de menú de copia de seguridad en SAP HANA Studio. Al elegir el tipo &quot;archivo&quot;, tiene que especificar una ruta de acceso den el sistema de archivos donde SAP HANA escribe los archivos de copia de seguridad. La restauración funciona del mismo modo.

Aunque esta opción suena simple y sencilla, existen algunas consideraciones. Como se mencionó antes, una máquina virtual de Azure tiene una limitación del número de discos de datos que se pueden asociar. No habrá capacidad para almacenar archivos de copia de seguridad de SAP HANA en los sistemas de archivos de la VM, según el tamaño de los requisitos de rendimiento del disco y la base de datos, que podría provocar que el RAID del software use la fragmentación en varios discos de datos. Se proporcionan más opciones para mover estos archivos de copia de seguridad y administrar las restricciones de tamaño de archivo y el rendimiento cuando se administran terabytes de datos más adelante en este artículo.

Otra opción, que ofrece más libertad con respecto a la capacidad total, es Azure Blob Storage. Aunque un único blob también se restringe a 1 TB, la capacidad total de un único contenedor de blobs es actualmente de 500 TB. Además, ofrece a los clientes la opción de seleccionar el denominado almacenamiento de blobs en &quot;frío&quot;, que tiene un costo-beneficio. Vea [Azure Blob Storage: capas de almacenamiento de acceso frecuente y esporádico](../../../storage/blobs/storage-blob-storage-tiers.md) para obtener más información sobre el almacenamiento de blobs en frío.

Para obtener una seguridad adicional, utilice una cuenta de almacenamiento con replicación geográfica para almacenar las copias de seguridad de SAP HANA. Vea [Replicación de Azure Storage](../../../storage/common/storage-redundancy.md) para obtener más información sobre la replicación de la cuenta de almacenamiento.

Puede colocar VHD dedicados para copias de seguridad de SAP HANA en una cuenta de almacenamiento de copia de seguridad dedicada con replicación geográfica. También puede copiar los VHD que conserven las copias de seguridad de SAP HANA en una cuenta de almacenamiento de replicación geográfica o en una cuenta de almacenamiento que se encuentre en una región diferente.

## <a name="azure-backup-agent"></a>Azure Backup Agent

Azure Backup Agent ofrece la opción de no solo realizar copias de seguridad de las VM completadas, sino también de archivos y directorios mediante el agente de copia de seguridad, que debe estar instalado en el sistema operativo invitado. Sin embargo, a partir de diciembre de 2016, este agente solo se admite en Windows (vea [Copia de seguridad desde Windows Server o un cliente de Windows en Azure mediante el modelo de implementación de Resource Manager](../../../backup/backup-configure-vault.md)).

Una solución alternativa es copiar primero archivos de copia de seguridad de SAP HANA en una VM Windows en Azure (por ejemplo, a través de recursos compartidos SAMBA) y, a continuación, usar Azure Backup Agent desde allí. Aunque es técnicamente posible, podría agregar complejidad y ralentizar la copia de seguridad o restaurar el proceso un poco debido a la copia entre la VM Windows y Linux. No se recomienda seguir este enfoque.

## <a name="azure-blobxfer-utility-details"></a>Detalles de la utilidad bloxfer de Azure

Para almacenar directorios y archivos en Azure Storage, puede usar CLI o PowerShell, o desarrollar una herramienta mediante uno de los [Azure SDK](https://azure.microsoft.com/downloads/). También hay una utilidad lista para usar, AzCopy, para copiar datos a Azure Storage, pero es solo para Windows (vea [Transferencia de datos con la utilidad en línea de comandos AzCopy](../../../storage/common/storage-use-azcopy.md)).

Por lo tanto, se usó blobxfer para copiar los archivos de copia de seguridad de SAP HANA. Se trata de código abierto, que usan muchos clientes en entornos de producción, y que está disponible en [GitHub](https://github.com/Azure/blobxfer). Esta herramienta permite copiar datos directamente a Azure Blob Storage o al recurso compartido de archivos de Azure. También ofrece una gama de características útiles, como hash md5 o paralelismo automático al copiar un directorio con varios archivos.

## <a name="sap-hana-backup-performance"></a>Rendimiento de copia de seguridad de SAP HANA

![Esta captura de pantalla es de la consola de copia de seguridad de SAP HANA en SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Esta captura de pantalla es de la consola de copia de seguridad de SAP HANA en SAP HANA Studio. Se tardó 42 minutos en realizar la copia de seguridad de 230 GB en un único disco de almacenamiento estándar de Azure asociado a la VM de HANA con el sistema de archivos XFS.

![Esta captura de pantalla es de YaST en la VM de prueba de SAP HANA](media/sap-hana-backup-file-level/image024.png)

Esta captura de pantalla es de YaST en la VM de prueba de SAP HANA. Puede ver el disco único de 1 TB para la copia de seguridad de SAP HANA como se mencionó antes. Se tardó 42 minutos en realizar la copia de seguridad de 230 GB. Además, se asocian cinco discos de 200 GB y software RAID md0 creado, con la fragmentación en la parte superior de estaos cinco discos de datos de Azure.

![La repetición de la misma copia de seguridad en software RAID con fragmentación en cinco discos de datos de Azure Standard Storage asociados](media/sap-hana-backup-file-level/image025.png)

La repetición de la misma copia de seguridad en software RAID con fragmentación en cinco discos de datos de Azure Standard Storage asociados provocó que el tiempo de copia de seguridad descendiera de 42 a 10 minutos. Los discos se asociaron sin el almacenamiento en caché en la VM. Por lo tanto, resulta evidente la importancia del rendimiento de la escritura en disco para el tiempo de copia de seguridad. Puede entonces cambiar a Azure Premium Storage para acelerar aún más el proceso para obtener un rendimiento óptimo. En general, debe usarse Azure Premium Storage para los sistemas de producción.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copia de archivos de copia de seguridad de SAP HA en Azure Blob Storage

A partir de diciembre de 2016, la mejor opción para almacenar rápidamente archivos de copia de seguridad de SAP HANA es Azure Blob Storage. Un contenedor de blob único tiene un límite de 500 TB, que es suficiente para la mayoría de los sistemas de SAP HANA que se ejecutan en una VM GS5 en Azure, para mantener bastantes copias de seguridad de SAP HANA. Los clientes pueden elegir entre el almacenamiento de blobs en &quot;caliente&quot; y en &quot;frío&quot; (vea [Azure Blob Storage: capas de almacenamiento de acceso frecuente y esporádico](../../../storage/blobs/storage-blob-storage-tiers.md)).

Con la herramienta blobxfer, es fácil copiar los archivos de copia de seguridad de SAP HANA directamente en Azure Blob Storage.

![Aquí puede ver los archivos de una copia de seguridad completa de archivos de SAP HANA](media/sap-hana-backup-file-level/image026.png)

Aquí puede ver los archivos de una copia de seguridad completa de archivos de SAP HANA. Existen cuatro archivos y el mayor tiene aproximadamente 230 GB.

![Se tardó aproximadamente 3000 segundos en copiar 230 GB en un contenedor de blobs de la cuenta de Azure Standard Storage](media/sap-hana-backup-file-level/image027.png)

Sin usar el hash md5 en la prueba inicial, se tardó aproximadamente 3000 segundos en copiar 230 GB en un contenedor de blobs de la cuenta de Azure Standard Storage.

![En esta captura de pantalla, puede ver el aspecto en Azure Portal](media/sap-hana-backup-file-level/image028.png)

En esta captura de pantalla, puede ver el aspecto en Azure Portal. Se creó un contenedor de blobs denominado &quot;sap-hana-backups&quot; e incluye los cuatro blobs, que representan los archivos de copia de seguridad de SAP HANA. Uno de ellos tiene un tamaño de aproximadamente 230 GB.

La consola de copia de seguridad de HANA Studio permite restringir el tamaño máximo de archivo de los archivos de copia de seguridad de HANA. En el entorno de ejemplo, mejoró el rendimiento haciendo posible disponer de varios archivos de copia de seguridad más pequeños, en lugar de un archivo grande de 230 GB.

![El establecimiento del límite del tamaño del archivo de copia de seguridad en la parte de HANA no mejora el tiempo de copia de seguridad](media/sap-hana-backup-file-level/image029.png)

El establecimiento del límite del tamaño del archivo de copia de seguridad en la parte de HANA no mejora el tiempo de copia de seguridad porque los archivos se escriben secuencialmente como se muestra en esta figura. El límite de tamaño de archivo se estableció en 60 GB, por lo que la copia de seguridad creó cuatro archivos de datos de gran tamaño en lugar del archivo único de 230 GB.

![Para probar el paralelismo de la herramienta blobxfer, el tamaño máximo de archivo para las copias de seguridad HANA se estableció en 15 GB](media/sap-hana-backup-file-level/image030.png)

Para probar el paralelismo de la herramienta blobxfer, el tamaño máximo de archivo para las copias de seguridad HANA se estableció en 15 GB, que generó 19 archivos de copia de seguridad. Esta configuración provocó que el tiempo en el que blobxfer copió 230 GB en Azure Blob Storage descendiera de 3000 a 875 segundos.

Este resultado se debe al límite de 60 MB/s para la escritura de un blob de Azure. El paralelismo a través de varios blobs resuelve el cuello de botella, pero tiene un inconveniente: el aumento del rendimiento de la herramienta blobxfer para copiar todos estos archivos de copia de seguridad de HANA en Azure Blob Storage supone una carga en la VM de HANA y la red. Afecta al funcionamiento del sistema de HANA.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Copia de blob de discos de datos de Azure dedicados en el software RAID de copia de seguridad

A diferencia de la copia de seguridad del disco de datos de VM, en este enfoque no se realizan copias de seguridad todos los discos de datos en una VM para guardar toda la instalación de SAP, incluidos los datos de HANA, los archivos de registro de HANA y los archivos de configuración. En su lugar, la idea es disponer del software RAID dedicado con fragmentación en varios VHD de datos de Azure para el almacenamiento de una copia de seguridad de archivos de SAP HANA completa. Copie solo estos discos, que tienen una copia de seguridad de SAP HANA. Pueden guardarse fácilmente en una cuenta de almacenamiento de copia de seguridad de HANA dedicada, o asociarse a una &quot;VM de administración de copia de seguridad&quot; dedicada para un procesamiento posterior.

![Todos los VHD se copiaron con el comando de PowerShell **start-azurestorageblobcopy**](media/sap-hana-backup-file-level/image031.png)

Después de complementar el software RAID local, todos los VHD implicados se copiaron con el comando de PowerShell **start-azurestorageblobcopy** (vea [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Como solo afecta al sistema de archivos dedicado para mantener archivos de copia de seguridad, no hay preocupaciones sobre la coherencia de los archivos de registro o de datos de SAP HANA en el disco. Una ventaja de este comando es que funciona mientras la VM se encuentra en línea. Para estar seguro de que no se escribe ningún proceso en el conjunto de fragmentos de copia de seguridad, asegúrese de desmontarlo antes de la copia de blob y montarlo de nuevo posteriormente. También puede usar una forma apropiada de &quot;inmovilizar&quot; el sistema de archivos. Por ejemplo, a través de xfs\_freeze para el sistema de archivos XFS.

![Esta captura de pantalla muestra la lista de blobs en el contenedor de VHD en Azure Portal](media/sap-hana-backup-file-level/image032.png)

Esta captura de pantalla muestra la lista de blobs en el contenedor de &quot;vhd&quot; en Azure Portal. La captura de pantalla muestra los cinco VHD, que se han asociado a la VM del servidor de SAP HANA para que actúe como software RAID para mantener los archivos de copia de seguridad de SAP HANA. También muestra las cinco copias, que se han realizado a través del comando de copia de blob.

![Para fines de prueba, las copias de los discos del software RAID de copia de seguridad de SAP HANA se asociaron a la VM del servidor de aplicaciones](media/sap-hana-backup-file-level/image033.png)

Para fines de prueba, las copias de los discos del software RAID de copia de seguridad de SAP HANA se asociaron a la VM del servidor de aplicaciones.

![La VM del servidor de aplicaciones se apagó para asociar las copias de disco](media/sap-hana-backup-file-level/image034.png)

La VM del servidor de aplicaciones se apagó para asociar las copias de disco. Después de iniciar la VM, se detectaron correctamente los discos y RAID (montado a través de UUID). Solo faltaba el punto de montaje, que se creó mediante el particionador YaST. Después, las copias del archivo de copia de seguridad pasaron a estar visibles en el nivel de SO.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copia de archivos de copia de seguridad de SAP HA en el recurso compartido NFS

Para reducir el impacto potencial en el sistema SAP HANA desde una perspectiva de espacio o disco o rendimiento, puede considerar almacenar los archivos de copia de seguridad de SAP HANA en un recurso compartido NFS. Técnicamente funciona, pero implica la utilización de una segunda máquina virtual de Azure como host del recurso compartido NFS. No debe ser un tamaño de VM pequeño, debido al ancho de banda de red de VM. Tendría sentido cerrar esta &quot;VM de copia de seguridad&quot; y solo activarla para la ejecución de la copia de seguridad de SAP HANA. La escritura en un recurso NFS supone una carga en la red y afecta al sistema de SAP HANA, pero la simple administración de los archivos de copia de seguridad posterior en la &quot;VM de copia de seguridad&quot; no influiría en absoluto en el sistema de SAP HANA.

![Se montó un recurso compartido NFS desde otra máquina virtual de Azure en la VM de servidor de SAP HANA](media/sap-hana-backup-file-level/image035.png)

Para comprobar el caso de uso de NFS, se montó un recurso compartido NFS desde otra Azure VM en la VM de servidor de SAP HANA. No se ha aplicado ningún ajuste de NFS especial.

![Se tardó 1 hora y 46 minutos en realizar la copia de seguridad directamente](media/sap-hana-backup-file-level/image036.png)

El recurso compartido NFS era un conjunto de fragmentación rápido, como el del servidor de SAP HANA. No obstante, se tardó 1 hora y 46 minutos en realizar la copia de seguridad directamente en el recurso compartido de NFS en lugar de 10 minutos, cuando se escribía en una fragmentación local.

![Alternativa no mucho más rápida que 1 hora y 43 minutos](media/sap-hana-backup-file-level/image037.png)

La alternativa de realizar una copia de seguridad en una fragmentación local y la copia del recurso compartido NFS en el nivel del SO (comando **cp -avr** simple) no fue mucho más rápida. Se tardó 1 hora y 43 minutos.

Por lo tanto, funciona. Sin embargo, el rendimiento no fue bueno para la prueba de copia de seguridad de 230 GB. Podría ser incluso peor para varios terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Copia de archivos de copia de seguridad de SAP HA en el servicio de archivos de Azure

Es posible montar un recurso compartido de archivos de Azure dentro de una VM Linux de Azure. En el artículo [Uso de Azure File Storage con Linux](../../../storage/files/storage-how-to-use-files-linux.md) encontrará información sobre cómo hacerlo. Tenga en cuenta que actualmente hay un límite de cuota de 5 TB de un recurso compartido de archivos de Azure y un límite de tamaño de archivo de 1 TB por cada archivo. Vea [Objetivos de escalabilidad y rendimiento de Azure Storage](../../../storage/common/storage-scalability-targets.md) para obtener más información sobre los límites de almacenamiento.

Sin embargo, las pruebas demuestran que la copia de seguridad de SAP HANA no funciona hoy en día directamente con este tipo de montaje CIFS. También se estableció en la [nota 1820529 de SAP](https://launchpad.support.sap.com/#/notes/1820529) que no se recomienda CIFS.

![Esta ilustración muestra un error en el cuadro de diálogo de copia de seguridad en SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Esta ilustración muestra un error en el cuadro de diálogo de copia de seguridad en SAP HANA Studio al intentar realizar una copia de seguridad directamente en un recurso compartido de archivos de Azure con CIFS montado. Por lo tanto, tiene que realizar una copia de seguridad de SAP HANA estándar en un sistema de archivos de VM primero y, a continuación, copiar los archivos de copia de seguridad desde ahí al servicio de archivos de Azure.

![Esta ilustración muestra que se tardó aproximadamente 929 segundos en copiar 19 archivos de copia de seguridad de SAP HANA](media/sap-hana-backup-file-level/image039.png)

Esta ilustración muestra que se tardó aproximadamente 929 segundos en copiar 19 archivos de copia de seguridad de SAP HANA con un tamaño total de aproximadamente 230 GB en el recurso de archivos de Azure.

![La estructura de directorios de origen en la VM de SAP HANA se copió en el recurso compartido de archivos de Azure](media/sap-hana-backup-file-level/image040.png)

En esta captura de pantalla, puede ver que la estructura de directorios de origen en la VM de SAP HANA se copió en el recurso compartido de archivos de Azure: un directorio (hana\_backup\_fsl\_15gb) y 19 archivos de copia de seguridad individuales.

El almacenamiento de los archivos de copia de seguridad de SAP HANA en archivos de Azure podría ser una opción interesante en el futuro cuando las copias de seguridad de archivos de SAP HANA lo admitan directamente, o cuando sea posible montar archivos de Azure a través de NFS y el límite de cuota máxima sea considerablemente superior a 5 TB.

## <a name="next-steps"></a>Pasos siguientes
* [Guía de copia de seguridad de SAP HANA en Azure Virtual Machines](sap-hana-backup-guide.md) ofrece una introducción e información sobre los pasos iniciales.
* [Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento](sap-hana-backup-storage-snapshots.md) describe la opción de copia de seguridad basada en instantáneas de almacenamiento.
* Para obtener información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure (instancias grandes), vea [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md).
