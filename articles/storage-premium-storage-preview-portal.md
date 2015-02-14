<properties 
	pageTitle="Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure" 
	description="Información sobre el uso del Almacenamiento Premium de Azure para discos Aprenda a crear una cuenta de Almacenamiento Premium." 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure

Bienvenido a la vista previa pública de **Almacenamiento premium de Azure para discos**

Con la introducción del nuevo Almacenamiento premium, Microsoft Azure ahora ofrece dos tipos de almacenamiento duradero: **Almacenamiento premium** y **Almacenamiento estándar**. Almacenamiento premium almacena datos en unidades de estado sólido (SSD) de última tecnología, mientras que el Almacenamiento estándar almacena los datos en unidades de disco duro (HDD). 

Almacenamiento premium ofrece soporte de disco de alto rendimiento y latencia baja para cargas de trabajo intensivas de E/S que se ejecutan en máquinas virtuales de Azure. Puede adjuntar varios discos de Almacenamiento premium a una máquina virtual (VM). Con el Almacenamiento premium, las aplicaciones pueden tener hasta 32 TB de almacenamiento por máquina virtual y logran 50.000 IOPS (operaciones de entrada/salida por segundo) por máquina virtual con latencias extremadamente bajas para operaciones de lectura. Actualmente, Almacenamiento premium solo está disponible para almacenar datos en discos usados por máquinas virtuales de Azure. 

