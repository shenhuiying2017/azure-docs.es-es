---
title: "Introducción a Azure Files | Microsoft Docs"
description: "Información general sobre Azure Files, el servicio que permite crear y utilizar recursos compartidos de archivos en red en la nube mediante el protocolo SMB estándar del sector."
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
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: f96aa9fe12aba28e1ac3429f012419341bdf92c1
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="introduction-to-azure-files"></a>Introducción a Azure Files
Azure Files ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede acceder mediante el [protocolo SMB (Bloque de mensajes del servidor) estándar](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (también conocido como Sistema de archivos de Internet común o CIFS). Los recursos compartidos de Azure Files se pueden montar simultáneamente en implementaciones de Windows, Linux y macOS en la nube o locales. Además, los recursos compartidos de Azure Files se pueden almacenar en caché en servidores de Windows Server con Azure Files Sync (versión preliminar), que permite un acceso rápido allí donde se utilizan los datos.

## <a name="videos"></a>Vídeos
| Introducing Azure File Sync (Introducción a Azure File Sync) (2 m) | Azure Files with Sync (Ignite 2017) (85 m)  |
|-|-|
| [![Presentación en pantalla del vídeo Introducing Azure File Sync, haga clic para reproducirlo.](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Presentación en pantalla de Azure Files with Sync, haga clic para reproducirlo.](./media/storage-files-introduction/azure-files-ignite-2017-video.png)](https://www.youtube.com/watch?v=r26jWDGF_rg) |

## <a name="why-azure-files-is-useful"></a>¿Por qué es útil Azure Files?
Los recursos compartidos de archivos de Azure se pueden usar para:

* **Reemplazar o complementar servidores de archivos locales**:  
    Azure Files puede utilizarse para reemplazar totalmente o complementar los servidores de archivos tradicionales locales o en dispositivos NAS. Desde sistemas operativos tan extendidos como Windows, macOS y Linux se puede montar directamente un recurso compartido de Azure Files desde cualquier lugar del mundo. Los recursos compartidos de Azure Files se pueden replicar también con Azure File Sync en servidores de Windows Server, locales o en la nube, para obtener un almacenamiento en caché eficiente y distribuido de los datos allí donde se usan.

* **Aplicaciones "Lift-and-shift"**:  
    Azure Files facilita la migración mediante "lift and shift" de aplicaciones a la nube que espera un recurso compartido de archivos para almacenar datos de la aplicación de archivos o de un usuario. Azure Files permite la migración clásica mediante "lift and shift" en la que tanto la aplicación como sus datos se mueven a Azure, y la migración híbrida mediante "lift and shift" en la que los datos de la aplicación se mueven a Azure Files pero la aplicación continúa ejecutándose de forma local. 

* **Simplificar el desarrollo en la nube**:  
    Azure Files también se puede utilizar de muchas formas para simplificar los nuevos proyectos de desarrollo en la nube. Por ejemplo: 
    * **Configuración de aplicaciones compartidas**  
        Un patrón habitual entre las aplicaciones distribuidas es contar con archivos de configuración en una ubicación centralizada que permite tener acceso a ellos desde muchas instancias de aplicaciones. Las instancias de la aplicación pueden cargar su configuración mediante la API de REST de Azure Files y los usuarios pueden acceder a ella según sea necesario montando el recurso compartido SMB localmente.

    * **Recurso compartido de diagnóstico**:  
        Un recurso compartido de Azure Files es un lugar adecuado para que las aplicaciones en la nube escriban sus registros, métricas y volcados de memoria. Las instancias de la aplicación pueden escribir los registros mediante la API de REST de Azure Files, y los desarrolladores pueden acceder a ellos montando el recurso compartido de archivos en su máquina local. Esto permite una gran flexibilidad, ya que los desarrolladores pueden adoptar el desarrollo en la nube sin tener que abandonar las herramientas existentes que ya conocen y disfrutan.

    * **Desarrollo, pruebas y depuración**:  
        Cuando los desarrolladores o administradores están trabajando en máquinas virtuales en la nube, a menudo necesitan diversas herramientas o utilidades. Copiar estas utilidades y herramientas en cada máquina virtual puede ser una tarea que consuma mucho tiempo. Mediante el montaje de un recurso compartido de Azure Files localmente en las máquinas virtuales, un desarrollador o administrador pueden acceder rápidamente a sus herramientas y utilidades, sin tener que copiarlos.

## <a name="key-benefits"></a>Ventajas principales
* **Acceso compartido**. Los recursos compartidos de archivos Azure admiten el protocolo SMB estándar del sector, lo que significa que puede reemplazar perfectamente los recursos compartidos de archivos en local por recursos compartidos de archivos de Azure sin preocuparse de compatibilidad de aplicaciones. La posibilidad de compartir un sistema de archivos entre varias máquinas, aplicaciones o instancias es una ventaja importante de Azure Files para aquellas aplicaciones que necesitan la posibilidad de compartir. 
* **Completamente administrado**. Los recursos compartidos de archivos de Azure pueden crearse sin necesidad de administrar ni el hardware ni un sistema operativo. Esto significa que no tiene que tratar con la aplicación de actualizaciones de seguridad críticas en el sistema operativo del servidor ni ocuparse de reemplazar discos duros defectuosos.
* **Herramientas y scripting**. Los cmdlets de PowerShell y la CLI de Azure pueden utilizarse para crear, montar y administrar recursos compartidos de Azure Files como parte de la administración de aplicaciones de Azure. Puede crear y administrar recursos compartidos de archivos de Azure mediante Azure Portal y el Explorador de Azure Storage. 
* **Resistencia**. Azure Files se creó desde sus orígenes para estar siempre disponible. Reemplazar los recursos compartidos de archivos en local por Azure Files significa que ya no tendrá que tratar con problemas de red o interrupciones del suministro eléctrico local. 
* **Programación amigable**. Las aplicaciones que se ejecutan en Azure pueden tener acceso a los datos en el recurso compartido mediante las [API de E/S del sistema](https://msdn.microsoft.com/library/system.io.file.aspx). Por tanto, los desarrolladores pueden aprovechar el código y los conocimientos que ya tienen para migrar las aplicaciones actuales. Además de las API de E/S del sistema, puede usar las [Bibliotecas de cliente de Azure Storage](https://msdn.microsoft.com/library/azure/dn261237.aspx) o la [API de REST de Azure Storage](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un recurso compartido de archivos de Azure](storage-how-to-create-file-share.md)
* [Conexión y montaje en Windows](storage-how-to-use-files-windows.md)
* [Conexión y montaje en Linux](storage-how-to-use-files-linux.md)
* [Conexión y montaje en macOS](storage-how-to-use-files-mac.md)
