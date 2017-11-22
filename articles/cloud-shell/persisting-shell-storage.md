---
title: Persistencia de archivos para Bash en Azure Cloud Shell | Microsoft Docs
description: "Tutorial de cómo Bash en Cloud Shell conserva archivos."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: a04342f77126afe1e0a2707f1d213e0a8eb5825d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Funcionamiento de Bash en el almacenamiento de Cloud Shell 
Bash en Cloud Shell conserva los archivos a través de los métodos siguientes: 
* Creación de una imagen de disco del directorio `$Home` para hacer persistente todo el contenido dentro del directorio. La imagen de disco se guarda en su recurso compartido de archivos especificado como `acc_<User>.img` en `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` y sincroniza automáticamente los cambios. 
* Montaje del recurso compartido de archivos especificado como `clouddrive` en el directorio `$Home` para la interacción directa del recurso compartido de archivos. `/Home/<User>/clouddrive` se asigna a `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos los archivos en el directorio `$Home`, como las claves de SSH, se conservan en la imagen de disco de usuario almacenada en el recurso compartido de archivos montado. Ponga en práctica los procedimientos recomendados correspondientes para conservar la información en el directorio `$Home` y en el recurso compartido de archivos montado.

## <a name="use-the-clouddrive-command"></a>Uso del comando `clouddrive`
Con Bash en Cloud Shell, puede ejecutar un comando denominado `clouddrive`, lo que permite actualizar manualmente el recurso compartido de archivos que está montado en Cloud Shell.
![Ejecución del comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montaje de un nuevo clouddrive

### <a name="prerequisites-for-manual-mounting"></a>Requisitos previos para el montaje manual
Puede actualizar el recurso compartido de archivos que esté asociada con Cloud Shell mediante el uso del comando `clouddrive mount`.

Si se monta un recurso compartido de archivos existente, las cuentas de almacenamiento tienen que ser:
* De almacenamiento con redundancia local o de almacenamiento con redundancia geográfica para que admitan recursos compartidos de archivos.
* Deben ubicarse en su región asignada. Al incorporarse, la región a la que está asignado aparece en el nombre del grupo de recursos `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>El comando de montaje clouddrive

> [!NOTE]
> Si monta un recurso compartido de archivos nuevo, se crea una imagen de usuario para el directorio `$Home`. La imagen `$Home` anterior se mantiene en el recurso compartido de archivos anterior.

Ejecute el comando `clouddrive mount` con los parámetros siguientes:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver más detalles, ejecute `clouddrive mount -h`, tal y como se muestra aquí:

![Ejecución del comando "clouddrive"](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Desmontaje de clouddrive
Puede desmontar un recurso compartido de archivos montado en Cloud Shell en cualquier momento. Como Cloud Shell requiere que se use un recurso compartido de archivos, se le pedirá crear y montar otro recurso compartido de archivos en la sesión siguiente.

1. Ejecute `clouddrive unmount`.
2. Reconozca y confirme los mensajes.

El recurso compartido de archivos seguirá existiendo a menos que se elimine manualmente. Cloud Shell dejará de buscar este recurso compartido de archivos en sesiones posteriores. Para ver más detalles, ejecute `clouddrive unmount -h`, tal y como se muestra aquí:

![Ejecución del comando "clouddrive amount"](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Aunque la ejecución de este comando no eliminará ningún recurso, al eliminar manualmente el grupo de recursos, la cuenta de almacenamiento o el recurso compartido de archivos asignados a Cloud Shell, borrará la imagen de disco del directorio `$Home` y los archivos del recurso compartido de archivos. Esta operación no se puede deshacer.

## <a name="list-clouddrive"></a>Enumeración de `clouddrive`
Para descubrir qué recurso compartido de archivos se monta como `clouddrive` ejecute el comando `df`. 

La ruta de acceso de archivo a clouddrive mostrará el recurso compartido de archivos y el nombre de la cuenta de almacenamiento en la dirección URL. Por ejemplo: `//storageaccountname.file.core.windows.net/filesharename`

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

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Pasos siguientes
[Guía de inicio rápido de Bash en Cloud Shell](quickstart.md) <br>
[Información sobre el almacenamiento de Microsoft Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Información sobre las etiquetas de Storage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
