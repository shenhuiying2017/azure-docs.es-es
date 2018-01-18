---
title: Uso de Azure Files con Linux | Microsoft Docs
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
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 0a87f8572af2620420faa0e3c2e575aa8add42ab
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="use-azure-files-with-linux"></a>Uso de Azure Files con Linux
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en distribuciones de Linux mediante el [cliente kernel de CIFS](https://wiki.samba.org/index.php/LinuxCIFS). En este artículo se muestran dos maneras de montar un recurso compartido de Azure File: a petición, con el comando `mount` y al inicio, mediante la creación de una entrada en `/etc/fstab`.

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

3. **Use el siguiente comando para anexar la siguiente línea a `/etc/fstab`**: no olvide reemplazar `<storage-account-name>`, `<share-name>`, y `<storage-account-key>` por la información correcta.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs nofail,vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Asegúrese de agregar la opción `nofail` en la entrada `/etc/fstab`, de lo contrario, la máquina virtual puede bloquearse durante el arranque en caso de una configuración incorrecta u otro error al montar el recurso compartido de archivos de Azure.

> [!Note]  
> Puede usar `sudo mount -a` para montar el recurso compartido de Azure File después de editar `/etc/fstab` en lugar de reiniciar el sistema.

## <a name="feedback"></a>Comentarios
Usuarios de Linux: nos gustaría conocer su opinión.

El grupo de usuarios de Azure Files para Linux ofrece un foro donde puede compartir sus comentarios a medida que evalúa y adopta File Storage en Linux. Envíe un correo electrónico a [Usuarios de Azure Files para Linux](mailto:azurefileslinuxusers@microsoft.com) para unirse al grupo de usuarios.

## <a name="next-steps"></a>pasos siguientes
Consulte los vínculos siguientes para más información acerca Azure Files.
* [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Uso de AzCopy con Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Uso de la CLI de Azure con Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [P+F](../storage-files-faq.md)
* [Solución de problemas](storage-troubleshoot-linux-file-connection-problems.md)
