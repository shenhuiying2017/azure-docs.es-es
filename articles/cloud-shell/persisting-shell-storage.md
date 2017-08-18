---
title: "Persistencia de archivos en Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Tutorial de cómo Azure Cloud Shell persiste archivos."
services: 
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
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 26428ad0d3acda959235ffa780294154ba61bca5
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>Persistencia de archivos en Azure Cloud Shell
Cloud Shell aprovecha Azure File Storage para conservar los archivos entre sesiones.

## <a name="set-up-a-clouddrive-file-share"></a>Configuración de un recurso compartido de archivos `clouddrive`
En el primer inicio, Cloud Shell le pedirá que asocie un recurso compartido de archivos nuevo o existente para conservar los archivos entre sesiones.

### <a name="create-new-storage"></a>creación de nuevo almacenamiento

Al usar la configuración básica y seleccionar solo una suscripción, Cloud Shell crea tres recursos en su nombre en la región admitida que esté más próxima:
* Grupos de recursos: `cloud-shell-storage-<region>`
* Cuenta de almacenamiento: `cs-uniqueGuid`
* Recurso compartido de archivos: `cs-<user>-<domain>-com-uniqueGuid`

![La configuración de la suscripción](media/basic-storage.png)

El recurso compartido de archivos se monta como `clouddrive` en su directorio `$Home`. Este recurso compartido de archivos también se usa para almacenar una imagen de 5 GB creada para usted, que se actualiza automáticamente y se conserva en el directorio `$Home`. Se trata de una acción única y el recurso compartido de archivos se monta automáticamente en sesiones posteriores.

### <a name="use-existing-resources"></a>Uso de recursos existentes

Mediante la opción avanzada puede asociar recursos existentes. Cuando aparezca el mensaje del programa de instalación, seleccione **Mostrar configuración avanzada** para ver otras opciones. Los recursos compartidos de archivos existentes recibirán una imagen de usuario de 5 GB para conservar el directorio `$Home`. Las listas desplegables se filtran para la región asignada de Cloud Shell y las cuentas de almacenamiento con redundancia local y de almacenamiento con redundancia geográfica.

