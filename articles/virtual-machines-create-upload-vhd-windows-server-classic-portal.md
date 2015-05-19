<properties 
	pageTitle="Creación y carga de un VHD de Windows Server en Azure" 
	description="Aprenda a crear y cargar un disco duro virtual \(VHD\) en Azure que tiene el sistema operativo Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn" 
	tags="PORTAL CLÁSICO DE AZURE"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="kathydav"/>


#Creación y carga de un VHD de Windows Server en el portal clásico de Azure#

En este artículo se muestra cómo puede cargar un disco duro virtual \(VHD\) con un sistema operativo que podrá utilizar como imagen para crear máquinas virtuales basadas en dicha imagen. Para obtener más información acerca de los discos y las imágenes en Microsoft Azure, consulte [Acerca de discos e imágenes en Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

> [AZURE.NOTE]Cuando se crea una máquina virtual basada en una imagen, puede personalizar la configuración del sistema operativo según corresponda para las aplicaciones que se van a ejecutar en la máquina virtual. Esta configuración se guarda para esa máquina virtual y no afecta a la imagen. Para obtener instrucciones, consulte [virtual-machines-windows-create-custom.md](virtual-machines-windows-create-custom.md).

##Requisitos previos##
En este artículo se supone que tiene lo siguiente:

1. **Suscripción de Azure** - Si no tiene una, puede [abrir una cuenta de Azure de forma gratuita](/pricing/free-trial/?WT.mc_id=A261C142F): obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá conservar la cuenta y usar los servicios gratuitos de Azure, como Sitios Web. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo. También puede [activar las ventajas de suscriptor de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): la suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.   

2. **Microsoft Azure PowerShell** - Tiene el módulo Microsoft Azure PowerShell instalado y configurado para usar su suscripción. Para descargar el módulo, consulte [Descargas de Microsoft Azure](http://azure.microsoft.com/downloads/). Está disponible un tutorial para instalar y configurar el módulo [aquí](install-configure-powershell.md). Usará el cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) para cargar el VHD.

3. **Un sistema operativo Windows compatible almacenado en un archivo .vhd** - Ha instalado un sistema operativo Windows Server compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Puede utilizar una solución de virtualización como Hyper-V para crear una máquina virtual e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE]El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Podrá encontrar un tutorial para ello [aquí](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Se admiten las siguientes versiones de Windows Server: <P> <TABLE BORDER="1" WIDTH="600"> <TR BGCOLOR="#E9E7E7"> <TH>SO</TH> <TH>SKU</TH> <TH>Service Pack</TH> <TH>Arquitectura</TH> </TR> <TR> <TD>Windows Server 2012 R2</TD> <TD>Todas las ediciones</TD> <TD>N/A</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2012</TD> <TD>Todas las ediciones</TD> <TD>N/A</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2008 R2</TD> <TD>Todas las ediciones</TD> <TD>SP1</TD> <TD>x64</TD> </TR> </TABLE> </P>


Esta tarea incluye los siguientes pasos:

- [Paso 1: Preparación de la imagen que se va a cargar][]
- [Paso 2: Creación de una cuenta de almacenamiento de Azure][]
- [Paso 3: Preparación de la conexión a Azure][]
- [Paso 4: Carga del archivo .vhd][]

## Paso 1: Preparación de la imagen que se va a cargar ##

Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando Sysprep. Para obtener más información acerca del uso de Sysprep, consulte [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Desde la máquina virtual en la que se instaló el sistema operativo, realice el procedimiento siguiente:

1. Inicie sesión en el sistema operativo.

2. Abra una ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.

	![Abrir una ventana de símbolo del sistema](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/sysprep_commandprompt.png)

3.	Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema**.

	![Iniciar Sysprep](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/sysprepgeneral.png)

4.  En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida \(OOBE\) del sistema** y asegúrese de que la casilla Generalizar esté seleccionada.

5.  En **Opciones de apagado**, seleccione **Apagar**.

6.  Haga clic en **Aceptar**.


## Paso 2: Creación de una cuenta de almacenamiento en Azure ##

Necesita una cuenta de almacenamiento de Azure para cargar un archivo .vhd, por lo que se puede usar en Azure para crear una máquina virtual. Puede utilizar el portal clásico de Azure para crear una cuenta de almacenamiento.

1. Inicie sesión en el portal clásico de Azure.

2. En la barra de comandos, haga clic en **Nuevo**.

3. Haga clic en **Servicios de datos** \> **Almacenamiento** \> **Creación rápida**.

	![Crear rápidamente una cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storage-quick-create.png)

4. Rellene los campos de la manera siguiente:
	
	- En **URL**, escriba un nombre de subdominio que vaya a usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.
			
	- Elija **la ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Un grupo de afinidad le permite colocar sus servicios y almacenamiento en la nube en el mismo centro de datos.
		 
	- Decida si va a utilizar la **replicación geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste, por lo que su almacenamiento conmuta por error a esa ubicación si se produce un error importante en la ubicación principal. La ubicación secundaria se asigna automáticamente y no se puede cambiar. Si necesita más control sobre la ubicación del almacenamiento en la nube debido a requisitos legales o las directivas de su organización, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Puede encontrar más detalles sobre la replicación geográfica de las cuentas de almacenamiento en: [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

	![Escribir los detalles de la cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storage-create-account.png)


5. Haga clic en **Crear cuenta de almacenamiento**. La cuenta aparece ahora en **Almacenamiento**.

	![Cuenta de almacenamiento creada correctamente](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storagenewaccount.png)

6. Después cree un contenedor para los VHD que ha cargado. Haga clic en el nombre de la cuenta de almacenamiento y, a continuación, en **Contenedores**.

	![Detalles de la cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_detail.png)

7. Haga clic en **Crear un contenedor**.

	![Detalles de la cuenta de almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_container.png)

8. Escriba un **Nombre** para el contenedor y seleccione la **Directiva de acceso**.

	![Nombre del contenedor](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_containervalues.png)

	> [AZURE.NOTE]De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir el acceso de lectura público a los blobs del contenedor, pero no a las propiedades y metadatos del contenedor, utilice la opción de "Public Blob". Para permitir el acceso de lectura público completo del contenedor y de los blobs, utilice la opción de "Public Container".

## Paso 3: Preparación de la conexión con Microsoft Azure ##

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure. Para ello, puede utilizar el método de Microsoft Azure Active Directory o el método del certificado.

### Uso del método de Microsoft Azure AD

1. Abra la consola de Azure PowerShell, siguiendo las instrucciones del apartado [Instalación de Microsoft Azure PowerShell](#Install).

2. Escriba el siguiente comando: `Add-AzureAccount`
	
	Este comando abre una ventana de inicio de sesión que le permitirá iniciar sesión con su cuenta profesional o educativa.

	![Ventana de PowerShell](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/add_azureaccount.png)

3. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

### Uso del método del certificado 

1. Abra la consola de Azure PowerShell. 

2.	Escriba: `Get-AzurePublishSettingsFile`.


3. Se abre una ventana del explorador que le solicita que descargue un archivo .publishsettings. Contiene información y un certificado para su suscripción de Microsoft Azure.

	![Página de descarga del explorador](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Browser_download_GetPublishSettingsFile.png)

3. Guarde el archivo .publishsettings.

4. Escriba: `Import-AzurePublishSettingsFile <PathToFile>`

	Donde `<PathToFile>` es la ruta completa al archivo .publishsettings.


	For more information, see [Get Started with Microsoft Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx). 
	
	For more information on installing and configuring PowerShell, see [How to install and configure Microsoft Azure PowerShell](install-configure-powershell.md). 


## Paso 4: Carga del archivo .vhd ##

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar las imágenes. **VHDName** es la etiqueta que aparece en el portal clásico para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.


1. Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/powershell_upload_vhd.png)

	Para obtener más información acerca de este cmdlet, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Paso 5: Incorporación de la imagen a la lista de imágenes personalizadas ##
Después de cargar el archivo .vhd, agréguelo como imagen a la lista de imágenes personalizadas asociadas con la suscripción.

1. En el portal clásico, en **Todos los elementos**, haga clic en **Máquinas virtuales**.

2. En Máquinas virtuales, haga clic en **Imágenes**.

3. Y, a continuación, haga clic en **Crear una imagen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Create_Image.png)

4. En **Crear una imagen desde un disco duro virtual**, realice lo siguiente:
 	

	- Specify **name**
	- Specify **description**
	- To specify the **URL of your VHD**, click the folder button to open the following window:
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Select_VHD.png)

	- Select the storage account your VHD is in and click **Open**. This returns you to the **Create an image from a VHD** window.
	- After you return to the **Create an image from a VHD** window, select the Operating System Family.
	- Check **I have run Sysprep on the virtual machine associated with this VHD** to acknowledge that you generalized the operating system in Step 1, and then click **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Create_Image_From_VHD.png)

5. **Opcional:** puede usar el cmdlet Add-AzureVMImage en lugar del portal clásico para agregar el disco duro virtual como una imagen. En la consola de Azure PowerShell, escriba:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/add_azureimage_powershell.png)

