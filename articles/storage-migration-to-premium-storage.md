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
    ms.date="04/14/2015" 
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
|**STANDARD_DS1**|1|3.200|32 MB por segundo|
|**STANDARD_DS2**|2|6.400|64 MB por segundo|
|**STANDARD_DS3**|4|12.800|128 MB por segundo|
|**STANDARD_DS4**|8|25.600|256 MB por segundo|
|**STANDARD_DS11**|2|6.400|64 MB por segundo|
|**STANDARD_DS12**|4|12.800|128 MB por segundo|
|**STANDARD_DS13**|8|25.600|256 MB por segundo|
|**STANDARD_DS14**|16|50.000|512 MB por segundo|

#### Tamaños de disco 
Hay tres tipos de discos que se pueden usar con las máquinas virtuales, cada uno de ellos con sus límites particulares de rendimiento y E/S por segundo. Tenga presentes estos límites a la hora de elegir el tipo de disco para la máquina virtual según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

|Tipo de disco de Almacenamiento premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Tamaño del disco|128 GB|512 GB|1.024 GB (1 TB)|
|IOPS por disco|500|2300|5000|
|Rendimiento de disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

#### Objetivos de escalabilidad de la cuenta de almacenamiento

Aparte de los [objetivos de escalabilidad y rendimiento del almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx), las cuentas de Almacenamiento premium tienen estos otros objetivos de escalabilidad. Si los requisitos de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento.

|Capacidad total de la cuenta|Ancho de banda total de una cuenta de almacenamiento con redundancia local|
|:--|:---|
|Capacidad de disco: 35 TB<br />Capacidad de instantánea: 10 TB|Hasta 50 gigabits por segundo de entrada y salida|

Para más información sobre las especificaciones del Almacenamiento premium, vea [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whes-esing-premium-storage).

#### Discos de datos adicionales 
Dependiendo de la carga de trabajo, decida si son necesarios más discos de datos para la máquina virtual. Puede conectar varios discos de datos persistentes a la máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen. Si secciona discos de datos Almacenamiento premium usando [Espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), será necesario configurarlo con una columna por cada disco que use. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado a causa de la distribución desigual de tráfico entre los discos. En las máquinas virtuales de Linux esto se logra con la utilidad mdadm. Vea el artículo [Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md) para más información.

#### Directiva de almacenamiento en caché de disco 
La directiva de almacenamiento en caché de los discos es de "Solo lectura" de forma predeterminada en todos los discos de datos premium y de "Lectura y escritura" en el disco de sistema operativo premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. En los discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación. La configuración de caché de los discos de datos existentes se puede actualizar con el Portal de Azure o con el parámetro *- HostCaching* del cmdlet *Set-AzureDataDisk*.

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

- Una suscripción de Azure, una cuenta de almacenamiento y un contenedor en dicha cuenta en el que copiar el disco duro virtual. Conviene saber que, según cuáles sean sus necesidades, la cuenta de almacenamiento de destino puede ser una cuenta de almacenamiento estándar o premium. 
- Una herramienta para generalizar el disco duro virtual, en caso de que tenga intención de crear varias instancias de máquina virtual a partir de dicho disco. Por ejemplo, sysprep para Windows o virt sysprep para Ubuntu. Una herramienta para cargar el archivo VHD en la cuenta de almacenamiento. Por ejemplo, [AzCopy](storage-use-azcopy.md) o el [explorador de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). En esta guía, el procedimiento descrito se realiza con la herramienta AzCopy. En caso de tener grandes cantidades de datos y un ancho de banda limitado, puede recurrir al [servicio de importación y exportación de Microsoft Azure](storage-import-export-service.md) para transferir los datos enviando las unidades de disco duro a un centro de datos de Azure. Con el servicio de importación y exportación, los datos solo se pueden copiar en una cuenta de almacenamiento estándar. Una vez que los datos estén en la cuenta de almacenamiento estándar, usará la [API de tipo Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o AzCopy para transferirlos a la cuenta de Almacenamiento premium.
- Una herramienta para copiar el disco duro virtual se puede ejecutar en una máquina virtual de Azure que esté en la misma región que los discos duros virtuales.
- Microsoft Azure solo admite archivos VHD de tamaño fijo. No se admiten archivos VHDX ni discos duros virtuales dinámicos. Si tiene un disco duro virtual dinámico, puede convertirlo a tamaño fijo con el cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Preparar los discos duros virtuales

|Escenario|Pasos|
|:---|:---|
|Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM|<p>Si está cargando un **disco duro virtual que se va usar para crear varias instancias de máquina virtual de Azure genéricas**, antes habrá que generalizarlo usando una utilidad sysprep. Esto es válido para discos duro virtuales tanto locales como en la nube. Sysprep quita cualquier información específica del equipo del disco duro virtual.</p><p>**Importante:** haga una instantánea o copia de seguridad de la máquina virtual antes de generalizarla. Cuando sysprep se ejecuta, se elimina la instancia de VM.</p>Siga los pasos aquí descritos para usar sysprep en un disco duro virtual del sistema operativo Windows:<br />Recuerde que, para ejecutar el comando Sysprep, hay que apagar la máquina virtual. Para más información sobre Sysprep, vea [Introducción a Sysprep](http://technet.microsoft.com/library/hh825209.aspx) o [Referencia técnica de Sysprep]http://technet.microsoft.com/library/cc766049(v=ws.10).aspx). <ul><li>Abra una ventana del símbolo del sistema como administrador.</li><li>Escriba el siguiente comando para abrir Sysprep:<br />**%windir%\\system32\\sysprep\\sysprep.exe**</li><li>En la Herramienta de preparación del sistema, seleccione Iniciar la configuración rápida (OOBE) del sistema, active la casilla Generalizar, seleccione **Apagar** y, luego, haga clic en **Aceptar**.</li><li>Esto hará que el sistema operativo se generalice y que el sistema se apague.![][1]</li></ul>En el caso de una VM de Ubuntu, use sysprep virt para lograr lo mismo. Vea [sysprep virt](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para más información. Vea también algunos de los [software de aprovisionamiento del servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) de código abierto correspondientes a otros sistemas operativos Linux.|
|Disco duro virtual de sistema operativo único para crear una sola instancia de VM|Si una aplicación que se ejecuta en la máquina virtual requiere los datos específicos de la máquina, no generalice el disco duro virtual. **Un disco duro virtual no generalizado se puede usar para crear una única instancia de la máquina virtual de Azure.** Por ejemplo, si tiene el controlador de dominio en el disco duro virtual, ejecutar sysprep lo anulará como controlador de dominio. Antes de generalizar el disco duro virtual, revise las aplicaciones que se ejecutan en la máquina virtual y el impacto que sysprep puede tener en ellos.|
|Discos duros virtuales de discos de datos que se van a conectar a instancias de máquina virtual|Si tiene discos de datos en un almacenamiento en la nube y va a migrarlos, asegúrese de que las máquinas virtuales que usan esos discos de datos estén apagadas. En el caso de los discos de datos locales, cree un disco duro virtual coherente.|
Ya tenemos listo el disco duro virtual, así que vamos a realizar los pasos descritos en la siguiente sección para cargar el disco duro virtual en Almacenamiento de Azure y registrarlo como una imagen de sistema operativo, como un disco de sistema de operativo aprovisionado o como un disco de datos aprovisionado.

## Copiar discos duros virtuales en Almacenamiento de Azure

### Origen

|Escenario|Pasos|
|:---|:---|
|Copiar el disco duro virtual desde Almacenamiento de Azure|Si va a migrar un disco duro virtual entre una cuenta de almacenamiento estándar de Azure y una cuenta de Almacenamiento premium de Azure, copie la ruta de acceso de origen y el nombre del contenedor de disco duro virtual y la clave de la cuenta de almacenamiento de origen.<ul><li>Vaya a Portal de Azure > Máquinas virtuales > Discos</li><li>Copie y guarde la dirección URL del contenedor del disco duro virtual de la columna de ubicación, https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|Copiar el disco duro virtual desde una nube que no es de Azure|Si va a migrar un disco duro virtual desde un almacenamiento en la nube que no es de Azure a Azure, exporte antes el disco duro virtual a un directorio local. Copie la ruta de acceso de origen completa del directorio local donde está el disco duro virtual. <ul><li>Si usa AWS, exporte la instancia de EC2 a un disco duro virtual en un depósito de Amazon S3. Siga los pasos descritos en el tema sobre [exportación de instancias de Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) para instalar la herramienta de interfaz de línea de comandos (CLI) de Amazon EC2 y ejecutar el comando para exportar la instancia de EC2 a un archivo VHD. Procure usar **VHD** para la variable DISK_IMAGE_FORMAT cuando ejecute el comando. El archivo VHD exportado se guarda en el depósito de Amazon S3 que haya especificado durante el proceso.</li></ul>![][2]<ul><li>Descargue el archivo VHD del depósito de S3. Seleccione el archivo VHD > **Acciones** > **Descargar**.</li></ul>![][3]|
|Copiar un disco duro virtual de un entorno local|Si va a migrar un disco duro virtual desde un entorno local, necesitará la ruta de acceso de origen completa en la que se almacena el disco duro virtual. Puede ser una ubicación de servidor o un recurso compartido de archivo.

### Destino 

Cree una cuenta de almacenamiento para mantener los discos duros virtuales. Tenga en cuenta los siguientes puntos cuando planee dónde almacenar los discos duros virtuales.

- La cuenta de almacenamiento de destino puede ser un almacenamiento estándar o premium, según cuáles sean los requisitos de la aplicación. 
- La ubicación de la cuenta de almacenamiento debe ser la misma que la de las VM de Azure de la serie DS que crearemos en el paso final. Puede copiar en una nueva cuenta de almacenamiento o decidir usar la misma cuenta de almacenamiento, según sus necesidades.
- Copie y guarde la clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino para el siguiente paso.
- En relación con los discos de datos, puede optar por mantener algunos en una cuenta de almacenamiento estándar (por ejemplo, los discos que disponen de almacenamiento más frío) y almacenar otros con una gran cantidad de E/S por segundo en la cuenta de Almacenamiento premium.

### Copiar un disco duro virtual con AzCopy

Con AzCopy, puede cargar el disco duro virtual fácilmente a través de Internet. Dependiendo del tamaño de los discos duros virtuales, esto puede tardar tiempo. Recuerde comprobar los límites de entrada/salida de la cuenta de almacenamiento cuando use esta opción. [Aquí](azure-subscription-service-limits#storage-limits) encontrará los límites de almacenamiento de Azure.

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)  
2. Abra PowerShell de Azure y vaya a la carpeta donde AzCopy está instalado.  
3. Use el siguiente comando para copiar el archivo VHD de “Origen” a “Destino”. **AzCopy /Source:** *&lt;Origen&gt;* **/SourceKey:** *&lt;Clave de almacenamiento de origen&gt;* **/Destination:** *&lt;Destino&gt;* **/DestKey:** *&lt;Clave de almacenamiento de origen&gt;* **/BlobType:page /Pattern:** *&lt;Nombre de archivo&gt;*  
  - *&lt;Origen&gt;:* ubicación de la carpeta o dirección URL del contenedor de almacenamiento que contiene el disco duro virtual.    
 - *&lt;Clave de almacenamiento de origen&gt;:* clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.  
 - *&lt;Destino&gt;:* dirección URL del contenedor de almacenamiento donde se va a copiar el disco duro virtual.
 - *&lt;Clave de almacenamiento de destino&gt;:* clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
 - /BlobType:page: indica que el destino es un blob de página.
 - *&lt;Nombre de archivo&gt;*: nombre del archivo de disco duro virtual que quiere copiar.
 - /Pattern:*&lt;Nombre de archivo&gt;:* escriba el nombre de archivo del disco duro virtual que va a copiar.
   
Con este comando se copiarán todos los archivos del *&lt;Origen&gt;* en el contenedor de *&lt;Destino&gt;*. Para más información sobre el uso de la herramienta AzCopy, vea [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md).
### Otras opciones para cargar un disco duro virtual 

- [API de tipo Copy Blob de Almacenamiento Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Servicio de importación y exportación de Azure](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]El servicio de importación y exportación solamente se puede usar para copiar en una cuenta de almacenamiento estándar. Para copiar desde el almacenamiento estándar a una cuenta de Almacenamiento premium, use una herramienta como AzCopy.

## Crear máquinas virtuales de Azure mediante Almacenamiento Premium

Tras cargar el disco duro virtual en la cuenta de almacenamiento de su elección, siga las instrucciones de esta sección para registrar el disco duro virtual como una imagen del sistema operativo o como un disco del sistema operativo, según el escenario y, tras ello, cree una instancia de máquina virtual a partir de este. El disco duro virtual de disco de datos se puede conectar a la máquina virtual una vez creado.

### Registrar el disco duro virtual
  
Para crear una máquina virtual a partir de un disco duro virtual de sistema operativo o conectar el disco duro virtual de datos a una máquina virtual nueva, antes hay que registrarlos. Siga estos pasos, dependiendo de sus circunstancias.

<table>
<tr>
<th>Escenario</th>
<th>Pasos para registrar el disco duro virtual</th>
</tr>
<tr>
<td>Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure</td>
<td><p>Después cargar el disco duro virtual de imagen de sistema operativo generalizado en la cuenta de almacenamiento, regístrelo como una <b>imagen de máquina virtual de Azure</b> para poder crear una o más instancias de VM a partir de él. </p><p>Use los siguientes cmdlets de PowerShell para registrar el disco duro virtual como una imagen de sistema operativo de máquina virtual de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation “https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd” -OS Windows</code></p>Copie y guarde el nombre de esta nueva imagen de máquina virtual de Azure. En el ejemplo anterior, es “OSImageName”.</td>
</tr>
</tr>
<td>Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure</td>
<td><p>Después de cargar el disco duro virtual de sistema operativo único en la cuenta de almacenamiento, regístrelo como un <b>disco de sistema operativo de Azure</b> para poder crear una instancia de VM a partir de él.</p><p>Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de sistema operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OS Disk" -OS "Windows"</code><p>Copie y guarde el nombre de este nuevo disco de sistema operativo de Azure. En el ejemplo anterior, es “OSDisk”.</p></td>
</tr>
</tr>
<td>Disco duro virtual de disco de datos que se va a conectar a nuevas instancias de máquina virtual de Azure</td>
<td><p>Después de cargar el disco duro virtual de disco de datos en la cuenta de almacenamiento, regístrelo como un disco de datos de Azure para poder conectarlo a la nueva instancia de máquina virtual de Azure de la serie DS.</p><p>Use estos cmdlets de PowerShell para registrar el disco duro virtual como un disco de datos operativo de Azure. Indique la dirección URL de contenedor completa donde copió el disco duro virtual.</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "Data Disk"</code><p>Copie y guarde el nombre de este nuevo disco de datos de Azure. En el ejemplo anterior, es “DataDisk”.</p></td>
</tr>
</table>

### Crear una máquina virtual de Azure de la serie DS  

Tras registrar la imagen de sistema operativo o disco de sistema operativo, se puede crear una instancia de máquina virtual de Azure de la serie DS. Para ello, usaremos el nombre de la imagen de sistema operativo o del disco de sistema operativo que haya registrado. Seleccione el tipo de máquina virtual en el nivel de Almacenamiento premium. En el siguiente ejemplo, usamos el tamaño de máquina virtual “Standard_DS2”.

>[AZURE.NOTE]Actualice el tamaño del disco para procurar que coincida con sus requisitos de capacidad y rendimiento y los tamaños de disco de Azure disponibles.

Siga los siguientes cmdlets de PowerShell detallados para crear la máquina virtual.

En primer lugar, establezca los parámetros comunes:

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

En segundo lugar, cree la instancia de máquina virtual de Azure a partir de la imagen de sistema operativo o del disco de sistema operativo que haya registrado, según cuál sea su caso.

<table>
<tr>
<th>Escenario</th>
<th>Pasos</th>
</tr>
<tr>
<td>Disco duro virtual de sistema operativo generalizado para crear varias instancias de VM de Azure</td>
<td>Cree una o varias instancias de máquina virtual de Azure de la serie DS usando la <b>imagen de sistema operativo de Azure</b> registrada. Especifique este nombre de imagen de SO en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName “OSImageName” $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code></td></tr>  
<tr>
<td>Disco duro virtual de sistema operativo único para crear una sola instancia de VM de Azure</td>
<td>Cree una instancia de máquina virtual de Azure de la serie DS usando el <b>disco de sistema operativo de Azure</b> registrado. Especifique este nombre de disco de sistema operativo en la configuración de la máquina virtual al crear la máquina virtual, como se muestra aquí.</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName “OSDisk” $vm = New-AzureVMConfig -Name “NewVM” -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code>
</td>
</tr>
</table>

Especifique más datos sobre la máquina virtual de Azure, como un servicio en la nube, la región, la cuenta de almacenamiento, el conjunto de disponibilidad y la directiva de almacenamiento en caché. La instancia de máquina virtual debe colocarse con discos de datos o de sistema operativo asociados, por lo que los datos seleccionados de servicio en la nube, región y cuenta de almacenamiento deben estar en la misma ubicación que los discos duros virtuales subyacentes de esos discos.

### Conectar un disco de datos  

Por último, si tiene registrados discos duros virtuales de discos de datos, conéctelos a la nueva máquina virtual de la serie DS.

Use los siguientes cmdlets de PowerShell para conectar un disco de datos a la nueva máquina virtual y especificar la directiva de almacenamiento en caché. En el siguiente ejemplo, la directiva de almacenamiento en caché se establece en como de solo lectura.

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]Puede que en esta guía no se contemplen otros pasos específicos necesarios para admitir sus aplicaciones.


## Pasos siguientes  

Eche un vistazo a los siguientes recursos para conocer otros escenarios específicos de migración de máquinas virtuales.

- [Migrar máquinas virtuales de Azure entre cuentas de almacenamiento](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Crear y cargar un VHD de Windows Server a Azure](virtual-machines-create-upload-vhd-windows-server.md)  
- [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-create-upload-vhd.md)  
- [Migrar máquinas virtuales de Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

Vea también estos recursos para más información sobre Almacenamiento de Azure y Máquinas virtuales de Azure:

- [Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)   
- [Máquinas virtuales de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
