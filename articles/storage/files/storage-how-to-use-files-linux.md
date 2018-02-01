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
ms.date: 12/20/2017
ms.author: renash
ms.openlocfilehash: cca0d315a815faca5db07099b8e8e451ef55fad5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-files-with-linux"></a>Uso de Azure Files con Linux
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en distribuciones de Linux mediante el [cliente kernel de CIFS](https://wiki.samba.org/index.php/LinuxCIFS). En este artículo se muestran dos maneras de montar un recurso compartido de archivos de Azure: a petición, con el comando `mount` y al inicio, mediante la creación de una entrada en `/etc/fstab`.

> [!NOTE]  
> Para montar un recurso compartido de archivos de Azure fuera de la región de Azure en la que se hospeda, bien sea en local o en una región distinta de Azure, el sistema operativo debe admitir la funcionalidad de cifrado de SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Requisitos previos para el montaje de un recurso compartido de archivos de Azure con Linux y el paquete cifs-utils
* **Seleccionar una distribución de Linux que pueda tener instalado el paquete cifs-utils**.  
    Las siguientes distribuciones de Linux están disponibles para su uso en la galería de Azure:

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8+
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**El paquete cifs-utils está instalado**.  
    El paquete cifs-utils se puede instalar con el administrador de paquetes en la distribución de Linux de su elección. 

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

* <a id="smb-client-reqs"></a>**Comprender los requisitos de cliente de SMB.**  
    Azure Files se puede montar ya sea a través de SMB 2.1 o de SMB 3.0. Para las conexiones procedentes de clientes locales o de otras regiones de Azure, Azure Files rechazará SMB 2.1 (o SMB 3.0 sin cifrado). Si *Se requiere transferencia segura* está habilitado para una cuenta de almacenamiento, Azure Files solo permitirá las conexiones que usen SMB 3.0 con cifrado.
    
    La compatibilidad con el cifrado de SMB 3.0 se introdujo en el kernel de Linux versión 4.11 y se ha modificado para poder utilizarla con versiones anteriores del kernel para las distribuciones más populares de Linux. En el momento de la publicación de este documento, las siguientes distribuciones de la galería de Azure admiten esta característica:

    - Ubuntu Server 16.04+
    - openSUSE 42.3+
    - SUSE Linux Enterprise Server 12 SP3+
    
    Si la distribución de Linux no aparece aquí, puede comprobar la versión del kernel de Linux con el siguiente comando:

    ```
    uname -r
    ```

* **Decidir sobre los permisos de archivo o directorio del recurso compartido montado**: en los ejemplos siguientes, se usa el permiso `0777` para proporcionar permisos de lectura, escritura y ejecución a todos los usuarios. Puede reemplazarlos por otros [permisos chmod](https://en.wikipedia.org/wiki/Chmod), según prefiera. 

* **Nombre de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesita el nombre de la cuenta de almacenamiento.

* **Clave de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesita la clave principal (o secundaria) de la cuenta de almacenamiento. Actualmente no se admiten claves SAS para el montaje.

* **Asegurarse de que el puerto 445 está abierto**: SMB se comunica a través del puerto TCP 445, así que compruebe que el firewall no bloquea el puerto TCP 445 en la máquina cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montaje del recurso compartido de archivos de Azure a petición con `mount`
1. **[Instale el paquete cifs-utils correspondiente a su distribución de Linux](#install-cifs-utils)**.

2. **Cree una carpeta para el punto de montaje**: se puede crear una carpeta para un punto de montaje en cualquier lugar del sistema de archivos pero es una convención común crearla en la carpeta `/mnt`. Por ejemplo: 

    ```
    mkdir /mnt/MyAzureFileShare
    ```

3. **Use el comando mount para montar el recurso compartido de archivos de Azure**: no olvide reemplazar `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` y `<mount-point>` por la información correcta para su entorno. Si la distribución de Linux es compatible con SMB 3.0 con cifrado (consulte [Comprender los requisitos de cliente de SMB](#smb-client-reqs) para más información), utilice `3.0` para `<smb-version>`. Para las distribuciones de Linux que no sean compatibles con SMB 3.0 con cifrado, use `2.1` para `<smb-version>`. Tenga en cuenta que solo se puede montar un recurso compartido de archivos de Azure fuera de una región de Azure (incluidos en local o en una región distinta de Azure) con SMB 3.0. 

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Cuando haya terminado de usar el recurso compartido de archivos de Azure, puede usar `sudo umount <mount-point>` para desmontarlo.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Creación de un punto de montaje persistente para el recurso compartido de archivos de Azure con `/etc/fstab`
1. **[Instale el paquete cifs-utils correspondiente a su distribución de Linux](#install-cifs-utils)**.

2. **Cree una carpeta para el punto de montaje**: se puede crear una carpeta para un punto de montaje en cualquier lugar del sistema de archivos pero es una convención común crearla en la carpeta `/mnt`. Siempre que la cree, tenga en cuenta la ruta de acceso absoluta de la carpeta. Por ejemplo, el siguiente comando crea una nueva carpeta en `/mnt` (la ruta de acceso es una ruta de acceso absoluta).

    ```
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Use este comando para anexar la siguiente línea a `/etc/fstab`**: no olvide reemplazar `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` y `<mount-point>` por la información correcta para su entorno. Si la distribución de Linux es compatible con SMB 3.0 con cifrado (consulte [Comprender los requisitos de cliente de SMB](#smb-client-reqs) para más información), utilice `3.0` para `<smb-version>`. Para las distribuciones de Linux que no sean compatibles con SMB 3.0 con cifrado, use `2.1` para `<smb-version>`. Tenga en cuenta que solo se puede montar un recurso compartido de archivos de Azure fuera de una región de Azure (incluidos en local o en una región distinta de Azure) con SMB 3.0. 

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Puede usar `sudo mount -a` para montar el recurso compartido de archivos de Azure después de editar `/etc/fstab` en lugar de reiniciar el sistema.

## <a name="feedback"></a>Comentarios
Usuarios de Linux: nos gustaría conocer su opinión.

El grupo de usuarios de Azure Files para Linux ofrece un foro donde puede compartir sus comentarios a medida que evalúa y adopta File Storage en Linux. Envíe un correo electrónico a [Usuarios de Azure Files para Linux](mailto:azurefileslinuxusers@microsoft.com) para unirse al grupo de usuarios.

## <a name="next-steps"></a>pasos siguientes
Consulte los vínculos siguientes para más información acerca Azure Files.
* [Introducción a Azure Files](storage-files-introduction.md)
* [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
* [P+F](../storage-files-faq.md)
* [Solución de problemas](storage-troubleshoot-linux-file-connection-problems.md)