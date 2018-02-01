---
title: "Cómo montar Azure Blob Storage como sistema de archivos en Linux | Microsoft Docs"
description: Montar un contenedor de Azure Blob Storage con FUSE en Linux
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 299b96c783fb3606347bb448d00d44f0071da429
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>Cómo montar el almacenamiento de blobs como sistema de archivos con blobfuse (versión preliminar)

## <a name="overview"></a>Información general
[Blobfuse](https://github.com/Azure/azure-storage-fuse) es un controlador del sistema de archivos virtual para Azure Blob Storage, que permite obtener acceso a los datos blob en bloque existentes en la cuenta de almacenamiento a través del sistema de archivos Linux. Azure Blob Storage es un servicio de almacenamiento de objetos y, por lo tanto, no tiene un espacio de nombres jerárquico. Blobfuse proporciona el espacio de nombres mediante el esquema de directorio virtual con el uso de la barra oblicua "/" como delimitador.  

En esta guía, se muestra cómo usar blobfuse, montar un contenedor de almacenamiento de blobs en Linux y obtener acceso a los datos. Para obtener más información sobre blobfuse, lea los detalles en [el repositorio de blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse no garantiza el cumplimiento al 100 % con POSIX ya que, simplemente, convierte las solicitudes en [API de REST de Blob](https://docs.microsoft.com/en-us/rest/api/storageservices/blob-service-rest-api). Por ejemplo, las operaciones de cambio de nombre son atómicas en POSIX, pero no en blobfuse.
> Para obtener una lista completa de las diferencias entre un sistema de archivos nativo y blobfuse, visite [el repositorio de código fuente de blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalar blobfuse en Linux
Los archivos binarios de blobfuse están disponibles en [los repositorios de software de Microsoft para Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Para instalar blobfuse, configure uno de estos repositorios.

### <a name="configure-the-microsoft-package-repository"></a>Configurar el repositorio de paquetes de Microsoft
Configure el [repositorio de paquetes de Linux para productos de Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Por ejemplo, en una distribución Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Del mismo modo, cambie la dirección URL a `.../rhel/7/...` para que señale a una distribución Enterprise Linux 7.

Otro ejemplo en Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Del mismo modo, cambie la dirección URL a `.../ubuntu/16.04/...` para que señale a una distribución Ubuntu 16.04.

### <a name="install-blobfuse"></a>Instalar blobfuse

En una distribución Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

En una distribución Enterprise Linux:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparación del montaje
Blobfuse requiere una ruta de acceso temporal en el sistema de archivos para almacenar en búfer y en caché los archivos abiertos, lo que ayuda a proporcionar un rendimiento similar al nativo. Para esta ruta de acceso temporal, elija el disco con mayor rendimiento o use un disco RAM para obtener el rendimiento óptimo. 

> [!NOTE]
> Blobfuse almacena todo el contenido de los archivos abiertos en la ruta de acceso temporal. Asegúrese de que tiene espacio suficiente para dar cabida a todos los archivos abiertos. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Usar un disco RAM para la ruta de acceso temporal
En el ejemplo siguiente, se crea un disco RAM de 16 GB, así como un directorio para blobfuse. Elija el tamaño según sus necesidades. Este disco RAM permite que blobfuse abra archivos de hasta 16 GB de tamaño. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Usar una SSD para la ruta de acceso temporal
En Azure, puede usar los discos efímeros (SSD) disponibles en las máquinas virtuales para proporcionar un búfer de baja latencia para blobfuse. En las distribuciones Ubuntu, este disco efímero se monta en "/mnt" mientras que, en las distribuciones RedHat y CentOS,+ se monta en "/mnt/resource/".

Asegúrese de que el usuario tenga acceso a la ruta de acceso temporal:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurar las credenciales de la cuenta de Storage
Blobfuse requiere que sus credenciales se almacenen en un archivo de texto con el formato siguiente: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Después de crear este archivo, asegúrese de restringir el acceso para que ningún otro usuario pueda leerlo.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Crear un directorio vacío para el montaje
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montaje

> [!NOTE]
> Para obtener una lista completa de opciones de montaje, consulte [el repositorio de blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Para montar blobfuse, ejecute el siguiente comando con su usuario. Este comando monta el contenedor especificado en "/path/to/fuse_connection.cfg" en la ubicación "/mycontainer".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Ahora debería tener acceso a los blobs en bloque a través de las API normales del sistema de archivos. Tenga en cuenta que solo puede obtener acceso al directorio montado el usuario que lo monta, lo que protege el acceso. Si desea permitir el acceso a todos los usuarios, se puede montar con la opción ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>pasos siguientes

* [Página de inicio de blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Informe de problemas de blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

