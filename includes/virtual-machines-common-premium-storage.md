# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Discos administrados y Premium Storage de alto rendimiento para VM
Azure Premium Storage le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales (VM) con cargas de trabajo intensivas de entrada/salida (E/S). Los discos de VM que usan Premium Storage almacenan los datos en unidades de estado sólido (SSD). Para aprovechar la ventaja de la velocidad y el rendimiento de discos de Premium Storage, puede migrar los discos de VM existentes a Premium Storage.

En Azure, puede conectar varios discos de Premium Storage a una VM. Usar varios discos proporciona a las aplicaciones hasta 256 TB de almacenamiento por máquina virtual. Con Premium Storage, las aplicaciones pueden lograr 80 000 operaciones de E/S por segundo (IOPS) por VM, y un rendimiento de disco de hasta 2000 megabytes por segundo (MB/s) por VM. Las operaciones de lectura proporcionan latencias muy bajas.

Con Premium Storage, Azure ofrece la capacidad de levantar y mover realmente sus exigentes aplicaciones empresariales como Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite y granjas de SharePoint en la nube. Puede ejecutar cargas de trabajo de bases de datos de rendimiento intensivo como SQL Server, Oracle, MongoDB, MySQL y Redis, que requieren una baja latencia y un alto rendimiento de forma coherente.

> [!NOTE]
> Para lograr el mejor rendimiento de la aplicación, se recomienda migrar todos los discos de VM que requieran E/S por segundo elevadas a Premium Storage. Si el disco no requiere E/S por segundo elevadas, puede limitar los costos conservando Azure Storage estándar. En Standard Storage, los datos de disco de VM se almacenan en unidades de disco duro (HDD) en lugar de en SSD.
> 

Azure ofrece dos maneras de crear discos de Premium Storage para las VM:

* **Discos no administrados**

    El método original es usar discos no administrados. En un disco no administrado, administra las cuentas de almacenamiento que utiliza para almacenar los archivos del disco duro virtual (VHD) que se corresponden con los discos de VM. Los archivos VHD se almacenan como blobs de páginas en las cuentas de Azure Storage. 

* **Discos administrados**

    Cuando elige [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md), Azure administra las cuentas de almacenamiento que utiliza para los discos de VM. Especifique el tipo de disco (Premium o Standard) y el tamaño del disco que necesite. Azure crea y administra el disco en su nombre. No tiene que preocuparse de colocar los discos en varias cuentas de almacenamiento para asegurarse de que permanece dentro de los límites de escalabilidad de las cuentas de almacenamiento. Azure se encarga de ello.

Se recomienda que elija discos administrados para aprovechar muchas de sus ventajas.

