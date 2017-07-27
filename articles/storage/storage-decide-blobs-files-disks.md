---
title: "Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Data Disks"
description: "Obtenga información sobre las distintas formas de almacenar datos y acceder a ellos en Azure para que le sea más fácil decidir qué la tecnología usar."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: e282a8a7bec1cb6e48110e7c9859a3a19ab8a11e
ms.contentlocale: es-es
ms.lasthandoff: 06/14/2017

---

# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-data-disks"></a>Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Data Disks

Microsoft Azure proporciona varias características en Azure Storage para almacenar los datos en la nube y tener acceso a ellos. En este artículo se describen Azure Files, Azure Blobs y Azure Data Disks, y está diseñado para ayudarle a elegir entre estas características.

## <a name="scenarios"></a>Escenarios

En la tabla siguiente se compara Azure Files, Azure Blobs y Azure Data Disks y se muestran los escenarios de ejemplo adecuados para cada uno.

| Característica | Descripción | Cuándo se deben usar |
|--------------|-------------|-------------|
| **Archivos de Azure** | Proporciona una interfaz SMB, bibliotecas de cliente y una [interfaz de REST](/rest/api/storageservices/file-service-rest-api) que permite el acceso desde cualquier lugar a archivos almacenados. | Cuando desee migrar mediante lift-and-shift una aplicación a la nube que ya usa las API del sistema de archivos nativo para compartir datos entre ella y otras aplicaciones que se ejecutan en Azure.<br/><br/>Cuando desee almacenar herramientas de desarrollo y depuración a las que es necesario acceder desde muchas máquinas virtuales. |
| **Azure Blobs** | Proporciona bibliotecas de cliente y una [interfaz de REST](/rest/api/storageservices/blob-service-rest-api) que permite que los datos no estructurados se almacenen y se acceda a ellos a gran escala en blobs en bloques. | Cuando desea que la aplicación admita escenarios de streaming y de acceso aleatorio.<br/><br/>Cuando desea poder tener acceso a datos de aplicación desde cualquier lugar. |
| **Azure Data Disks** | Proporciona bibliotecas de cliente y una [interfaz de REST](/rest/api/compute/virtualmachines/virtualmachines-create-or-update) que permite que los datos se almacenen persistentemente y se acceda a ellos desde un disco duro virtual conectado. | Cuando desea migrar mediante lift-and-shift aplicaciones que usan las API del sistema de archivos nativo para leer y escribir datos en discos persistentes.<br/><br/>Cuando desea almacenar datos a los que no se necesita acceder desde fuera de la máquina virtual a la que está conectado el disco. |

## <a name="comparison-files-and-blobs"></a>Comparación: Azure Files y Azure Blobs

En la tabla siguiente se compara Azure Files con Azure Blobs.  
  
||||  
|-|-|-|  
|**Atributo**|**Azure Blobs**|**Archivos de Azure**|  
|Opciones de durabilidad|LRS, ZRS y GRS (y RA-GRS para mayor disponibilidad)|LRS y GRS|  
|Accesibilidad|API de REST|API de REST<br /><br /> SMB 2.1 y SMB 3.0 (API del sistema de archivos estándar)|  
|Conectividad|API de REST: en todo el mundo|API de REST: en todo el mundo<br /><br /> SMB 2.1: dentro de región<br /><br /> SMB 3.0: en todo el mundo|  
|Extremos|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Directorios|Espacio de nombres plano|Objetos de directorio verdaderos|  
|Distingue mayúsculas de minúsculas en los nombres|Distingue mayúsculas de minúsculas|No distingue mayúsculas de minúsculas, pero las conserva|  
|Capacity|Contenedores de hasta 500 TB|Recursos compartidos de archivos de hasta 5 TB|  
|Rendimiento|Hasta 60 MB/s por blob en bloques|Hasta 60 MB/s por recurso compartido|  
|Tamaño de objeto|Hasta 200 GB por blob en bloques|Hasta 1 TB por archivo|  
|Capacidad facturada|En función de los bytes escritos|Según el tamaño de archivo|  
|Bibliotecas de clientes|Varios idiomas|Varios idiomas|  
  
## <a name="comparison-files-and-data-disks"></a>Comparación: Azure Files y Azure Data Disks

Azure Files complementa a Azure Data Disks. Solo se pueden conectar un disco de datos a una máquina virtual de Azure en cada momento. Los discos de datos son VHD de formato fijo almacenados como blobs en páginas en Azure Storage y los utiliza la máquina virtual para almacenar datos duraderos. Se puede acceder a los recursos compartidos de archivos de Azure Files de la misma manera que se accede al disco local (mediante API del sistema de archivos nativo) y se pueden compartir entre muchas máquinas virtuales.  
 
En la tabla siguiente se compara Azure Files con Azure Data Disks.  
 
||||  
|-|-|-|  
|**Atributo**|**Azure Data Disks**|**Archivos de Azure**|  
|Scope|Exclusivo para una máquina virtual individual|Acceso compartido entre varias máquinas virtuales|  
|Instantáneas y copia|Sí|No|  
|Configuración|Se conecta al iniciarse la máquina virtual|Se conecta una vez iniciada la máquina virtual|  
|Autenticación|Característica integrada|Configurar con el uso de la red|  
|Limpieza|Automático|Manual|  
|Acceso con REST|No se puede acceder a archivos dentro del VHD|Se puede acceder a archivos almacenados en un recurso compartido|  
|Tamaño máximo|Disco de 1 TB|Recurso compartido de archivos de 5 TB y archivo de 1 TB dentro del recurso compartido|  
|IOPS de 8 KB como máximo|500 IOPS|1000 IOPS|  
|Rendimiento|Hasta 60 MB/s por disco|Hasta 60 MB/s por recurso compartido de archivos|  

## <a name="next-steps"></a>Pasos siguientes

Al tomar decisiones sobre cómo se almacenan los datos y cómo se accede a ellos, también debe tener en cuenta los costos implicados. Para más información, consulte [Precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Algunas características SMB no son aplicables a la nube. Para más información, consulte [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Características que no admite el servicio Azure File).
  
Para más información acerca de los discos de datos, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales de Linux en Azure](storage-about-disks-and-vhds-linux.md) y [Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica](../virtual-machines/windows/classic/attach-disk.md).
