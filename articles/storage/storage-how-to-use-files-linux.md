---
title: Uso de Azure File Storage con Linux | Microsoft Docs
description: Aprenda a montar un recurso compartido de archivos de Azure mediante SMB en Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/8/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: d93c82b9c2e66c7241ddd579c1be74396174fd65
ms.contentlocale: es-es
ms.lasthandoff: 08/07/2017

---
# <a name="use-azure-file-storage-with-linux"></a>Uso de Azure File Storage con Linux
[Azure File Storage](storage-dotnet-how-to-use-files.md) es el sistema de archivos en la nube de Microsoft fácil de usar. Los recursos compartidos de Azure File se pueden montar en distribuciones de Linux mediante el [paquete cifs-utils](https://wiki.samba.org/index.php/LinuxCIFS_utils) del [proyecto de Samba](https://www.samba.org/). En este artículo se muestran dos maneras de montar un recurso compartido de Azure File: a petición, con el comando `mount` y al inicio, mediante la creación de una entrada en `/etc/fstab`.

> [!NOTE]  
> Para montar un recurso compartido de Azure File fuera de la región de Azure en la que se hospeda, bien sea en local o en una región distinta de Azure, el sistema operativo debe admitir la funcionalidad de cifrado de SMB 3.0. La característica de cifrado de SMB 3.0 para Linux se introdujo en el kernel 4.11. Esta característica permite el montaje de recursos compartidos de Azure File desde el entorno local o una región distinta de Azure. En el momento de la publicación, esta funcionalidad se ha usado en Ubuntu 16.04 y en versiones anteriores.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Requisitos previos para el montaje de un recurso compartido de Azure File con Linux y el paquete cifs-utils
* **Seleccionar una distribución de Linux que pueda tener instalado el paquete cifs-utils**: Microsoft recomienda las siguientes distribuciones de Linux de la galería de imágenes de Azure:

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12   

    > [!Note]  
    > No se puede usar con Azure File Storage ninguna distribución de Linux que descargue e instale (o compile) versiones recientes del paquete cifs-utils.

* <a id="install-cifs-utils"></a>**El paquete cifs-utils está instalado**: este paquete se puede instalar con el administrador de paquetes de la distribución de Linux de su elección. 

    En distribuciones de **Ubuntu** y **Debian**, use el administrador de paquetes `apt-get`:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    En **RHEL** y **CentOS**, use el administrador de paquetes `yum`:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    En **openSUSE**, use el administrador de paquetes `zypper`:

    ```
    sudo zypper install samba*
    ```

    En otras distribuciones, use el administrador de paquetes apropiado o [compile desde el origen](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Decidir sobre los permisos de archivo o directorio del recurso compartido montado**: en los ejemplos siguientes, usamos 0777 para proporcionar permisos de lectura, escritura y ejecución a todos los usuarios. Puede reemplazarlos por otros [permisos chmod](https://en.wikipedia.org/wiki/Chmod), según prefiera. 

* **Nombre de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará el nombre de la cuenta de almacenamiento.

* **Clave de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará la clave principal (o secundaria) de la cuenta de almacenamiento. Actualmente no se admiten claves SAS para el montaje.

* **Asegurarse de que el puerto 445 está abierto**: SMB se comunica a través del puerto TCP 445, así que compruebe que el firewall no bloquea el puerto TCP 445 en la máquina cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montaje del recurso compartido de Azure File a petición con `mount`
1. **[Instale el paquete cifs-utils correspondiente a su distribución de Linux](#install-cifs-utils)**.

2. **Cree una carpeta para el punto de montaje**: esta operación se puede hacer en cualquier parte del sistema de archivos.

    ```
    mkdir mymountpoint
    ```

3. **Use el comando mount para montar el recurso compartido de Azure File**: no olvide reemplazar `<storage-account-name>`, `<share-name>`, `<storage-account-key>` y por la información correcta.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Cuando haya terminado de usar el recurso compartido de Azure File, puede usar `sudo umount ./mymountpoint` para desmontarlo.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creación de un punto de montaje persistente para el recurso compartido de Azure File`/etc/fstab`
1. **[Instale el paquete cifs-utils correspondiente a su distribución de Linux](#install-cifs-utils)**.

2. **Cree una carpeta para el punto de montaje**: esta operación se puede realizar en cualquier parte del sistema de archivos, pero debe tener en cuenta la ruta de acceso absoluta de la carpeta. En el ejemplo siguiente se crea una carpeta en la raíz.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Use el siguiente comando para anexar la siguiente línea a `/etc/fstab`** : no olvide reemplazar `<storage-account-name>`, `<share-name>`, y `<storage-account-key>` por la información correcta.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Puede usar `sudo mount -a` para montar el recurso compartido de Azure File después de editar `/etc/fstab` en lugar de reiniciar el sistema.

## <a name="feedback"></a>Comentarios
Usuarios de Linux: nos gustaría conocer su opinión.

Azure File Storage para el grupo de usuarios de Linux cuenta con un foro donde puede compartir sus comentarios a medida que evalúa y adopta File Storage en Linux. Envíe un correo electrónico a [Usuarios de Linux de Azure File Storage](mailto:azurefileslinuxusers@microsoft.com) para unirse al grupo de usuarios.

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para obtener más información acerca Azure File Storage.
* [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Usar Azure PowerShell con Azure Storage](storage-powershell-guide-full.md)
* [Uso de AzCopy con Microsoft Azure Storage](storage-use-azcopy.md)
* [Uso de la CLI de Azure con Azure Storage](storage-azure-cli.md#create-and-manage-file-shares)
* [Preguntas más frecuentes](storage-files-faq.md)
* [Solución de problemas](storage-troubleshoot-file-connection-problems.md)

