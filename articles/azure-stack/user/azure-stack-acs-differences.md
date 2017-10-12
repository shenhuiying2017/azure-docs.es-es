---
title: 'Azure Stack Storage: Diferencias y consideraciones'
description: "Comprender las diferencias entre Azure Stack Storage y Azure Storage, junto con las consideraciones de implementación de Azure Stack."
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 381950321ac3a5ea8a43b76f3fba868da4be4682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack Storage: Diferencias y consideraciones

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Azure Stack Storage es el conjunto de servicios de almacenamiento en la nube de Microsoft Azure Stack. Azure Stack Storage proporciona blob, tabla, cola y funcionalidad de administración de cuenta con una semántica coherente de Azure.

En este artículo se resumen las diferencias entre Azure Stack Storage y Azure Storage. También se resumen otras consideraciones a tener en cuenta al implementar la Azure Stack. Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el tema [Consideraciones clave](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Hoja de referencia rápida: Diferencias de almacenamiento

| Característica | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Recursos compartidos de archivos SMB basado en la nube admitidos|Todavía no se admite
|Cifrado de datos en reposo|Cifrado de AES de 256 bits|Todavía no se admite
|Tipo de cuenta de almacenamiento|Cuentas de Azure Blob Storage y de uso general|Solo para uso general
|Opciones de replicación|Almacenamiento con redundancia local, almacenamiento con redundancia geográfica, almacenamiento con redundancia geográfica con acceso de lectura y almacenamiento con redundancia de zona|Almacenamiento con redundancia local
|Premium Storage|Totalmente compatible|Se pueden aprovisionar, pero no hay límite de rendimiento o garantía
|Discos administrados|Premium y estándar admitidos|Todavía no se admite
|Nombre de blob|1 024 caracteres (2 048 bytes)|880 caracteres (1 760 bytes)
|Tamaño máximo de blob en bloque|4,75 TB (100 MB x 50 000 bloques)|50 000 x 4 MB (195 GB aproximadamente)
|Copia de instantáneas incrementales del blob de página|Blobs en páginas de Azure estándar y premium admitidos|Todavía no se admite
|Tamaño máximo de blob en página|8 TB|1 TB
|Tamaño de página de blob en página|512 bytes|4 KB
|Clave de partición de tabla y tamaño de clave de fila|1 024 caracteres (2 048 bytes)|400 caracteres (800 bytes)

### <a name="metrics"></a>Métricas
También hay algunas diferencias con las métricas de almacenamiento:
* Los datos de transacción de las métricas de almacenamiento no distinguirán el ancho de banda de red interna o externa.
* Los datos de transacción de las métricas de almacenamiento no incluyen el acceso de la máquina virtual a los discos montados.

## <a name="api-version"></a>Versión de API
Solo las siguientes versiones son compatibles con Azure Stack Storage:

* Servicios de datos de Azure Storage: [Versión de API de REST de 05-04-2015](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Servicios de administración de Azure Storage: [ Versión preliminar de 01-05-2015, 15-06-2015 y 01-01-2016](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Pasos siguientes

* [Empezar a trabajar con herramientas de desarrollo de Azure Stack Storage](azure-stack-storage-dev.md)
* [Introducción a Azure Stack Storage](azure-stack-storage-overview.md)