![La configuración de grupo de recursos](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir la creación de recursos con una directiva de recursos de Azure
Las cuentas de almacenamiento creadas en Cloud Shell se etiquetan con `ms-resource-usage:azure-cloud-shell`. Si quiere impedir que los usuarios creen cuentas de almacenamiento en Cloud Shell, cree una [directiva de recursos de Azure para etiquetas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) que se desencadene mediante esta etiqueta específica.

## <a name="how-cloud-shell-works"></a>Cómo funciona Cloud Shell
Cloud Shell conserva los archivos a través de los métodos siguientes:
* Creación de una imagen de disco del directorio `$Home` para hacer persistente todo el contenido dentro del directorio. La imagen de disco se guarda en su recurso compartido de archivos especificado como `acc_<User>.img` en `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` y sincroniza automáticamente los cambios.

* Montaje del recurso compartido de archivos especificado como `clouddrive` en el directorio `$Home` para la interacción directa del recurso compartido de archivos. `/Home/<User>/clouddrive` se asigna a `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos los archivos en el directorio `$Home`, como las claves de SSH, se conservan en la imagen de disco de usuario almacenada en el recurso compartido de archivos montado. Ponga en práctica los procedimientos recomendados correspondientes para conservar la información en el directorio `$Home` y en el recurso compartido de archivos montado.

## <a name="use-the-clouddrive-command"></a>Uso del comando `clouddrive`
Con Cloud Shell, puede ejecutar un comando denominado `clouddrive`, lo que permite actualizar manualmente el recurso compartido de archivos que está montado en Cloud Shell.
![Ejecución del comando "clouddrive"](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Montaje de un nuevo `clouddrive`

### <a name="prerequisites-for-manual-mounting"></a>Requisitos previos para el montaje manual
Puede actualizar el recurso compartido de archivos que esté asociada con Cloud Shell mediante el uso del comando `clouddrive mount`.

Si se monta un recurso compartido de archivos existente, las cuentas de almacenamiento tienen que ser:
* De almacenamiento con redundancia local o de almacenamiento con redundancia geográfica para que admitan recursos compartidos de archivos.
* Deben ubicarse en su región asignada. Al incorporarse, la región a la que está asignado aparece en el nombre del grupo de recursos `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Regiones de almacenamiento admitidas
Los archivos de Azure tienen que residir en la misma región que la máquina de Cloud Shell en la que se montan. Las máquinas de Cloud Shell existen en las regiones siguientes:
|Ámbito|Region|
|---|---|
|América|Este de EE. UU., centro-sur de EE. UU. y oeste de EE. UU.|
|Europa|Norte de Europa y Oeste de Europa|
|Asia Pacífico|India central, Sudeste Asiático|

### <a name="the-clouddrive-mount-command"></a>El comando `clouddrive mount`

> [!NOTE]
> Si está montando un nuevo recurso compartido de archivos, se creará una nueva imagen de usuario para el directorio `$Home`, ya que la imagen de `$Home` anterior se conserva en el recurso compartido de archivos anterior.

Ejecute el comando `clouddrive mount` con los parámetros siguientes:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver más detalles, ejecute `clouddrive mount -h`, tal y como se muestra aquí:

![Ejecución del comando "clouddrive"](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Desmontar `clouddrive`
Puede desmontar un recurso compartido de archivos montado en Cloud Shell en cualquier momento. Sin embargo, ya que Cloud Shell requiere un recurso compartido de archivos montado, se le pedirá que cree y monte un nuevo recurso compartido de archivos en la próxima sesión si se ha quitado.

Para quitar un recurso compartido de archivos de Cloud Shell:
1. Ejecute `clouddrive unmount`.
2. Reconozca y confirme los avisos.

El recurso compartido de archivos seguirá existiendo a menos que se elimine manualmente. Cloud Shell dejará de buscar este recurso compartido de archivos en sesiones posteriores.

Para ver más detalles, ejecute `clouddrive unmount -h`, tal y como se muestra aquí:

![Ejecución del comando "clouddrive amount"](media/unmount-h.png)

> [!WARNING]
> Aunque la ejecución de este comando no eliminará ningún recurso, al eliminar manualmente el grupo de recursos, la cuenta de almacenamiento o el recurso compartido de archivos asignados a Cloud Shell, borrará la imagen de disco del directorio `$Home` y los archivos del recurso compartido de archivos. Esta operación no se puede deshacer.

## <a name="list-clouddrive-file-shares"></a>Listado de Recursos compartidos de archivos de `clouddrive`
Para descubrir qué recurso compartido de archivos se monta como `clouddrive` ejecute el siguiente comando `df`: 

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

## <a name="transfer-local-files-to-cloud-shell"></a>Transferir archivos locales a Cloud Shell
El directorio `clouddrive` se sincroniza con la hoja de almacenamiento de Azure Portal. Use esta hoja para transferir archivos locales al recurso compartido de archivos y viceversa. La actualización de archivos en Cloud Shell se refleja en la GUI de File Storage al actualizarse las hojas.

### <a name="download-files"></a>Descarga de archivos

![Lista de archivos locales](media/download.png)
1. En Azure Portal, vaya al recurso compartido de archivos montados.
2. Seleccione el archivo de destino.
3. Seleccione el botón **Descargar**.

### <a name="upload-files"></a>Carga de archivos

![Archivos locales para cargarse](media/upload.png)
1. Vaya al recurso compartido de archivos montado.
2. Seleccione el botón **Cargar**.
3. Seleccione el archivo o archivos que desea cargar.
4. Confirmación de la actualización.

Ahora debería ver los archivos accesibles en el directorio `clouddrive` en Cloud Shell.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md) <br>
[Información sobre Azure File Storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Información sobre las etiquetas de Storage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

