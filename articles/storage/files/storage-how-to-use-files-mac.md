---
title: Montaje de un recurso compartido de archivos de Azure mediante SMB con macOS | Microsoft Docs
description: "Obtenga información acerca de cómo montar un recurso compartido de archivos de Azure mediante SMB con macOS."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 6e71a13f99160fdd310be1e9a59717c9fecbf35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montaje de un recurso compartido de archivos de Azure mediante SMB con macOS
[Azure Files](storage-files-introduction.md) es el servicio de Microsoft que permite crear y utilizar recursos compartidos de archivos en red en Azure utilizando el estándar del sector. Los recursos compartidos de archivos de Azure se pueden montar en macOS Sierra (10.12) y El Capitan (10.11). En este artículo se muestran dos maneras diferentes para montar un recurso compartido de archivos de Azure en macOS, con la interfaz de usuario de Finder y utilizando el Terminal.

> [!Note]  
> Antes de montar un recurso compartido de archivos de Azure con SMB, se recomienda deshabilitar la firma de paquetes SMB. De no hacerlo, puede producirse un rendimiento deficiente en el acceso al recurso compartido de archivos de Azure desde macOS. La conexión SMB será cifrada, por lo que esto no afecta a la seguridad de la conexión. Desde el terminal, los siguientes comandos deshabilitan firma del paquete SMB, como se describe en este [artículo de soporte técnico de Apple acerca de cómo deshabilitar la firma de paquetes SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Requisitos previos para el montaje de un recurso compartido de archivos de Azure en macOS
* **Nombre de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará el nombre de la cuenta de almacenamiento.

* **Clave de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará la clave principal (o secundaria) de la cuenta de almacenamiento. Actualmente no se admiten claves SAS para el montaje.

* **Asegúrese de que el puerto 445 está abierto**: SMB se comunica a través del puerto TCP 445. En el equipo cliente (Mac), compruebe que el firewall no bloquea el puerto TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montaje de un recurso compartido de archivos de Azure con Finder
1. **Abrir Finder**: Finder está abierto en macOS de forma predeterminada, pero puede asegurarse de sea la aplicación seleccionada haciendo clic en el "icono de cara de macOS" en el Dock:  
    ![Icono de cara de macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Seleccione "Conectar a servidor" en el menú "Ir"**: en la ruta de acceso UNC de los [requisitos previos](#preq), sustituya la doble barra diagonal inversa (`\\`) por `smb://` y todas las otras barras diagonales inversas (`\`) por barras diagonales (`/`). El vínculo debe ser similar al siguiente: ![Cuadro de diálogo "Conectar a servidor"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Utilice el nombre del recurso compartido y la clave de la cuenta de almacenamiento cuando se le solicite un nombre de usuario y contraseña**: al hacer clic en "Conectar" en el cuadro de diálogo "Conectar a servidor", se le pedirá el nombre de usuario y una contraseña (se rellenará automáticamente con el nombre de usuario de macOS). Puede guardar el nombre del recurso compartido y la clave de la cuenta de almacenamiento en la cadena de claves de macOS.

4. **Uso del recurso compartido de archivos de Azure**: después de sustituir el nombre del recurso compartido y la clave de la cuenta de almacenamiento por el nombre de usuario y contraseña, se montará el recurso compartido. Se puede utilizar como lo haría normalmente con una carpeta local o un recurso compartido de archivos, incluido arrastrar y soltar archivos en el recurso compartido de archivos:

    ![Captura de pantalla de un recurso compartido de archivos de Azure montado](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montaje de un recurso compartido de archivos de Azure con el Terminal
1. Reemplace `<storage-account-name>` por el nombre de la cuenta de almacenamiento. Proporcione la clave de cuenta de almacenamiento como contraseña cuando se le solicite. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Uso del recurso compartido de archivos de Azure**: el recurso compartido de archivos de Azure se montará en el punto de montaje especificado por el comando anterior.  

    ![Captura de pantalla del recurso compartido de archivos de Azure montado](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para más información acerca Azure Files.

* [Artículo de soporte técnico de Apple: cómo conectarse con uso compartido de archivos en el equipo Mac](https://support.apple.com/HT204445)
* [Preguntas más frecuentes](../storage-files-faq.md)
* [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solución de problemas en Linux](storage-troubleshoot-linux-file-connection-problems.md)    