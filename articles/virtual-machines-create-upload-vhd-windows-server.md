<properties urlDisplayName="Upload a VHD" pageTitle="Crear y cargar un VHD de Windows Server a Azure" metaKeywords="Azure VHD, carga de VHD" description="Aprenda a crear y cargar un disco duro virtual (VHD) en Azure que tiene el sistema operativo Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />


#Crear y cargar un VHD de Windows Server a Azure#

En este artículo se muestra cómo puede cargar un disco duro virtual (VHD) con un sistema operativo que podrá utilizar como imagen para crear máquinas virtuales basadas en dicha imagen. Para obtener más información acerca de los discos y las imágenes en Microsoft Azure, consulte [Administrar discos e imágenes en Azure](http://msdn.microsoft.com/es-es/library/windowsazure/jj672979.aspx).

**Nota**: al crear una máquina virtual, puede personalizar la configuración del sistema operativo para facilitar la ejecución de su aplicación. La configuración que establezca se almacena en el disco de esa máquina virtual. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada](http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-windows-tutorial/).

##Requisitos previos##
En este artículo se supone que tiene los siguientes elementos:

**Una suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/).  

**Microsoft Azure PowerShell**: ha instalado el módulo de Microsoft Azure PowerShell. Para descargar el módulo, consulte [Descargas de Microsoft Azure](http://www.windowsazure.com/es-es/downloads/). Podrá encontrar un tutorial para instalar y configurar PowerShell con su suscripción de Azure [aquí](http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/).

- El cmdlet [Add-AzureVHD](http://msdn.microsoft.com/es-es/library/windowsazure/dn205185.aspx), que forma parte del módulo Microsoft Azure PowerShell. Utilizará este cmdlet para cargar el VHD.

**Un sistema operativo Windows compatible almacenado en un archivo .vhd**: ha instalado un sistema operativo Windows Server compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalar el rol Hyper-V y configurar una máquina virtual](http://technet.microsoft.com/es-es/library/hh846766.aspx).

**Importante**: El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD](http://technet.microsoft.com/es-es/library/hh848454.aspx). Podrá encontrar un tutorial para ello [aquí](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
**Medios de sistema operativo de Windows Server.** Esta tarea requiere un archivo .iso que contiene el sistema operativo Windows Server. Se admiten las siguientes versiones de Windows Server:
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

## <a id="prepimage"> </a>Paso 1: Prepare la imagen que se va a cargar ##


Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando Sysprep. Para obtener más información acerca del uso Sysprep, consulte [How to Use Sysprep: An Introduction](http://technet.microsoft.com/es-es/library/bb457073.aspx).

En la máquina virtual que acaba de crear, realice el siguiente procedimiento:

1. Inicie sesión en el sistema operativo.

2. Abra una ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep** y, luego, ejecute 'sysprep.exe'.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Aparecerá el cuadro de diálogo **System Preparation Tool**.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE) del sistema** y asegúrese de que la casilla Generalizar esté seleccionada.

5.  En **Shutdown Options**, seleccione **Shutdown**.

6.  Haga clic en **OK**. 




## <a id="createstorage"> </a>Paso 2: Creación de una cuenta de almacenamiento en Azure ##

Una cuenta de almacenamiento representa el más alto nivel del espacio de nombres para el acceso a los servicios de almacenamiento, y está asociada con la suscripción de Azure. Necesitará una cuenta de almacenamiento en Azure para cargar en Azure el archivo .vhd que se va a utilizar para crear una máquina virtual. Puede utilizar el Portal de administración de Azure para crear la cuenta de almacenamiento.

1. Inicie sesión en el Portal de administración de Azure.

2. En la barra de comandos, haga clic en **New**.

3. Haga clic en **Cuenta de almacenamiento** y, luego, en **Creación rápida**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Rellene los campos de la manera siguiente:

	
	
- En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.
			
- Elija la **ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Al especificar un grupo de afinidad, puede colocar sus servicios en la nube del mismo centro de datos con su almacenamiento.
		 
- Decida si va a usar la **replicación geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste para usted, de modo que si se produce un error principal que no se pueda gestionar en la ubicación primaria, su almacenamiento conmuta por error a una ubicación secundaria. La ubicación secundaria se asigna automáticamente y no se puede cambiar. En caso de que se necesite un control más estricto sobre la ubicación de su almacenamiento basado en la nube por exigencias legales o de la política organizativa, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Puede encontrar más detalles sobre la replicación geográfica de las cuentas de almacenamiento en: [Crear, administrar o eliminar una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Haga clic en **Crear cuenta de almacenamiento**.

	La cuenta aparece ahora en **Cuentas de almacenamiento**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Después cree un contenedor para los VHD que ha cargado. Haga clic en el **Nombre de la cuenta de almacenamiento**, después haga clic en **Contenedores**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Haga clic en **Crear un contenedor**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Escriba un **Nombre** para el contenedor y seleccione la **Directiva de acceso**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [WACOM.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir al público tener acceso de lectura a los blobs en el contenedor, pero no a las propiedades y metadatos del contenedor, utilice la opción de "Public Blob". Para permitir el acceso de lectura público completo del contenedor y de los blobs, utilice la opción de "Public Container".

## <a id="PrepAzure"> </a>Paso 3: Preparar la conexión a Microsoft Azure ##

Antes de poder cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Microsoft Azure. Para ello, puede utilizar el método de Microsoft Azure Active Directory o el método del certificado.

<h3>Uso del método de Microsoft Azure AD</h3>

1. Abra la consola de Microsoft Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Microsoft Azure PowerShell](#Install).

2. Escriba el siguiente comando:  
	`Add-AzureAccount`
	
	Este comando abre una ventana de inicio de sesión que le permitirá iniciar sesión con su cuenta profesional o educativa.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Microsoft Azure autentica y guarda las credenciales y después cierra la ventana.

<h3>Uso del método del certificado</h3> 

1. Abra la ventana de Microsoft Azure PowerShell. 

2.	Tipo: 
	`Get-AzurePublishSettingsFile`.


3. Se abre una ventana del explorador y le pide que descargue un archivo .publishsettings file. Contiene información y un certificado para su suscripción de Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Guarde el archivo .publishsettings. 

4. Tipo: 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Donde '<PathToFile>' es la ruta de acceso completa al archivo .publishsettings. 


	Para obtener información, consulte [Introducción a los cmdlets de Microsoft Azure](http://msdn.microsoft.com/es-es/library/windowsazure/jj554332.aspx). 
	
	Para obtener más información acerca de la instalación y configuración de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/). 


## <a id="upload"> </a>Paso 4: Cargar el archivo .vhd ##

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar sus imágenes. **VHDName** es la etiqueta que aparece en el Portal de administración para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd. 


1. Desde la ventana de Microsoft Azure PowerShell que ha usado en el paso anterior, escriba:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Para obtener más información acerca del cmdlet Add-AzureVhd, consulte [Add-AzureVhd](http://msdn.microsoft.com/es-es/library/dn495173.aspx).

##Adición de la imagen a la lista de imágenes personalizadas ##
Después de cargar el archivo .vhd, agréguelo como imagen a la lista de imágenes personalizadas asociadas con la suscripción.

1. En el Portal de administración, en **All Items**, haga clic en **Máquinas virtuales**.

2. En Máquinas virtuales, haga clic en **Imágenes**.

3. Después haga clic en **Crear una imagen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. En **Crear una imagen desde un disco duro virtual**, haga lo siguiente:
 	
	- Escriba un **nombre**
	- Escriba una **descripción**
	- Para especificar la **URL del VHD**, haga clic en el botón de carpeta para abrir el cuadro de diálogo siguiente
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)
	- Seleccione la cuenta de almacenamiento en la que se encuentra el VHD y haga clic en **Abrir**. Esto le devolverá a la ventana **Crear una imagen desde un disco duro virtual**.
	- Tras volver a la ventana **Crear una imagen desde un disco duro virtual**, seleccione la Familia del sistema operativo.
	- Active la casilla **I have run Sysprep on the virtual machine associated with this VHD** para confirmar que ha generalizado el sistema operativo en el paso 1 y, a continuación, haga clic en **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **OPCIONAL:** también puede usar el cmdlet Add-AzureVMImage de Azure PowerShell para agregar el VHD como una imagen.

	Desde la ventana de Microsoft Azure PowerShell, escriba:

	'Add-AzureVMImage -ImageName <nombre de la imagen> -MediaLocation <ubicación del VHD> -OS <tipo de sistema operativo en el VHD>'
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Tras completar los pasos anteriores, la nueva imagen aparecerá en la lista cuando elija la pestaña **Imágenes**. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Cuando cree una máquina virtual, ya podrá elegir esta nueva imagen. Elija **Mis imágenes** para mostrar la nueva imagen. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server](http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

## Pasos siguientes ##
 

Después de crear una máquina virtual, intente crear una máquina virtual de SQL Server. Para obtener instrucciones, consulte [Aprovisionar una máquina virtual de SQL Server en Microsoft Azure](http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-provision-sql-server/). 

[Paso 1: Preparar la imagen que se va a cargar]: #prepimage
[Paso 2: Crear una cuenta de almacenamiento en Azure]: #createstorage
[Paso 3: Preparar la conexión a Azure]: #prepAzure
[Paso 4: Cargar el archivo .vhd]: #upload

<!--HONumber=35.2-->
