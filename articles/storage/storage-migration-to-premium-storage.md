<properties 
    pageTitle="Migrar a Almacenamiento premium de Azure | Microsoft Azure" 
    description="Migre a Almacenamiento premium de Azure para tener compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un alto consumo de E/S que se ejecutan en Máquinas virtuales de Azure." 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2015" 
    ms.author="tamram"/>


# Migrar a Almacenamiento premium de Azure

## Información general

El Almacenamiento premium de Azure almacena datos en unidades de estado sólido (SSD) de última tecnología y admite el uso de discos de alto rendimiento y baja latencia para cargas de trabajo con un alto consumo de E/S que se ejecutan en Máquinas virtuales de Azure. Con el Almacenamiento premium, las aplicaciones pueden tener hasta 32 TB de almacenamiento por máquina virtual y logran 50.000 IOPS (operaciones de entrada/salida por segundo) por máquina virtual con latencias extremadamente bajas para operaciones de lectura. Este artículo sirve de guía para migrar sus discos y máquinas virtuales (VM) desde un almacenamiento estándar o local, o bien desde una plataforma de nube diferente, al Almacenamiento premium de Azure. Para ver una descripción pormenorizada de la oferta de Almacenamiento premium de Azure, vea [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage-preview-portal.md).

## Antes de empezar 

