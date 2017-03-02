---
title: "Diagnóstico y recuperación de errores para los trabajos de Azure Import/Export | Microsoft Docs"
description: "Obtenga información sobre cómo habilitar el registro detallado para los trabajos del servicio Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 9aca8aad3f268bf21f3bad9fa22821f5d825f99d
ms.openlocfilehash: 88c42ff541aac2e43724fe62f99e3ddea56afc3c
ms.lasthandoff: 02/16/2017


---

# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnóstico y recuperación de errores de los trabajos de Azure Import/Export
Para cada unidad de disco procesada, el servicio Azure Import/Export crea un registro de errores en la cuenta de almacenamiento asociada. También puede habilitar el registro detallado estableciendo la propiedad `LogLevel` en `Verbose` al llamar a las operaciones [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update).

 De forma predeterminada, los registros se escriben en un contenedor denominado `waimportexport`. Puede especificar un nombre distinto estableciendo la propiedad `DiagnosticsPath` al llamar a las operaciones `Put Job` o `Update Job Properties`. Los registros se almacenan como blobs en bloques con la convención de nomenclatura siguiente: `waies/jobname_driveid_timestamp_logtype.xml`.

 Puede recuperar el identificador URI de los registros de un trabajo llamando a la operación [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). El identificador URI para el registro detallado se devuelve en la propiedad `VerboseLogUri` para cada unidad, mientras que el identificador URI para el registro de errores se devuelve en la propiedad `ErrorLogUri`.

Puede usar los datos de registro para identificar los problemas siguientes:

**Errores en la unidad**

-   Errores de acceso o lectura en el archivo de manifiesto

-   Claves de BitLocker incorrectas

-   Errores de escritura/lectura en la unidad

**Errores de blobs**

-   Nombres o blobs incorrectos o conflictivos

-   Archivos que faltan

-   Blob no encontrado

-   Archivos truncados (los archivos del disco son más pequeños de lo especificado en el manifiesto)

-   Contenido de archivo dañado (para los trabajos de importación, se ha detectado una incoherencia de suma de comprobación MD5)

-   Archivos de metadatos y propiedades de blobs dañados (detectados con una incoherencia de suma de comprobación MD5)

-   Esquema incorrecto para las propiedades de blob o los archivos de metadatos

Puede haber casos donde algunas partes de un trabajo de importación o exportación no finalizan correctamente, mientras que el trabajo completo sí finaliza. En este caso, puede cargar o descargar los elementos que faltan de los datos a través de la red, o puede crear un nuevo trabajo para transferir los datos. Vea [Azure Import-Export Tool Reference](storage-import-export-tool-how-to-v1.md) (Referencia de la herramienta Azure Import/Export) para obtener información sobre cómo reparar los datos a través de la red.

## <a name="see-also"></a>Otras referencias
[Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)

