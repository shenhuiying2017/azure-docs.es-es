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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 3d3abfb93ba09aa9f09258743ef6d4c1fc932ae5


---

# <a name="backing-up-drive-manifests"></a>Realización de copias de seguridad de los manifiestos de las unidades
Es posible realizar copias de seguridad automáticas de los manifiestos de las unidades en blobs estableciendo la propiedad `BackupDriveManifest` en `true` en las operaciones [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (Poner trabajo) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) (Actualizar propiedades del trabajo). De forma predeterminada, no se realizan copias de seguridad de los manifiestos de las unidades. Las copias de seguridad de los manifiestos de las unidades se almacenan como blobs en bloques en un contenedor dentro de la cuenta de almacenamiento asociada al trabajo. De forma predeterminada, el nombre del contenedor es `waimportexport`, pero puede especificar uno distinto en la propiedad `DiagnosticsPath` al llamar a las operaciones `Put Job` o `Update Job Properties`. Los nombres de los blobs de manifiestos de copia de seguridad presentan el siguiente formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 Puede recuperar el identificador URI de los manifiestos de las unidades de copia de seguridad de un trabajo llamando a la operación [Obtener trabajo](/rest/api/storageimportexport/jobs#Jobs_Get). El identificador URI del blob se devuelve en la propiedad `ManifestUri` de cada unidad.

## <a name="see-also"></a>Otras referencias
 [Uso de la API de REST del servicio Azure Import/Export ](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


