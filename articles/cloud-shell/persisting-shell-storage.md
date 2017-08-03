---
title: "Persistencia de archivos en Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Tutorial de cómo Azure Cloud Shell persiste archivos."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 9f5883a2a611b986aa305087084d05d6fab1ab7d
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Persistencia de archivos en Azure Cloud Shell
Al principio, Azure Cloud Shell solicita su suscripción para crear una cuenta de almacenamiento LRS y un recurso compartido de archivos de Azure para usted. La suscripción debe tener acceso para crear una cuenta de almacenamiento a fin de obtener acceso a Cloud Shell.

![](media/storage-prompt.png)

## <a name="how-it-works"></a>Cómo funciona
### <a name="three-resources-will-be-created-on-your-behalf-in-a-supported-region-nearest-to-you"></a>Se crearán tres recursos en su nombre en la región admitida más cercana a usted:
1. Grupo de recursos llamado: `cloud-shell-storage-<region>`
2. Cuenta de almacenamiento llamada: `cs-uniqueGuid`
3. Recurso compartido de archivos llamado: `cs-<user>-<domain>-com-uniqueGuid`

Este recurso compartido de archivos se montará como `clouddrive` en el directorio $Home. Este recurso compartido de archivos también se usa para almacenar una imagen de 5 GB creada para usted que se actualiza automáticamente y hace persistente el directorio $Home. Se trata de una acción única y se monta automáticamente para sesiones posteriores.

### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell hace persistentes los archivos con los dos métodos mostrados a continuación:
1. Cree una imagen de disco del directorio $Home para hacer persistentes los archivos de $Home. Esta imagen de disco se guarda en su recurso compartido de archivos especificado como `acc_<User>.img` en `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`

2. Monte el recurso compartido de archivos especificado como `clouddrive` en el directorio $Home para la interacción directa del recurso compartido de archivos. 
`/Home/<User>/clouddrive` se asigna a `fileshare.storage.windows.net/fileshare`.
 
> [!Note]
> Todos los archivos en el directorio $Home, como las claves de SSH, se conservan en la imagen de disco de usuario almacenada en el recurso compartido de archivos montado. Ponga en práctica los procedimientos recomendados correspondientes para conservar la información en el directorio $Home y en el recurso compartido de archivos montado.

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir la creación de recursos con una directiva de recursos de Azure
La cuenta de almacenamiento tiene la etiqueta "ms-resource-usage:azure-cloud-shell". Si su organización quiere impedir que los usuarios creen cuentas de almacenamiento para Cloud Shell, cree una [directiva de recursos de Azure para etiquetas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) que se desencadene mediante esta clave y valor específicos.

## <a name="using-clouddrive"></a>Uso de clouddrive
Cloud Shell permite a los usuarios ejecutar un comando llamado `clouddrive` que permite actualizar manualmente el recurso compartido de archivos montado en Cloud Shell.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montaje de un nuevo clouddrive

### <a name="pre-requisites-for-manual-mounting"></a>Requisitos previos para el montaje manual
Cloud Shell creará una cuenta de almacenamiento y un recurso compartido de archivos para usted al iniciar la primera vez. Sin embargo, puede actualizar el recurso compartido de archivos con el comando `clouddrive mount`.

Requisitos de las cuentas de almacenamiento si se monta un recurso compartido de archivos existente:
1. Deben ser LRS o GRS para admitir los recursos compartidos de archivos.
2. Deben ubicarse en su región asignada. Al incorporarse, la región a la que está asignado aparece en el nombre del grupo de recursos `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Regiones de almacenamiento admitidas
Azure Files debe residir en la misma región como la máquina en la que se monta. Las máquinas de Cloud Shell existen en las regiones siguientes:
|Ámbito|Region|
|---|---|
|América|Este de EE. UU., centro-sur de EE. UU. y oeste de EE. UU.|
|Europa|Norte de Europa y Oeste de Europa|
|Asia Pacífico|India central, Sudeste Asiático|

### <a name="mount-command"></a>Comando de montaje

> [!NOTE]
> Si se monta un nuevo recurso compartido de archivos, se creará una nueva imagen de usuario para el directorio $Home, ya que la imagen de $Home anterior se conserva en el recurso compartido de archivos anterior.

1. Ejecute `clouddrive mount` con los parámetros siguientes <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver más detalles, ejecute `clouddrive mount -h`: <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Desmontaje de clouddrive
Puede desmontar un recurso compartido de archivos montado en Cloud Shell en cualquier momento. Sin embargo, Cloud Shell requiere un recurso compartido de archivos montado, de modo que se le pedirá que cree y monte un nuevo recurso compartido de archivos en la próxima sesión si se quita.

Para desasociar un recurso compartido de archivos de Cloud Shell:
1. Ejecute `clouddrive unmount`
2. Reconozca y confirme los mensajes

El recurso compartido de archivos seguirá existiendo a menos que se elimine manualmente. Cloud Shell dejará de buscar este recurso compartido de archivos en sesiones posteriores.

Para ver más detalles, ejecute `clouddrive mount -h`: <br>
![](media/unmount-h.png)

> [!WARNING]
> Este comando no eliminará ningún recurso. Sin embargo, al eliminar manualmente el grupo de recursos, la cuenta de almacenamiento o el recurso compartido de archivos asignados a Cloud Shell, se borrarán la imagen de disco del directorio $Home y los archivos del recurso compartido de archivos. Esto no se puede deshacer.

## <a name="list-clouddrive"></a>Enumeración de clouddrive
Para descubrir qué recurso compartido de archivos se monta como `clouddrive`:
1. Ejecute `df` 

La ruta de acceso de archivo mostrará el recurso compartido de archivos y el nombre de la cuenta de almacenamiento en la dirección URL.

`//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>Transferir archivos locales a Cloud Shell
El directorio `clouddrive` se sincroniza con la hoja de almacenamiento de Azure Portal. Use esto para transferir archivos locales a o desde el recurso compartido de archivos. La actualización de archivos en Cloud Shell se refleja en la GUI de File Storage al actualizarse las hojas.

### <a name="download-files"></a>Descarga de archivos
![](media/download.gif)
1. Vaya al recurso compartido de archivos montado.
2. Seleccione el archivo de destino en el portal
3. Seleccione "Descargar".

### <a name="upload-files"></a>Carga de archivos
![](media/upload.gif)
1. Vaya al recurso compartido de archivos montado.
2. Seleccione "Cargar".
3. Seleccione el archivo que quiera cargar.
4. Confirme la carga.

Ahora debería ver el archivo accesible en el directorio clouddrive de Cloud Shell.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md) <br>
[Información sobre Azure File Storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Información sobre las etiquetas de Storage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