### Requisitos previos
- Necesitará una suscripción de Azure. Si no tiene una, puede crear una suscripción de [prueba gratuita](http://azure.microsoft.com/pricing/free-trial/) de un mes o visitar la página [Precios de Azure](http://azure.microsoft.com/pricing/) para conocer más opciones. 
- Para ejecutar los cmdlets de PowerShell, necesitará un módulo de PowerShell de Microsoft Azure. Vaya a [Descargas de Microsoft Azure](http://azure.microsoft.com/downloads/) para descargar el módulo.
- Si tiene previsto ejecutar máquinas virtuales de Azure en Almacenamiento premium, deberá usar las máquinas virtuales de la serie DS. Con la serie DS de máquinas virtuales se pueden usar discos de almacenamiento tanto estándar como premium. Pronto habrá disponibles discos de Almacenamiento premium con más tipos de VM. Para más información sobre todos los tamaños y tipos de disco de máquina virtual de Azure disponibles, vea [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

### Consideraciones 

#### Tamaños de VM 
Aquí condensamos los tamaños de máquina virtual de la serie DS y sus características. Repase las características de rendimiento de estas ofertas de Almacenamiento premium y elija la opción para sus discos y VM de Azure que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

|Tamaño de VM|Núcleos de CPU|Máx. E/S|Máx. Ancho de banda de disco|
|:---:|:---:|:---:|:---:|
|**STANDARD\_DS1**|1|3\.200|32 MB por segundo|
|**STANDARD\_DS2**|2|6\.400|64 MB por segundo|
|**STANDARD\_DS3**|4|12\.800|128 MB por segundo|
|**STANDARD\_DS4**|8|25\.600|256 MB por segundo|
|**STANDARD\_DS11**|2|6\.400|64 MB por segundo|
|**STANDARD\_DS12**|4|12\.800|128 MB por segundo|
|**STANDARD\_DS13**|8|25\.600|256 MB por segundo|
|**STANDARD\_DS14**|16|50\.000|512 MB por segundo|

#### Tamaños de disco 
Hay tres tipos de discos que se pueden usar con las máquinas virtuales, cada uno de ellos con sus límites particulares de rendimiento y E/S por segundo. Tenga presentes estos límites a la hora de elegir el tipo de disco para la máquina virtual según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

|Tipo de disco de Almacenamiento premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Tamaño del disco|128 GB|512 GB|1\.024 GB (1 TB)|
|IOPS por disco|500|2300|5000|
|Rendimiento de disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

#### Objetivos de escalabilidad de la cuenta de almacenamiento

Aparte de los [objetivos de escalabilidad y rendimiento del almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx), las cuentas de Almacenamiento premium tienen estos otros objetivos de escalabilidad. Si los requisitos de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento.

|Capacidad total de la cuenta|Ancho de banda total de una cuenta de almacenamiento con redundancia local|
|:--|:---|
|Capacidad de disco: 35 TB<br />Capacidad de instantánea: 10 TB|Hasta 50 gigabits por segundo de entrada y salida|

Para más información sobre las especificaciones del Almacenamiento premium, vea [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whes-esing-premium-storage).

#### Discos de datos adicionales 
Dependiendo de la carga de trabajo, decida si son necesarios más discos de datos para la máquina virtual. Puede conectar varios discos de datos persistentes a la máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen. Si secciona discos de datos Almacenamiento premium usando [Espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), será necesario configurarlo con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. En las máquinas virtuales de Linux esto se logra con la utilidad *mdadm*. Vea el artículo [Configuración del software RAID en Linux](../virtual-machines-linux-configure-raid.md) para más información.

#### Directiva de almacenamiento en caché de disco 
De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación. La configuración de caché de los discos de datos existentes se puede actualizar con el Portal de Azure o con el parámetro *- HostCaching* del cmdlet *Set-AzureDataDisk*.

#### Ubicación 
Elija una ubicación donde el Almacenamiento premium de Azure esté disponible. Vea [Cosas importantes acerca del Almacenamiento premium](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage) para obtener información actualizada sobre las ubicaciones disponibles. El rendimiento de las máquinas virtuales será superior si están en la misma región que la cuenta de almacenamiento donde se almacenan los discos de máquina virtual, y no en regiones separadas.

#### Otras opciones de configuración de máquina virtual de Azure 

Al crear una máquina virtual de Azure, hay que establecer ciertas configuraciones de máquina virtual. Recuerde que hay algunas configuraciones que son fijas durante la vigencia de la máquina virtual, además de que puede modificar o agregar otras más adelante. Revise estos valores de configuración de máquina virtual de Azure y asegúrese de que están configurados correctamente para que coincidan con sus requisitos de carga de trabajo. Vea [Valores de configuración de VM](https://msdn.microsoft.com/library/azure/dn763935.aspx) para más información.

## Preparar los discos duros virtuales para la migración 

En la siguiente sección encontrará las instrucciones necesarias para preparar los discos duros virtuales de la máquina virtual de forma que estén preparados para la migración. El disco duro virtual puede ser:
 
- Una imagen de sistema operativo generalizada que se puede usar para crear varias máquinas virtuales de Azure.  
- Un disco del sistema operativo que se puede usar con una sola instancia de máquina virtual de Azure.  
- Un disco de datos que se puede conectar a una máquina virtual de Azure con fines de almacenamiento persistente.

### Requisitos previos

Para migrar las máquinas virtuales, necesitará:

- Una suscripción de Azure, una cuenta de almacenamiento y un contenedor en dicha cuenta en el que copiar el disco duro virtual. Conviene saber que, según cuáles sean sus necesidades, la cuenta de almacenamiento de destino puede ser una cuenta de almacenamiento estándar o premium. 
- Una herramienta para generalizar el disco duro virtual, en caso de que tenga intención de crear varias instancias de máquina virtual a partir de dicho disco. Por ejemplo, sysprep para Windows o virt sysprep para Ubuntu. 
- Una herramienta para cargar el archivo VHD en la cuenta de almacenamiento. Por ejemplo, [AzCopy](storage-use-azcopy.md) o el [explorador de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Esta guía describe el procedimiento de copiar su VDN con la herramienta AzCopy. 

> [AZURE.NOTE]Para obtener un rendimiento óptimo, copie el disco duro virtual ejecutando una de estas herramientas desde una máquina virtual de Azure que está en la misma región que la cuenta de almacenamiento de destino. Si está copiando un VHD de una máquina virtual de Azure en una región distinta, el rendimiento puede ser más lento.
>
> En caso de copiar grandes cantidades de datos sobre un ancho de banda limitado, piense en recurrir al [servicio de importación y exportación de Microsoft Azure](storage-import-export-service.md) para transferir los datos enviando las unidades de disco duro a un centro de datos de Azure. Puede usar el servicio de importación y exportación de Azure para copiar los datos solo en una cuenta de almacenamiento estándar. Una vez que los datos estén en la cuenta de almacenamiento estándar, usará la [API de tipo Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o AzCopy para transferirlos a la cuenta de almacenamiento premium.
> 
> Tenga en cuenta que Microsoft Azure solo admite archivos VHD de tamaño fijo. No se admiten archivos VHDX ni discos duros virtuales dinámicos. Si tiene un disco duro virtual dinámico, puede convertirlo a tamaño fijo con el cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Escenarios para preparar los discos duros virtuales

A continuación se describen algunos escenarios para preparar los discos duros virtuales.

#### Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM

Si está cargando un disco duro virtual que se va usar para crear varias instancias de máquina virtual de Azure genéricas, antes habrá que generalizarlo usando una utilidad sysprep. Esto es válido para discos duro virtuales tanto locales como en la nube. Sysprep quita cualquier información específica del equipo del disco duro virtual.

>[AZURE.IMPORTANT]Realice una instantánea o copia de seguridad de la máquina virtual antes de generalizarla. Al ejecutar sysprep se eliminará la instancia de máquina virtual. Siga los pasos que se muestran a continuación para un sysprep de un disco duro virtual del sistema operativo Windows. Tenga en cuenta que la ejecución del comando Sysprep requerirá que apague la máquina virtual. Para más información sobre Sysprep, vea [Introducción a Sysprep](http://technet.microsoft.com/library/hh825209.aspx) o [Referencia técnica de Sysprep](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx).

1. Abra una ventana de símbolo del sistema como administrador.
2. Escriba el siguiente comando para abrir Sysprep:
 
		%windir%\system32\sysprep\sysprep.exe

4. En la herramienta de preparación del sistema, seleccione Entrar en System Out-of-Box Experience (OOBE), active la casilla Generalizar, seleccione **Apagado**, y, a continuación y haga clic en **Aceptar**, como se muestra en la imagen siguiente. Esto generalizará el sistema operativo y apagará el sistema.

	![][1]

Para una máquina virtual de Ubuntu, use virt-sysprep para lograr lo mismo. Vea [sysprep virt](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para más información. Vea también algunos de los [software de aprovisionamiento del servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) de código abierto correspondientes a otros sistemas operativos Linux.

#### Disco duro virtual de sistema operativo único para crear una sola instancia de VM

Si una aplicación que se ejecuta en la máquina virtual requiere los datos específicos de la máquina, no generalice el disco duro virtual. Un disco duro virtual no generalizado se puede usar para crear una única instancia de la máquina virtual de Azure. Por ejemplo, si tiene el controlador de dominio en el disco duro virtual, ejecutar sysprep lo anulará como controlador de dominio. Antes de generalizar el disco duro virtual, revise las aplicaciones que se ejecutan en la máquina virtual y el impacto que sysprep puede tener en ellos.

#### Discos duros virtuales de discos de datos que se van a conectar a instancias de máquina virtual

Si tiene discos de datos en un almacenamiento en la nube y va a migrarlos, asegúrese de que las máquinas virtuales que usan esos discos de datos estén apagadas. En el caso de los discos de datos locales, cree un disco duro virtual coherente.

## Copiar discos duros virtuales en Almacenamiento de Azure

Ya tenemos listo el disco duro virtual, así que vamos a realizar los pasos descritos a continuación para cargar el disco duro virtual en Almacenamiento de Azure y registrarlo como una imagen de sistema operativo, como un disco de sistema de operativo aprovisionado o como un disco de datos aprovisionado.

### Crear el destino para el disco duro virtual

Cree una cuenta de almacenamiento para mantener los discos duros virtuales. Tenga en cuenta los siguientes puntos cuando planee dónde almacenar los discos duros virtuales.

- La cuenta de almacenamiento de destino puede ser un almacenamiento estándar o premium, según cuáles sean los requisitos de la aplicación. 
- La ubicación de la cuenta de almacenamiento debe ser la misma que la de las VM de Azure de la serie DS que crearemos en el paso final. Puede copiar en una nueva cuenta de almacenamiento o decidir usar la misma cuenta de almacenamiento, según sus necesidades.
- Copie y guarde la clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino para el siguiente paso.
- En relación con los discos de datos, puede optar por mantener algunos en una cuenta de almacenamiento estándar (por ejemplo, los discos que disponen de almacenamiento más frío) y mover discos con una gran cantidad de E/S por segundo a una cuenta de almacenamiento premium.

### Copiar el disco duro virtual desde el origen

A continuación se describen algunos escenarios para copiar el disco duro virtual.

#### Copiar el disco duro virtual desde el Almacenamiento de Azure

Si va a migrar un disco duro virtual desde una cuenta de almacenamiento estándar de Azure y una cuenta de Almacenamiento premium de Azure, copie la ruta de acceso de origen y el nombre del contenedor de disco duro virtual y la clave de la cuenta de almacenamiento de origen.

1. Vaya a **Portal de Azure > Máquinas virtuales > Discos**.
2. Copie y guarde la dirección URL del contenedor de los discos duros virtuales de la columna de la ubicación. La URL del contenedor será similar a `https://myaccount.blob.core.windows.net/mycontainer/`.

#### Copiar el disco duro virtual desde una nube que no es de Azure

Si va a migrar un disco duro virtual desde un almacenamiento en la nube que no es de Azure a Azure, exporte antes el disco duro virtual a un directorio local. Copie la ruta de acceso de origen completa del directorio local donde está el disco duro virtual.

1. Si usa AWS, exporte la instancia de EC2 a un disco duro virtual en un depósito de Amazon S3. Siga los pasos descritos en la documentación de Azure para [exportación de instancias de Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) para instalar la herramienta de interfaz de línea de comandos (CLI) de Amazon EC2 y ejecutar el comando para exportar la instancia de EC2 a un archivo VHD. 
	
	Procure usar **VHD** para la variable DISK&#95;IMAGE&#95;FORMAT cuando ejecute el comando. El archivo VHD exportado se guarda en el depósito de Amazon S3 que haya especificado durante el proceso.

	![][2]

2. Descargue el archivo VHD desde el depósito de S3. Seleccione el archivo VHD > **Acciones** > **Descargar**.

	![][3]|

#### Copiar un disco duro virtual desde un entorno local

Si va a migrar un disco duro virtual desde un entorno local, necesitará la ruta de acceso de origen completa en la que se almacena el disco duro virtual. Puede ser una ubicación de servidor o un recurso compartido de archivo.

### Copiar un disco duro virtual con AzCopy

Con AzCopy, puede cargar el disco duro virtual fácilmente a través de Internet. Dependiendo del tamaño de los discos duros virtuales, esto puede tardar tiempo. Recuerde comprobar los límites de entrada/salida de la cuenta de almacenamiento cuando use esta opción. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](storage-scalability-targets.md) para obtener detalles.

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)  
2. Abra PowerShell de Azure y vaya a la carpeta donde AzCopy está instalado.  
3. Use el siguiente comando para copiar el archivo VHD de "Origen" a "Destino". 

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>  

	Estas son las descripciones de los parámetros utilizados en el comando AzCopy:

 - **/Source: *&lt;source&gt;:*** ubicación de la carpeta o dirección URL del contenedor de almacenamiento que contiene el disco duro virtual.    
 - **/SourceKey: *&lt;source-account-key&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.  
 - **/Dest: *&lt;destino&gt;:*** dirección URL del contenedor de almacenamiento donde se va a copiar el disco duro virtual.
 - **/DestKey: *&lt;dest-account-key&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
 - **/BlobType: page:** indica que el destino es un blob en páginas.
 - **/Pattern: *&lt;file-name&gt;:*** escriba el nombre de archivo del disco duro virtual que va a copiar.
   
Para más información sobre el uso de la herramienta AzCopy, vea [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md).

### Otras opciones para cargar un disco duro virtual 

También puede cargar un VHD a la cuenta de almacenamiento mediante uno de los siguientes medios:

- [API de tipo Copy Blob de Almacenamiento Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Servicio de importación y exportación de Azure](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]El servicio de importación y exportación solamente se puede usar para copiar en una cuenta de almacenamiento estándar. Para copiar desde el almacenamiento estándar a una cuenta de Almacenamiento premium, use una herramienta como AzCopy.

## Crear máquinas virtuales de Azure mediante Almacenamiento Premium

Tras cargar el disco duro virtual en la cuenta de almacenamiento de su elección, siga las instrucciones de esta sección para registrar el disco duro virtual como una imagen del sistema operativo o como un disco del sistema operativo, según el escenario y, tras ello, cree una instancia de máquina virtual a partir de este. El disco duro virtual de disco de datos se puede conectar a la máquina virtual una vez creado.

### Registrar el disco duro virtual
  
Para crear una máquina virtual a partir de un disco duro virtual de sistema operativo o conectar un disco de datos a una máquina virtual nueva, antes hay que registrarlos. Siga estos pasos, dependiendo de sus circunstancias.

#### Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure

Después cargar el disco duro virtual de imagen de sistema operativo generalizado en la cuenta de almacenamiento, regístrelo como una **imagen de máquina virtual de Azure** para poder crear una o más instancias de VM a partir de él. Use los siguientes cmdlets de PowerShell para registrar el disco duro virtual como una imagen de sistema operativo de máquina virtual de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

	Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copie y guarde el nombre de esta nueva imagen de máquina virtual de Azure. En el ejemplo anterior, es *OSImageName*.

#### Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure

Después de cargar el disco duro virtual de sistema operativo único en la cuenta de almacenamiento, regístrelo como un **disco de sistema operativo de Azure** para poder crear una instancia de VM a partir de él. Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de sistema operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

	Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copie y guarde el nombre de este nuevo disco de sistema operativo de Azure. En el ejemplo anterior, es *OSDisk*.

#### Disco duro virtual de disco de datos que se va a conectar a nuevas instancias de máquina virtual de Azure

Después de cargar el disco duro virtual de disco de datos en la cuenta de almacenamiento, regístrelo como un disco de datos de Azure para poder conectarlo a la nueva instancia de máquina virtual de Azure de la serie DS.

Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de datos operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copie y guarde el nombre de este nuevo disco de datos de Azure. En el ejemplo anterior, es *DataDisk*.

### Crear una máquina virtual de la serie DS de Azure  

Tras registrar la imagen de sistema operativo o disco de sistema operativo, cree una instancia de máquina virtual de Azure de la serie DS. Para ello, usaremos el nombre de la imagen de sistema operativo o del disco de sistema operativo que haya registrado. Seleccione el tipo de máquina virtual en el nivel de Almacenamiento premium. En el siguiente ejemplo, usamos el tamaño de máquina virtual *Standard\_DS2*.

>[AZURE.NOTE]Actualice el tamaño del disco para procurar que coincida con sus requisitos de capacidad y rendimiento y los tamaños de disco de Azure disponibles.

Siga los siguientes cmdlets de PowerShell detallados para crear la nueva máquina virtual. En primer lugar, establezca los parámetros comunes:

	$serviceName = "yourVM"
	$location = "location-name" (e.g., West US) 
	$vmSize ="Standard_DS2"
	$adminUser = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$vmSize = "Standard_DS2"

En primer lugar, cree un servicio en la nube en el que hospedar las máquinas virtuales nuevas.

	New-AzureService -ServiceName $serviceName -Location $location

A continuación, cree la instancia de máquina virtual de Azure a partir de la imagen de sistema operativo o del disco de sistema operativo que haya registrado, según cuál sea su caso.

#### Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure

Cree una o varias instancias de máquina virtual de Azure de la serie DS usando la **imagen de sistema operativo de Azure** registrada. Especifique este nombre de imagen de SO en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.

	$OSImage = Get-AzureVMImage –ImageName "OSImageName"	

	$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

	Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm  

	New-AzureVM -ServiceName $serviceName -VM $vm	

#### Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure

Cree una instancia de máquina virtual de Azure de la serie DS usando el **disco de sistema operativo de Azure** registrado. Especifique este nombre de disco de sistema operativo en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.

	$OSDisk = Get-AzureDisk –DiskName "OSDisk"  
	
	$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName
	
	New-AzureVM -ServiceName $serviceName –VM $vm

Especifique más datos sobre la máquina virtual de Azure, como un servicio en la nube, la región, la cuenta de almacenamiento, el conjunto de disponibilidad y la directiva de almacenamiento en caché. La instancia de máquina virtual debe colocarse con discos de datos o de sistema operativo asociados, por lo que los datos seleccionados de servicio en la nube, región y cuenta de almacenamiento deben estar en la misma ubicación que los discos duros virtuales subyacentes de esos discos.

### Conectar un disco de datos  

Por último, si tiene registrados discos duros virtuales de discos de datos, conéctelos a la nueva máquina virtual de la serie DS.

Use los siguientes cmdlets de PowerShell para conectar un disco de datos a la nueva máquina virtual y especificar la directiva de almacenamiento en caché. En el siguiente ejemplo, la directiva de almacenamiento en caché se establece en como de *ReadOnly*.

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm  

	Update-AzureVM  -VM $vm

>[AZURE.NOTE]Puede que en esta guía no se contemplen otros pasos adicionales necesarios para admitir sus aplicaciones.


## Pasos siguientes  

Vea los siguientes recursos para conocer otros escenarios específicos de migración de máquinas virtuales.

- [Migrar máquinas virtuales de Azure entre cuentas de almacenamiento](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Crear y cargar un VHD de Windows Server a Azure](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](../virtual-machines-linux-create-upload-vhd.md)  
- [Migrar máquinas virtuales de Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

Vea también los siguientes recursos para obtener más información sobre Almacenamiento de Azure y Máquinas virtuales de Azure:

- [Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)   
- [Máquinas virtuales de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage-preview-portal.md)  

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=August15_HO6-->