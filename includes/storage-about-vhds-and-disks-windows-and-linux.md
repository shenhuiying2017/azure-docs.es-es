---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f64645db782b055e1c544f257149411f29fc99d7
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806322"
---
## <a name="about-vhds"></a>Acerca de los discos duros virtuales
Los discos duros virtuales usados en Azure son archivos .vhd almacenados como blobs en páginas en una cuenta de almacenamiento estándar o premium de Azure. Para obtener información detallada sobre blobs en páginas, consulte [Introducción a los blobs en bloques y a los blobs en páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Para más información sobre Premium Storage, consulte [Premium Storage de alto rendimiento y máquinas virtuales de Azure](../articles/virtual-machines/windows/premium-storage.md).

Azure admite el formato VHD de disco fijo. El formato fijo coloca el disco lógico linealmente dentro del archivo, de manera que el desplazamiento de disco X se almacena en el desplazamiento de blob X. Un pequeño pie de página al final del blob describe las propiedades del VHD. El formato fijo suele desaprovechar el espacio porque la mayoría de discos contienen grandes intervalos sin usar. Sin embargo, Azure almacena los archivos .vhd en un formato disperso; así pues, se beneficia de las ventajas de los discos fijos y dinámicos al mismo tiempo. Para más información, consulte [Introducción a discos duros virtuales](https://technet.microsoft.com/library/dd979539.aspx).

Todos los archivos VHD de Azure que quiera usar como origen para crear discos o imágenes son de solo lectura, excepto los archivos .vhd que el usuario cargó o copió en Azure Storage (los cuales pueden ser de lectura/escritura o de solo lectura). Cuando se crea un disco o una imagen, Azure hace copias de los archivos .vhd de origen. Estas copias pueden ser de solo lectura o de lectura y escritura, en función de cómo use el disco duro virtual.

Cuando se crea una máquina virtual desde una imagen, Azure crea un disco para la máquina virtual que es una copia del archivo .vhd de origen. Para proteger contra la eliminación accidental, Azure coloca una concesión en cada archivo .vhd de origen que se usa para crear una imagen, un disco del sistema operativo o un disco de datos.

Para poder eliminar un archivo .vhd de origen, deberá quitar la concesión eliminando el disco o la imagen. Para eliminar un archivo .vhd que está siendo usado por una máquina virtual como disco del sistema operativo, puede eliminar la máquina virtual, el disco del sistema operativo y el archivo .vhd de origen a la vez, eliminando la máquina virtual y todos los discos asociados. Sin embargo, para eliminar un archivo .vhd que es el origen de un disco de datos es necesario realizar varios pasos en un orden determinado. Primero, desasocie el disco de la máquina virtual y, a continuación, elimine el disco y el archivo .vhd.

> [!WARNING]
> Si elimina un archivo .vhd de origen del almacenamiento o elimina la cuenta de almacenamiento, Microsoft no puede recuperar esos datos por usted.

## <a name="types-of-disks"></a>Tipos de discos 
Los discos de Azure están diseñados para ofrecer una disponibilidad del 99,999 %. Los discos de Azure ofrecen durabilidad de nivel empresarial, con una tasa de error anualizada del 0 %.

Hay tres niveles de rendimiento para el almacenamiento que puede elegir al crear los discos: discos SSD premium, SSD estándar (versión preliminar) y almacenamiento HDD estándar. Además, hay dos tipos de discos: administrado y no administrado.

### <a name="standard-hdd-disks"></a>Discos HDD estándar
Los discos HDD estándar están respaldados por unidades de disco duro y proporcionan un almacenamiento de menor costo. El almacenamiento HDD estándar se puede replicar de forma local en un centro de datos o tener redundancia geográfica con centros de datos principales y secundarios. Para más información sobre la replicación del almacenamiento, consulte [Replicación en Azure Storage](../articles/storage/common/storage-redundancy.md). 

Para más información sobre el uso de discos HDD estándar, consulte [Discos y almacenamiento estándar](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks-preview"></a>Discos SSD estándar (versión preliminar)
Los discos SSD estándar están diseñados para resolver el mismo tipo de cargas de trabajo que los discos HDD estándar, pero ofrecen un rendimiento más coherente y mayor fiabilidad que las unidades de disco duro. Los discos SSD estándar combinan elementos de los discos SSD premium y los HDD estándar para conformar una solución rentable adecuada para aplicaciones como los servidores web, que no necesitan un índice elevado de IOPS en los discos. Donde están disponibles, los discos SSD estándar son la opción de implementación recomendada para la mayoría de las cargas de trabajo. Los discos SSD estándar solo están disponibles como discos administrados y mientras están en versión preliminar solo están disponibles en [regiones seleccionadas](../articles/virtual-machines/windows/faq-for-disks.md) y con el tipo de resistencia del almacenamiento con redundancia local (LRS).

### <a name="premium-ssd-disks"></a>Discos SSD premium 
Los discos SSD premium están respaldados por unidades de estado sólido y ofrecen soporte de disco de alto rendimiento y baja latencia para máquinas virtuales que ejecutan cargas de trabajo intensivas de entrada y salida. Normalmente los discos SSD premium se utilizan con tamaños que incluyen una "s" en el nombre de la serie. Por ejemplo, están la serie Dv3 y la serie Dsv3; la serie Dsv3 se puede utilizar con discos SSD premium.  Para más información, consulte [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Discos no administrados
Los discos no administrados constituyen el tipo tradicional de discos que han usado las máquinas virtuales. Con estos discos se crea la propia cuenta de almacenamiento y se especifica esa cuenta de almacenamiento al crear el disco. Asegúrese de no incluir demasiados discos en la misma cuenta de almacenamiento, ya que podría superar los [objetivos de escalabilidad](../articles/storage/common/storage-scalability-targets.md) de la cuenta de almacenamiento (20 000 IOPS, por ejemplo), lo que da como resultado una limitación de las máquinas virtuales. Con los discos no administrados, tendrá que averiguar cómo maximizar el uso de una o varias cuentas de almacenamiento para obtener el máximo rendimiento de las máquinas virtuales.

### <a name="managed-disks"></a>Discos administrados 
El servicio Managed Disks controla la creación y administración de las cuentas de almacenamiento en segundo plano y se asegura de que no tiene que preocuparse de los límites de escalabilidad de la cuenta de almacenamiento. Simplemente especifique el tamaño del disco y el nivel de rendimiento (Estándar o Premium) y Azure crea y administra el disco en su nombre. Al agregar discos o escale y reduzca la máquina virtual no tendrá que preocuparse por el almacenamiento que se va a usar. 

También puede administrar sus imágenes personalizadas en una cuenta de almacenamiento por región de Azure y utilizarlas para crear cientos de máquinas virtuales en la misma suscripción. Para más información acerca de Managed Disks, consulte [Introducción a Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Se recomienda usar Azure Managed Disks para las nuevas máquinas virtuales y que convierta los discos no administrados anteriores en discos administrados, para aprovechar las múltiples características disponibles en Managed Disks.

### <a name="disk-comparison"></a>Comparación de discos
En la tabla siguiente se ofrece una comparación de los discos HDD estándar, SSD estándar y SSD premium para discos administrados y no administrados para ayudar a decidir cuál utilizar.

|    | Disco Premium de Azure |Disco SSD estándar de Azure (versión preliminar)| Disco HDD estándar de Azure 
|--- | ------------------ | ------------------------------- | ----------------------- 
| Tipo de disco | Unidades de estado sólido (SSD) | Unidades de estado sólido (SSD) | Unidades de disco duro (HDD)  
| Información general  | Compatibilidad con discos de alto rendimiento y baja latencia basados en SSD para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S o que hospedan un entorno de producción crítico |Rendimiento y fiabilidad más coherentes que los discos HDD. Optimizados para cargas de trabajo de bajas IOPS| Discos rentables basados en unidades de disco duro para acceso poco frecuente
| Escenario  | Cargas de trabajo confidenciales de producción y rendimiento |Servidores web, aplicaciones empresariales poco utilizadas y desarrollo y pruebas| Copia de seguridad, no crítico, acceso poco frecuente 
| Tamaño del disco | P4: 32 GiB (solo discos administrados)<br>P6: 64 GiB (solo discos administrados)<br>P10: 128 GiB<br>P15: 256 GiB (solo discos administrados)<br>P20: 512 GiB<br>P30: 1 024 GiB<br>P40: 2 048 GiB<br>P50: 4 095 GiB |Solo discos administrados:<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1 024 GiB<br>E40: 2 048 GiB<br>E50: 4 095 GiB | Discos no administrados: 1 GiB a 4 TiB (4 095 GiB) <br><br>Discos administrados:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1 024 GiB <br>S40: 2 048 GiB<br>S50: 4 095 GiB
| Rendimiento máximo por disco | 250 MiB/s | Hasta 60 MiB/s | Hasta 60 MiB/s 
| IOPS máximas por disco | 7500 IOPS | Hasta 500 IOPS | Hasta 500 IOPS 