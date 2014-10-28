<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav"></tags>

# Crear y cargar un VHD de Windows Server a Azure

En este artículo se muestra cómo puede cargar un disco duro virtual (VHD) con un sistema operativo que podrá utilizar como imagen para crear máquinas virtuales basadas en dicha imagen. Para obtener más información acerca de los discos y las imágenes en Microsoft Azure, consulte [Administrar discos e imágenes en Azure][].

**Nota**: al crear una máquina virtual, puede personalizar la configuración del sistema operativo para facilitar la ejecución de su aplicación. La configuración que establezca se almacena en el disco de esa máquina virtual. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada][].

## Requisitos previos

En este artículo se supone que tiene los siguientes elementos:

**Una suscripción de Azure**: si no tiene ninguna, puede crear una cuenta de evaluación gratuita en un par de minutos. Para obtener más información, consulte [Creación de una cuenta de Azure][].

**Microsoft Azure PowerShell**: ha instalado el módulo de Microsoft Azure PowerShell. Para descargar el módulo, consulte [Descargas de Microsoft Azure][]. Podrá encontrar un tutorial para instalar y configurar PowerShell con su suscripción de Azure [aquí][].

-   El cmdlet [Add-AzureVHD][], que forma parte del módulo Microsoft Azure PowerShell. Utilizará este cmdlet para cargar el VHD.

**Un sistema operativo Windows compatible almacenado en un archivo .vhd**: Ha instalado un sistema operativo Windows Server compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalar el rol Hyper-V y configurar una máquina virtual][].

**Importante**: El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD][]. Podrá encontrar un tutorial para ello [aquí][1].

**Medios del sistema operativo Windows Server.** Esta tarea requiere un archivo .iso que contenga el sistema operativo Windows Server. Se admiten las siguientes versiones de Windows Server:

<table border="1" width="600">

<tr bgcolor="#E9E7E7">

<th>
SO

</th>

<th>
SKU

</th>

<th>
Service Pack

</th>

<th>
Arquitectura

</th>

</tr>

<tr>

<td>
Windows Server 2012 R2

</td>

<td>
Todas las ediciones

</td>

<td>
N/D

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2012

</td>

<td>
Todas las ediciones

</td>

<td>
N/D

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2008 R2

</td>

<td>
Todas las ediciones

</td>

<td>
SP1

</td>

<td>
x64

</td>

</tr>

</table>

</p>
</p>
Esta tarea incluye los siguientes pasos:

-   [Paso 1: Preparar la imagen que se va a cargar][]
-   [Paso 2: Crear una cuenta de almacenamiento en Azure][]
-   [Paso 3: Preparar la conexión a Azure][]
-   [Paso 4: Cargar el archivo .vhd][]

## <span id="prepimage"></span> </a>Paso 1: Preparar la imagen que se va a cargar

Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando Sysprep. Para obtener más información acerca del uso Sysprep, consulte [How to Use Sysprep: An Introduction][].

En la máquina virtual que acaba de crear, realice el siguiente procedimiento:

1.  Inicie sesión en el sistema operativo.

2.  Abra una ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y después ejecute `sysprep.exe`.

    ![Abrir una ventana de símbolo del sistema][]

3.  Aparecerá el cuadro de diálogo **System Preparation Tool**.

    ![Iniciar Sysprep][]

4.  En **System Preparation Tool**, seleccione **Enter System Out of Box Experience (OOBE)** y asegúrese de que la casilla Generalize esté seleccionada.

5.  En **Shutdown Options**, seleccione **Shutdown**.

6.  Haga clic en **OK**.

## <span id="createstorage"></span> </a>Paso 2: Crear una cuenta de almacenamiento en Azure

Una cuenta de almacenamiento representa el más alto nivel del espacio de nombres para el acceso a los servicios de almacenamiento, y está asociada con la suscripción de Azure. Necesitará una cuenta de almacenamiento en Azure para cargar en Azure el archivo .vhd que se va a utilizar para crear una máquina virtual. Puede utilizar el Portal de administración de Azure para crear la cuenta de almacenamiento.

1.  Inicie sesión en el Portal de administración de Azure.

2.  En la barra de comandos, haga clic en **New**.

