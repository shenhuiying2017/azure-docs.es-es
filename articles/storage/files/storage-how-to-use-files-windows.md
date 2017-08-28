---
title: Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows | Microsoft Docs
description: Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 67b8e2e0039c8bc63f50f177e3c0d18b07df45e6
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Montaje de un recurso compartido de archivos de Azure y acceso al recurso compartido en Windows
[Azure File Storage](../storage-dotnet-how-to-use-files.md) es el sistema de archivos en la nube de Microsoft fácil de usar. Los recursos compartidos de archivos de Azure se pueden montar en Windows y Windows Server. En este artículo se muestran tres maneras diferentes para montar un recurso compartido de archivos de Azure en Windows: con la interfaz del explorador de archivos, a través de PowerShell y mediante el símbolo del sistema. 

Para montar un recurso compartido de archivos de Azure fuera de la región de Azure en la que se hospeda, bien sea en local o en una región distinta de Azure, el sistema operativo debe admitir SMB 3.0. 

Un recurso compartido de archivos de Azure se puede montar en un equipo Windows tanto en local como en una máquina virtual de Azure en función de la versión del sistema operativo. La tabla siguiente lo describe 

| Versión de Windows        | Versión de SMB |Se puede montar en una máquina virtual de Azure|Se puede montar en un entorno local|
|------------------------|-------------|---------------------|---------------------|
| Windows 7              | SMB 2.1     | Sí                 | No                  |
| Windows Server 2008 R2 | SMB 2.1     | Sí                 | No                  |
| Windows 8              | SMB 3.0     | Sí                 | Sí                 |
| Windows Server 2012    | SMB 3.0     | Sí                 | Sí                 |
| Windows Server 2012 R2 | SMB 3.0     | Sí                 | Sí                 |
| Windows 10             | SMB 3.0     | Sí                 | Sí                 |

> [!Note]  
> Siempre se recomienda disponer de la KB más reciente para su versión de Windows.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Requisitos previos para el montaje de un recurso compartido de archivos de Azure en Windows 
* **Nombre de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará el nombre de la cuenta de almacenamiento.

* **Clave de la cuenta de almacenamiento**: para montar un recurso compartido de archivos de Azure, necesitará la clave principal (o secundaria) de la cuenta de almacenamiento. Actualmente no se admiten claves SAS para el montaje.

* **Asegúrese de que el puerto 445 está abierto**: Azure File Storage usa el protocolo SMB. SMB se comunica a través del puerto TCP 445: compruebe que el firewall no bloquea el puerto TCP 445 en el equipo cliente.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Montaje del recurso compartido de archivos de Azure con el explorador de archivos
> [!Note]  
> Tenga en cuenta que las instrucciones siguientes se muestran en Windows 10 y pueden variar ligeramente en las versiones anteriores. 

1. **Abra el Explorador de archivos**: esto puede hacerse desde el menú Inicio o bien presionando las teclas Win + E.

2. **Desplácese hasta el elemento "Este PC" en el lado izquierdo de la ventana. Esta operación cambiará los menús disponibles en la barra de herramientas. En el menú Equipo, seleccione "Conectar a unidad de red"**.
    
    ![Captura de pantalla del menú desplegable "Conectar a unidad de red"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Copie la ruta de acceso UNC desde el panel "Conectar" de Azure Portal**: una descripción detallada de cómo buscar esta información puede encontrarse [aquí](storage-how-to-use-files-portal.md#connect-to-file-share).

    ![Ruta de acceso UNC en el panel Conectar de Azure File Storage](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **Seleccione la letra de unidad y escriba la ruta de acceso UNC.** 
    
    ![Captura de pantalla del cuadro de diálogo "Conectar a unidad de red"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Utilice el nombre de la cuenta de almacenamiento prefijada con `Azure\` como el nombre de usuario y la clave de la cuenta de almacenamiento como contraseña.**
    
    ![Captura de pantalla del cuadro de diálogo credenciales de red](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Uso del recurso compartido de archivos de Azure**.
    
    ![El recurso compartido de archivos de Azure ahora está montado](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Cuando esté listo para desmontar (o desconectar) el recurso compartido de archivos de Azure, puede hacerlo haciendo clic en la entrada para el recurso compartido en "Ubicaciones de red" en el explorador de archivos y seleccionando "Desconectar"**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Montaje del recurso compartido de archivos de Azure con PowerShell
1. **Use el siguiente comando para montar el recurso compartido de archivos de Azure**: no olvide reemplazar `<storage-account-name>`, `<share-name>`, `<storage-account-key>` y `<desired-drive-letter>` con la información correcta.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Uso del recurso compartido de archivos de Azure**.

3. **Cuando haya terminado, puede desmontar el recurso compartido de archivos de Azure mediante el siguiente comando**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> Puede usar el parámetro `-Persist` en `New-PSDrive` para hacer visible el recurso compartido de archivos de Azure para el resto del sistema operativo durante el montaje.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Montaje del recurso compartido de archivos de Azure desde el símbolo del sistema
1. **Use el siguiente comando para montar el recurso compartido de archivos de Azure**: no olvide reemplazar `<storage-account-name>`, `<share-name>`, `<storage-account-key>` y `<desired-drive-letter>` con la información correcta.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Uso del recurso compartido de archivos de Azure**.

3. **Cuando haya terminado, puede desmontar el recurso compartido de archivos de Azure mediante el siguiente comando**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Puede configurar el recurso compartido de archivos de Azure para conectar automáticamente al reiniciar haciendo conservar las credenciales de Windows. El siguiente comando conservará las credenciales:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para obtener más información acerca de Azure File Storage.

* [Preguntas más frecuentes](../storage-files-faq.md)
* [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>Artículos y vídeos conceptuales
* [Azure File Storage: un sistema de archivos SMB en la nube sin dificultades para Windows y Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Uso de Azure File Storage con Linux](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-file-storage"></a>Compatibilidad de herramientas con Azure File Storage
* [Uso de AzCopy con Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Uso de la CLI de Azure con Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Solución de problemas de Azure File Storage (Windows)](storage-troubleshoot-windows-file-connection-problems.md)
* [Solución de problemas de Azure File Storage (Linux)](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Publicaciones de blog
* [Azure File Storage ya está disponible de manera general](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [En el interior de Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (Introducción al servicio de archivos de Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migración de datos a Azure Files](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referencia
* [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

