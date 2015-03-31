<properties 
	pageTitle="Crear y cargar un VHD de Windows Server a Azure" 
	description="Aprenda a crear y cargar un disco duro virtual (VHD) en Azure que tiene el sistema operativo Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2015" 
	ms.author="kathydav"/>


#Crear y cargar un VHD de Windows Server a Azure#

En este artículo se muestra cómo puede cargar un disco duro virtual (VHD) con un sistema operativo que podrá utilizar como imagen para crear máquinas virtuales basadas en dicha imagen. Para obtener más información acerca de los discos y las imágenes en Microsoft Azure, consulte [Administrar discos e imágenes en Azure](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

> [AZURE.NOTE] Cuando se crea una máquina virtual basada en una imagen, puede personalizar la configuración del sistema operativo según corresponda para las aplicaciones que se van a ejecutar en la máquina virtual. Esta configuración se guarda para esa máquina virtual y no afecta a la imagen. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada](http://www.windowsazure.com/documentation/articles/virtual-machines-windows-tutorial/).

##Requisitos previos##
En este artículo se supone que tiene lo siguiente:

1. **Una suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/).  

2. **Microsoft Azure PowerShell**: tiene el módulo Microsoft Azure PowerShell instalado y configurado para usar su suscripción. Para descargar el módulo, consulte [Descargas de Microsoft Azure](http://www.windowsazure.com/downloads/). Está disponible un tutorial para instalar y configurar el módulo [aquí](http://www.windowsazure.com/documentation/articles/install-configure-powershell/). Usará el cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) para cargar el VHD.

3. **Un sistema operativo Windows compatible almacenado en un archivo .vhd**: ha instalado un sistema operativo Windows Server compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Puede utilizar una solución de virtualización como Hyper-V para crear una máquina virtual e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalar el rol Hyper-V y configurar una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Podrá encontrar un tutorial para ello [aquí](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Se admiten las siguientes versiones de Windows Server:
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>SO</TH>
    <TH>SKU</TH>
    <TH>Service Pack</TH>
    <TH>Arquitectura</TH>
  </TR>
  <TR>
    <TD>Windows Server 2012 R2</TD>
    <TD>Todas las ediciones</TD>
    <TD>N/D</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2012</TD>
    <TD>Todas las ediciones</TD>
    <TD>N/D</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2008 R2</TD>
    <TD>Todas las ediciones</TD>
    <TD>SP1</TD>
    <TD>x64</TD>
  </TR>
  </TABLE>
</P>


Esta tarea incluye los siguientes pasos:

- [Paso 1: Preparar la imagen que se va a cargar] []
- [Paso 2: Crear una cuenta de almacenamiento en Azure] []
- [Paso 3: Preparar la conexión a Azure] []
- [Paso 4: Cargar el archivo .vhd] []

## <a id="prepimage"> </a>Paso 1: Preparar la imagen que se va a cargar ##

Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando Sysprep. Para obtener más información acerca del uso Sysprep, consulte [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx).

Desde la máquina virtual en la que se instaló el sistema operativo, realice el procedimiento siguiente:

1. Inicie sesión en el sistema operativo.

2. Abra una ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep** y, a continuación, ejecute  `sysprep.exe`.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Aparecerá el cuadro de diálogo **System Preparation Tool**.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE) del sistema** y asegúrese de que la casilla Generalizar esté seleccionada.

5.  En **Shutdown Options**, seleccione **Shutdown**.

6.  Haga clic en **OK**. 


## <a id="createstorage"> </a>Paso 2: Crear una cuenta de almacenamiento en Azure ##

Necesita una cuenta de almacenamiento de Azure para cargar un archivo .vhd, por lo que se puede usar en Azure para crear una máquina virtual. Puede utilizar el Portal de administración de Azure para crear la cuenta de almacenamiento.

1. Inicie sesión en el Portal de administración de Azure.

2. En la barra de comandos, haga clic en **New**.

3. Haga clic en **Servicios de datos** > **Almacenamiento** > **Creación rápida**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Rellene los campos de la manera siguiente:
	
	- En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.
			
	- Elija la **ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Un grupo de afinidad le permite colocar sus servicios en la nube y el almacenamiento en el mismo centro de datos.
		 
	- Decida si va a usar la **replicación geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste, por lo que su almacenamiento conmuta por error a esa ubicación si se produce un error importante en la ubicación principal. La ubicación secundaria se asigna automáticamente y no se puede cambiar. Si necesita más control sobre la ubicación del almacenamiento en la nube debido a requisitos legales o las directivas de su organización, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Puede encontrar más detalles sobre la replicación geográfica de las cuentas de almacenamiento en: [Crear, administrar o eliminar una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Haga clic en **Crear cuenta de almacenamiento**. La cuenta aparece ahora en **Almacenamiento**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Después cree un contenedor para los VHD que ha cargado. Haga clic en el nombre de la cuenta de almacenamiento y, a continuación, haga clic en **Contenedores**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Haga clic en **Crear un contenedor**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Escriba un **Nombre** para el contenedor y seleccione la **Directiva de acceso**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir al público tener acceso de lectura a los blobs en el contenedor, pero no a las propiedades y metadatos del contenedor, utilice la opción de "Public Blob". Para permitir el acceso de lectura público completo del contenedor y de los blobs, utilice la opción de "Public Container".

## <a id="PrepAzure"> </a>Paso 3: Preparar la conexión a Microsoft Azure ##

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure. Para ello, puede utilizar el método de Microsoft Azure Active Directory o el método del certificado.

<h3>Uso del método de Microsoft Azure AD</h3>

1. Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Microsoft Azure PowerShell](#Install).

2. Escriba el siguiente comando:  
	`Add-AzureAccount`
	
	Este comando abre una ventana de inicio de sesión que le permitirá iniciar sesión con su cuenta profesional o educativa.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

<h3>Uso del método del certificado</h3> 

1. Abra la consola de PowerShell de Azure. 

2.	Tipo: 
	`Get-AzurePublishSettingsFile`.


3. Se abre una ventana del explorador y le pide que descargue un archivo .publishsettings file. Contiene información y un certificado para su suscripción de Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Guarde el archivo .publishsettings. 

4. Tipo: 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Donde `<PathToFile>` es la ruta completa al archivo .publishsettings. 


	Para obtener información, consulte [Introducción a los cmdlets de Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx). 
	
	Para obtener más información acerca de la instalación y configuración de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](http://www.windowsazure.com/documentation/articles/install-configure-powershell/). 


## <a id="upload"> </a>Paso 4: Cargar el archivo .vhd ##

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar sus imágenes. **VHDName** es la etiqueta que aparece en el Portal de administración para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd. 


1. Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Para obtener más información acerca del cmdlet Add-AzureVhd, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

##Paso 5: Incorporación de la imagen a la lista de imágenes personalizadas ##
Después de cargar el archivo .vhd, agréguelo como imagen a la lista de imágenes personalizadas asociadas con la suscripción.

1. En el Portal de administración, en **All Items**, haga clic en **Máquinas virtuales**.

2. En Máquinas virtuales, haga clic en **Imágenes**.

3. Después haga clic en **Crear una imagen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. En **Crear una imagen desde un disco duro virtual**, haga lo siguiente:
 	

	- Escriba un **nombre**
	- Escriba una **descripción**
	- Para especificar la **Dirección URL de su disco duro virtual**, haga clic en el botón de la carpeta para abrir la ventana siguiente:
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- Seleccione la cuenta de almacenamiento en la que se encuentra el VHD y haga clic en **Abrir**. Esto le devolverá a la ventana **Crear una imagen desde un disco duro virtual**.
	- Tras volver a la ventana **Crear una imagen desde un disco duro virtual**, seleccione la Familia del sistema operativo.
	- Active la casilla **I have run Sysprep on the virtual machine associated with this VHD** para confirmar que ha generalizado el sistema operativo en el paso 1 y, a continuación, haga clic en **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **OPCIONAL:** puede usar el cmdlet Add-AzureVMImage en lugar del Portal de administración para agregar el disco duro virtual como una imagen. 	En la consola de Azure PowerShell, escriba:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Tras completar los pasos anteriores, la nueva imagen aparecerá en la lista cuando elija la pestaña **Imágenes**. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Esta nueva imagen ahora está disponible en **Mis imágenes** al crear una máquina virtual. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server](http://www.windowsazure.com/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP] Si se produce un error al intentar crear una máquina virtual, con este mensaje de error: "El VHD https://XXXXX... tiene un tamaño virtual no compatible de YYYY bytes. El tamaño debe ser un número entero (en MB)", significa que el disco duro virtual no es un número entero de MB y debe ser un disco duro virtual de tamaño fijo. Pruebe a usar el cmdlet de PowerShell Add-AzureVMImage en lugar del Portal de administración para agregar la imagen (vea el paso 5, anteriormente). Los cmdlets de Azure se aseguran de que el disco duro virtual cumple los requisitos de Azure.
	
## Pasos siguientes ##
 

Después de crear una máquina virtual, intente crear una máquina virtual de SQL Server. Para obtener instrucciones, consulte [Aprovisionar una máquina virtual de SQL Server en Microsoft Azure](http://www.windowsazure.com/documentation/articles/virtual-machines-provision-sql-server/). 

[Paso 1: Preparar la imagen que se va a cargar]: #prepimage
[Paso 2: Crear una cuenta de almacenamiento en Azure]: #createstorage
[Paso 3: Preparar la conexión a Azure]: #prepAzure
[Paso 4: Cargar el archivo .vhd]: #upload

<!--HONumber=47-->
