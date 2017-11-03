---
title: Objetivos de escalabilidad y rendimiento de Azure Files | Microsoft Docs
description: "Obtenga información sobre los objetivos de escalabilidad y rendimiento de Azure Files, como la capacidad, la tasa de solicitudes, y los límites de ancho de banda entrante y saliente."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/17/2017
ms.author: wgries
ms.openlocfilehash: 35d430b683224d1035cccdfb58b9db415d47ea3b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Objetivos de escalabilidad y rendimiento de Azure Files
[Azure Files](storage-files-introduction.md) ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede acceder mediante el protocolo SMB estándar. En este artículo se explican los objetivos de escalabilidad y rendimiento de Azure Files y Azure File Sync (versión preliminar).

Los objetivos de escalabilidad y rendimiento que aparecen aquí son exigentes, pero pueden verse afectados por otras variables de la implementación. Por ejemplo, el rendimiento de un archivo puede verse limitado también por el ancho de banda de red disponible, no solo los servidores que hospedan el servicio Azure Files. Se recomienda probar el patrón de uso para determinar si la escalabilidad y el rendimiento de Azure Files cumplen sus requisitos. También nos comprometemos a aumentar estos límites con el tiempo. No dude en enviarnos comentarios, ya sea a través del cuadro de comentarios de debajo o de [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre los límites que quiere que aumentemos.

## <a name="azure-storage-account-scale-targets"></a>Objetivos de escalabilidad de la cuenta de almacenamiento Azure
El recurso primario de un recurso compartido de Azure Files es una cuenta de almacenamiento de Azure. Una cuenta de almacenamiento representa un grupo de almacenamiento de Azure que pueden utilizar varios servicios de almacenamiento, como Azure Files, para almacenar datos. Otros servicios que almacenan datos en cuentas de almacenamiento son Azure Blob Storage, Azure Queue Storage y Azure Table Storage. Los objetivos siguientes se aplican a todos los servicios de almacenamiento que guardan datos en una cuenta de almacenamiento:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> La utilización de la cuenta de almacenamiento por parte de otros servicios de almacenamiento afecta a los recursos compartidos de Azure Files de su cuenta de almacenamiento. Por ejemplo, si se alcanza la capacidad máxima de la cuenta de almacenamiento con el almacenamiento de Azure Blob Storage, no podrá crear archivos en el recurso compartido de Azure Files, aunque esté por debajo del tamaño máximo.

## <a name="azure-files-scale-targets"></a>Objetivos de escalabilidad de Azure Files
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Objetivos de escalabilidad de Azure File Sync
Con Azure File Sync, hemos tratado de diseñar, en la medida de lo posible, un uso sin límites; sin embargo, no siempre es posible. La tabla siguiente indica los límites de nuestras pruebas y los objetivos que son límites estrictos realmente:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Otras referencias
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
- [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md)
- [Objetivos de escalabilidad y rendimiento de otros servicios de almacenamiento](../common/storage-scalability-targets.md)