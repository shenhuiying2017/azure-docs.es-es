<properties
	pageTitle="Uso de Almacenamiento de archivos de Azure con Linux | Microsoft Azure"
        description="Cree un recurso compartido de archivos en la nube y móntelo desde una máquina virtual de Azure o una aplicación local que se ejecute en Linux."
        services="storage"
        documentationCenter="na"
        authors="jasontang501"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="10/26/2015"
      ms.author="jutang;tamram" />


# Uso de Almacenamiento de archivos de Azure con Linux 

## Información general

Almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar. Almacenamiento de archivos ya está disponible y es compatible con SMB 3.0 y SMB 2.1.

Puede crear recursos compartidos de archivos de Azure mediante el portal de vista previa de Azure, los cmdlets de PowerShell de Almacenamiento de Azure, las bibliotecas de cliente de Almacenamiento de Azure o la API de REST de Almacenamiento de Azure. Además, dado que los recursos compartidos de archivos son recursos compartidos de SMB, puede tener acceso a ellos a través de la API del sistema de archivos estándar y familiar.

Las aplicaciones que se ejecutan en Azure pueden montar fácilmente recursos compartidos de archivos de máquinas virtuales de Azure. Y con la última versión de Almacenamiento de archivos, es posible también montar un recurso compartido de archivos desde una aplicación local que sea compatible con SMB 3.0.

Tenga en cuenta que dado que el cliente SMB de Linux todavía no admite el cifrado, montar un recurso compartido de archivos de Linux requiere aún que el cliente se encuentre en la misma región de Azure que el recurso compartido de archivos. Sin embargo, la compatibilidad con el cifrado para Linux está en la hoja de ruta de los desarrolladores de Linux responsables de la funcionalidad SMB. Las distribuciones de Linux que admiten el cifrado en el futuro también podrán montar un recurso compartido de archivos de Azure desde cualquier lugar.

## Vídeo: Uso del almacenamiento de archivos de Azure con Linux

Este es un vídeo que muestra cómo crear y usar recursos compartidos de archivos de Azure en Linux.

> [AZURE.VIDEO azure-file-storage-with-linux]

## Elija una distribución de Linux para usar ##

Al crear una máquina virtual de Linux en Azure, puede especificar una imagen de Linux que admita SMB 2.1 o superior de la Galería de imágenes de Azure. A continuación se muestra una lista de imágenes de Linux recomendadas:

- Ubuntu Server 14.04	
- Ubuntu Server 15.04	
- CentOS 7.1	
- Open SUSE 13.2	
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium Image)

## Montaje del recurso compartido de archivos ##

Para montar el recurso compartido de archivos desde una máquina virtual con Linux, puede que necesite instalar un cliente SMB/CIFS si la distribución que está utilizando no tiene un cliente integrado. Este es el comando de Ubuntu para instalar una opción cifs-utils:

    sudo apt-get install cifs-utils

A continuación, tiene que crear un punto de montaje (mkdir mymountpoint) y luego emitir el comando de montaje, que es similar al siguiente:

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

También puede agregar configuraciones en su /etc/fstab para montar el recurso compartido.

Tenga en cuenta que aquí 0777 representa un código de permisos de directorio o archivo que concede permisos de ejecución/lectura/escritura a todos los usuarios. Puede reemplazarlo con otro código de permiso de archivos siguiendo el documento de permisos de archivos de Linux.
 
Asimismo, para mantener un recurso compartido de archivos montado tras reiniciar el equipo, puede agregar una configuración como la que se muestra a continuación en etc/fstab:

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Por ejemplo, si creó una máquina virtual de Azure con una imagen de Linux Ubuntu Server 15.04 (disponible en la galería de imágenes de Azure), puede montar el archivo de la manera siguiente:

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Si utiliza CentOS 7.1, puede montar el archivo como sigue:

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Si utiliza Open SUSE 13.2, puede montar el archivo como sigue:

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## Administre el recurso compartido de archivos ##

El [Portal de vista previa de Azure](https://portal.azure.com/) ahora proporciona una interfaz de usuario para administrar el almacenamiento de archivos de Azure. Puede realizar las siguientes acciones desde el explorador web:

- Cargar y descargar archivos al recurso compartido de archivos y desde este.
- Supervisar el uso real de cada recurso compartido de archivos.
- Ajustar la cuota de tamaño del recurso compartido de archivos.
- Copie el comando `net use` para usarlo para montar el recurso compartido de archivos desde un cliente Windows. 

También puede usar la interfaz de la línea de comandos multiplataforma de Azure (CLI de Azure) desde Linux para administrar el recurso compartido de archivos. La CLI de Azure proporciona un conjunto de comandos de código abierto multiplataforma para trabajar con Almacenamiento de Azure, incluido el almacenamiento de archivos. Proporciona muchas de las funcionalidades que se encuentran en el Portal de Azure, así como la funcionalidad de acceso a datos enriquecidos. Para obtener ejemplos, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](storage-azure-cli.md).

## Desarrollo con almacenamiento de archivos ##

Como desarrollador, puede compilar una aplicación con almacenamiento de archivos mediante el uso de la [Biblioteca de clientes de Almacenamiento de Azure para Java](https://github.com/azure/azure-storage-java). Para conocer los ejemplos de código, consulte [Uso del almacenamiento de archivos de Java](storage-java-how-to-use-file-storage.md).

También puede usar la [Biblioteca de clientes de Almacenamiento de Azure para Node.js](https://github.com/Azure/azure-storage-node) para desarrollar en el almacenamiento de archivos.

## Comentarios y más información ##

Usuarios de Linux: nos gustaría conocer su opinión.

El almacenamiento de archivos de Azure para el grupo de usuarios de Linux cuenta con un foro donde puede compartir sus comentarios a medida que evalúa y adopta el almacenamiento de archivos en Linux. Envíe un correo electrónico a [Usuarios de Linux de Almacenamiento de archivos de Azure](mailto:azurefileslinuxusers@microsoft.com) para unirse al grupo de usuarios.

## Pasos siguientes

Consulte los vínculos siguientes para obtener más información acerca de Almacenamiento de archivos de Azure.

### Artículos y vídeos conceptuales

- [Almacenamiento de archivos de Azure: un sistema de archivos SMB en la nube sin dificultades para Windows y Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Uso del Almacenamiento de archivos de Azure con Windows](storage-dotnet-how-to-use-files.md)

### Herramientas de soporte técnico para Almacenamiento de archivos

- [Uso de AzCopy con Almacenamiento de Microsoft Azure](storage-use-azcopy.md)
- [Uso de la CLI de Azure con Almacenamiento de Azure](storage-azure-cli.md#create-and-manage-file-shares)

### Referencia

- [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Publicaciones de blog

- [El almacenamiento de archivos de Azure ya está disponible de manera general](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Profundización en el almacenamiento de archivos de Azure](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Introducción al servicio de archivos de Microsoft Azure)
- [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx) (Persistencia de conexiones en archivos de Microsoft Azure)

<!---HONumber=Nov15_HO1-->