Para obtener la vista previa de Almacenamiento premium de Azure, visite la página [Vista previa de Azure](http://azure.microsoft.com/services/preview/).

Este artículo proporciona información general detallada del Almacenamiento premium de Azure.

**Tabla de contenido:**


* [Cosas importantes acerca del Almacenamiento premium](#important)

* [Usar Almacenamiento premium para discos](#using)

* [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scale)	

* [Limitación al usar Almacenamiento premium](#throttling)	

* [Instantáneas y copia de blob al Almacenamiento premium](#restapi)	

* [Precios y facturación al usar Almacenamiento premium](#pricing)	

* [Crear y usar la cuenta de Almacenamiento premium para discos](#howto1)	

* [Recursos adicionales](#see)

###<a id="important">Cosas importantes acerca del Almacenamiento premium</a>


La siguiente es una lista de las cosas importantes que deben tenerse en cuenta antes o al usar Almacenamiento premium:

- Para usar Almacenamiento premium, debes tener una cuenta de Almacenamiento premium. Para obtener información sobre cómo crear una cuenta de Almacenamiento premium, consulte [Crear y usar la cuenta de Almacenamiento premium para discos](#howto1).

- El Almacenamiento Premium está disponible actualmente en el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/) y es accesible a través de las siguientes bibliotecas de SDK: [API de REST de almacenamiento](http://msdn.microsoft.com//library/azure/dd179355.aspx) versión 14-02-2014 o posterior; [API de REST de administración de servicios](http://msdn.microsoft.com/library/azure/ee460799.aspx) versión 01-10-2014 o posterior; y [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) versión 0.8.10 o posterior. 

- Almacenamiento premium está disponible para la vista previa limitada en las siguientes regiones: Oeste de EE.UU., Este de EE. UU. 2 y Europa occidental.

- Almacenamiento premium admite solo blobs en páginas de Azure, que se usan para contener discos persistentes para máquinas virtuales de Azure (VM). Para obtener información acerca de los blobs en página de Azure, consulte [Introducción a los blobs en bloques y a los blobs en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx). Almacenamiento premium no admite Blobs en bloque de Azure, archivos de Azure, tablas de Azure ni colas de Azure.

- Una cuenta de Almacenamiento premium es almacenamiento con redundancia local (LRS) y mantiene tres copias de los datos en una única región.  Para consideraciones relativas a la replicación geográfica al usar Almacenamiento premium, consulte la sección [Instantáneas y copia de blob al Almacenamiento premium](#restapi) en este artículo.

- Si quiere usar una cuenta de Almacenamiento premium para los discos de máquinas virtuales, necesitará usar la serie DS de máquinas virtuales. Puede usar discos de Almacenamiento estándar y premium con la serie DS de máquinas virtuales. Pero no puede usar discos de Almacenamiento premium con series que no sean DS de máquinas virtuales. Para obtener información sobre los tamaños y tipos de disco de máquina virtual de Azure disponibles, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

- El proceso para configurar los discos de Almacenamiento Premium para una máquina virtual es similar al de los discos de Almacenamiento Estándar. Deberá elegir la opción de Almacenamiento premium más adecuada para sus discos y máquina virtual de Azure. El tamaño de la máquina virtual debe ser el adecuado para su carga de trabajo en función de las características de rendimiento de las ofertas Premium. Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scale).
 
- Una cuenta de almacenamiento premium no se puede asignar a un nombre de dominio personalizado.

- El análisis de almacenamiento no se admite actualmente para el Almacenamiento premium.

###<a id="using">Uso de Almacenamiento Premium para discos</a>
Puede usar Almacenamiento premium para discos de una de las siguientes maneras:

- Crear una nueva cuenta de almacenamiento premium y usarla al crear la máquina virtual.
- Crear una nueva máquina virtual de serie DS. Al crear la máquina virtual, puede seleccionar una cuenta de Almacenamiento premium creada anteriormente, crear una nueva o dejar que el Portal de Azure cree una cuenta premium de manera predeterminada.

Azure usa la cuenta de almacenamiento como un contenedor para el sistema operativo y los discos de datos. En otras palabras, si crea una máquina virtual de Azure serie DS y selecciona una cuenta de Almacenamiento premium de Azure, el sistema operativo y los discos de datos se almacenan en dicha cuenta de almacenamiento.

Para aprovechar las ventajas del Almacenamiento Premium, cree primero una cuenta de Almacenamiento Premium mediante un tipo de cuenta *Premium_LRS*. Para ello, puede usar el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/), [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) o la [API de REST de administración del servicio](http://msdn.microsoft.com/library/azure/ee460799.aspx). Para obtener instrucciones detalladas, consulte [Crear y usar la cuenta de Almacenamiento premium para discos](#howto1).

<h4>Notas importantes:</h4>

- Para obtener información sobre los límites de capacidad y de ancho de banda de la vista previa de las cuentas de Almacenamiento premium, consulte la sección [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scale) . Si las necesidades de su aplicación superan los objetivos de escalabilidad de una sola cuenta de almacenamiento, compile la aplicación para que use varias cuentas de almacenamiento y divida los datos entre esas cuentas de almacenamiento. Por ejemplo, si quiere adjuntar discos de 51 TB a diferentes máquinas virtuales, distribúyalos entre dos cuentas de almacenamiento ya que 32 TB es el límite de una sola cuenta de Almacenamiento premium.
- De forma predeterminada, la directiva de almacenamiento en caché de los discos es "Solo lectura" para todos los discos de datos Premium y "Lectura y Escritura" para el disco de sistema operativo Premium conectado a la máquina virtual. Se recomienda esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.
- Asegúrese de que hay suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco. Para conocer el ancho de banda de disco disponible para cada tamaño de máquina virtual, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).
- Puede usar discos de Almacenamiento premium y estándar en la misma máquina virtual serie DS.
- Con el Almacenamiento Premium, puede aprovisionar una máquina virtual de serie DS y conectar varios discos de datos persistentes a una máquina virtual. Si es necesario, puede crear bandas en los discos para aumentar la capacidad y el rendimiento del volumen. Si bandas con los discos de datos Premium almacenamiento [espacios de almacenamiento](http://technet.microsoft.com/library/hh831739.aspx), debe configurar con una columna para cada disco que se utiliza. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a la distribución desigual de tráfico entre los discos. De forma predeterminada, la interfaz de usuario (IU) del administrador del servidor permite configurar columnas de hasta 8 discos. Pero si tiene más de 8 discos, necesitará usar PowerShell para crear el volumen y especificar manualmente el número de columnas. De lo contrario, la IU del administrador del servidor sigue usando 8 columnas aunque tenga más discos. Por ejemplo, si tiene 32 discos en un conjunto de bandas único, debe especificar 32 columnas. Puede usar el parámetro *NumberOfColumns* del cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) de PowerShell para especificar el número de columnas que usa el disco virtual. Para obtener más información, consulte [Storage Spaces Overview](http://technet.microsoft.com/library/jj822938.aspx) (Introducción a espacios de almacenamiento) y [Storage Spaces Frequently Asked Questions](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx) (Preguntas más frecuentes sobre espacios de almacenamiento).


###<a id="scale">Objetivos de escalabilidad y rendimiento al usar Almacenamiento Premium</a>
Cuando aprovisiona un disco con una cuenta de Almacenamiento premium, la cantidad de operaciones de E/S por segundo (IOPS) y el rendimiento (ancho de banda) que se pueden obtener depende del tamaño del disco. Actualmente, hay tres tipos de discos de Almacenamiento premium: P10, P20 y P30. Cada uno tiene límites de IOPS y de rendimiento específicos, tal como se especifica en la tabla siguiente:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Tipo de disco de Almacenamiento Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Tamaño de disco</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS por disco</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>Rendimiento por disco</strong></td>
	<td>100 MB por segundo</td>
	<td>150 MB por segundo</td>
	<td>200 MB por segundo</td>
</tr>
</tbody>
</table>

Azure asigna el tamaño de disco (redondeado) a la opción de disco de Almacenamiento premium más cercana según se especifica en la tabla. Por ejemplo, un disco de 100 GB se clasifica como una opción P10, puede realizar hasta 500 unidades de E/S por segundo y dispone de un rendimiento de hasta 100 MB por segundo. De forma similar, un disco de 400 GB se clasifica como una opción P20, puede realizar hasta 2300 unidades de E/S por segundo y dispone de un rendimiento de hasta 150 MB por segundo.

El tamaño de la unidad de entrada y salida (E/S) es de 256 KB. Si el tamaño de los datos transferidos es inferior a 256 KB, se considera una sola unidad de E/S. Los tamaños de E/S más grandes se cuentan como varias operaciones de E/S con un tamaño de 256 KB. Por ejemplo, una E/S de 1100 KB se cuenta como cinco unidades de E/S.

El límite de rendimiento incluye las lecturas y las escrituras en el disco. Por ejemplo, la suma de las lecturas y de las escrituras debe ser inferior o igual a 100 MB por segundo para un disco P10.  De forma similar, por ejemplo, un único disco P10 puede tener 100 MB por segundo de lecturas o 100 MB por segundo de escrituras, o 60 MB por segundo de lecturas con 40 MB de escrituras por segundo.
 
Al crear los discos de Azure, seleccione la oferta de disco de Almacenamiento premium más adecuada según las necesidades de su aplicación en cuanto a capacidad, rendimiento, escalabilidad y picos de carga.

En la tabla siguiente se describen los objetivos de escalabilidad para las cuentas de Almacenamiento premium:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacidad total de la cuenta</strong></td>
	<td><strong>Ancho de banda total de una cuenta de almacenamiento con redundancia local</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacidad de disco: 32 TB</li>
       <li type=round>Capacidad de instantánea: 10 TB</li>
    </ul>
	</td>
	<td>Hasta 50 gigabits por segundo de entrada y salida</td>
</tr>
</tbody>
</table>

- Entrada se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento.
- Salida se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure](http://msdn.microsoft.com/library/dn249410.aspx).


###<a id="throttling">Limitación al usar Almacenamiento Premium</a>
Es posible que vea la limitación si IOPS o el rendimiento de su aplicación supera los límites asignados para un disco de Almacenamiento premium. Para evitar la limitación, recomendamos que limite el número de solicitudes de E/S pendientes para discos basándose en los objetivos de rendimiento y escalabilidad para el disco que se ha aprovisionado.  

La aplicación puede lograr la menor latencia cuando se ha diseñado para evitar la limitación. Por otro lado, si el número de solicitudes de E/S pendientes del disco es demasiado pequeño, la aplicación no puede aprovechar al máximo los niveles de rendimiento y IOPS que están disponibles en el disco.

En los ejemplos siguientes se muestra cómo calcular los niveles de limitación:

<h4>Ejemplo 1:</h4>
La aplicación hizo 495 E/S de tamaño de disco de 16 KB (que es igual a 495 unidades de E/S) en un segundo en un disco P10. Si intenta una E/S de 2 MB en el mismo segundo, el total de unidades de E/S es igual a 495 + 8. Esto es porque una E/S de 2 MB resulta en 2048 KB / 256 KB = 8 unidades de E/S cuando el tamaño de la unidad de E/S es de 256 KB. Puesto que la suma de 495 + 8 supera el límite IOPS de 500 para el disco, se produce la limitación. 

<h4>Nota:</h4> 
Todos los cálculos se basan en el tamaño de una unidad de E/S de 256 KB.

<h4>Ejemplo 2:</h4>
La aplicación hizo 400 E/S de tamaño de disco de 256 KB en un disco P10. El ancho de banda total consumido es (400 * 256) / 1024 = 100 MB/s. Observe que el límite de rendimiento de un disco P10 es de 100 MB por segundo. Si la aplicación intenta realizar más operaciones de E/S en ese segundo, se limita porque supera el límite asignado.

<h4>Nota:</h4>
Si el tráfico de disco consta principalmente de tamaños de E/S pequeños, es muy probable que la aplicación alcance el límite de IOPS antes que el límite de rendimiento. Por otro lado, si el tráfico de disco consiste principalmente en tamaños grandes de E/S, es muy probable que la aplicación alcance el límite de rendimiento en lugar del límite de IOPS. Puede aumentar la tasa de IOPS de su aplicación y la capacidad de rendimiento mediante el uso de tamaños de E/S óptimos y también mediante la limitación del número de solicitudes de E/S pendientes de un disco.


###<a id="restapi">Instantáneas y copias de blobs al usar Almacenamiento Premium</a>
Puede crear una instantánea para el Almacenamiento premium de la misma que crea una instantánea al usar Almacenamiento estándar. Dado que el Almacenamiento premium es redundante localmente, se recomienda crear instantáneas y, luego, copiarlas en una cuenta de almacenamiento estándar con redundancia geográfica. Para obtener más información, consulte [Opciones de redundancia de Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Si un disco está conectado a una máquina virtual, no se permiten determinadas operaciones de API en el blob en página que respalda el disco. Por ejemplo, no puede realizar una operación [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) en ese blob mientras el disco esté conectado a una máquina virtual. En su lugar, cree una instantánea del blob mediante el método de API de REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) y, luego, realice la operación [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) de la instantánea para copiar el disco conectado. Como alternativa, puede desasociar el disco y, luego, realizar las operaciones necesarias en el blob subyacente.

<h4>Notas importantes:</h4>

- Si la operación de blob de copia en un Almacenamiento premium sobrescribe un blob existente en el destino, el blob sobrescrito no deberá tener instantáneas.  Una copia en o entre cuentas de almacenamiento premium requiere que el blob de destino no tenga instantáneas al iniciar la copia.
- El número de instantáneas de un único blob está limitado a 100. Una instantánea puede tomarse cada 10 minutos como máximo.
- 10 TB es la capacidad máxima para las instantáneas por cuenta de Almacenamiento premium. Tenga en cuenta que la capacidad de instantánea es el único dato que existe en las instantáneas. En otras palabras, la capacidad de instantánea no incluye el tamaño del blob base.
- Puede copiar instantáneas desde una cuenta de almacenamiento premium a una cuenta de almacenamiento estándar con redundancia geográfica con AzCopy o Copy Blob. Para obtener más información, consulte [Uso de AzCopy con Almacenamiento de Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) y [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Para obtener más información acerca de cómo realizar operaciones REST en blobs en página en las cuentas de Almacenamiento premium, consulte [Uso de operaciones de servicios blob con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969) en MSDN library.


###<a id="pricing">Precios y facturación al usar Almacenamiento Premium</a>
Al usar Almacenamiento premium, se aplican las siguientes consideraciones de facturación:

- La facturación de un disco de Almacenamiento premium depende del tamaño aprovisionado del disco. Azure asigna el tamaño de disco (redondeado) a la opción de disco de Almacenamiento premium más cercana según se especifica en la tabla que se proporciona en la sección [Objetivos de escalabilidad y rendimiento al usar Almacenamiento premium](#scale) . La facturación de cualquier disco aprovisionado se prorratea cada hora con el precio mensual de la oferta de Almacenamiento premium. Por ejemplo, si se aprovisiona un disco P10 y se elimina después de 20 horas, se factura la oferta P10 prorrateada a 20 horas. Esto es así independientemente de la cantidad de datos que se escriba en el disco o del rendimiento/IOPS usado.
- Las instantáneas en el Almacenamiento premium se facturan por la capacidad adicional usada por las instantáneas. Para obtener información sobre las instantáneas, consulte [Crear una instantánea de un blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- [Las transferencias de datos salientes](http://azure.microsoft.com/pricing/details/data-transfers/) (datos que salen de los centros de datos de Azure) incurren en la facturación por el uso de ancho de banda.

Para obtener información detallada sobre los precios de Almacenamiento premium y máquinas virtuales de la serie DS, consulte:

- [Precios de Almacenamiento de Azure](http://azure.microsoft.com/pricing/details/storage/)
- [Precios de máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">Creación y uso de una cuenta de Almacenamiento Premium para discos</a>
En esta sección se muestra cómo crear una cuenta de Almacenamiento premium mediante el Portal de vista previa de Azure y Azure PowerShell. Además, muestra un caso de uso de ejemplo para las cuentas de almacenamiento premium: crear una máquina virtual y adjuntar un disco de datos a una máquina virtual al usar Almacenamiento premium.

En esta sección:

* [Portal de vista previa de Azure: Crear una cuenta de Almacenamiento premium](#howto3)	

* [Azure PowerShell: Crear una cuenta de Almacenamiento premium y usarla para operaciones básicas de VM](#howto2)	
 
###<a id="howto3">Portal de vista previa de Azure: Crear una cuenta de Almacenamiento premium</a>
En esta sección se muestra cómo crear una cuenta de Almacenamiento premium mediante el Portal de vista previa de Azure.

1.	Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com/). Consulte la oferta [Evaluación gratuita](http://www.windowsazure.com/pricing/free-trial/) si todavía no tiene una suscripción. 


    >[AZURE.NOTE] Si inicia sesión en el Portal de administración de Azure, haga clic en el nombre de cuenta de usuario en la esquina superior derecha del portal. A continuación, haga clic en **Cambiar al nuevo portal**.
        

2.	En el menú Concentrador, haga clic en **Nuevo**.

3.	En **Nuevo**, haga clic en **Todo**. Seleccione **Almacenamiento, caché, + copia de seguridad**. Desde allí, haga clic en **Almacenamiento** y, luego, haga clic en **Crear**.

4.	En el cuadro Cuenta de almacenamiento, escriba un nombre para la cuenta de almacenamiento. Haga clic en **Nivel de precios**. En el cuadro **Niveles de precios recomendados**, haga clic en **Examinar todos los niveles de precios**. En el cuadro **Elegir nivel de precios**, elija **Redundancia local premium**. Haga clic en **Seleccionar**. Tenga en cuenta que el cuadro **Cuenta de almacenamiento** muestra **GRS estándar** como **Nivel de precios** predeterminado. Tras hacer clic en **Seleccionar**, el **Nivel de precios** se muestra como **LRS premium**.
	
	![Pricing Tier][Image1]

	
5.	En el cuadro **Cuenta de almacenamiento**, mantenga los valores predeterminados de **Grupo de recursos**, **Suscripción**, **Ubicación** y **Diagnósticos**. Haga clic en **Crear**.

Para obtener un tutorial completo del entorno de Azure, consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de vista previa de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/).

###<a id="howto2">Azure PowerShell: Crear una cuenta de Almacenamiento premium y usarla para operaciones básicas de VM</a>
En esta sección se muestra cómo crear una cuenta de Almacenamiento premium y cómo usarla al crear una máquina virtual y adjuntar un disco de datos a una máquina virtual con Azure PowerShell.

1. Configure el entorno de PowerShell siguiendo los pasos que se indican en [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/).
2. Inicie la consola de PowerShell, conéctese a su suscripción y ejecute el cmdlet siguiente de PowerShell en la ventana de la consola. Tal como se muestra la instrucción de PowerShell, deberá especificar el parámetro **Type** como **Premium_LRS** al crear una cuenta de almacenamiento premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. A continuación, cree una nueva máquina virtual de la serie DS y especifique que quiere Almacenamiento premium mediante la ejecución de los siguientes cmdlets de PowerShell en la ventana de consola:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. Si quiere más espacio en disco para la máquina virtual, adjunte un nuevo disco de datos a una máquina virtual de la serie DS existente después de crearlo mediante la ejecución de los siguientes cmdlets de PowerShell en la ventana de consola:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">Recursos adicionales</a>
[Uso de operaciones de servicios blob con Almacenamiento premium de Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

[Creación de una máquina virtual que ejecuta Windows](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[Documentación de almacenamiento](http://azure.microsoft.com/documentation/services/storage/)

[Referencia de MSDN](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
<!--HONumber=42-->
