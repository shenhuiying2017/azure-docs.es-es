---
title: Directrices de soluciones de almacenamiento | Microsoft Docs
description: Obtenga información sobre las directrices clave de diseño e implementación para implementar soluciones de almacenamiento en los servicios de infraestructura de Azure.
documentationcenter: ''
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou

---
# Directrices de infraestructura de almacenamiento
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artículo se centra en entender las necesidades de almacenamiento y las consideraciones de diseño para conseguir un rendimiento óptimo de la máquina virtual.

## Directrices de implementación para el almacenamiento
Decisiones:

* ¿Necesita usar el almacenamiento Estándar o Premium para la carga de trabajo?
* ¿Necesita crear bandas en los discos para crear discos de más de 1023 GB?
* ¿Necesita crear bandas en los discos para lograr un rendimiento óptimo de E/S para la carga de trabajo?
* ¿Qué conjunto de cuentas de almacenamiento necesita para hospedar su infraestructura o carga de trabajo de TI?

Tareas:

* Revise las solicitudes de E/S de las aplicaciones que va a implementar y planee el número y el tipo adecuados de cuentas de almacenamiento.
* Cree el conjunto de cuentas de almacenamiento usando su convención de nomenclatura. Puede usar la CLI o el Portal de Azure.

## Almacenamiento
Almacenamiento de Azure es una parte clave del proceso de implementación y administración de aplicaciones y máquinas virtuales. Almacenamiento de Azure proporciona servicios para almacenar datos de archivos, datos sin estructura y mensajes, y también forma parte de la infraestructura que da soporte a las máquinas virtuales.

Existen dos tipos de cuentas de almacenamiento disponibles para la compatibilidad con máquinas virtuales.

* Las cuentas de Standard Storage proporcionan acceso a Blob Storage (que se usa para almacenar discos de VM de Azure), Table Storage, Queue Storage y File Storage.
* Las cuentas de [Premium Storage](../storage/storage-premium-storage.md) ofrecen compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo de E/S intensivas, como el clúster MongoDB particionado. En estos momentos, Premium Storage solo admite discos de VM de Azure.

Azure crea máquinas virtuales con un disco del sistema operativo, un disco temporal y ninguno o varios discos de datos opcionales. Tanto el disco del sistema operativo como los discos de datos son blobs en páginas de Azure, mientras que el disco temporal se almacena localmente en el nodo en que reside el equipo. Tenga cuidado al diseñar aplicaciones para que solo utilice este disco temporal para datos no persistentes, ya que la máquina virtual se puede migrar entre hosts durante un evento de mantenimiento. Se podrían perder los datos almacenados en el disco temporal.

El entorno de Azure Storage subyacente proporciona alta disponibilidad y durabilidad para garantizar que los datos permanezcan protegidos frente a errores de hardware o mantenimientos no planeados. Al diseñar el entorno de Azure Storage, puede decidir replicar el almacenamiento de VM de los siguientes modos:

* Localmente dentro de un determinado centro de datos de Azure
* En centros de datos de Azure dentro de una región determinada
* En centros de datos de Azure de diferentes regiones

Puede leer [más información acerca de las opciones de replicación para una alta disponibilidad](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Los discos de datos y del sistema operativo tienen un tamaño máximo de 1023 gigabytes (GB). El tamaño máximo de un blob es de 1024 GB y debe contener los metadatos (pie de página) del archivo VHD (un GB son 1024<sup>3</sup> bytes). Puede usar el Administrador de volúmenes lógicos (LVM) para superar este límite mediante la agrupación de discos de datos para presentar volúmenes lógicos de más de 1023 GB a la máquina virtual.

Hay algunos límites de escalabilidad al diseñar las implementaciones de Azure Storage. Consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md#storage-limits) para obtener más información. Vea también [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](../storage/storage-scalability-targets.md).

En cuanto al almacenamiento de aplicaciones, puede guardar datos de objetos no estructurados como documentos, imágenes, copias de seguridad, datos de configuración, registros, etc., mediante Blob Storage. En lugar de que aplicación escriba en un disco virtual asociado a la máquina virtual, la aplicación puede escribir directamente en el Almacenamiento de blobs de Azure. Blob Storage también proporciona la opción de [capas de almacenamiento de acceso frecuente y acceso esporádico](../storage/storage-blob-storage-tiers.md) según las necesidades de disponibilidad y las restricciones de costo.

## Discos con bandas
Además de ofrecer la posibilidad de crear discos de más de 1023 GB, en muchos casos, la creación de bandas en los discos de datos mejorará el rendimiento, ya que permite que varios blobs respalden el almacenamiento de un solo volumen. Con la creación de bandas, las operaciones de E/S necesarias para escribir y leer datos de un único disco lógico se realizan en paralelo.

Azure impone límites en la cantidad de discos de datos y la cantidad de ancho de banda disponible en función del tamaño de la VM. Para obtener más información, vea [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).

Si usa la creación de bandas en discos de datos de Azure, tenga en cuenta las siguientes directrices:

* Los discos de datos siempre deben tener el tamaño máximo (1023 GB).
* Conecte los discos de datos máximos que se permiten para el tamaño de la VM.
* Use LVM.
* Evite el uso de opciones de almacenamiento en caché del disco de datos de Azure (directiva de almacenamiento en caché = ninguna).

Para más información, consulte [Configuración del LVM en una VM Linux en Azure](virtual-machines-linux-configure-lvm.md).

## Cuentas de almacenamiento múltiples
Al diseñar el entorno de Azure Storage, puede utilizar varias cuentas de almacenamiento a medida que el número de VM implementadas aumente. Este enfoque ayuda a distribuir la E/S entre la infraestructura subyacente de Azure Storage con el fin de mantener un rendimiento óptimo para las VM y aplicaciones. Al diseñar las aplicaciones que se van a implementar, tenga en cuenta los requisitos que E/S de cada VM tendrá y compense esas VM entre las cuentas de Azure Storage. Trate de evitar agrupar todas las VM que exigen E/S elevadas en una o dos cuentas de almacenamiento.

Para obtener más información sobre las funcionalidades de E/S de las distintas opciones de Azure Storage y algunos máximos recomendados, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](../storage/storage-scalability-targets.md).

## Pasos siguientes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->