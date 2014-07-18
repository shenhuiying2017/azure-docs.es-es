<properties  linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn how to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Windows Server Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# Creación y carga de un disco duro virtual que contiene el sistema operativo Windows Server

Una máquina virtual en Azure ejecuta el sistema operativo que elige cuando crea la máquina virtual. En Azure, el sistema operativo de una máquina virtual se almacena en un disco duro virtual en formato VHD (un archivo .vhd). Un VHD de un sistema operativo que se ha preparado para la duplicación se conoce como imagen. En este artículo se muestra cómo crear su propia imagen mediante la carga de un archivo .vhd con un sistema operativo que ha instalado y generalizado. Para obtener más información acerca de los discos y las imágenes en Azure, consulte [Administrar discos e imágenes][1].

**Nota**: al crear una máquina virtual, puede personalizar la configuración del sistema operativo para facilitar la ejecución de su aplicación. La configuración que establezca se almacena en el disco de esa máquina virtual. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada](/en-us/manage/windows/how-to-guides/custom-create-a-vm/).

## Requisitos previos

En este artículo se supone que tiene los siguientes elementos:

**Un certificado de administración**: Ha creado un certificado de administración para la suscripción para la que desea cargar un VHD, y ha exportado el certificado a un archivo .cer. Para obtener más información acerca de la creación de certificados, consulte [Crear y cargar un certificado de administración para Azure][2].

**Un sistema operativo Windows compatible almacenado en un archivo.vhd**: Ha instalado un sistema operativo Windows Server compatible en un disco duro virtual. Existen varias herramientas para crear archivos
.vhd. Puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalar el rol Hyper-V y configurar una máquina virtual][3].

**Importante**: el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD mediante el uso del Administrador de Hyper-V o el cmdlet convert-vhd.

* **Medios del sistema operativo Windows Server.** Esta tarea requiere un archivo .iso que contenga el sistema operativo Windows Server. Se admiten las siguientes versiones de Windows Server:

  <table  border="1" width="600">
  <tr  bgcolor="#E9E7E7">
    <th>SO</th>
  
    <th>SKU</th>
  
    <th>Service Pack</th>
  
    <th>Arquitectura</th>
  
  </tr>
  
  <tr>
    <td>Windows Server 2012</td>
  
    <td>Todas las ediciones</td>
  
    <td>N/D</td>
  
    <td>x64</td>
  
  </tr>
  
  <tr>
    <td>Windows Server 2008 R2</td>
  
    <td>Todas las ediciones</td>
  
    <td>SP1</td>
  
    <td>x64</td>
  
  </tr>
  
  </table>
  

</P>

* El cmdlet [Add-AzureVHD][4], que forma parte del módulo Azure PowerShell. Para descargar el módulo, consulte la página de [descargas de Azure](/en-us/develop/downloads/).

Esta tarea incluye los siguientes pasos:

* [Paso 1: Preparar la imagen que se va a cargar](#prepimage)
* [Paso 2: Crear una cuenta de almacenamiento en Azure](#createstorage)
* [Paso 3: Preparar la conexión a Azure](#prepAzure)
* [Paso 4: Cargar el archivo .vhd](#upload)

## <a  id="prepimage"> </a>Paso 1: Preparar la imagen que se va a cargar

Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando Sysprep. Para obtener más información acerca del uso Sysprep, consulte [How to Use Sysprep: An Introduction][5].

En la máquina virtual que acaba de crear, realice el siguiente procedimiento:

1.  Inicie sesión en el sistema operativo.

2.  Abra una ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.
    
    ![Abrir una ventana de símbolo del
    sistema](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.  Aparecerá el cuadro de diálogo **System Preparation Tool**.
    
    ![Iniciar
    Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  En **System Cleanup Action**, seleccione **Enter System Out-of-Box Experience (OOBE)** y asegúrese de que la casilla **Generalize** esté seleccionada.

5.  En **Shutdown Options**, seleccione **Shutdown**.

6.  Haga clic en **OK**.

## <a  id="createstorage"> </a>Paso 2: Crear una cuenta de almacenamiento en Azure

Una cuenta de almacenamiento representa el más alto nivel del espacio de nombres para el acceso a los servicios de almacenamiento, y está asociada con la suscripción de Azure. Necesitará una cuenta de almacenamiento en Azure para cargar en Azure el archivo .vhd que se va a utilizar para crear una máquina virtual. Puede utilizar el Portal de administración de Azure para crear la cuenta de almacenamiento.

1.  Inicie sesión en el Portal de administración de Azure.

2.  En la barra de comandos, haga clic en **New**.

3.  Haga clic en **Cuenta de almacenamiento** y, a continuación, haga clic en **Quick Create**.
    
    ![Creación rápida de una cuenta de
    almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4.  Rellene los campos de la manera siguiente:
    
    ![Escribir los detalles de la cuenta de
    almacenamiento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

* En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

* Elija la ubicación o el grupo de afinidad para la cuenta de almacenamiento. Al especificar un grupo de afinidad, puede colocar sus servicios en la nube del mismo centro de datos con su almacenamiento.

* Decida si va a utilizar la replicación geográfica para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste para usted, de modo que si se produce un error principal que no se pueda gestionar en la ubicación primaria, su
  almacenamiento conmuta por error a una ubicación secundaria. La ubicación secundaria se asigna automáticamente y no se puede cambiar. En caso de que se necesite un control más estricto sobre la ubicación
  de su almacenamiento basado en la nube por exigencias legales o de la política organizativa, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento.

1.  Haga clic en **Create Storage Account**.
    
    La cuenta aparece ahora en **Cuentas de almacenamiento**.
    
    ![Cuenta de almacenamiento creada
    correctamente](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

## <a  id="PrepAzure"> </a>Paso 3: Preparar la conexión a Azure

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure.

1.  Abra una ventana de Azure PowerShell.

2.  Escriba:
    
    `Get-AzurePublishSettingsFile`
    
    Este comando abre una ventana de explorador y descarga automáticamente un archivo .publishsettings que contiene información y un certificado para su suscripción de Azure.

3.  Guarde el archivo .publishsettings.

4.  Escriba:
    
    `Import-AzurePublishSettingsFile <PathToFile>`
    
    Donde `<PathToFile>` es la ruta de acceso completa al archivo .publishsettings.
    
    Para obtener información, consulte [Introducción a los cmdlets de Windows Azure][6].

## <a  id="upload"> </a>Paso 4: Cargar el archivo .vhd

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar sus imágenes. **VHDName** es la etiqueta que aparece en el Portal de administración para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.

1.  Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:
    
    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`
    
    Para obtener más información, consulte [Add-AzureVhd][4].

## Incorporación de la imagen a la lista de imágenes personalizadas

Después de cargar el archivo .vhd, agréguelo como imagen a la lista de imágenes personalizadas asociadas con la suscripción.

1.  En el Portal de administración, en **All Items**, haga clic en **Máquinas virtuales**.

2.  En Máquinas virtuales, haga clic en **Images** y, a continuación, haga clic en **Create**.

3.  En **Create an image from a VHD**, especifique un nombre para el archivo .vhd que ha cargado y la URL que dirige al mismo.

4.  Active la casilla **I have run Sysprep on the virtual machine associated with this VHD** para confirmar que ha generalizado el sistema operativo en el paso 2 y, a continuación, haga clic en **OK**.

## Pasos siguientes

Cuando la imagen esté disponible en la lista, puede usarla para crear máquinas virtuales. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines-windows-tutorial/).



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx
[3]: http://technet.microsoft.com/en-us/library/hh846766.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/dn205185.aspx
[5]: http://technet.microsoft.com/en-us/library/bb457073.aspx
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx