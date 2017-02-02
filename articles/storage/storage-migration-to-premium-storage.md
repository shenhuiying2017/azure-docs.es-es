---
title: Migrar a Azure Premium Storage | Microsoft Docs
description: "Migre las máquinas virtuales existentes a Azure Premium Storage. Premium Storage le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en máquinas virtuales de Azure."
services: storage
documentationcenter: na
author: aungoo-msft
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: yuemlu
translationtype: Human Translation
ms.sourcegitcommit: 7611f7940b076ba18b3966b0bc9a63fe53b55592
ms.openlocfilehash: 0ebec265fe2ac2d53dbe3afcb660dddbe7b050ea


---
# <a name="migrating-to-azure-premium-storage"></a>Migrar a Azure Premium Storage
## <a name="overview"></a>Información general
Azure Premium Storage le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Puede aprovechar la velocidad y el rendimiento de estos discos si migra los discos de máquina virtual de la aplicación a Azure Premium Storage.

La finalidad de esta guía es ayudar a los nuevos usuarios de Azure Premium Storage a prepararse mejor para realizar una transición sin problemas desde su sistema actual a Premium Storage. La guía trata de tres de los componentes clave de este proceso:

* [Planeamiento de la migración a Premium Storage](#plan-the-migration-to-premium-storage)
* [Preparación y copia de discos duros virtuales (VHD) en Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Creación de una máquina virtual de Azure mediante Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Puede migrar máquinas virtuales de otras plataformas a Azure Premium Storage o migrar máquinas virtuales de Azure existentes de almacenamiento Estándar a Premium Storage. En esta guía se describen los pasos para ambos escenarios. Siga los pasos especificados en la sección correspondiente en función de su escenario.

> [!NOTE]
> Encontrará una introducción a las características y los precios de Premium Storage en [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](storage-premium-storage.md). Se recomienda migrar cualquier disco de máquina virtual que requiera un número elevado de operaciones de entrada/salida por segundo al Almacenamiento premium de Azure para mejorar el rendimiento de la aplicación. Si el disco no requiere un número elevado de operaciones de entrada/salida por segundo, puede limitar los costos mediante el mantenimiento del almacenamiento estándar, que almacena los datos de disco de máquina virtual en unidades de disco duro (HDD) en lugar de SSD.
>
>

Para completar el proceso de migración en su totalidad puede ser necesario realizar acciones adicionales antes y después de los pasos proporcionados en esta guía, Por ejemplo, configurar redes virtuales o puntos de conexión o realizar cambios en el código dentro de la propia aplicación que pueden requerir algún tiempo de inactividad de la aplicación. Estas acciones son únicas para cada aplicación y deben completarse junto con los pasos proporcionados en esta guía para realizar la transición completa a Premium Storage lo más fácilmente posible.

## <a name="a-nameplan-the-migration-to-premium-storageaplan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>Planeamiento de la migración a Premium Storage
Esta sección le garantiza que está preparado para seguir los pasos de migración de este artículo y le ayuda a tomar la mejor decisión sobre los tipos de disco y máquina virtual.

### <a name="prerequisites"></a>Requisitos previos
* Necesitará una suscripción de Azure. Si no tiene una, puede crear una suscripción de [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) de un mes o visitar la página [Precios de Azure](https://azure.microsoft.com/pricing/) para conocer más opciones.
* Para ejecutar los cmdlets de PowerShell, necesitará un módulo de Microsoft Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener instrucciones sobre el punto de instalación y la instalación.
* Si tiene previsto ejecutar máquinas virtuales de Azure en Premium Storage, deberá usar las máquinas virtuales de este tipo de almacenamiento. Puede usar discos de almacenamiento Estándar y Premium Storage con las máquinas virtuales compatibles con Premium Storage. Pronto habrá disponibles discos de Almacenamiento premium con más tipos de VM. Para obtener más información sobre todos los tamaños y tipos de disco de máquina virtual de Azure disponibles, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y [Tamaños de Cloud Services](../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Consideraciones
Una máquina virtual de Azure admite la conexión de varios discos de Premium Storage para que las aplicaciones puedan disponer de hasta 64 TB de almacenamiento por máquina virtual. Con el Almacenamiento premium, las aplicaciones pueden tener hasta 80.000 E/S por segundo (operaciones de entrada/salida por segundo) por máquina virtual, así como un rendimiento del disco de 2.000 MB por segundo por máquina virtual, con latencias extremadamente bajas para operaciones de lectura. Dispone de opciones en varias máquinas virtuales y discos. Esta sección es para ayudarle a encontrar la opción que mejor se adapte a su carga de trabajo.

#### <a name="vm-sizes"></a>Tamaños de VM
Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Repase las características de rendimiento de las máquinas virtuales que trabajan con Almacenamiento premium y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

#### <a name="disk-sizes"></a>Tamaños de disco
Hay tres tipos de discos que se pueden usar con las máquinas virtuales, cada uno de ellos con sus límites particulares de rendimiento y E/S por segundo. Tenga presentes estos límites a la hora de elegir el tipo de disco para la máquina virtual según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

| Tipo de disco de Almacenamiento premium | P10 | P20 | P30 |
|:---:|:---:|:---:|:---:|
| Tamaño del disco |128 GB |512 GB |1.024 GB (1 TB) |
| IOPS por disco |500 |2300 |5000 |
| Rendimiento de disco |100 MB por segundo |150 MB por segundo |200 MB por segundo |

Dependiendo de la carga de trabajo, decida si son necesarios más discos de datos para la máquina virtual. Puede conectar varios discos de datos persistentes a la máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen. (Consulte qué es el seccionamiento de discos [aquí](storage-premium-storage-performance.md#disk-striping)). Si secciona discos de datos de Premium Storage mediante [Espacios de almacenamiento][4], tendrá que configurarlos con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. En las máquinas virtuales de Linux, esto se logra con la utilidad *mdadm* . Vea el artículo [Configuración del software RAID en Linux](../virtual-machines/virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información.

#### <a name="storage-account-scalability-targets"></a>Objetivos de escalabilidad de la cuenta de almacenamiento
Aparte de los [objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md), las cuentas de Premium Storage tienen estos otros objetivos de escalabilidad. Si los requisitos de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento.

| Capacidad total de la cuenta | Ancho de banda total de una cuenta de almacenamiento con redundancia local |
|:--- |:--- |
| Capacidad de disco: 35 TB<br />Capacidad de instantánea: 10 TB |Hasta 50 gigabits por segundo de entrada y salida |

Para más información sobre las especificaciones del Almacenamiento premium, consulte [Objetivos de escalabilidad y rendimiento al usar el Almacenamiento premium](storage-premium-storage.md#premium-storage-scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Directiva de almacenamiento en caché de disco
De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación. La configuración de caché de los discos de datos existentes se puede actualizar con el [Azure Portal](https://portal.azure.com) o con el parámetro *-HostCaching* del cmdlet *Set-AzureDataDisk*.

#### <a name="location"></a>La ubicación
Elija una ubicación donde el Almacenamiento premium de Azure esté disponible. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para obtener información actualizada sobre las ubicaciones disponibles. El rendimiento de las máquinas virtuales será mucho mejor si están en la misma región que la cuenta de almacenamiento donde se almacenan los discos de máquina virtual, y no en regiones separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Otras opciones de configuración de máquina virtual de Azure
Al crear una máquina virtual de Azure, hay que establecer ciertas configuraciones de máquina virtual. Recuerde que hay algunas configuraciones que son fijas durante la vigencia de la máquina virtual, además de que puede modificar o agregar otras más adelante. Revise estos valores de configuración de máquina virtual de Azure y asegúrese de que están configurados correctamente para que coincidan con sus requisitos de carga de trabajo.

### <a name="optimization"></a>Optimización
En [Azure Premium Storage: diseño de alto rendimiento](storage-premium-storage-performance.md) se proporcionan instrucciones para crear aplicaciones de alto rendimiento con Azure Premium Storage. Puede seguir las instrucciones junto con los procedimientos recomendados de rendimiento aplicables a las tecnologías usadas en la aplicación.

## <a name="a-nameprepare-and-copy-virtual-hard-disks-vhds-to-premium-storageaprepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparación y copia de discos duros virtuales (VHD) en Premium Storage
En la siguiente sección se proporcionan instrucciones para preparar discos duros virtuales de la máquina virtual y copiarlos en Azure Storage.

* [Escenario 1: "Se realiza la migración de máquinas virtuales de Azure existentes a Azure Premium Storage".](#scenario1)
* [Escenario 2: "Se realiza la migración de máquinas virtuales de otras plataformas a Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Requisitos previos
Para preparar los VHD para la migración, necesitará:

* Una suscripción a Azure, una cuenta de almacenamiento y un contenedor en dicha cuenta en el que se pueda copiar el VHD. Conviene saber que, según cuáles sean sus necesidades, la cuenta de almacenamiento de destino puede ser una cuenta de almacenamiento estándar o premium.
* Una herramienta para generalizar el VHD si planea crear varias instancias de máquina virtual a partir de él. Por ejemplo, sysprep para Windows o virt sysprep para Ubuntu.
* Una herramienta para cargar el archivo VHD en la cuenta de almacenamiento. Consulte [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md) o use un [Explorador de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Esta guía describe el procedimiento de copiar su VDN con la herramienta AzCopy.

> [!NOTE]
> Si elige la opción de copia sincrónica con AzCopy, para obtener un rendimiento óptimo, copie el VHD mediante la ejecución de una de estas herramientas desde una máquina virtual de Azure que está en la misma región que la cuenta de almacenamiento de destino. Si está copiando un VHD de una máquina virtual de Azure en una región distinta, el rendimiento puede ser más lento.
>
> En caso de copiar grandes cantidades de datos sobre un ancho de banda limitado, piense en recurrir al [servicio Importación/Exportación de Azure para transferir los datos al Almacenamiento de blobs](storage-import-export-service.md); esto permite transferir los datos enviando las unidades de disco duro a un centro de datos de Azure. Puede usar el servicio de importación y exportación de Azure para copiar los datos solo en una cuenta de almacenamiento estándar. Una vez que los datos estén en la cuenta de almacenamiento estándar, puede usar la [API Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o AzCopy para transferirlos a la cuenta de almacenamiento premium.
>
> Tenga en cuenta que Microsoft Azure solo admite archivos VHD de tamaño fijo. No se admiten archivos VHDX ni discos duros virtuales dinámicos. Si tiene un disco duro virtual dinámico, puede convertirlo a tamaño fijo con el cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="a-namescenario1ascenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>Escenario 1: "Se realiza la migración de máquinas virtuales de Azure existentes a Azure Premium Storage".
Si va a migrar máquinas virtuales de Azure existentes, detenga la máquina virtual, prepare los VHD por el tipo de disco duro virtual que desee y cópielo con AzCopy o PowerShell.

La máquina virtual debe estar totalmente fuera de servicio para migrar a un estado limpio. Habrá un tiempo de inactividad hasta que se complete la migración.

#### <a name="step-1-prepare-vhds-for-migration"></a>Paso 1. Preparar los VHD para la migración
Si va a migrar máquinas virtuales de Azure existentes a Premium Storage, el VHD puede ser:

* Una imagen del sistema operativo generalizado
* Un disco del sistema operativo exclusivo
* Un disco de datos

A continuación se describen estos tres escenarios para preparar un disco duro virtual.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Uso de un VHD del sistema operativo generalizado para crear varias instancias de VM
Si está cargando un disco duro virtual que se va usar para crear varias instancias de máquina virtual de Azure genéricas, antes habrá que generalizarlo usando una utilidad sysprep. Esto es válido para discos duros virtuales tanto locales como en la nube. Sysprep quita del VHD cualquier información específica del equipo.

> [!IMPORTANT]
> Realice una instantánea o copia de seguridad de la máquina virtual antes de generalizarla. La ejecución de sysprep detendrá y desasignará la instancia de la máquina virtual. Siga los pasos que se muestran a continuación para un sysprep de un disco duro virtual del sistema operativo Windows. Tenga en cuenta que la ejecución del comando Sysprep requerirá que apague la máquina virtual. Para obtener más información sobre Sysprep, consulte [Introducción a Sysprep](http://technet.microsoft.com/library/hh825209.aspx) o [Referencia técnica de Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Abra una ventana de símbolo del sistema como administrador.
2. Escriba el siguiente comando para abrir Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. En la herramienta de preparación del sistema, seleccione Iniciar la Configuración rápida (OOBE) del sistema, active la casilla Generalizar, seleccione **Apagado** y, a continuación, haga clic en **Aceptar**, tal y como se muestra en la imagen siguiente. Sysprep generalizará el sistema operativo y apagará el sistema.

    ![][1]

Para una máquina virtual de Ubuntu, use virt-sysprep para lograr lo mismo. Vea [sysprep virt](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para más información. Vea también algunos de los [software de aprovisionamiento del servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) de código abierto correspondientes a otros sistemas operativos Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>uso de un VHD de sistema operativo único para crear una instancia de una máquina virtual individual
Si una aplicación que se ejecuta en la máquina virtual requiere los datos específicos de la máquina, no generalice el disco duro virtual. Un disco duro virtual no generalizado se puede usar para crear una única instancia de la máquina virtual de Azure. Por ejemplo, si tiene el controlador de dominio en el disco duro virtual, ejecutar sysprep lo anulará como controlador de dominio. Antes de generalizar el HD, revise las aplicaciones que se ejecutan en la máquina virtual y el impacto que sysprep puede tener en ellas.

##### <a name="register-data-disk-vhd"></a>Registro de VHD de disco de datos
Si tiene en Azure discos de datos para migrarlos, debe asegurarse de que las máquinas virtuales que usan dichos discos de datos están apagadas.

Siga los pasos que se describen a continuación para copiar un VHD a Azure Premium Storage y registrarlo como disco de datos aprovisionado.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Paso 2: Crear el destino para el disco duro virtual
Cree una cuenta de almacenamiento para mantener los discos duros virtuales. Tenga en cuenta los siguientes aspectos al planear dónde almacenar los discos duros virtuales:

* La cuenta de Premium Storage de destino.
* La ubicación de la cuenta de almacenamiento debe ser la misma que la de las máquinas virtuales de Azure compatibles con Premium Storage que se crearán en el paso final. Puede copiar en una nueva cuenta de almacenamiento o decidir usar la misma cuenta de almacenamiento, según sus necesidades.
* Copie y guarde la clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino para el siguiente paso.

En cuanto a los discos de datos, puede optar por mantener algunos en una cuenta de almacenamiento estándar (por ejemplo, los discos que menos se calienten), pero se recomienda encarecidamente mover todos los datos de la carga de trabajo de producción para usar Premium Storage.

#### <a name="a-namecopy-vhd-with-azcopy-or-powershellastep-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>Paso 3. Copiar un VHD con AzCopy o PowerShell
Para procesar cualquiera de estas dos opciones será preciso que busque la ruta de acceso del contenedor y la clave de la cuenta de almacenamiento. Tanto una como otra pueden encontrarse en **Azure Portal** > **Storage**. La dirección URL del contenedor será similar a esta: "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opción 1: Copiar un VHD con AzCopy (copia asincrónica)
Con AzCopy resulta muy fácil cargar el VHD a través de Internet. Dependiendo del tamaño de los discos duros virtuales, esto puede tardar tiempo. Recuerde comprobar los límites de entrada/salida de la cuenta de almacenamiento cuando use esta opción. Vea [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](storage-scalability-targets.md) para obtener detalles.

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)
2. Abra PowerShell de Azure y vaya a la carpeta donde AzCopy está instalado.
3. Use el siguiente comando para copiar el archivo VHD de "Origen" a "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Ejemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Estas son las descripciones de los parámetros utilizados en el comando AzCopy:

   * **/Source: *&lt;source&gt;:*** ubicación de la carpeta o dirección URL del contenedor de almacenamiento que contiene el disco duro virtual.
   * **/SourceKey: *&lt;source-account-key&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.
   * **/Dest: *&lt;destination&gt;:*** dirección URL del contenedor de almacenamiento donde se va a copiar el disco duro virtual.
   * **/Dest: *&lt;destination&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
   * **/Pattern: *&lt;file-name&gt;:*** escriba el nombre de archivo del disco duro virtual que va a copiar.

Para obtener más información sobre cómo usar la herramienta AzCopy, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opción 2: Copiar un VHD con PowerShell (copia sincronizada)
También puede copiar el archivo VHD con el cmdlet de PowerShell Start-AzureStorageBlobCopy. Use el siguiente comando de Azure PowerShell para copiar el VHD: Reemplace los valores de <> por los valores correspondientes de la cuenta de almacenamiento de origen y de destino. Para usar este comando, debe tener un contenedor llamado vhds en la cuenta de almacenamiento de destino. Si el contenedor no existe, créelo antes de ejecutar el comando.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Ejemplo:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="a-namescenario2ascenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>Escenario 2: "Se realiza la migración de máquinas virtuales de otras plataformas a Azure Premium Storage."
Si va a migrar un disco duro virtual desde un almacenamiento en la nube que no es de Azure a Azure, exporte antes el disco duro virtual a un directorio local. Se recomienda tener a mano la ruta de acceso de origen completa del directorio local en el que se almacena el VHD y utilizar AzCopy para cargarlo en Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Paso 1. Exportar el VHD a un directorio local
##### <a name="copy-a-vhd-from-aws"></a>Copia de un VHD desde AWS
1. Si usa AWS, exporte la instancia de EC2 a un disco duro virtual en un depósito de Amazon S3. Siga los pasos que se describen en la documentación de Azure para exportar instancias de Amazon EC2 para instalar la herramienta de interfaz de la línea de comandos (CLI) de Amazon EC2 y ejecute el comando create-instance-export-task para exportar la instancia de EC2 a un archivo VHD. Procure usar **VHD** para la variable DISK&#95;IMAGE&#95;FORMAT cuando ejecute el comando **create-instance-export-task**. El archivo VHD exportado se guarda en el depósito de Amazon S3 que haya especificado durante el proceso.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Descargue el archivo VHD desde el depósito de S3. Seleccione el archivo VHD y después **Acciones** > **Descargar**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copia un VHD de una nube que no es de Azure
Si va a migrar un disco duro virtual desde un almacenamiento en la nube que no es de Azure a Azure, exporte antes el disco duro virtual a un directorio local. Copie la ruta de acceso de origen completa del directorio local en el que se almacena el VHD.

##### <a name="copy-a-vhd-from-on-premise"></a>Copia de un VHD de un entorno local
Si va a migrar un VHD desde un entorno local, necesitará la ruta de acceso de origen completa en la que se almacena. Dicha ruta puede ser la ubicación de un servidor o un recurso compartido de archivos.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Paso 2: Crear el destino para el disco duro virtual
Cree una cuenta de almacenamiento para mantener los discos duros virtuales. Tenga en cuenta los siguientes aspectos al planear dónde almacenar los discos duros virtuales:

* La cuenta de almacenamiento de destino puede ser un almacenamiento estándar o premium, según cuáles sean los requisitos de la aplicación.
* La región de la cuenta de almacenamiento debe ser la misma que la de las máquinas virtuales de Azure compatibles con Premium Storage que se crearán en el paso final. Puede copiar en una nueva cuenta de almacenamiento o decidir usar la misma cuenta de almacenamiento, según sus necesidades.
* Copie y guarde la clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino para el siguiente paso.

Se recomienda encarecidamente mover todos los datos de la carga de trabajo de producción para usar Premium Storage.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Paso 3: Cargar el VHD en Azure Storage
Una vez que el VHD se encuentra en el directorio local, se puede utilizar AzCopy o Azure PowerShell para cargar el archivo .vhd en Azure Storage. A continuación se explican ambas opciones:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opción 1: Uso del cmdlet Add-AzureVhd de Azure PowerShell para cargar el archivo .vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Un ejemplo <Uri> podría ser ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Un ejemplo <FileInfo> podría ser ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opción 2: Uso de AzCopy para cargar el archivo .vhd
Con AzCopy resulta muy fácil cargar el VHD a través de Internet. Dependiendo del tamaño de los discos duros virtuales, esto puede tardar tiempo. Recuerde comprobar los límites de entrada/salida de la cuenta de almacenamiento cuando use esta opción. Vea [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](storage-scalability-targets.md) para obtener detalles.

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)
2. Abra PowerShell de Azure y vaya a la carpeta donde AzCopy está instalado.
3. Use el siguiente comando para copiar el archivo VHD de "Origen" a "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```
    
    Ejemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Estas son las descripciones de los parámetros utilizados en el comando AzCopy:

   * **/Source: *&lt;source&gt;:*** ubicación de la carpeta o dirección URL del contenedor de almacenamiento que contiene el disco duro virtual.
   * **/SourceKey: *&lt;source-account-key&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.
   * **/Dest: *&lt;destination&gt;:*** dirección URL del contenedor de almacenamiento donde se va a copiar el disco duro virtual.
   * **/Dest: *&lt;destination&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
   * **/BlobType: page:** indica que el destino es un blob en páginas.
   * **/Pattern: *&lt;file-name&gt;:*** escriba el nombre de archivo del disco duro virtual que va a copiar.

Para obtener más información sobre cómo usar la herramienta AzCopy, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Otras opciones para cargar un disco duro virtual
También puede cargar un VHD a la cuenta de almacenamiento mediante uno de los siguientes medios:

* [API de tipo Copy Blob de Almacenamiento Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobs de carga de Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
* [Referencia de la API de REST de servicios de importación y exportación de almacenamiento](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Se recomienda utilizar el servicio Import/Export si se calcula que el tiempo de carga va a ser superior a siete días. Puede usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para calcular el tiempo a partir del tamaño de los datos y la unidad de transferencia.
>
> Import/Export se puede usar para realizar copias en una cuenta de almacenamiento estándar. Para copiar desde el almacenamiento estándar a una cuenta de Almacenamiento premium, use una herramienta como AzCopy.
>
>

## <a name="a-namecreate-azure-virtual-machine-using-premium-storageacreate-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Creación de máquinas virtuales de Azure mediante Premium Storage
Una vez que el VHD se cargue o se copie en la cuenta de almacenamiento deseada, siga las instrucciones de esta sección para registrarlo como una imagen del sistema operativo o como un disco del sistema operativo, según cuál sea el escenario y, después, cree una instancia de la máquina virtual a partir de esta. El disco duro virtual de disco de datos se puede conectar a la máquina virtual una vez creado.
Al final de esta sección encontrará un script de migración de ejemplo. Dicho script no se ajusta a todos los escenarios. Puede que necesite actualizar el script para que coincida con su escenario específico. Para ver si se puede aplicar a su escenario, consulte la sección [Script de migración de ejemplo](#a-sample-migration-script), que encontrará más abajo.

### <a name="checklist"></a>Lista de comprobación
1. Espere hasta que se complete la copia de todos los discos VHD.
2. Asegúrese de que Almacenamiento premium está disponible en la región a la que se va a migrar.
3. Decida la nueva serie de máquinas virtuales que se va a usar. Debe ser compatible con Premium Storage y el tamaño dependerá tanto de la disponibilidad en la región como de sus necesidades.
4. Decida el tamaño exacto de máquina virtual que usará. El tamaño de la máquina virtual debe ser suficientemente grande para admitir el número de discos de datos que tiene. Por ejemplo, si tiene 4 discos de datos, la máquina virtual debe tener 2 o más núcleos. Considere también las necesidades de potencia de procesamiento, memoria y ancho de banda de red.
5. Cree una cuenta de Almacenamiento premium en la región de destino. Esta es la cuenta que se va a usar para la nueva máquina virtual.
6. Tenga a mano los detalles de la máquina virtual, incluida la lista de discos y los blobs de disco duro virtual correspondientes.

Prepare la aplicación para el tiempo de inactividad. Para realizar una migración limpia, tendrá que detener todo el procesamiento del sistema actual. Solo entonces se puede obtener un estado coherente que se puede migrar a la nueva plataforma. La duración del tiempo de inactividad dependerá de la cantidad de datos en los discos para migrar.

> [!NOTE]
> Si va a crear una máquina virtual de Azure Resource Manager desde un disco VHD especializado, consulte [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para implementar la máquina virtual de Resource Manager mediante el disco existente.
>
>

### <a name="register-your-vhd"></a>Registrar el disco duro virtual
Para crear una máquina virtual a partir de un VHD con el sistema operativo o para asociar un disco de datos a una máquina virtual nueva, antes hay que registrarla. Siga estos pasos, en función del escenario de su VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure
Después cargar el VHD con la imagen del sistema operativo generalizado en la cuenta de almacenamiento, regístrelo como una **imagen de máquina virtual de Azure** para poder crear una o varias instancias de la máquina virtual a partir de él. Use los siguientes cmdlets de PowerShell para registrar el disco duro virtual como una imagen de sistema operativo de máquina virtual de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copie y guarde el nombre de esta nueva imagen de máquina virtual de Azure. En el ejemplo anterior, es *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure
Después de cargar el VHD de sistema operativo único en la cuenta de almacenamiento, regístrelo como **disco de sistema operativo de Azure** para poder crear una instancia de la máquina virtual desde él. Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de sistema operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copie y guarde el nombre de este nuevo disco de sistema operativo de Azure. En el ejemplo anterior, es *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD de disco de datos que se va a asociar a las nuevas instancias de la máquina virtual de Azure
Después de cargar el disco duro virtual de disco de datos en la cuenta de almacenamiento, regístrelo como un disco de datos de Azure para poder conectarlo a la nueva instancia de máquina virtual de Azure de las series DS, DSv2 o GS.

Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de datos operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copie y guarde el nombre de este nuevo disco de datos de Azure. En el ejemplo anterior, es *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Creación de una máquina virtual compatible con Premium Storage
Tras registrar la imagen del sistema operativo o el disco del sistema operativo, cree una máquina virtual de las series DS, DSv2 o GS. Para ello, usaremos el nombre de la imagen de sistema operativo o del disco de sistema operativo que haya registrado. Seleccione el tipo de máquina virtual en el nivel de Almacenamiento premium. En el siguiente ejemplo, usamos el tamaño de VM *Standard_DS2*.

> [!NOTE]
> Actualice el tamaño del disco para asegurarse de que se ajusta a sus requisitos de capacidad y rendimiento, y a los tamaños de disco de Azure disponibles.
>
>

Siga los siguientes cmdlets de PowerShell detallados para crear la nueva máquina virtual. En primer lugar, establezca los parámetros comunes:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

En primer lugar, cree un servicio en la nube en el que hospedar las máquinas virtuales nuevas.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

A continuación, cree la instancia de máquina virtual de Azure a partir de la imagen de sistema operativo o del disco de sistema operativo que haya registrado, según cuál sea su caso.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure
Cree una o varias instancias de máquina virtual de Azure de la serie DS usando la **imagen de sistema operativo de Azure** registrada. Especifique este nombre de imagen de SO en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure
Cree una instancia de máquina virtual de Azure de la serie DS usando el **disco de sistema operativo de Azure** registrado. Especifique este nombre de disco de sistema operativo en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Especifique más datos sobre la máquina virtual de Azure, como un servicio en la nube, la región, la cuenta de almacenamiento, el conjunto de disponibilidad y la directiva de almacenamiento en caché. Tenga en cuenta que la instancia de la máquina virtual debe estar en la misma ubicación que el sistema operativo o los discos de datos asociados, por lo que el servicio en la nube, la región y la cuenta de almacenamiento seleccionados deben encontrarse en la misma ubicación que los discos duros virtuales subyacentes de dichos discos.

### <a name="attach-data-disk"></a>Acoplar disco de datos
Por último, si ha registrado discos duros virtuales de discos de datos, asócielos a la nueva máquina virtual de Azure compatible con Premium Storage.

Use los siguientes cmdlets de PowerShell para conectar un disco de datos a la nueva máquina virtual y especificar la directiva de almacenamiento en caché. En el siguiente ejemplo, la directiva de almacenamiento en caché se establece en *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Puede que en esta guía no se contemplen otros pasos necesarios para admitir su aplicación.
>
>

### <a name="checking-and-plan-backup"></a>Comprobación y planeamiento de la copia de seguridad
Una vez que la nueva máquina virtual está en funcionamiento, acceda a ella con el mismo identificador de inicio de sesión y contraseña que la máquina virtual original y compruebe que todo funciona según lo previsto. Toda la configuración, incluidos los volúmenes seccionados, debería estar presente en la nueva máquina virtual.

El último paso es planear la copia de seguridad y la programación del mantenimiento de la nueva máquina virtual en función de las necesidades de la aplicación.

### <a name="a-namea-sample-migration-scriptaa-sample-migration-script"></a><a name="a-sample-migration-script"></a>Un script de migración de ejemplo
Si tiene varias máquinas virtuales para migrar, la automatización mediante scripts de PowerShell le resultará útil. El siguiente es un script de ejemplo que automatiza la migración de una máquina virtual. Tenga en cuenta que el siguiente script es solo un ejemplo y que se han realizado pocas suposiciones acerca los discos de máquina virtual actuales. Puede que necesite actualizar el script para que coincida con su escenario específico.

Dichas suposiciones son:

* Va a crear máquinas virtuales de Azure clásico.
* Los discos de SO de origen y los discos de datos de origen están en la misma cuenta de almacenamiento y en el mismo contenedor. Si los discos de SO y los discos de datos no están en el mismo lugar, puede utilizar AzCopy o Azure PowerShell para copiar discos duros virtuales de unos contenedores y cuentas de almacenamiento a otros. Consulte el paso anterior: [Copiar un VHD con AzCopy o PowerShell](#copy-vhd-with-azcopy-or-powershell). Otra posibilidad es editar este script para que se ajuste al escenario, pero se recomienda utilizar AzCopy o PowerShell, ya que es más rápido y sencillo.

A continuación se proporciona el script de automatización. Reemplace el texto por su información y actualice el script para que se ajuste a su escenario concreto.

> [!NOTE]
> El uso del script existente no conserva la configuración de red de la máquina virtual de origen. Será preciso que vuelva a configurar las opciones de red en las máquinas virtuales migradas.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="a-nameoptimizationaoptimization"></a><a name="optimization"></a>Optimización
La configuración actual de la máquina virtual se puede personalizar específicamente para que funcione bien con discos estándar. Por ejemplo, para aumentar el rendimiento, puede usar muchos discos en un volumen seccionado. Por ejemplo, en lugar de usar cuatro discos independientemente en Premium Storage, puede tener un único disco para optimizar el costo. Las optimizaciones como esta deben tratarse individualmente y requieren pasos de personalización después de la migración. Tenga en cuenta también que es posible que no funcione para las bases de datos y las aplicaciones que dependan del diseño de discos definido en el programa de instalación.

##### <a name="preparation"></a>Preparación
1. Complete la migración simple, tal y como se describe en la sección anterior. Las optimizaciones se realizarán en la nueva máquina virtual después de la migración.
2. Define los nuevos tamaños de disco necesarios para la configuración optimizada.
3. Determine las asignaciones de los discos o volúmenes actuales a las especificaciones de los nuevos discos.

##### <a name="execution-steps"></a>Pasos de ejecución
1. Cree los discos nuevos con los tamaños correctos en la máquina virtual de Almacenamiento premium.
2. Inicie sesión en la máquina virtual y copie los datos del volumen actual en el nuevo disco asignado a ese volumen. Haga esto para todos los volúmenes actuales que se deben asignar a un nuevo disco.
3. A continuación, cambie la configuración de la aplicación para cambiar a los nuevos discos, y desasocie los volúmenes antiguos.

Para ajustar la aplicación para mejorar el rendimiento de disco, consulte [Optimización del rendimiento de las aplicaciones](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migraciones de aplicaciones
Las bases de datos y otras aplicaciones complejas pueden requerir pasos especiales tal y como defina el proveedor de la aplicación para la migración. Consulte la documentación de la aplicación correspondiente. Por ejemplo, las bases de datos normalmente se pueden migrar mediante copia de seguridad y restauración.

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes recursos para conocer otros escenarios específicos de migración de máquinas virtuales.

* [Migrar máquinas virtuales de Azure entre cuentas de almacenamiento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Crear y cargar un VHD de Windows Server a Azure](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrar máquinas virtuales de Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consulte también los siguientes recursos para más información sobre Azure Storage y Azure Virtual Machines:

* [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx



<!--HONumber=Dec16_HO3-->


