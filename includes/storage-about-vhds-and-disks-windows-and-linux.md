
## <a name="about-vhds"></a>Acerca de los discos duros virtuales

Los discos duros virtuales usados en Azure son archivos .vhd almacenados como blobs en páginas en una cuenta de almacenamiento estándar o premium de Azure. Para obtener información detallada sobre blobs en páginas, consulte [Introducción a los blobs en bloques y a los blobs en páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Para más información sobre Premium Storage, consulte [Premium Storage de alto rendimiento y máquinas virtuales de Azure](../articles/storage/common/storage-premium-storage.md).

Azure admite el formato VHD de disco fijo. El formato fijo coloca el disco lógico linealmente dentro del archivo, de manera que el desplazamiento de disco X se almacena en el desplazamiento de blob X. Un pequeño pie de página al final del blob describe las propiedades del VHD. El formato fijo a menudo desaprovecha el espacio porque la mayoría de discos contienen grandes rangos sin utilizar. Sin embargo, Azure almacena los archivos .vhd en un formato disperso; así pues, se beneficia de las ventajas de los discos fijos y dinámicos al mismo tiempo. Para obtener más información, consulte [Introducción a los discos duros virtuales](https://technet.microsoft.com/library/dd979539.aspx).

Todos los archivos .vhd de Azure que desea usar como origen para crear discos o imágenes son de solo lectura. Cuando se crea un disco o una imagen, Azure hace copias de los archivos .vhd. Estas copias pueden ser de solo lectura o de lectura y escritura, en función de cómo use el disco duro virtual.

Cuando se crea una máquina virtual desde una imagen, Azure crea un disco para la máquina virtual que es una copia del archivo .vhd de origen. Para proteger contra la eliminación accidental, Azure coloca una concesión en cada archivo .vhd de origen que se usa para crear una imagen, un disco del sistema operativo o un disco de datos.

Para poder eliminar un archivo .vhd de origen, deberá quitar la concesión eliminando el disco o la imagen. Para eliminar un archivo .vhd que está siendo usado por una máquina virtual como disco del sistema operativo, puede eliminar la máquina virtual, el disco del sistema operativo y el archivo .vhd de origen a la vez, eliminando la máquina virtual y todos los discos asociados. Sin embargo, para eliminar un archivo .vhd que es el origen de un disco de datos es necesario realizar varios pasos en un orden determinado. Primero, desasocie el disco de la máquina virtual y, a continuación, elimine el disco y el archivo .vhd.

> [!WARNING]
> Si elimina un archivo .vhd de origen del almacenamiento o elimina la cuenta de almacenamiento, Microsoft no puede recuperar esos datos por usted.
> 

## <a name="types-of-disks"></a>Tipos de discos 

Los discos de Azure están diseñados para ofrecer una disponibilidad del 99,999 %. Los discos de Azure ofrecen durabilidad de nivel empresarial, con una tasa de error anualizada del 0 %.

Hay dos niveles de rendimiento para el almacenamiento que puede elegir al crear los discos: almacenamiento estándar y Premium Storage. Además, hay dos tipos de discos, no administrados y administrados, y pueden residir en cualquier nivel de rendimiento.


### <a name="standard-storage"></a>Standard Storage 

El almacenamiento estándar está respaldado por unidades de disco duro y ofrece un almacenamiento rentable al mismo tiempo que tiene un rendimiento superior. El almacenamiento estándar se puede replicar de forma local en un centro de datos, o tener redundancia geográfica con centros de datos principales y secundarios. Para más información sobre la replicación almacenamiento, consulte [Replicación de Azure Storage](../articles/storage/common/storage-redundancy.md). 

Para más información sobre el uso del almacenamiento estándar con discos de máquina virtual, consulte el artículo sobre [Almacenamiento estándar y discos](../articles/storage/common/storage-standard-storage.md).

### <a name="premium-storage"></a>Premium Storage 

Premium Storage está respaldado por discos SSD y ofrece compatibilidad con discos de alto rendimiento y baja latencia para máquinas virtuales con cargas de trabajo intensivas de E/S. Puede usar Premium Storage con máquinas virtuales de Azure de las series DS, DSv2, GS, Ls o FS. Para más información, consulte [Premium Storage](../articles/storage/common/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Discos no administrados

Los discos no administrados constituyen el tipo tradicional de discos que han usado las máquinas virtuales. Con esto, cree su propia cuenta de almacenamiento y especifique esa cuenta de almacenamiento al crear el disco. Tiene que asegurarse de que no incluir demasiados discos en la misma cuenta de almacenamiento, porque podría superar los [objetivos de escalabilidad](../articles/storage/common/storage-scalability-targets.md) de la cuenta de almacenamiento (20 000 IOPS, por ejemplo), lo que da como resultado una limitación de las máquinas virtuales. Con los discos no administrados, tendrá que averiguar cómo maximizar el uso de una o varias cuentas de almacenamiento para obtener el máximo rendimiento de las máquinas virtuales.

### <a name="managed-disks"></a>Discos administrados 

El servicio Managed Disks controla la creación y administración de las cuentas de almacenamiento en segundo plano y se asegura de que no tiene que preocuparse de los límites de escalabilidad de la cuenta de almacenamiento. Simplemente especifique el tamaño del disco y el nivel de rendimiento (Estándar o Premium) y Azure crea y administra el disco en su nombre. Incluso a medida que agrega discos o escala y reduce verticalmente la máquina virtual, no tendrá que preocuparse por el almacenamiento que se va a usar. 

También puede administrar sus imágenes personalizadas en una cuenta de almacenamiento por región de Azure y utilizarlas para crear cientos de máquinas virtuales en la misma suscripción. Para más información sobre Managed Disks, consulte [Información general de Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Se recomienda usar Azure Managed Disks para las nuevas máquinas virtuales y que convierta los discos no administrados anteriores en discos administrados, para aprovechar las múltiples características disponibles en Managed Disks.

### <a name="disk-comparison"></a>Comparación de discos

En la tabla siguiente se ofrece una comparación de almacenamiento premium y estándar para discos administrados y no administrados para ayudar a decidir cuál utilizar.

|    | Disco Premium de Azure | Disco Estándar de Azure |
|--- | ------------------ | ------------------- |
| Tipo de disco | Unidades de estado sólido (SSD) | Unidades de disco duro (HDD)  |
| Información general  | Compatibilidad con discos de alto rendimiento y baja latencia basados en SSD para máquinas virtuales que ejecutan cargas de trabajo intensivas de E/S o que hospedan un entorno de producción crítico | Compatibilidad rentable con discos basados en HHD para escenarios de máquinas virtuales de desarrollo o pruebas |
| Escenario  | Cargas de trabajo confidenciales de producción y rendimiento | Desarrollo y pruebas, no crítico, <br>Acceso infrecuente |
| Tamaño del disco | P4: 32 GB (solo Managed Disks)<br>P6: 64 GB (solo Managed Disks)<br>P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB<br>P40: 2048 GB<br>P50: 4095 GB | Discos no administrados: de 1 GB a 4 TB (4095 GB) <br><br>Discos administrados:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB <br>S40: 2048 GB<br>S50: 4095 GB| 
| Rendimiento máximo por disco | 250 MB/s | 60 MB/s | 
| IOPS máximas por disco | 7500 IOPS | 500 IOPS | 

