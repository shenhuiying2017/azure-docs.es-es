---
title: "Planeamiento de una implementación de Azure Files | Microsoft Docs"
description: "Conozca los puntos que debe tener en cuenta al planear una implementación de Azure Files."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.openlocfilehash: b11cd632fc4735648581e77eb2570dd32604067d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="planning-for-an-azure-files-deployment"></a>Planeamiento de una implementación de Azure Files
[Azure Files](storage-files-introduction.md) ofrece recursos compartidos de archivos en la nube totalmente administrados a los que se puede acceder mediante el protocolo SMB estándar. Dado que Azure Files está totalmente administrado, su implementación en escenarios de producción resulta mucho más sencilla que la implementación y administración de un servidor de archivos o un dispositivo NAS. En este artículo se tratan las cuestiones que deben tenerse en cuenta al implementar un recurso compartido de Azure Files para su uso en producción dentro de la organización.

## <a name="management-concepts"></a>Conceptos de administración
 El siguiente diagrama muestra las construcciones de administración de Azure Files:

![Estructura de archivos](./media/storage-files-introduction/files-concepts.png)

* **Cuenta de almacenamiento**: todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte el artículo sobre los [objetivos de escalado y rendimiento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para información sobre la capacidad de la cuenta de almacenamiento.

* **Recurso compartido:** un recurso compartido de almacenamiento de archivos es un recurso compartido de archivos de SMB en Azure. Todos los directorios y archivos se deben crear en un recurso compartido principal. Una cuenta puede contener un número ilimitado de recursos compartidos, y un recurso compartido puede almacenar un número ilimitado de archivos, hasta la capacidad total de 5 TiB del recurso compartido de archivos.

* **Directorio:** una jerarquía de directorios opcional.

* **Archivo:** un archivo del recurso compartido. Un archivo puede tener un tamaño de hasta 1 TiB.

* **Formato de dirección URL**: en las solicitudes a un recurso compartido de Azure Files realizadas con el protocolo de REST de archivo, los archivos son direccionables mediante el formato de dirección URL siguiente:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Método de acceso a datos
Azure Files ofrece dos cómodos métodos de acceso a datos integrados que puede usar por separado o combinados entre sí para acceder a los datos:

1. **Acceso directo a la nube**: cualquier recurso compartido de Azure Files se puede montar mediante [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) o [Linux](storage-how-to-use-files-linux.md) con el protocolo de bloque de mensaje de servidor (SMB) estándar del sector o a través de la API de REST de archivo. Con SMB, las operaciones de lectura y escritura en archivos del recurso compartido se realizan directamente en el recurso compartido de archivos en Azure. Para montar mediante una máquina virtual en Azure, el cliente SMB del sistema operativo debe ser compatible al menos con SMB 2.1. Para montar en local, como en la estación de trabajo de un usuario, el cliente SMB compatible con la estación de trabajo debe ser compatible al menos con SMB 3.0 (con cifrado). Además de SMB, hay nuevas aplicaciones o servicios que pueden acceder directamente al recurso compartido de archivos a través de REST de archivo, lo que proporciona una interfaz de programación de aplicaciones escalable y sencilla para el desarrollo de software.
2. **Azure File Sync** (versión preliminar): con Azure File Sync, los recursos compartidos se pueden replicar en servidores de Windows Server locales o en Azure. Los usuarios accederían al recurso compartido de archivos mediante el servidor de Windows Server, por ejemplo, a través de un recurso compartido de SMB o NFS. Esto resulta útil en escenarios en los que es necesario acceder a los datos y modificarlos lejos de un centro de datos de Azure, como puede ser en una sucursal. Lo datos pueden replicarse entre varios puntos de conexión de Windows Server, por ejemplo, entre varias sucursales. Por último, los datos pueden colocarse en niveles en Azure Files, de modo que se pueda seguir accediendo a todos los datos a través del servidor, pero este no tenga una copia completa de ellos. Los datos se recuperan sin problemas cuando los abre el usuario.

En la tabla siguiente se muestra cómo pueden acceder los usuarios y las aplicaciones al recurso compartido de Azure Files:

| | Acceso directo a la nube | Azure File Sync |
|------------------------|------------|-----------------|
| ¿Qué protocolos necesita usar? | Azure Files admite SMB 2.1, SMB 3.0 y API de REST de archivo. | Acceda al recurso compartido de Azure Files a través de cualquier protocolo compatible de Windows Server (SMB, NFS, FTPS, etc.) |  
| ¿Dónde se ejecuta la carga de trabajo? | **En Azure**: Azure Files ofrece acceso directo a los datos. | **En local con red lenta**: los clientes de Windows, Linux y macOS pueden montar un recurso compartido de archivos de Windows local como una memoria caché rápida del recurso compartido de Azure Files. |
| ¿Qué nivel de ACL necesita? | Nivel de recurso compartido y archivo. | Nivel de recurso compartido, archivo y usuario. |

## <a name="data-security"></a>Seguridad de los datos
Azure Files tiene varias opciones integradas para garantizar la seguridad de los datos:

* Compatibilidad con el cifrado en ambos protocolos a través de cable: cifrado SMB 3.0 y REST de archivo a través de HTTPS. De forma predeterminada: 
    * Los clientes que admiten el cifrado SMB 3.0 envían y reciben datos a través de un canal cifrado.
    * Los clientes que no admiten SMB 3.0, pueden comunicarse dentro de centros de datos a través de SMB 2.1 o SMB 3.0 sin cifrado. Tenga en cuenta que no se permite a los clientes comunicarse entre centros de datos a través de SMB 2.1 o SMB 3.0 sin cifrado.
    * Los clientes pueden comunicarse a través de REST de archivo con HTTP o HTTPS.
* Cifrado en reposo: todos los datos se cifran con claves completamente administradas. En el cifrado en reposo no se aumentan los costos de almacenamiento ni se reduce el rendimiento. 
* Requisito opcional de datos cifrados en tránsito: cuando está seleccionado, Azure Files no permite el acceso a los datos a través de canales sin cifrar. En concreto, solo se permiten HTTPS y SMB 3.0 con conexiones de cifrado. 

    > [!Important]  
    > La exigencia de transferencia segura de datos hace que los clientes SMB más antiguos que no son capaces de comunicarse con SMB 3.0 con cifrado experimenten un error. Vea [Montaje en Windows](storage-how-to-use-files-windows.md), [Montaje en Linux](storage-how-to-use-files-linux.md) y [Montaje en macOS](storage-how-to-use-files-mac.md) para más información.

Para lograr la máxima seguridad, se recomienda encarecidamente habilitar siempre el cifrado en reposo y el cifrado de datos en tránsito cuando se usen clientes modernos para acceder a los datos. Por ejemplo, si tiene que montar un recurso compartido en una máquina virtual de Windows Server 2008 R2 que solo es compatible con SMB 2.1, debe permitir el tráfico sin cifrar a la cuenta de almacenamiento, dado que SMB 2.1 no admite el cifrado.

Si usa Azure File Sync para acceder al recurso compartido de Azure Files, use siempre HTTPS y SMB 3.0 con cifrado para sincronizar los datos en los servidores de Windows Server, independientemente de si se exige cifrado de datos en reposo.

## <a name="data-redundancy"></a>Redundancia de datos
Azure Files admite dos opciones de redundancia de datos: almacenamiento con redundancia local (LRS) y almacenamiento con redundancia geográfica (GRS). En las siguientes secciones se explican las diferencias entre el almacenamiento con redundancia local y el almacenamiento con redundancia geográfica:

### <a name="locally-redundant-storage"></a>Almacenamiento con redundancia local
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="geo-redundant-storage"></a>Almacenamiento con redundancia geográfica
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Patrón de crecimiento de datos
En la actualidad, el tamaño máximo de un recurso compartido de Azure Files es de 5 TiB, incluidas las instantáneas del recurso compartido. Debido a esta limitación actual, debe tener en cuenta el crecimiento esperado de los datos al implementar un recurso compartido de Azure Files. Tenga en cuenta que una cuenta de Azure Storage puede almacenar varios recursos compartidos con un total de 500 TiB almacenados en todos los recursos compartidos.

Es posible sincronizar varios recursos compartidos de Azure Files en un único servidor de archivos de Windows con Azure File Sync. Esto permite garantizar que los recursos compartidos de archivos anteriores de gran tamaño que pueda tener en local se incluyan en Azure File Sync. Vea [Planeamiento de una implementación de Azure File Sync](storage-files-planning.md) para más información.

## <a name="data-transfer-method"></a>Método de transferencia de datos
Existen muchas opciones sencillas para la transferencia masiva de datos desde un recurso de archivos existente, como un recurso compartido de archivos local, a Azure Files. Algunas populares incluyen (lista no exhaustiva):

* **Azure File Sync**: como parte de una primera sincronización entre un recurso compartido de Azure Files (un "punto de conexión de nube") y un espacio de nombres de directorio de Windows (un "punto de conexión de servidor"), Azure File Sync replica todos los datos del recurso compartido de archivos existente en Azure Files.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: el servicio Azure Import/Export permite transferir de forma segura grandes cantidades de datos a un recurso compartido de Azure Files mediante el envío de unidades de disco duro a un centro de datos de Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy es una herramienta de copia conocida que se incluye con Windows y Windows Server. Robocopy puede usarse para transferir datos a Azure Files al montar el recurso compartido de archivos localmente y luego usar la ubicación montada como destino en el comando de Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy es una utilidad de línea de comandos diseñada para copiar datos en y desde Azure Files, así como Azure Blob Storage, mediante sencillos comandos con un rendimiento óptimo. AzCopy está disponible para Windows y Linux.

## <a name="next-steps"></a>Pasos siguientes
* [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md)
* [Implementación de Azure Files](storage-files-deployment-guide.md)
* [Implementación de Azure File Sync](storage-sync-files-deployment-guide.md)