6. La nueva imagen aparece ahora cuando elige la pestaña **Imágenes**.


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/vm_custom_image.png)

	This new image is now available under **My Images** when you create a virtual machine. For instructions, see [Create a Virtual Machine Running Windows Server](virtual-machines-windows-tutorial.md).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/create_vm_custom_image.png)

	> [AZURE.TIP] If you get an error when you try to create a VM, with this error message, "The VHD https://XXXXX... has an unsupported virtual size of YYYY bytes. The size must be a whole number (in MBs)," it means your VHD is not a whole number of MBs and needs to be a fixed size VHD. Try using the Add-AzureVMImage PowerShell cmdlet instead of the classic portal to add the image (see step 5, above). The Azure cmdlets ensure that the VHD meets the Azure requirements.
	
## Pasos siguientes ##

Después de crear una máquina virtual, intente crear una máquina virtual de SQL Server. Para obtener instrucciones, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Microsoft Azure](virtual-machines-provision-sql-server.md).

[Paso 1: Preparación de la imagen que se va a cargar]: #prepimage
[Paso 2: Creación de una cuenta de almacenamiento de Azure]: #createstorage
[Paso 3: Preparación de la conexión a Azure]: #prepAzure
[Paso 4: Carga del archivo .vhd]: #upload

<!--HONumber=52-->
