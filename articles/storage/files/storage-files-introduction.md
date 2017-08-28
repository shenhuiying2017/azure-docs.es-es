---
title: "Introducción a Azure File Storage | Microsoft Docs"
description: "Introducción a Azure File Storage, que proporciona recursos compartidos de archivos de red en Microsoft Cloud"
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
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Introducción a Azure File Storage

Azure File Storage ofrece recursos compartidos de archivos en red en la nube mediante el uso del estándar del sector, el [Protocolo Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) y [Common Internet File System (CIFS)](https://technet.microsoft.com/library/cc939973.aspx). Los recursos compartidos de archivos de Azure los pueden montar simultáneamente Azure Virtual Machines y las implementaciones locales que ejecuten Windows, macOS o Linux. Una cuenta de almacenamiento general proporciona acceso a Azure File Storage, Azure Blob Storage y Azure Queue Storage.

## <a name="videos"></a>Vídeos
| Introducción a Azure File Storage (27 minutos) | Tutorial de Azure File Storage (5 minutos)  |
|-|-|
| [![Presentación en pantalla del vídeo de introducción a Azure File Storage: haga clic para la reproducción.](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Presentación en pantalla del tutorial de Azure File Storage: haga clic para reproducir.](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>¿Por qué es útil Azure File Storage?

Azure File Storage le permite reemplazar los servidores de archivos con Windows Server, Linux y basados en NAS, tanto hospedados de modo local como en la nube, por un recurso compartido de archivos en la nube independiente del sistema operativo. Azure File Storage tiene las siguientes ventajas:

* **Acceso compartido** Los recursos compartidos de archivos de Azure admiten el protocolo SMB estándar del sector, lo que significa que puede reemplazar perfectamente los recursos compartidos de archivos locales por recursos compartidos de archivos de Azure sin preocuparse de la compatibilidad de aplicaciones. La posibilidad de acceder a recursos compartidos de archivos desde varias máquinas, aplicaciones o instancias es una ventaja considerable de Azure File Storage.

* **Administración total** Se pueden crear recursos compartidos de archivos de Azure sin necesidad de administrar el hardware o un sistema operativo, lo que significa que no tiene que aplicar revisiones en el sistema operativo del servidor con actualizaciones de seguridad ni cambiar los discos duros defectuosos.

* **Scripts y herramientas** Se pueden usar cmdlets de PowerShell y la CLI de Azure para crear, montar y administrar recursos compartidos de archivos de Azure como parte de la administración de las aplicaciones de Azure. Puede crear y administrar recursos compartidos de archivos de Azure desde [Azure Portal](https://portal.azure.com) y el [Explorador de Azure Storage](https://storageexplorer.com). 

* **Resistencia** Azure File Storage se creó desde cero para estar siempre disponible. Reemplazar los recursos compartidos de archivos en local por Azure File Storage significa que ya no tendrá que tratar con problemas de red o interrupciones del suministro eléctrico local. 

* **Programación familiar** Las aplicaciones que se ejecutan en Azure pueden acceder a los datos compartidos a través de las [API de E/S del sistema](https://msdn.microsoft.com/library/system.io.file.aspx). Por tanto, los desarrolladores pueden aprovechar el código y los conocimientos que ya tienen para migrar las aplicaciones actuales. Además de las API de E/S del sistema, puede usar cualquiera de las bibliotecas de cliente de Azure Storage, como la de [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) o la [API de REST de Azure Storage](/rest/api/storageservices/file-service-rest-api).

Los recursos compartidos de archivos de Azure se pueden usar para:

* **Reemplazar servidores de archivos locales** Azure File Storage puede utilizarse para reemplazar completamente los recursos compartidos de archivos de servidores de archivos locales tradicionales o dispositivos de almacenamiento conectado a la red. Desde sistemas operativos tan extendidos como Windows, Mac OS y Linux se puede montar de un modo sencillo un recurso compartido de archivos de Azure desde cualquier lugar del mundo.

* **"Levantar y mover" aplicaciones**

    Azure File Storage hace sencillo "levantar y mover" aplicaciones a la nube que utilizan recursos compartidos de archivos locales para compartir datos entre diferentes partes de la aplicación. Para implementarlo, las máquinas virtuales se conectan al recurso compartido de archivos y así pueden leer y escribir archivos del mismo modo que lo harían sobre un recurso compartido de archivos local.

* **Simplificar el desarrollo en la nube**
    
    Azure File Storage puede utilizarse de varias maneras diferentes para simplificar los nuevos proyectos de desarrollo en la nube.
    
    * **Configuración de aplicaciones compartida**
    
        Un patrón habitual entre las aplicaciones distribuidas es contar con archivos de configuración en una ubicación centralizada que permite tener acceso a ellos desde muchas máquinas virtuales diferentes. Estos archivos de configuración ahora se pueden almacenar en un recurso compartido de archivos de Azure para que lo lean todas las instancias de la aplicación. La configuración se puede administrar también a través de la interfaz REST, que permite un acceso a los archivos de configuración desde cualquier parte del mundo.

    * **Recurso compartido de diagnóstico**
    
        Un recurso compartido de archivos de Azure también puede usarse para guardar archivos de diagnóstico como los registros, las métricas y los volcados de memoria. El hecho de poder acceder a estos recursos compartidos de archivos a través tanto de SMB como de la interfaz REST permite a las aplicaciones utilizar una variedad de herramientas de análisis para procesar y analizar los datos de diagnóstico.

    * **Desarrollo, pruebas y depuración**

        Cuando los desarrolladores o administradores están trabajando en máquinas virtuales en la nube, a menudo necesitan diversas herramientas o utilidades. La instalación y distribución de estas utilidades en cada una de las máquinas virtuales en las que se necesiten requiere mucho tiempo. Con Azure File Storage, un desarrollador o administrador puede almacenar sus herramientas favoritas en un recurso compartido de archivos y conectarse a él desde cualquier máquina virtual.
        
## <a name="how-does-it-work"></a>¿Cómo funciona?

La administración de recursos compartidos de archivos de Azure es mucho más sencilla que la administración de recursos compartidos de archivos locales. El siguiente diagrama muestra la administración de Azure File Storage:

![Estructura de archivos](./media/storage-files-introduction/files-concepts.png)

* **Cuenta de almacenamiento** El acceso a Azure Storage se realiza a través de una cuenta de almacenamiento. Consulte el artículo sobre los [objetivos de escalado y rendimiento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para información sobre la capacidad de la cuenta de almacenamiento.

* **Recurso compartido** Un recurso compartido de archivos es un recurso compartido de archivos de SMB en Azure. Todos los directorios y archivos se deben crear en un recurso compartido principal. Una cuenta puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar un número ilimitado de archivos, hasta una capacidad total de 5 TB del recurso compartido de archivos.

* **Directorio** Una jerarquía de directorios opcional.

* **Archivo** Un archivo del recurso compartido. Un archivo puede tener un tamaño de hasta 1 TB.

* **Formato de URL** Los archivos son están disponibles con el formato de URL siguiente:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un recurso compartido de archivos de Azure](storage-how-to-create-file-share.md)
* [Conexión y montaje en Windows](storage-how-to-use-files-windows.md)
* [Conexión y montaje en Linux](storage-how-to-use-files-linux.md)
* [Conexión y montaje en macOS](storage-how-to-use-files-mac.md)
* [Preguntas más frecuentes](../storage-files-faq.md)
* [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)   
* [Solución de problemas en Linux](storage-troubleshoot-linux-file-connection-problems.md)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>Artículos y vídeos conceptuales
* [Azure File Storage: un sistema de archivos SMB en la nube sin dificultades para Windows y Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Compatibilidad de herramientas con Azure File Storage
* [Uso de AzCopy con Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Uso de la CLI de Azure con Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>Publicaciones de blog
* [Azure File Storage ya está disponible de manera general](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [En el interior de Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (Introducción al servicio de archivos de Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migración de datos a Azure Files](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referencia
* [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