Para empezar a trabajar con Premium Storage, [cree su cuenta de Azure gratuita](https://azure.microsoft.com/pricing/free-trial/). 

Para más información sobre la migración de VM existentes en Premium Storage, vea [Conversión de una VM Windows de discos sin administrar a discos administrados](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) o [Conversión de una VM Linux de discos sin administrar a discos administrados](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Premium Storage está disponible en la mayoría de las regiones. Para obtener la lista de regiones disponibles, consulte la fila de **Almacenamiento en disco** en [productos de Azure disponibles por región](https://azure.microsoft.com/regions/#services).
> 

## <a name="features"></a>Características

Aquí se muestran algunas de las características de Premium Storage:

* **Discos de Premium Storage**

    Premium Storage es compatible con discos de VM que se pueden adjuntar a VM de series de tamaño específico. Premium Storage es compatible con máquinas virtuales de las series DS, DSv2, GS, Ls y Fs. Tiene la opción de siete tamaños de disco: P4 (32 GB), P6 (64 GB), P10 (128 GB), P20 (512 GB), P30 (1024 GB), P40 (2048 GB), P50 (4095 GB). Los tamaños de disco P4 y P6 siguen admitiéndose solo para Managed Disks. Cada tamaño de disco tiene sus propias especificaciones de rendimiento. En función de los requisitos de la aplicación puede conectar uno o varios discos a la VM. En [Objetivos de rendimiento y escalabilidad de Premium Storage](#scalability-and-performance-targets) se describen las especificaciones más detalladamente.

* **Blobs en páginas Premium**

    Premium Storage admite blobs en páginas. Utilice blobs en páginas para almacenar discos persistentes y no administrados para las VM en Premium Storage. A diferencia de Azure Storage estándar, Premium Storage no admite blobs en bloques, blobs en anexos, archivos, tablas o colas. Blobs en páginas Premium admite seis tamaños de P10 a P50 y P60 (8191GiB). Blob en páginas Premium P60 no se admite para estar conectado como discos de máquina virtual. 

    Cualquier objeto que se coloque en una cuenta de Premium Storage será un blob en páginas. El blob en páginas se ajusta a uno de los tamaños de aprovisionamiento admitidos. Ese es el motivo por el que las cuentas de Premium Storage no están pensadas para el almacenamiento de blobs muy pequeños.

* **Cuenta de Premium Storage**

    Para empezar a usar Premium Storage, cree una cuenta de Premium Storage para discos no administrados. En [Azure Portal](https://portal.azure.com), para crear una cuenta de Premium Storage, elija el nivel de rendimiento **Premium**. Seleccione la opción de replicación **Almacenamiento con redundancia local (LRS)**. También puede crear una cuenta de Premium Storage estableciendo el tipo en **Premium_LRS** en una de las siguientes ubicaciones:
    * [API de REST de almacenamiento](https://docs.microsoft.com/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (versión 2014-02-14 o una versión posterior)
    * [API de REST de administración de servicios](http://msdn.microsoft.com/library/azure/ee460799.aspx) (versión 2014-10-01 o una versión posterior, para las implementaciones de Azure clásicas)
    * [API de REST del proveedor de recursos de Azure Storage](https://docs.microsoft.com/rest/api/storagerp) (para implementaciones de Azure Resource Manager)
    * [Azure PowerShell](/powershell/azureps-cmdlets-docs.md) (versión 0.8.10 o una versión posterior)

    Para más información sobre los límites de la cuenta de Premium Storage en la siguiente sección, vea [Objetivos de rendimiento y escalabilidad de Premium Storage](#premium-storage-scalability-and-performance-targets).

* **Premium Storage con redundancia local**

    Una cuenta de Premium Storage admite solo almacenamiento con redundancia local como opción de replicación. El almacenamiento con redundancia local mantiene tres copias de los datos en una única región. Para la recuperación ante desastres regionales, debe realizar una copia de los discos de máquina virtual en una región distinta con [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). También debe usar una cuenta de almacenamiento con redundancia geográfica (GRS) como el almacén de Backup. 

    Azure usa la cuenta de almacenamiento como contenedor para discos no administrados. Cuando crea una máquina virtual de Azure de las series DS, DSv2, GS o Fs con discos no administrados y selecciona una cuenta de Premium Storage, tanto el disco del sistema operativo como el de datos se almacenan en dicha cuenta de almacenamiento.

## <a name="supported-vms"></a>VM admitidas
Premium Storage es compatible con máquinas virtuales de las series DS, DSv2, GS, Ls, Fs y B. Con estos tipos de VM puede usar discos de Premium Storage y Standard Storage. No puede utilizar discos de Premium Storage con series de VM que no sean compatibles con Premium Storage.

Para más información sobre los tamaños y tipos de VM en Azure para Windows, vea [Tamaños de VM para Windows](../articles/virtual-machines/windows/sizes.md). Para más información sobre los tamaños y tipos de VM en Azure para Linux, vea [Tamaños de VM para Linux](../articles/virtual-machines/linux/sizes.md).

A continuación, se muestran algunas de las características de las máquinas virtuales de las series DS, DSv2, GS, Ls y Fs:

* **Servicio en la nube**

    Puede agregar VM de la serie DS a un servicio en la nube que tenga solo VM de la serie DS. No agregue VM de la serie DS a un servicio en la nube existente que incluya otras VM que no sean de la serie DS. Puede migrar los VHD existentes a un nuevo servicio en la nube que ejecute solo VM de la serie DS. Si desea usar la misma dirección IP virtual (VIP) para el nuevo servicio en la nube que hospeda las VM de la serie DS, use [Direcciones IP reservadas](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). Las VM de la serie GS se pueden agregar a un servicio en la nube que solo tenga VM de la serie GS.

* **Disco del sistema operativo**

    Puede configurar la VM de Premium Storage para usar un disco del sistema operativo premium o estándar. Para obtener la mejor experiencia, se recomienda usar un disco de sistema operativo basado en Premium Storage.

* **Discos de datos**

    Puede usar discos premium y estándar en la misma VM de Premium Storage. Con Premium Storage, puede aprovisionar una máquina virtual y conectar varios discos de datos persistentes a la máquina virtual. Si es necesario, puede fragmentar los discos para aumentar la capacidad y el rendimiento del volumen.

    > [!NOTE]
    > Si fragmenta discos de datos de Premium Storage mediante [Espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), tendrá que configurarlos con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. De forma predeterminada, en el administrador de servidores, puede configurar las columnas para hasta 8 discos. Si dispone de más de 8 discos, use PowerShell para crear el volumen. Especifique manualmente el número de columnas. De lo contrario, la IU del administrador del servidor sigue usando 8 columnas aunque tenga más discos. Por ejemplo, si tiene 32 discos en un conjunto de bandas único, especifique 32 columnas. Use el parámetro [NumberOfColumns](http://technet.microsoft.com/library/hh848643.aspx) del cmdlet *New-VirtualDisk* de PowerShell para especificar el número de columnas que usa el disco virtual. Para más información, consulte [Introducción a los espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx) y [Preguntas más frecuentes sobre los espacios de almacenamiento](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Memoria caché**

    Las VM de la serie de tamaño que admiten Premium Storage tienen una funcionalidad de almacenamiento en caché exclusiva para altos niveles de rendimiento y latencia. La funcionalidad de almacenamiento en caché supera el rendimiento del disco de Premium Storage subyacente. Puede establecer la directiva de almacenamiento en caché del disco en los discos de Premium Storage en **ReadOnly**, **ReadWrite** o **None**. La directiva de almacenamiento en caché de disco predeterminada es **ReadOnly** para todos los discos de datos premium y **ReadWrite** para los discos del sistema operativo. Para obtener un rendimiento óptimo para la aplicación, use la configuración correcta de la caché. Por ejemplo, en el caso de los discos de datos de solo lectura o con mucha actividad de lectura, como los archivos de datos de SQL Server, establezca la directiva de almacenamiento en caché de disco en **ReadOnly**. En el caso de los discos de datos de solo escritura o con mucha actividad de escritura, como los archivos de registro de SQL Server, establezca la directiva de almacenamiento en caché del disco en **None**. Consulte más información sobre cómo optimizar el diseño con Premium Storage en [Diseño para rendimiento con Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Analytics**

    Para analizar el rendimiento de la VM mediante el uso de discos en Premium Storage, active el diagnóstico de VM en [Azure Portal](https://portal.azure.com). Para más información, consulte [Supervisión de una máquina virtual de Azure con Extensión de Diagnósticos de Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Para ver el rendimiento del disco, use herramientas del sistema operativo, como el [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) en las VM Windows y el comando [iostat](http://linux.die.net/man/1/iostat) en las VM Linux.

* **Rendimiento y límites de escala de VM**

    Cada tamaño de máquina virtual compatible con Premium Storage tiene límites de escala y una especificación de rendimiento para E/S por segundo, ancho de banda y número de discos que se pueden conectar por máquina virtual. Cuando use discos de Premium Storage con VM, asegúrese de que las ES/S por segundo y el ancho de banda de la VM sean suficientes para dirigir el tráfico del disco.

    Por ejemplo, una VM STANDARD_DS1 tiene un ancho de banda dedicado de 32 MB/s para el tráfico de disco de Premium Storage. Un disco de Premium Storage P10 puede proporcionar un ancho de banda de 100 MB/s. Si un disco de Premium Storage P10 está asociado a esta VM, solo puede ir a 32 MB/s. No puede utilizar los 100 MB/s máximos que puede proporcionar el disco P10.

    Actualmente, la VM más grande de la serie DS es Standard_DS15_v2. Standard_DS15_v2 puede proporcionar hasta 960 MB/s en todos los discos. La VM más grande de la serie GS es Standard_GS5. Standard_GS5 puede proporcionar hasta 2000 MB/s en todos los discos.

    Tenga en cuenta que estos límites son solo para el tráfico de disco. Estos límites no incluyen los aciertos de la caché y el tráfico de red. Hay disponible un ancho de banda independiente para el tráfico de red de VM. El ancho de banda para el tráfico de red es diferente del ancho de banda dedicado utilizado por los discos de Premium Storage.

    Para obtener la información más actualizada acerca de los niveles máximos de E/S por segundo y rendimiento (ancho de banda) de las VM compatibles con Premium Storage, consulte los artículos sobre los [tamaños de VM Windows](../articles/virtual-machines/windows/sizes.md) o [Linux](../articles/virtual-machines/linux/sizes.md).

    Para más información sobre los discos de Premium Storage y sus límites de rendimiento y E/S por segundo, vea la tabla en la sección siguiente.

## <a name="scalability-and-performance-targets"></a>Objetivos de escalabilidad y rendimiento
En esta sección, describiremos todos los objetivos de escalabilidad y rendimiento que se deben tener en cuenta al usar Premium Storage.

Las cuentas de Premium Storage tienen los siguientes objetivos de escalabilidad:

| Capacidad total de la cuenta | Ancho de banda total de una cuenta de almacenamiento con redundancia local |
| --- | --- | 
| Capacidad de disco: 35 TB <br>Capacidad de instantánea: 10 TB | Hasta 50 gigabits por segundo de entrada<sup>1</sup> y salida<sup>2</sup> |

<sup>1</sup> Todos los datos (solicitudes) que se envían a una cuenta de almacenamiento

<sup>1</sup> Todos los datos (respuestas) que se reciben desde una cuenta de almacenamiento

Para más información, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../articles/storage/common/storage-scalability-targets.md).

Si usa cuentas de Premium Storage para los discos no administrados y la aplicación supera los objetivos de escalabilidad de una cuenta de almacenamiento individual, es posible que quiera migrar a Managed Disks. Si no desea migrar a Managed Disks, compile la aplicación para que use varias cuentas de almacenamiento. A continuación, divida los datos en esas cuentas de almacenamiento. Por ejemplo, si desea asociar discos de 51 TB entre varias VM, distribúyalos entre dos cuentas de almacenamiento. 35 TB es el límite de una cuenta de Premium Storage única. Asegúrese de que una sola cuenta de Premium Storage nunca tenga más de 35 TB de discos aprovisionados.

### <a name="premium-storage-disk-limits"></a>Límites de discos de Premium Storage
Cuando se aprovisiona un disco de Premium Storage, el tamaño del disco determina los valores máximos de IOPS y rendimiento (ancho de banda). Azure ofrece siete tipos de discos de Premium Storage: P4 (solo Managed Disks), P6 (solo Managed Disks), P10, P20, P30, P40 y P50. Cada tipo de disco de Premium Storage tiene límites específicos de E/S por segundo y rendimiento. Los límites de los tipos de disco se describen en la siguiente tabla.

| Tipo de discos Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamaño del disco           | 32 GB| 64 GB| 128 GB| 512 GB            | 1.024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS por disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Rendimiento de disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo | 

> [!NOTE]
> Asegúrese de que haya ancho de banda suficiente disponible en la VM para el tráfico de disco de unidad, como se describe en [Máquinas virtuales compatibles con Premium Storage](#premium-storage-supported-vms). En caso contrario, el rendimiento del disco y la E/S por segundo estarán restringidos a valores inferiores. El rendimiento y la E/S por segundo máximos se basan en los límites de la VM, no en los límites de disco descritos en la tabla anterior.  
> 
> 

Estos son algunos aspectos importantes que debe conocer sobre los objetivos de escalabilidad y rendimiento de Premium Storage:

* **Rendimiento y capacidad aprovisionados**

    Cuando se aprovisiona un disco de Premium Storage, a diferencia de Standard Storage, se garantizan la capacidad, las E/S por segundo y el rendimiento del mismo. Por ejemplo, si crea un disco P50, Azure aprovisiona una capacidad de almacenamiento de 4095 GB, 7500 E/S por segundo y un rendimiento de 250 MB/s para él. La aplicación puede usar toda la capacidad y el rendimiento o parte de ellos.

* **Tamaño del disco**

    Azure asigna el tamaño del disco (redondeado al alza) a la opción de disco de Premium Storage más cercana según se especifica en la tabla en la sección anterior. Por ejemplo, un tamaño de disco de 100 GB se clasifica como una opción P10. Puede realizar hasta 500 E/S por segundo, con un rendimiento de hasta 100 MB/s. De forma similar, un disco con un tamaño de 400 GB se clasifica como P20. Puede realizar hasta 2300 E/S por segundo, con un rendimiento de 150 MB/s.
    
    > [!NOTE]
    > Puede aumentar fácilmente el tamaño de los discos existentes. Por ejemplo, puede aumentar el tamaño de un disco de 30 GB a 128 GB o, incluso, a 1 TB. O bien, puede convertir un disco P20 en un disco P30 porque necesita más capacidad o más E/S por segundo y rendimiento. 
    > 
 
* **Tamaño de E/S**

    El tamaño de una E/S es de 256 KB. Si el tamaño de los datos transferidos es inferior a 256 KB, se considera una unidad de E/S. Los tamaños de E/S más grandes se cuentan como varias operaciones de E/S con un tamaño de 256 KB. Por ejemplo, una E/S de 1,100 KB se cuenta como cinco unidades de E/S.

* **Rendimiento**

    El límite de rendimiento incluye escrituras en el disco, así como operaciones de lectura en el disco que no se sirven desde la memoria caché. Por ejemplo, un disco P10 tiene un rendimiento de 100 MB/s por disco. En la tabla siguiente se muestran algunos ejemplos de rendimientos válidos para un disco P10:

    | Rendimiento máximo por disco P10 | Lecturas no en la memoria caché desde el disco | Escrituras no en la memoria caché en el disco. |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Aciertos de caché**

    Los aciertos de caché no están limitados por las E/S por segundo ni el rendimiento asignados del disco. Por ejemplo, cuando se utiliza un disco de datos con la configuración de caché **ReadOnly** en una VM compatible con Premium Storage, las lecturas que se sirven desde la memoria caché no están sujetas a los límites de E/S por segundo y rendimiento del disco. Si la carga de trabajo de un disco es predominantemente lecturas, puede que obtenga un rendimiento muy alto. La caché está sujeta a límites de E/S por segundo y rendimiento independientes en el nivel de la VM que se basan en el tamaño de esta. Las VM de la serie DS tienen aproximadamente 4000 E/S por segundo y un rendimiento de 33 MB/s por núcleo de caché y E/S de SSD locales. Las VM de la serie DS tienen aproximadamente un límite de 5000 E/S por segundo y un rendimiento de 50 MB/s por núcleo de caché y E/S de SSD locales. 

## <a name="throttling"></a>Limitaciones
La limitación puede ocurrir si la E/S por segundo o el rendimiento de la aplicación supera los límites asignados para un disco de Premium Storage. Limitación también se podría producir si el tráfico total en disco en todos los discos en la VM supera el límite de ancho de banda de disco disponible para la VM. Para evitar la limitación, se recomienda que limite el número de solicitudes de E/S pendientes del disco. Utilice un límite basado en objetivos de escalabilidad y rendimiento para el disco que se ha aprovisionado y en el ancho de banda de disco disponible para la VM.  

La aplicación puede lograr la menor latencia cuando se ha diseñado para evitar la limitación. Sin embargo, si el número de solicitudes de E/S pendientes del disco es demasiado pequeño, la aplicación no puede aprovechar al máximo los niveles de rendimiento y E/S por segundo que están disponibles en el disco.

En los ejemplos siguientes se muestra cómo calcular los niveles de limitación. Todos los cálculos se basan en un tamaño de unidad de E/S de 256 KB.

### <a name="example-1"></a>Ejemplo 1
La aplicación procesó 495 unidades de E/S de 16 KB de tamaño en un segundo en un disco P10. Las unidades de E/S se cuentan como 495 E/S por segundo. Si intenta una E/S de 2 MB en el mismo segundo, el total de unidades de E/S es igual a 495 + 8 E/S por segundo. Esto es porque una E/S de 2 MB = 2048 KB/256 KB = 8 unidades de E/S cuando el tamaño de la unidad de E/S es de 256 KB. Puesto que la suma de 495 + 8 supera el límite de E/S por segundo de 500 para el disco, se produce la limitación.

### <a name="example-2"></a>Ejemplo 2
La aplicación procesó 400 unidades de E/S de 256 KB de tamaño en un disco P10. El ancho de banda total consumido es (400 × 215)/256 = 1,024 MB/s. El rendimiento de un disco P10 es de 100 MB/s. Si la aplicación intenta realizar más operaciones de E/S en ese segundo, se limita porque supera el límite asignado.

### <a name="example-3"></a>Ejemplo 3
Tiene una máquina virtual DS4 con dos discos P30 conectados. Cada disco P30 es capaz de tener un rendimiento de 200 MB/s. Sin embargo, una VM DS4 tiene una capacidad de ancho de banda de disco total de 256 MB/s. No puede dirigir los discos conectados al máximo rendimiento en esta VM DS4 al mismo tiempo. Para resolver este problema, puede mantener el tráfico de 200 MB/s en un disco y 56 MB/s en el otro disco. Si la suma del disco tráfico supera los 256 MB/s, el tráfico de disco queda limitado.

> [!NOTE]
> Si el tráfico de disco consta principalmente de tamaños de E/S pequeños, es probable que la aplicación alcance el límite de E/S por segundo antes que el límite de rendimiento. Sin embargo, si el tráfico de disco consiste principalmente en tamaños grandes de E/S, es probable que la aplicación alcance primero el límite de rendimiento en lugar del límite de E/S por segundo. Puede maximizar la E/S por segundo y el rendimiento de su aplicación mediante el uso de tamaños de E/S óptimos. Además, puede limitar el número de solicitudes de E/S pendientes para un disco.
> 

Para más información sobre el diseño de alto rendimiento con Premium Storage, consulte [Diseño para rendimiento con Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Instantáneas y Copy Blob

Para el servicio Storage, el archivo VHD es un blob en páginas. Es posible tomar instantáneas de blobs en páginas y copiarlos en otra ubicación, como por ejemplo en otra cuenta de almacenamiento.

### <a name="unmanaged-disks"></a>Discos no administrados

Cree [instantáneas incrementales](../articles/virtual-machines/linux/incremental-snapshots.md) para discos premium no administrados de la misma manera que se usan las instantáneas con Standard Storage. Premium Storage admite solo almacenamiento con redundancia local como opción de replicación. S recomienda crear instantáneas y, luego, copiarlas en una cuenta de Standard Storage con redundancia geográfica. Para obtener más información, vea [Opciones de redundancia de Azure Storage](../articles/storage/common/storage-redundancy.md).

Si un disco está conectado a una VM, no se permiten determinadas operaciones de la API en el disco. Por ejemplo, no puede realizar una operación [Copy Blob](/rest/api/storageservices/Copy-Blob) en ese blob mientras el disco esté conectado a una VM. En su lugar, cree primero una instantánea de ese blob utilizando la API de REST del [blob de instantánea](/rest/api/storageservices/Snapshot-Blob). A continuación, realice la operación [Copy Blob](/rest/api/storageservices/Copy-Blob) en la instantánea para copiar el disco asociado. Como alternativa, puede separar el disco y, después, realizar las operaciones necesarias.

Los siguientes límites se aplican a las instantáneas de blobs de Premium Storage:

| Límite de Premium Storage | Valor |
| --- | --- |
| Número máximo de instantáneas por blob | 100 |
| Capacidad de la cuenta de almacenamiento de instantáneas<br>(Incluye solo datos en las instantáneas. No incluye datos en blob base). | 10 TB |
| Tiempo mínimo entre instantáneas consecutivas | 10 minutos |

Para mantener copias con redundancia geográfica, puede copiar instantáneas desde una cuenta de almacenamiento premium a una cuenta de almacenamiento estándar con redundancia geográfica con AzCopy o Copy Blob. Para más información, vea [Transferencia de datos con la utilidad en línea de comandos AzCopy](../articles/storage/common/storage-use-azcopy.md) y [Copy Blob](/rest/api/storageservices/Copy-Blob).

Para más información acerca de cómo realizar operaciones de REST en blobs en páginas en cuentas de Premium Storage, consulte [Uso de operaciones de Blob service con Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Discos administrados

Una instantánea para un disco administrado es una copia de solo lectura de un disco administrado. La instantánea se almacena como un disco administrado estándar. Actualmente, las [instantáneas incrementales](../articles/virtual-machines/windows/incremental-snapshots.md) no son compatibles con discos administrados. Para más información acerca de cómo tomar una instantánea de un disco administrado, vea [Creación de una copia de un VHD almacenado como disco administrado de Azure mediante el uso de instantáneas administradas en Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) o [Creación de una copia de un VHD almacenado como disco administrado de Azure mediante el uso de instantáneas administradas en Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Si un disco administrado está conectado a una VM, no se permiten determinadas operaciones de la API en el disco. Por ejemplo, mientras el disco esté conectado a una máquina virtual, no se puede generar una firma de acceso compartido (SAS) para realizar una operación de copia. En su lugar, primero hay que crear una instantánea del disco y, después, realizar la copia de la instantánea. Como alternativa, puede separar el disco y, luego, generar una SAS para realizar la operación de copia.


## <a name="premium-storage-for-linux-vms"></a>Premium Storage para VM Linux
Puede usar la siguiente información para ayudarle a configurar VM Linux en Premium Storage:

Para conseguir objetivos de escalabilidad en Premium Storage, debe deshabilitar todas las "barreras" cuando monte el sistema de archivos en todos los discos de Premium Storage con la caché establecida en **ReadOnly** o **None**. En este escenario no se necesitan barreras, ya que las operaciones de escritura en los discos de Premium Storage son duraderas para esta configuración de la caché. Cuando la solicitud de escritura se completa correctamente, los datos están escritos en el almacenamiento permanente. Para deshabilitar las "barreras", utilice uno de los siguientes métodos. Elija uno para el sistema de archivos:
  
* Para **reiserFS**, para deshabilitar las barreras, utilice la opción de montaje `barrier=none`. (Para habilitar las barreras, use `barrier=flush`).
* Para **ext3/ext4**,, para deshabilitar las barreras, utilice la opción de montaje `barrier=0`. (Para habilitar las barreras, use `barrier=1`).
* Para **XFS**, para deshabilitar las barreras, utilice la opción de montaje `nobarrier`. (Para habilitar las barreras, use `barrier`).
* Para los discos de Premium Storage con la caché establecida en **ReadWrite**, habilite las barreras para la durabilidad de escritura.
* Para que las etiquetas de volumen persistan después de reiniciar la VM, debe actualizar /etc/fstab con las referencias del identificador único universal (UUID) en los discos. Para más información, vea [Adición de un disco administrado a una máquina virtual Linux](../articles/virtual-machines/linux/add-disk.md).

Las distribuciones de Linux siguientes han sido validadas para Azure Premium Storage. Se recomienda actualizar las VM al menos a una de estas versiones (o posteriores) para mejorar el rendimiento y la estabilidad con Premium Storage. Algunas de las versiones requieren Linux Integration Services (LIS) v4.0 para Azure. Para descargar e instalar una distribución, siga el vínculo que aparece en la tabla siguiente. Agregamos imágenes a la lista cuando se completa la validación. Tenga en cuenta que nuestras validaciones muestran que el rendimiento en cada imagen. El rendimiento depende en las características de carga de trabajo y la configuración de la imagen. Se ajustan diferentes imágenes a los distintos tipos de cargas de trabajo.

| Distribución | Versión | Kernel compatible | Detalles |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 requerido](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vea la nota en la siguiente sección* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Vea la nota en la siguiente sección* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+ y 7.2+ | &nbsp; | UEK4 o RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 o RHCK con[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 o RHCK con[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Controladores de LIS para CentOS Openlogic

Si tiene VM de OpenLogic CentOS, ejecute el comando siguiente para instalar a los controladores más recientes:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Para activar los nuevos controladores, reinicie el equipo.

## <a name="pricing-and-billing"></a>Precios y facturación

Al usar Premium Storage, se aplican las siguientes consideraciones de facturación:

* **Tamaño de disco y blob de Premium Storage**

    La facturación de un disco de Premium Storage o blog depende del tamaño aprovisionado del disco o blob. Azure asigna el tamaño aprovisionado (redondeado) a la opción de disco de Premium Storage más cercana. Para más información, consulte la tabla en [Objetivos de escalabilidad y rendimiento de Storage](#premium-storage-scalability-and-performance-targets). Cada disco se asigna a un tamaño de disco aprovisionado compatible y se factura según corresponda. La facturación de cualquier disco aprovisionado se prorratea cada hora con el precio mensual de la oferta de Premium Storage. Por ejemplo, si se aprovisiona un disco P10 y se elimina después de 20 horas, se factura la oferta P10 prorrateada a 20 horas. Esto es así independientemente de la cantidad de datos que se escriba en el disco o del rendimiento y E/S por segundo usados.

* **Instantáneas de Unmanaged Disks premium**

    Las instantáneas de Unmanaged Disks premium se facturan por la capacidad adicional usada por las instantáneas. Para más información sobre las instantáneas, consulte [Creación de una instantánea de un blob](/rest/api/storageservices/Snapshot-Blob).

* **Instantáneas de Managed Disks premium**

    Una instantánea para un disco administrado es una copia de solo lectura de un disco. El disco se almacena como un disco administrado estándar. Los costes de una instantánea administrada serán los mismos que los de un disco administrado estándar. Por ejemplo, si toma una instantánea de Managed Disks premium de 128 GB, el costo de la instantánea administrada equivale al de un disco administrado estándar de 128 GB.  

* **Transferencias de datos de salida**

    [Transferencias de datos de salida](https://azure.microsoft.com/pricing/details/data-transfers/) (datos que salen de los centros de datos de Azure) incurren en la facturación por el uso de ancho de banda.

Para información detallada sobre los precios de Premium Storage, de las máquinas virtuales compatibles con Premium Storage y de Managed Disks, consulte los estos artículos:

* [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Soporte técnico de Azure Backup 

Para recuperación ante desastres regionales, debe realizar una copia de los discos de máquina virtual en una región distinta con [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) y una cuenta de almacenamiento GRS como almacén de Backup.

Para crear un trabajo de copia de seguridad con copias de seguridad basadas en tiempo, fácil restauración de la VM y directivas de retención de copia de seguridad, use Azure Backup. Puede utilizar Backup con discos administrados y no administrados. Para más información, vea [Azure Backup para VM con Unmanaged Disks ](../articles/backup/backup-azure-vms-first-look-arm.md) y [Azure Backup para VM con Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>pasos siguientes
Para más información sobre el Premium Storage, consulte los siguientes artículos.

### <a name="design-and-implement-with-premium-storage"></a>Diseño e implementación con Premium Storage
* [Diseño para rendimiento con Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md)
* [Operaciones de almacenamiento de blobs con Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Guía de operaciones
* [Migrar a Azure Premium Storage](../articles/storage/common/storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Publicaciones de blog
* [Disponibilidad general de Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Anuncio de la serie GS: Incorporación de compatibilidad con Premium Storage a las VM más grandes en la nube pública](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
