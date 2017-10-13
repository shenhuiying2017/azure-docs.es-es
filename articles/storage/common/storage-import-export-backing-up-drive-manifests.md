---
title: "Realización de copias de seguridad de los manifiestos de las unidades de Azure Import/Export | Microsoft Docs"
description: "Descubra cómo conseguir que se realicen copias de seguridad automáticas de los manifiestos de las unidades del servicio Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Realización de copias de seguridad de los manifiestos de los trabajos de Azure Import/Export

Es posible realizar copias de seguridad automáticas de los manifiestos de las unidades en blobs estableciendo la propiedad `BackupDriveManifest` en `true` en las operaciones de API de REST [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (Poner trabajo) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) (Actualizar propiedades del trabajo). De forma predeterminada, no se realizan copias de seguridad de los manifiestos de las unidades. Las copias de seguridad de los manifiestos de las unidades se almacenan como blobs en bloques en un contenedor dentro de la cuenta de almacenamiento asociada al trabajo. De forma predeterminada, el nombre del contenedor es `waimportexport`, pero puede especificar uno distinto en la propiedad `DiagnosticsPath` al llamar a las operaciones `Put Job` o `Update Job Properties`. Los nombres de los blobs de manifiestos de copia de seguridad presentan el siguiente formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 Puede recuperar el identificador URI de los manifiestos de las unidades de copia de seguridad de un trabajo llamando a la operación [Obtener trabajo](/rest/api/storageimportexport/jobs#Jobs_Get). El identificador URI del blob se devuelve en la propiedad `ManifestUri` de cada unidad.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)
