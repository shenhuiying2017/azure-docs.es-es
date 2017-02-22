---
title: "Premium Storage de alto rendimiento y discos de máquina virtual de Azure | Microsoft Docs"
description: "Examine discos de máquina virtual de Azure administrados y no administrados, y Premium de alto rendimiento. Las máquinas virtuales de Azure de la serie DS, DSv2 y GS admiten el Premium Storage."
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 3a353bc874c1827f8a0fc85352894ad96cff16b5
ms.openlocfilehash: c9e43df37784999036c6cf250f27a808f79ebe2f


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Discos de máquina virtual de Azure administrados y no administrados, y Premium Storage de alto rendimiento
Microsoft Azure Premium Storage ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales (VM) que ejecutan cargas de trabajo con mucha E/S. Los discos de máquinas virtuales que usan Premium Storage almacenan los datos en unidades de estado sólido (SSD). Puede migrar los discos de máquina virtual de su aplicación al Almacenamiento premium de Azure para aprovechar la velocidad y rendimiento de estos discos.

Una máquina virtual de Azure admite la conexión de varios discos de Premium Storage para que las aplicaciones puedan disponer de hasta 64 TB de almacenamiento por máquina virtual. Con Premium Storage, las aplicaciones pueden lograr 80 000 operaciones de entrada/salida por segundo (IOPS) por máquina virtual, así como un rendimiento del disco de hasta 2000 MB por segundo por máquina virtual, con latencias extremadamente bajas para las operaciones de lectura.

Con Premium Storage, le Azure ofrece la capacidad de usar sus exigentes aplicaciones empresariales como Dynamics AX, Dynamics CRM, Exchange Server, granjas de servidores de SharePoint y SAP Business Suite en la nube. Puede ejecutar una variedad de cargas de trabajo de bases de datos de rendimiento intensivo como SQL Server, Oracle, MongoDB, MySQL y Redis, que requieren una baja latencia y un alto rendimiento de forma consistente en Premium Storage.

> [!NOTE]
> Para que el rendimiento de la aplicación sea el mejor posible, se recomienda migrar todos los discos de máquinas virtuales que requieran IOPS a Premium Storage. Si el disco no requiere un número elevado de operaciones de entrada/salida por segundo, puede limitar los costos mediante el mantenimiento del almacenamiento estándar, que almacena los datos de disco de máquina virtual en unidades de disco duro (HDD) en lugar de SSD.
> 

Hay dos maneras de crear discos Premium para máquinas virtuales de Azure:

**Discos no administrados**: este es el método original, en el que se administran las cuentas de almacenamiento que se utilizan para almacenar los archivos VHD que se corresponden con los discos de máquina virtual. Los archivos VHD se almacenan como blobs en páginas en las cuentas de almacenamiento. 

**[Azure Managed Disks](storage-managed-disks-overview.md)**: esta característica administra las cuentas de almacenamiento que se usan automáticamente para los discos de máquina virtual. Especifique el tipo (premium o estándar) y el tamaño del disco que necesita, y Azure lo crea y administra automáticamente. No tiene que preocuparse de colocar los discos en varias cuentas de almacenamiento para estar seguro de que permanece dentro de los límites de escalabilidad de las cuentas de almacenamiento, Azure se encarga de ello.

Aunque están disponibles los dos tipos de discos, se recomienda utilizar Managed Disks para aprovechar su gran cantidad de características.

Para empezar a usar Azure Premium Storage, visite la página de [creación de una cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/). 

Para obtener información acerca de la migración de máquinas virtuales existentes a Premium Storage, consulte [Convert a VM from unmanaged disks to managed disks](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md) (Conversión de una máquina virtual de discos sin administrar a discos administrados) o [How to convert a Linux VM from unmanaged disks to Azure Managed Disks](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md) (Conversión de una máquina virtual Linux de discos no administrados a Azure Managed Disks).

