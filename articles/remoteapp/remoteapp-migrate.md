---
title: "Migración de datos de usuario desde Azure RemoteApp | Microsoft Docs"
description: Aprenda a migrar los datos de usuario en Azure RemoteApp como origen y destino.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: ba3cf4c6834279bbd7f94d666fd8abbb7ac05bf0
ms.contentlocale: es-es
ms.lasthandoff: 07/22/2017

---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Procedimiento para migrar datos en Azure RemoteApp como origen y destino
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Puede usar muchos métodos y herramientas diferentes para transferir [datos de usuario](remoteapp-upd.md) en Azure RemoteApp como origen y destino. Estos son algunos métodos:

* Copiar y pegar mediante el uso compartido del Portapapeles
* Copiar archivos y datos en un servidor de archivos
* Copiar archivos en OneDrive para la Empresa a través de un explorador
* Copiar archivos mediante redirección

> [!NOTE]
> No se pueden habilitar los agentes de sincronización de OneDrive para la Empresa o usuarios particulares; [no son compatibles](remoteapp-onedrive.md) con Azure RemoteApp.
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>Uso de copiar y pegar en el Explorador de archivos
La característica de copiar y pegar mediante el Portapapeles está habilitada [de forma predeterminada](remoteapp-redirection.md)en las implementaciones de RemoteApp. Gracias a esto, los usuarios pueden copiar archivos entre su equipo local y aplicaciones de RemoteApp. A menudo, durante el uso normal de las aplicaciones de RemoteApp, los usuarios guardan archivos en sus UPD, así que resulta sencillo mover los datos de RemoteApp:

1. [Publique el Explorador de archivos como aplicación](remoteapp-publish.md) en una colección de RemoteApp. (tenga en cuenta que se trata de una tarea administrativa).
2. Pídale a los usuarios que inicien la aplicación Explorador de archivos que publicó y que la utilicen para copiar y pegar archivos en su UPD como origen y destino.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Carga de archivos y datos en un servidor de archivos mediante la copia de archivos de red estándar
A menudo, las organizaciones usan servidores de archivos para almacenar datos generales. Si conoce el nombre o la ubicación del servidor, los usuarios pueden examinar la red local del servidor y, luego, copiar sus archivos allí, al igual que anteriormente. De nuevo, querrá publicar el Explorador de archivos en RemoteApp y, después, compartirlo con los usuarios.

> [!NOTE]
> El servidor de archivos debe estar en la red enrutable en la que se implementó RemoteApp.
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>Copia de archivos en OneDrive para la Empresa
Aunque no se puede habilitar el agente de sincronización de OneDrive para la Empresa en RemoteApp, puede seguir copiando archivos desde su UPD a OneDrive para la Empresa a través de un explorador. 

1. Publique el Explorador de archivos en RemoteApp y, después, indique a los usuarios que accedan a los archivos a través de esa aplicación. 
2. Es más fácil transferir archivos si están comprimidos; por tanto, los usuarios deben crear un archivo .zip que contenga todos los archivos que se van a mover a OneDrive para la Empresa.
3. Pida a los usuarios que accedan al portal de Office 365 y, luego, que visiten OneDrive y carguen el archivo .zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copia de archivos mediante la redirección de unidades
Si ha habilitado la [redirección de unidades](remoteapp-redirection.md), ya ha creado una unidad asignada para los usuarios. En este caso, puede comprimir los archivos en la unidad redirigida y, después, guardarlos en su equipo local.

## <a name="how-administrators-can-export-data"></a>Cómo pueden exportar datos los administradores

Los administradores de Azure RemoteApp pueden exportar todos los discos de perfil de usuario (UPD) para todas las colecciones dentro de una suscripción a Azure Storage mediante el cmdlet Export-AzureRemoteAppUserDisk de Azure PowerShell.  No hay ninguna posibilidad de seleccionar UPD individuales.  Cuando se ejecuta el comando de PowerShell, cada disco de usuario será tendrá un tamaño de disco fijo de 50 GB y se exportará al almacenamiento de Azure.  Los costos del almacenamiento de Azure se cobrarán inmediatamente para este almacenamiento.  Al ejecutar este comando asegúrese de que no hay ninguna sesión; en caso contrario, la exportación fallará.

Los UPD para las implementaciones de Azure RemoteApp unidas a un dominio solo pueden usarse de nuevo en una implementación de RDS; las implementaciones no unidas a un dominio no se pueden usar.  Si estos discos se van a utilizar en una implementación de RDS se recomienda usar nuestros [scripts automatizados](https://github.com/arcadiahlyy/aramigration) que exportarán, convertirán e importarán los UPD en una implementación de RDS.


