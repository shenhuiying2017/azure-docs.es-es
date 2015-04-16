<properties 
	pageTitle="Creación y carga de un VHD de Linux en Azure" 
	description="Aprenda a crear y cargar un disco duro virtual de Azure (VHD) que contiene el sistema operativo Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# Creación y carga de un disco duro virtual que contiene el sistema operativo Linux

En este artículo se muestra cómo puede crear y cargar un disco duro virtual (VHD) que podrá utilizar como imagen propia para crear máquinas virtuales en Azure. Aprenderá a preparar el sistema operativo que podrá utilizar para crear máquinas virtuales basadas en esa imagen.  

> [AZURE.NOTE] No es necesario que tenga experiencia con máquinas virtuales de Azure para completar los pasos de este artículo. Sin embargo, sí necesita una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Una máquina virtual en Azure ejecuta el sistema operativo basado en la imagen que elige cuando crea la máquina virtual. Las imágenes se almacenan en formato VHD en archivos .vhd en una cuenta de almacenamiento. Para obtener más información acerca de los discos y las imágenes en Azure, consulte [Administrar discos e imágenes](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

Al crear la máquina virtual, puede personalizar parte de la configuración del sistema operativo para adaptarla a la aplicación que desea ejecutar. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada](/es-es/manage/windows/how-to-guides/custom-create-a-vm/).

**Importante**: El contrato de nivel de servicio de la plataforma Azure se aplica a las máquinas virtuales que ejecutan el sistema operativo Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en [este artículo](http://support.microsoft.com/kb/2805216). Todas las distribuciones de Linux que se ofrezcan en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.


##Requisitos previos##
En este artículo se supone que tiene los siguientes elementos:

- **Un certificado de administración**: ha creado un certificado de administración para la suscripción para la que desea cargar un VHD, y ha exportado el certificado a un archivo .cer. Para obtener más información acerca de la creación de certificados, consulte [Crear y cargar un certificado de administración para Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx). 

- **Sistema operativo Linux instalado en un archivo .vhd**. Ha instalado un sistema operativo Linux compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalar el rol Hyper-V y configurar una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). 

	**Importante**: el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

	Para ver una lista de distribuciones aprobadas, consulte [Linux en distribuciones aprobadas por Azure](linux-endorsed-distributions.md). Como alternativa, vea la sección al final de este artículo para obtener [información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-vhd-generic.md).

- **Herramienta de línea de comandos de Azure para Linux**. Si usa el sistema operativo Linux para crear su imagen, utilizará las [Herramientas de línea de comando de Azure para Linux y Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409) para cargar el VHD.

- **Herramientas Powershell de Azure**. El cmdlet  `Add-AzureVhd` se puede utilizar también para cargar el VHD. Visite las [Descargas de Azure](http://azure.microsoft.com/downloads/) para descargar los cmdlets de Powershell de Azure. Para obtener más información, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).


Esta tarea incluye los siguientes pasos:

- [Paso 1: Preparar la imagen que se va a cargar] []
- [Paso 2: Crear una cuenta de almacenamiento en Azure] []
- [Paso 3: Preparar la conexión a Azure] []
- [Paso 4: Cargar la imagen en Azure] []

## <a id="prepimage"></a>Paso 1: Preparar la imagen que se va a cargar ##

Microsoft Azure admite varias distribuciones de Linux (consulte [Distribuciones aprobadas](linux-endorsed-distributions.md)). Los artículos siguientes le guiarán a través del proceso de preparación de las distintas distribuciones de Linux admitidas en Azure:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES y openSUSE](virtual-machines-linux-create-upload-vhd-suse.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Otros - Distribuciones no aprobadas](virtual-machines-linux-create-upload-vhd-generic.md)**

Consulte también las **[Notas de instalación de Linux](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)** para obtener sugerencias adicionales sobre la preparación de imágenes de Linux para Azure.

Tras seguir los pasos de las guías enumeradas anteriormente debería contar con un archivo VHD listo para cargarse en Azure.


## <a id="createstorage"></a>Paso 2: Crear una cuenta de almacenamiento en Azure ##

Una cuenta de almacenamiento representa el más alto nivel del espacio de nombres para el acceso a los servicios de almacenamiento, y está asociada con la suscripción de Azure. Necesitará una cuenta de almacenamiento en Azure para cargar en Azure el archivo .vhd que se va a utilizar para crear una máquina virtual. Puede crear una cuenta de almacenamiento en el Portal de administración de Azure.

1. Inicie sesión en el Portal de administración de Azure.

2. En la barra de comandos, haga clic en **Nuevo**.

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. Haga clic en **Cuenta de almacenamiento** y, luego, en **Creación rápida**.

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. Rellene los campos de la manera siguiente:

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.
	
- Elija la ubicación o el grupo de afinidad para la cuenta de almacenamiento. Al especificar un grupo de afinidad, puede colocar sus servicios en la nube del mismo centro de datos con su almacenamiento.
 
- Decida si va a utilizar la replicación geográfica para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste para usted, de modo que si se produce un error principal que no se pueda gestionar en la ubicación primaria, su almacenamiento conmuta por error a una ubicación secundaria. La ubicación secundaria se asigna automáticamente y no se puede cambiar. En caso de que se necesite un control más estricto sobre la ubicación de su almacenamiento basado en la nube por exigencias legales o de la política organizativa, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento.

5. Haga clic en **Crear cuenta de almacenamiento**.

	La cuenta aparece ahora en **Cuentas de almacenamiento**.

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>Paso 3: Preparar la conexión a Azure ##

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure. 

1. Abra una ventana de Azure PowerShell.

2. Tipo: 

	`Get-AzurePublishSettingsFile`

	Este comando abre una ventana de explorador y descarga automáticamente un archivo .publishsettings que contiene información y un certificado para su suscripción de Azure. 

3. Guarde el archivo .publishsettings. 

4. Tipo:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Donde `<PathToFile>` es la ruta de acceso completa al archivo .publishsettings. 

	Para obtener información, consulte [Introducción a los cmdlets de Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx). 


## <a id="upload"> </a>Paso 4: Cargar la imagen en Azure ##

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar sus imágenes. **VHDName** es la etiqueta que aparece en el Portal de administración para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd. 

Realice una de las operaciones siguientes:

- Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Para obtener más información, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx).

- Utilice la herramienta de línea de comandos de Linux para cargar la imagen. Puede cargar una imagen mediante el siguiente comando:

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[Paso 1: Preparar la imagen que se va a cargar]: #prepimage
[Paso 2: Crear una cuenta de almacenamiento en Azure]: #createstorage
[Paso 3: Preparar la conexión a Azure]: #connect
[Paso 4: Cargar la imagen en Azure]: #upload





<!--HONumber=42-->