3.  Haga clic en **Cuenta de almacenamiento** y, a continuación, en **Quick Create**.

    ![Creación rápida de una cuenta de almacenamiento][]

4.  Rellene los campos de la manera siguiente:

-   En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

-   Elija la **ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Al especificar un grupo de afinidad, puede colocar sus servicios en la nube del mismo centro de datos con su almacenamiento.

-   Decida si va a utilizar la **replicación geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste para usted, de modo que si se produce un error principal que no se pueda gestionar en la ubicación primaria, su almacenamiento conmuta por error a una ubicación secundaria. La ubicación secundaria se asigna automáticamente y no se puede cambiar. En caso de que se necesite un control más estricto sobre la ubicación de su almacenamiento basado en la nube por exigencias legales o de la política organizativa, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Puede encontrar más detalles sobre la replicación geográfica de las cuentas de almacenamiento en: [Administración de cuentas de almacenamiento][].

    ![Escribir los detalles de la cuenta de almacenamiento][]

1.  Haga clic en **Crear cuenta de almacenamiento**.

    La cuenta aparece ahora en **Cuentas de almacenamiento**.

    ![Cuenta de almacenamiento creada correctamente][]

2.  Después cree un contenedor para los VHD que ha cargado. Haga clic en el **Nombre de la cuenta de almacenamiento** después haga clic en **Contenedores**.

    ![Detalles de la cuenta de almacenamiento][]

3.  Haga clic en **Crear un contenedor**.

    ![Detalles de la cuenta de almacenamiento][2]

4.  Escriba un **Nombre** para el contenedor y seleccione la **Directiva de acceso**.

    ![Nombre de contenedor][]

    > [WACOM.NOTE] De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir al público tener acceso de lectura a los blobs en el contenedor, pero no a las propiedades y metadatos del contenedor, utilice la opción de "Public Blob". Para permitir el acceso de lectura público completo del contenedor y de los blobs, utilice la opción de "Public Container".

## <span id="PrepAzure"></span> </a>Paso 3: Preparar la conexión a Microsoft Azure

Antes de poder cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Microsoft Azure. Para ello, puede utilizar el método de Microsoft Azure Active Directory o el método del certificado.

### Uso del método de Microsoft Azure AD

1.  Abra la consola de Microsoft Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Microsoft Azure PowerShell][].

2.  Escriba el siguiente comando:
    `Add-AzureAccount`

    Este comando abre una ventana de inicio de sesión que le permitirá iniciar sesión con su cuenta profesional o educativa.

    ![Ventana de PowerShell][]

3.  Microsoft Azure autentica y guarda las credenciales y después cierra la ventana.

### Uso del método del certificado

1.  Abra la ventana de Microsoft Azure PowerShell.

2.  Escriba:
    `Get-AzurePublishSettingsFile`.

3.  Se abre una ventana del explorador y le pide que descargue un archivo .publishsettings file. Contiene información y un certificado para su suscripción de Microsoft Azure.

    ![Página de descarga del explorador][]

4.  Guarde el archivo .publishsettings.

5.  Escriba:
    `Import-AzurePublishSettingsFile <PathToFile>`

    Donde `<PathToFile>` es la ruta de acceso completa al archivo .publishsettings.

    Para obtener información, consulte [Introducción a los cmdlets de Microsoft Azure][].

    Para obtener mas información acerca de la instalación y configuración de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell][aquí].

## <span id="upload"></span> </a>Paso 4: Cargar el archivo .vhd

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar sus imágenes. **VHDName** es la etiqueta que aparece en el Portal de administración para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.

1.  Desde la ventana de Microsoft Azure PowerShell que ha usado en el paso anterior, escriba:

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    ![PowerShell Add-AzureVHD][]

    Para obtener más información acerca del cmdlet Add-AzureVhd, consulte [Add-AzureVhd][].

## Incorporación de la imagen a la lista de imágenes personalizadas

Después de cargar el archivo .vhd, agréguelo como imagen a la lista de imágenes personalizadas asociadas con la suscripción.

1.  En el Portal de administración, en **All Items**, haga clic en **Máquinas virtuales**.

2.  En Máquinas virtuales, haga clic en **Images**.

3.  Después haga clic en **Create an Image**.

    ![PowerShell Add-AzureVHD][3]

