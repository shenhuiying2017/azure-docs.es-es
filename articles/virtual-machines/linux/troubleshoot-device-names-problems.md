---
title: "Se cambian los nombres de dispositivo de máquina virtual Linux en Azure | Microsoft Docs"
description: "Se explica por qué se cambian los nombres de dispositivo y se proporcionan soluciones para este problema."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>Solución de problemas: Se cambian los nombres de dispositivo de máquina virtual de Linux

En este artículo se explica por qué se cambian los nombres de dispositivo después de reiniciar una máquina virtual (VM) de Linux o volver a adjuntar los discos. También se proporciona la solución para este problema.

## <a name="symptom"></a>Síntoma

Es posible que experimente los siguientes problemas al ejecutar máquinas virtuales Linux en Microsoft Azure.

- La máquina virtual no puede arrancar después de un reinicio.

- Si los discos de datos se desasocian y se vuelven a adjuntar, se cambian los nombres de dispositivo de los discos.

- Se produce un error en una aplicación o un script que hace referencia a un disco con el nombre de dispositivo. Descubre que se cambia el nombre de dispositivo del disco.

## <a name="cause"></a>Causa

No se garantiza que las rutas de acceso de dispositivo en Linux sean coherentes entre reinicios. Los nombres de dispositivo constan de números principales (letras) y secundarios.  Cuando el controlador del dispositivo de almacenamiento de Linux detecta un nuevo dispositivo, le asigna números de dispositivo principales y secundarios del intervalo disponible. Cuando se quita un dispositivo, los números de dispositivo se liberan para ser reutilizados más adelante.

El problema se produce porque el análisis de dispositivos en Linux programado por el subsistema de SCSI se realiza de forma asincrónica. La nomenclatura de rutas de acceso de dispositivo final puede variar entre reinicios. 

## <a name="solution"></a>Solución

Para resolver este problema, use nombres persistentes. Hay cuatro métodos de nomenclatura persistente: por etiqueta de sistema de archivos, por UUID, por Id. y por ruta de acceso. Se recomiendan los métodos de etiqueta del sistema de archivos y UUID para las máquinas virtuales Linux de Azure. 

La mayoría de las distribuciones también proporcionan las opciones de fstab **nofail** o **nobootwait**. Estas opciones permiten que el sistema arranque incluso si no se monta el disco en el inicio. Consulte la documentación de la distribución para obtener más información sobre estos parámetros. Para más información sobre cómo configurar una máquina virtual de Linux para que use un UUID cuando se agrega un disco de datos, vea [Conexión a la máquina virtual con Linux para montar el nuevo disco](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Cuando se instala el agente Linux de Azure en una máquina virtual, se usan reglas de Udev para crear un conjunto de vínculos simbólicos en **/dev/disk/azure**. Estas reglas de Udev pueden ser usadas por aplicaciones y scripts para identificar los discos que están conectados a la máquina virtual, su tipo y el LUN.

## <a name="more-information"></a>Más información

### <a name="identify-disk-luns"></a>Identificar LUN de disco

Una aplicación puede usar LUN para encontrar todos los discos conectados y construir los vínculos simbólicos. El agente Linux de Azure ahora incluye reglas de udev que configuran vínculos simbólicos desde un LUN a los dispositivos, como se muestra a continuación:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

También se puede recuperar información de LUN desde el invitado de Linux mediante lsscsi o una herramienta similar como se indica a continuación.

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Esta información de LUN de invitado se puede usar con los metadatos de suscripción de Azure para identificar la ubicación en Azure Storage del VHD que almacena los datos de la partición. Por ejemplo, use la interfaz de la línea de comandos de az:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Detectar UUID del sistema de archivos mediante blkid

Un script o una aplicación puede leer la salida de blkid, o fuentes de información similares, y crear vínculos simbólicos en **/dev** para su uso. El resultado mostrará los UUID de todos los discos conectados a la máquina virtual y el archivo de dispositivo al que están asociados:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Las reglas de udev waagent crean un conjunto de vínculos simbólicos en **/dev/disk/azure**:


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


La aplicación puede usar esta información para identificar el dispositivo de disco de arranque y el disco de recursos (efímero). En Azure, las aplicaciones deben hacer referencia a **/dev/disk/azure/root-part1** o **/dev/disk/azure-resource-part1** para detectar estas particiones.

Si hay particiones adicionales en la lista de blkid, residen en un disco de datos. Las aplicaciones puedan mantener el UUID de estas particiones y usar una ruta de acceso como la siguiente para detectar el nombre de dispositivo en tiempo de ejecución:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Obtener las reglas de almacenamiento de Azure más recientes

Para obtener las reglas de almacenamiento de Azure más recientes, ejecute los siguientes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


Para más información, consulte los siguientes artículos.

- [Ubuntu: Using UUID](https://help.ubuntu.com/community/UsingUUID) (Ubuntu: Uso de UUID)

- [Red Hat: Persistent Naming](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html) (Red Hat: Nomenclatura persistente)

- [Linux: What UUIDs can do for you](https://www.linux.com/news/what-uuids-can-do-you) (Linux: ¿Qué pueden hacer los UUID por usted?)

- [Udev: Introduction to Device Management In Modern Linux System](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) (UDev: Introducción a la administración de dispositivos en sistemas Linux modernos)