> [!NOTE]
> Actualmente, Premium Storage se admite en la mayoría de las regiones. La lista de regiones disponibles se pueden encontrar en [Productos disponibles por región](https://azure.microsoft.com/regions/#services) examinando las regiones en que se admiten las máquinas virtuales de varias series de tamaños (DS, DSV2, Fs y GS).
> 

## <a name="premium-storage-features"></a>Características de Premium Storage

Examinemos algunas de las características de Premium Storage.

**Discos de Premium Storage**: Azure Premium Storage admite discos de máquina virtual que se pueden conectar a las máquinas virtuales de varias series de tamaños concretos, entre los que se incluyen las series DS, DSv2, GS o Fs. Puede elegir entre tres tamaños de disco [P10 (128 GiB), P20 (512 GiB) y P30 (1024 GiB)], cada uno de ellos con sus propias especificaciones de rendimiento. En función de los requisitos de la aplicación puede conectar uno o varios discos a la máquina virtual. En la sección siguiente, [Objetivos de escalabilidad y rendimiento del Almacenamiento premium ](#premium-storage-scalability-and-performance-targets) , describiremos las especificaciones con más detalle.

**Blob en páginas Premium**: Premium Storage admite blobs en páginas, que se usan para almacenar discos no administrados persistentes para máquinas virtuales. A diferencia del almacenamiento estándar, Premium Storage no admite blobs en bloques, blobs en anexos, archivos, tablas o colas.
Cualquier otro objeto colocado en una cuenta de Premium Storage será un blob en páginas y se ajustará a uno de los tamaños aprovisionados admitidos. Ese es el motivo por el que las cuentas de Premium Storage no están pensadas para el almacenamiento de blobs muy pequeño.

**Cuenta de Premium Storage**: para empezar a usar Premium Storage, cree una cuenta de Premium Storage para discos no administrados. Si prefiere usar [Azure Portal](https://portal.azure.com), puede crear una cuenta de Premium Storage mediante la especificación del nivel de rendimiento "Premium" y de "Almacenamiento con redundancia local (LRS)" como opción de replicación. También puede crear una cuenta de Premium Storage si especifica "Premium_LRS" como tipo con la versión 2014-02-14, o posterior, de la [API de REST de Storage](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference); la versión 2014-10-01, o posterior, (implementaciones de la versión clásica) de la [API de REST de Service Management](http://msdn.microsoft.com/library/azure/ee460799.aspx); la [Referencia de la API de REST del proveedor de recursos de Azure Storage](/rest/api/storagerp) (implementaciones de Resource Manager) y la versión 0.8.10, o posterior, de [Azure PowerShell](../powershell-install-configure.md). Obtenga información acerca de los límites de la cuenta de Almacenamiento premium en la siguiente sección, en [Objetivos de escalabilidad y rendimiento del Almacenamiento premium](#premium-storage-scalability-and-performance-targets.md).

**Almacenamiento con redundancia local Premium**: una cuenta de Premium Storage solo admite el almacenamiento con redundancia local (LRS) como opción de replicación, lo que significa que mantiene tres copias de los datos en una única región. Para consideraciones relativas a la replicación geográfica cuando se usa el Almacenamiento premium, consulte la sección [Instantáneas y Copy Blob](#snapshots-and-copy-blob) de este artículo.

Azure usa la cuenta de almacenamiento como contenedor para los discos no administrados. Cuando crea una máquina virtual de Azure de las series DS, DSv2, GS o Fs con discos no administrados y selecciona una cuenta de Premium Storage, tanto el disco del sistema operativo como el de datos se almacenan en dicha cuenta de almacenamiento.

## <a name="premium-storage-supported-vms"></a>Máquinas virtuales compatibles con Premium Storage
Premium Storage es compatible con las máquinas virtuales de las series DS, DSv2, GS y Fs. Con estas máquinas virtuales puede usar discos de almacenamiento tanto estándar como premium. No puede utilizar discos de almacenamiento premium con series de máquinas virtuales que no sean compatibles con Premium Storage.

Para más información sobre los tamaños y tipos de máquina virtual de Azure disponibles para Windows, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para más información sobre los tamaños y tipos de máquina virtual de Azure disponibles para Linux, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A continuación, se muestran algunas de las características de las máquinas virtuales de las series DS, DSv2, GS y Fs:

**Servicio en la nube**: las máquinas virtuales de la serie DS se pueden agregar a un servicio en la nube que incluya solo máquinas virtuales de la serie DS. No agregue máquinas virtuales de la serie DS a un servicio en la nube existente que incluya otras que no sean de la serie DS. Puede migrar los discos duros virtuales existentes a un nuevo servicio en la nube donde solo se ejecuten máquinas virtuales de la serie DS. Si desea conservar la misma dirección IP virtual (VIP) para el nuevo servicio en la nube que hospeda las máquinas virtuales de la serie DS, use [Direcciones IP reservadas](../virtual-network/virtual-networks-instance-level-public-ip.md). Las máquinas virtuales de la serie GS se pueden agregar a un servicio en la nube que ejecute solo máquinas virtuales de la serie GS.

**Disco de sistema operativo**: las máquinas virtuales que se pueden ejecutar en Premium Storage puede configurarse para usar un disco de sistema operativo (SO) premium o estándar. Para que el funcionamiento sea óptimo, se recomienda usar un disco de sistema operativo basado en Premium Storage.

**Discos de datos**: puede usar discos premium y estándar en la misma máquina virtual que se ejecuta en Premium Storage. Con Premium Storage, puede aprovisionar una máquina virtual y conectar varios discos de datos persistentes a la máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen.

> [!NOTE]
> Si secciona los discos de datos de almacenamiento premium mediante [espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), deberá configurarlos con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. De forma predeterminada, la interfaz de usuario (IU) del Administrador del servidor permite configurar columnas de hasta ocho discos. Si tiene más de ocho discos, se necesita usar PowerShell para crear el volumen, así como especificar manualmente el número de columnas. De lo contrario, la IU del administrador del servidor sigue usando 8 columnas aunque tenga más discos. Por ejemplo, si tiene 32 discos en un conjunto de bandas único, debe especificar 32 columnas. Puede usar el parámetro *NumberOfColumns* del cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) de PowerShell para especificar el número de columnas que usa el disco virtual. Para más información, consulte [Introducción a los espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx) y [Storage Spaces Frequently Asked Questions (Preguntas más frecuentes sobre los espacios de almacenamiento)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
> 

**Caché**: las máquinas virtuales de las series de tamaños que admiten Premium Storage cuentan con una funcionalidad de almacenamiento en caché única con la que puede obtener altos niveles de rendimiento y latencia, lo que supera el rendimiento de disco de Premium Storage subyacente. La directiva de almacenamiento en caché del disco en los discos de Premium Storage se puede configurar como ReadOnly, ReadWrite o None. La directiva de almacenamiento en caché de disco predeterminada es ReadOnly para todos los discos de datos premium y ReadWrite para los discos del sistema operativo. Use la opción de configuración adecuada para lograr un rendimiento óptimo para su aplicación. Por ejemplo, en el caso de los discos de datos de solo lectura o con mucha actividad de lectura, como los archivos de datos de SQL Server, establezca la directiva de almacenamiento en caché de disco en "ReadOnly". En el caso de los discos de datos de solo escritura o con mucha actividad de escritura, como los archivos de registro de SQL Server, establezca la directiva de almacenamiento en caché del disco en "None". Consulte más información sobre cómo optimizar el diseño con el Almacenamiento premium en [Almacenamiento premium de Azure: diseño de alto rendimiento](storage-premium-storage-performance.md).

**Análisis**: para analizar el rendimiento de las máquinas virtuales con discos en Premium Storage, puede habilitar los diagnósticos de máquina virtual en [Azure Portal](https://portal.azure.com). Para más información, puede consultar [Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) (Supervisión de máquinas virtuales de Microsoft Azure con la extensión de Azure Diagnostics). Para ver el rendimiento del disco, use herramientas del sistema operativo, como el [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) en las máquinas virtuales Windows e [IOSTAT](http://linux.die.net/man/1/iostat) en las máquinas virtuales Linux.

**Rendimiento y límites de escala de máquinas virtuales**: cada tamaño de máquina virtual compatible con Premium Storage tiene límites de escala y una especificación de rendimiento para IOPS, ancho de banda y número de discos que se pueden conectar por máquina virtual. Cuando use discos de Premium Storage con máquinas virtuales, asegúrese de que las IOPS y el ancho de banda de la máquina virtual sean suficientes para dirigir el tráfico del disco.
Por ejemplo, una máquina virtual STANDARD_DS1 tiene un ancho de banda dedicado de 32 MB/s disponible para el tráfico de disco de Premium Storage. Un disco de Premium Storage P10 puede proporcionar un ancho de banda de 100 MB/s. Si se conecta un disco de Premium Storage P10 a esta máquina virtual, solo puede ofrecer 32 MB/s, pero no los 100 MB/s que puede proporcionar el disco P10.

Actualmente, la máquina virtual más grande de la serie DS es Standard_DS15_v2 y puede proporcionar un máximo de 960 MB/s en todos los discos. La mayor máquina virtual de la serie GS es STANDARD_GS5 y puede proporcionar hasta 2000 MB/s en todos los discos.
Tenga en cuenta que estos límites son solo para el tráfico de disco y no incluyen los aciertos de caché y el tráfico de red. Hay un ancho de banda independiente disponible para el tráfico de red de la máquina virtual, que no es el mismo que el ancho de banda dedicado para los discos de Premium Storage.

Para obtener la información más actualizada acerca de los niveles máximos de IOPS y rendimiento (ancho de banda) de las máquinas virtuales compatibles con Premium Storage, consulte los artículos sobre los [tamaños de máquinas virtuales Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para información acerca de los discos de Premium Storage, y sus límites de IOPS y rendimiento, consulte la tabla que encontrará en la sección [Objetivos de rendimiento y escalabilidad de Premium Storage](#premium-storage-scalability-and-performance-targets) de este artículo.

## <a name="premium-storage-scalability-and-performance-targets"></a>Objetivos de rendimiento y escalabilidad de Premium Storage
En esta sección, describiremos todos los objetivos de escalabilidad y rendimiento que se deben tener en cuenta al usar el Almacenamiento premium.

Las cuentas de Premium Storage tienen los siguientes objetivos de escalabilidad:

| Capacidad total de la cuenta | Ancho de banda total de una cuenta de almacenamiento con redundancia local |
| --- | --- | 
| Capacidad de disco: 35 TB <br>Capacidad de instantánea: 10 TB | Hasta 50 gigabits por segundo de entrada y salida |

* Entrada se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento.
* Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](storage-scalability-targets.md).

Si usas cuentas de Premium Storage para los discos no administrados y la aplicación supera los objetivos de escalabilidad de una cuenta de almacenamiento individual, considere la posibilidad de migrar a Managed Disks. Si no desea migrar a Managed Disks, compile la aplicación para que use varias cuentas de almacenamiento y crear particiones de los datos en dichas cuentas. Por ejemplo, si desea conectar discos de 51 terabytes (TB) a varias máquinas virtuales, distribúyalos entre dos cuentas de almacenamiento, ya que 35 TB es el límite de una sola cuenta de Premium Storage. Asegúrese de que una sola cuenta de Premium Storage nunca tenga más de 35 TB de discos aprovisionados.

### <a name="premium-storage-disk-limits"></a>Límites de discos de Premium Storage
Cuando se aprovisiona un disco de Premium Storage, el tamaño del disco determina los valores máximos de IOPS y rendimiento (ancho de banda). Hay tres tipos de discos de Premium Storage: P10, P20 y P30. Cada uno tiene límites de IOPS y de rendimiento específicos, tal como se especifica en la tabla siguiente:

|Tipo de disco de Almacenamiento premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamaño del disco | 128 GB | 512 GB | 1024 GB (1 TB) |
| IOPS por disco | 500 | 2300 | 5000 |
Rendimiento de disco | 100 MB/s | 150 MB/s | 200 MB/s |

> [!NOTE]
> Asegúrese de que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco, tal como se explica en la sección [Máquinas virtuales compatibles con Premium Storage](#premium-storage-supported-vms) de este artículo. De lo contrario, el rendimiento y las E/S por segundo del disco estarán obligados a reducir los valores basándose en los límites de la máquina virtual en vez de en los límites de disco que se mencionan en la tabla anterior.  
> 
> 

Estos son algunos aspectos importantes que debe conocer sobre los objetivos de escalabilidad y rendimiento del Almacenamiento premium:

* **Rendimiento y capacidad aprovisionados**: cuando se aprovisiona un disco de Premium Storage, a diferencia del almacenamiento estándar, se garantizan la capacidad, las IOPS y el rendimiento del mismo. Por ejemplo, si crea un disco P30, Azure aprovisiona una capacidad de almacenamiento de 1024 GB, 5000 IOPS y un rendimiento de 200 MB/s para él. La aplicación puede usar toda la capacidad y el rendimiento o parte de ellos.

* **Tamaño del disco**: Azure asigna el tamaño del disco (redondeado al alza) a la opción de disco de Premium Storage más cercana según se especifica en la tabla. Por ejemplo, un disco de 100 GiB se clasifica como una opción P10, puede realizar hasta 500 IOPS y dispone de un rendimiento de hasta 100 MB/s. De igual forma, un disco de 400 GiB se clasifica como P20, puede realizar hasta 2300 IOPS y tiene un rendimiento de hasta 150 MB/s.
  
> [!NOTE]
> Puede aumentar fácilmente el tamaño de los discos existentes. Por ejemplo, puede aumentar el tamaño de un disco de 30 GB a 128 GB o, incluso, a 1 TB. O bien, puede convertir un disco P20 en un disco P30 porque necesita más capacidad o más IOPS y rendimiento. 
> 
 
* **Tamaño de E/S**: el tamaño de la unidad de entrada y salida (E/S) es de 256 KB. Si el tamaño de los datos transferidos es inferior a 256 KB, se considera una sola unidad de E/S. Los tamaños de E/S más grandes se cuentan como varias operaciones de E/S con un tamaño de 256 KB. Por ejemplo, una E/S de 1100 KB se cuenta como cinco unidades de E/S.

* **Rendimiento**: el límite de rendimiento incluye escrituras en el disco, así como lecturas desde ese disco que no se sirven desde la memoria caché. Por ejemplo, un disco P10 tiene un rendimiento de 100 MB/s por disco. Algunos ejemplos de rendimiento válido para el disco P10 son:

| Rendimiento máximo por disco P10 | Lecturas del disco no en memoria caché | Escrituras en el disco no memoria caché |
| --- | --- | --- |
| 100 MB por segundo | 100 MB por segundo | 0 |
| 100 MB por segundo | 0 | 100 MB por segundo |
| 100 MB por segundo | 60 MB por segundo | 40 MB por segundo |

* **Aciertos de caché**: los aciertos de caché no están limitados por las IOPS ni el rendimiento asignados del disco. Por ejemplo, cuando se utiliza un disco de datos con la configuración de caché ReadOnly en una máquina virtual compatible con Premium Storage, las lecturas que se sirven desde la memoria caché no están sujetas a los límites de IOPS y rendimiento del disco. Por consiguiente, si la carga de trabajo de un disco es fundamentalmente de lectura, se puede obtener un rendimiento muy alto. Tenga en cuenta que la caché está sujeta a límites de IOPS y rendimiento independientes en el nivel de la máquina virtual que se basan en el tamaño de esta. Las máquinas virtuales de la serie DS tienen aproximadamente 4000 E/S por segundo y 33 MB/seg. por núcleo de caché y E/S de SSD locales. Las máquinas virtuales de la serie GS tienen un límite de 5000 IOPS y 50 MB/s por núcleo para las E/S de SSD local y memoria caché. 

## <a name="throttling"></a>Limitaciones
Pueden aparecer limitaciones si la IOPS o el rendimiento de la aplicación superan los límites asignados a un disco de Premium Storage o si el tráfico total de disco en todos los discos de la máquina virtual supera el límite de ancho de banda de discos disponible para la máquina virtual. Para evitarlo, se recomienda limitar el número de solicitudes de E/S pendientes para el disco en función de los objetivos de escalabilidad y rendimiento del disco y del ancho de banda de disco disponible para la máquina virtual.  

La aplicación puede lograr la menor latencia cuando se ha diseñado para evitar la limitación. Por otro lado, si el número de solicitudes de E/S pendientes del disco es demasiado pequeño, la aplicación no puede aprovechar al máximo los niveles de rendimiento y IOPS que están disponibles en el disco.

En los ejemplos siguientes se muestra cómo calcular los niveles de limitación. Todos los cálculos se basan en un tamaño de unidad de E/S de 256 KB.

### <a name="example-1"></a>Ejemplo 1:
La aplicación hizo 495 unidades de E/S de 16 KB de tamaño en un segundo en un disco P10. Se contarán como 495 unidades de E/S por segundo (IOPS). Si intenta una E/S de 2 MB en el mismo segundo, el total de unidades de E/S es igual a 495 + 8. Esto es porque una E/S de 2 MB resulta en 2048 KB / 256 KB = 8 unidades de E/S cuando el tamaño de la unidad de E/S es de 256 KB. Puesto que la suma de 495 + 8 supera el límite IOPS de 500 para el disco, se produce la limitación.

### <a name="example-2"></a>Ejemplo 2:
La aplicación hizo 400 unidades de E/S de 256 KB de tamaño en un disco P10. El ancho de banda total consumido es (400 × 256)/1024 = 100 MB/s. El rendimiento de un disco P10 es de 100 MB por segundo. Si la aplicación intenta realizar más operaciones de E/S en ese segundo, se limita porque supera el límite asignado.

### <a name="example-3"></a>Ejemplo 3:
Tiene una máquina virtual DS4 con dos discos P30 conectados. Cada disco P30 es capaz de tener un rendimiento de 200 MB por segundo. Sin embargo, una máquina virtual DS4 tiene una capacidad de ancho de banda de disco total de 256 MB por segundo. Por lo tanto, no puede dirigir los discos conectados al máximo rendimiento en esta máquina virtual DS4 al mismo tiempo. Para resolver este problema, puede mantener el tráfico de 200 MB por segundo en un disco y 56 MB por segundo en el otro disco. Si la suma del disco tráfico supera los 256 MB por segundo, el tráfico de disco queda limitado.

> [!NOTE]
> Si el tráfico de disco consta principalmente de tamaños de E/S pequeños, es muy probable que la aplicación alcance el límite de IOPS antes que el límite de rendimiento. Por otro lado, si el tráfico de disco consiste principalmente en tamaños grandes de E/S, es muy probable que la aplicación alcance el límite de rendimiento en lugar del límite de IOPS. Puede aumentar la tasa de IOPS de su aplicación y la capacidad de rendimiento mediante el uso de tamaños de E/S óptimos y también mediante la limitación del número de solicitudes de E/S pendientes de un disco.
> 

Para obtener más información sobre el diseño de alto rendimiento con el Almacenamiento premium, consulte el artículo [Almacenamiento premium de Azure: diseño de alto rendimiento](storage-premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Instantáneas y copia de blob

Para el servicio Storage, el archivo VHD es un blob en páginas. Es posible tomar instantáneas de blobs en páginas y copiarlos en otra ubicación, como por ejemplo en otra cuenta de almacenamiento.

### <a name="unmanaged-disks"></a>Discos no administrados

Se pueden crear [instantáneas incrementales](storage-incremental-snapshots.md) para discos premium no administrados de la misma manera que se usan las instantáneas con el almacenamiento estándar. Dado que el Almacenamiento premium solo admite almacenamiento con redundancia local (LRS) como opción de replicación, se recomienda crear instantáneas y luego copiarlas en una cuenta de Almacenamiento estándar con redundancia geográfica. Para obtener más información, consulte [Opciones de redundancia de Almacenamiento de Azure](storage-redundancy.md).

Si un disco está conectado a una máquina virtual, determinadas operaciones de la API no se permiten en los discos. Por ejemplo, no puede realizar una operación [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) en ese blob mientras el disco esté conectado a una máquina virtual. En su lugar, cree una instantánea del blob mediante el método de API de REST [Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob) y, luego, realice la operación [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) de la instantánea para copiar el disco conectado. Como alternativa, puede separar el disco y, después, realizar las operaciones necesarias.

Los siguientes límites se aplican a las instantáneas de blobs de Premium Storage:

| Límite del Almacenamiento premium | Valor |
| --- | --- |
| Máx. Número máximo de instantáneas por blob | 100 |
| Capacidad para instantáneas de la cuenta de almacenamiento (incluye datos solo en instantáneas, pero no los datos en el blob de base) | 10 TB |
| Mín. Tiempo mínimo entre instantáneas consecutivas | 10 minutos |

Para mantener copias con redundancia geográfica, puede copiar instantáneas desde una cuenta de almacenamiento premium a una cuenta de almacenamiento estándar con redundancia geográfica con AzCopy o Copy Blob. Para más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md) y [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob).

Para más información acerca de cómo realizar operaciones de REST en blobs en páginas en cuentas de Premium Storage, consulte [Using Blob Service Operations with Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969) (Uso de operaciones de Blob service con Azure Premium Storage).

### <a name="managed-disks"></a>Discos administrados

Una instantánea administrada es una copia de solo lectura de un disco administrado que se almacena como disco administrado estándar. Actualmente, las [instantáneas incrementales](storage-incremental-snapshots.md) no son compatibles con Managed Disks, pero lo serán en el futuro. Para obtener información acerca de cómo tomar una instantánea de un disco administrado, consulte [Create a copy of a VHD stored as an Azure Managed Disk by using Managed Snapshots](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md) (Creación de una copia de un VHD almacenado como disco administrado de Azure mediante el uso de instantáneas administradas en Windows) o [Create a copy of a VHD stored as an Azure Managed Disk by using Managed Snapshots in Linux](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md) (Creación de una copia de un VHD almacenado como disco administrado de Azure mediante el uso de instantáneas administradas en Linux)

Si un disco administrado está conectado a una máquina virtual, determinadas operaciones de la API no se permiten en los discos. Por ejemplo, mientras el disco esté conectado a una máquina virtual, no se puede generar una firma de acceso compartido (SAS) para realizar una operación de copia. En su lugar, primero hay que crear una instantánea del disco y, después, realizar la copia de la instantánea. Como alternativa, se puede separar el disco y, luego, generar una firma de acceso compartido (SAS) para realizar la operación de copia.


## <a name="using-linux-vms-with-premium-storage"></a>Uso de máquinas virtuales Linux con Premium Storage
Consulte a continuación las instrucciones para configurar máquinas virtuales Linux en Premium Storage:

* En todos los discos de Premium Storage en los que la caché se haya configurado como "ReadOnly" o "None", debe deshabilitar "barreras" al montar el sistema de archivos para lograr los objetivos de escalabilidad de Premium Storage. En este escenario no se necesitan barreras, ya que las operaciones de escritura en los discos de Premium Storage son duraderas para esta configuración de la caché. Cuando la solicitud de escritura se completa correctamente, los datos están escritos en el almacenamiento permanente. Los siguientes métodos se pueden usar para deshabilitar "barreras", elija el más apropiado para su sistema de archivos:
  
* Si utiliza **reiserFS**, deshabilite las barreras mediante la opción de montaje “barrier=none” (para habilitar las barreras, use “barrier=flush”)
* Si utiliza **ext3/ext4**, deshabilite las barreras mediante la opción de montaje “barrier=0” (para habilitar las barreras, use “barrier=1”)
* Si utiliza **XFS**, deshabilite las barreras mediante la opción de montaje “nobarrier” (para habilitar las barreras, use la opción “barrier”)
* En el caso de los discos de Premium Storage con la opción "ReadWrite" de caché, deben habilitarse barreras para que las operaciones de escritura sean duraderas.
* Para que las etiquetas de volumen persistan después de reiniciar la VM, debe actualizar/etc/fstab con las referencias UUID a los discos. Consulte también [Adición de un disco administrado a una máquina virtual Linux](../virtual-machines/virtual-machines-linux-add-disk.md).

Las siguientes son las distribuciones de Linux que se validan con Almacenamiento premium. Se recomienda actualizar las máquinas virtuales al menos a una de estas versiones (o posteriores) para mejorar el rendimiento y la estabilidad con Almacenamiento premium. Además, algunas de las versiones requieren Linux Integration Services (LIS) v4.0 para Microsoft Azure. Siga el vínculo proporcionado para su descarga e instalación. Seguiremos agregando más imágenes a la lista según vayamos completando validaciones adicionales. Tenga en cuenta que nuestras validaciones demostraron que el rendimiento varía para estas imágenes y también depende de las características de carga de trabajo y la configuración de las imágenes. Se ajustan diferentes imágenes a los distintos tipos de cargas de trabajo.

| Distribución | Versión | Kernel compatible | Detalles |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 requerido](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vea la nota siguiente* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vea la nota siguiente* |
| RHEL | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+ y 7.2+ | &nbsp; | UEK4 o RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 o RHCK con[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 o RHCK con[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Controladores de LIS para CentOS Openlogic

Los clientes que ejecutan las máquinas virtuales de OpenLogic CentOS tienen que ejecutar el comando siguiente para instalar a los controladores más recientes:

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

A continuación, se le pedirá un reinicio para activar los nuevos controladores.

## <a name="pricing-and-billing"></a>Precios y facturación

Al usar Almacenamiento premium, se aplican las siguientes consideraciones de facturación:

* Tamaño de disco o blob de Premium Storage
* Instantáneas de Premium Storage
* Transferencias de datos de salida

**Tamaño de disco o blob de Premium Storage**: la facturación de un disco o blob de Premium Storage depende de su tamaño aprovisionado. Azure asigna el tamaño aprovisionado (redondeado) a la opción de disco de Premium Storage más cercana, según se especifica en la tabla de la sección [Objetivos de escalabilidad y rendimiento al usar Premium Storage](#premium-storage-scalability-and-performance-targets). Cada uno de los discos se asignará a uno de los tamaños aprovisionados admitidos y se facturará en consecuencia. La facturación de cualquier disco aprovisionado se prorratea cada hora con el precio mensual de la oferta de Almacenamiento premium. Por ejemplo, si se aprovisiona un disco P10 y se elimina después de 20 horas, se factura la oferta P10 prorrateada a 20 horas. Esto es así independientemente de la cantidad de datos que se escriba en el disco o del rendimiento/IOPS usado.

**Instantáneas de discos no administrados premium**: las instantáneas de discos no administrados premium se facturan en función de la capacidad adicional que hayan usado. Para obtener información sobre las instantáneas, consulte [Crear una instantánea de un blob](/rest/api/storageservices/fileservices/Snapshot-Blob).

**Instantáneas de discos administrados premium**: una instantánea de un disco administrado es una copia de solo lectura del mismo que se almacena como disco administrado estándar. El coste de una instantánea administrada será el mismo que el de un disco administrado estándar. Por ejemplo, si toma una instantánea de un disco administrado premium de 128 GB, su coste equivaldrá al de un disco administrado estándar de 128 GB.  

**Transferencias de datos de salida**: las [transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/) (datos que salen de los centros de datos de Azure) se facturan en función del uso de ancho de banda.

Para información detallada acerca de los precios de Premium Storage, de las máquinas virtuales compatibles con Premium Storage y de Managed Disks, consulte los siguientes artículos:

* [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Precios de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Soporte técnico del servicio Azure Backup 

Se pueden realizar copias de seguridad de máquinas virtuales con discos no administrados mediante Azure Backup. [Más detalles](../backup/backup-azure-vms-first-look-arm.md).

El servicio Azure Backup también se puede usar con Managed Disks para crear un trabajo de copia de seguridad con copias de seguridad basadas en tiempo, fácil restauración de la máquina virtual y directivas de retención de copia de seguridad. En [Uso de máquinas virtuales de disco administrado con Azure Backup](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) puede encontrar más información al respecto. 

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el Almacenamiento premium de Azure, consulte los siguientes artículos.

### <a name="design-and-implement-with-azure-premium-storage"></a>Diseño e implementación con el Almacenamiento premium de Azure
* [Almacenamiento premium de Azure: diseño de alto rendimiento](storage-premium-storage-performance.md)
* [Uso de operaciones de servicios BLOB con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Guía de operaciones
* [Migrar a Almacenamiento premium de Azure](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Entradas de blog
* [Azure Premium Storage Generally Available (Disponibilidad general del Almacenamiento premium de Azure)](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Announcing the GS-Series: Adding Premium Storage Support to the Largest VMs in the Public Cloud (Anuncio de la serie GS: Incorporación de compatibilidad con el Almacenamiento premium a las mayores máquinas virtuales en la nube pública)](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)


<!--HONumber=Feb17_HO2-->