4.  En **Create an image from a VHD**, haga lo siguiente:

    -   Especifique un **nombre**
    -   Especifique una **descripción**
    -   Para especificar la **URL del VHD** haga clic en el botón de carpeta para abrir el siguiente cuadro de diálogo

    ![Seleccionar VHD][]

    -   Seleccione la cuenta de almacenamiento en la que se encuentra el VHD y haga clic en **Open**. Esto le devolverá a la ventana de **Crear una imagen desde un disco duro virtual**.
    -   Tras volver a la ventana de **Crear una imagen desde un disco duro virtual**, seleccione la Familia del sistema operativo.
    -   Active la casilla **I have run Sysprep on the virtual machine associated with this VHD** para confirmar que ha generalizado el sistema operativo en el paso 1 y, a continuación, haga clic en **OK**.

    ![Agregar imagen][]

5.  **OPCIONAL:** También puede utilizar el cmdlet Add-AzureVMImage de Azure PowerShell para agregar el VHD como una imagen.

    Desde la ventana de Microsoft Azure PowerShell, escriba:

    `Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

    ![PowerShell Add-AzureVMImage][]

6.  Tras completar los pasos anteriores, la nueva imagen aparecerá en la lista cuando elija la pestaña **Imágenes**.

    ![imagen personalizada][]

    Cuando cree una máquina virtual, ya podrá elegir esta nueva imagen. Elija **Mis imágenes** para mostrar la nueva imagen. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server][Creación de una máquina virtual personalizada].

    ![Creación de una máquina virtual a partir de una imagen personalizada][]

## Pasos siguientes

Después de crear una máquina virtual, intente crear una máquina virtual de SQL Server. Para obtener instrucciones, consulte [Aprovisionar una máquina virtual de SQL Server en Microsoft Azure][].

  [Administrar discos e imágenes en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj672979.aspx
  [Creación de una máquina virtual personalizada]: http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-windows-tutorial/
  [Creación de una cuenta de Azure]: http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/
  [Descargas de Microsoft Azure]: http://www.windowsazure.com/es-es/downloads/
  [aquí]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/
  [Add-AzureVHD]: http://msdn.microsoft.com/es-es/library/windowsazure/dn205185.aspx
  [Instalar el rol Hyper-V y configurar una máquina virtual]: http://technet.microsoft.com/es-es/library/hh846766.aspx
  [cmdlet Convert-VHD]: http://technet.microsoft.com/es-es/library/hh848454.aspx
  [1]: http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx
  [Paso 1: Preparar la imagen que se va a cargar]: #prepimage
  [Paso 2: Crear una cuenta de almacenamiento en Azure]: #createstorage
  [Paso 3: Preparar la conexión a Azure]: #prepAzure
  [Paso 4: Cargar el archivo .vhd]: #upload
  [How to Use Sysprep: An Introduction]: http://technet.microsoft.com/es-es/library/bb457073.aspx
  [Abrir una ventana de símbolo del sistema]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png
  [Iniciar Sysprep]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png
  [Creación rápida de una cuenta de almacenamiento]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png
  [Administración de cuentas de almacenamiento]: http://www.windowsazure.com/es-es/documentation/articles/storage-manage-storage-account/
  [Escribir los detalles de la cuenta de almacenamiento]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png
  [Cuenta de almacenamiento creada correctamente]: ./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png
  [Detalles de la cuenta de almacenamiento]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png
  [2]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png
  [Nombre de contenedor]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png
  [Instalación de Microsoft Azure PowerShell]: #Install
  [Ventana de PowerShell]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png
  [Página de descarga del explorador]: ./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png
  [Introducción a los cmdlets de Microsoft Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554332.aspx
  [PowerShell Add-AzureVHD]: ./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png
  [Add-AzureVhd]: http://msdn.microsoft.com/es-es/library/dn495173.aspx
  [3]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png
  [Seleccionar VHD]: ./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png
  [Agregar imagen]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png
  [PowerShell Add-AzureVMImage]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png
  [imagen personalizada]: ./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png
  [Creación de una máquina virtual a partir de una imagen personalizada]: ./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png
  [Aprovisionar una máquina virtual de SQL Server en Microsoft Azure]: http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-provision-sql-server/
