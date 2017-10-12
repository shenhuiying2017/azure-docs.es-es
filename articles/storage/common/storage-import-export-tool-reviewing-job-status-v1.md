---
title: "Revisión del estado de un trabajo de Azure Import/Export (versión 1) | Microsoft Docs"
description: "Obtenga información sobre cómo usar los archivos de registro creados cuando se ejecutó el trabajo de importación o exportación para ver el estado del trabajo de importación y exportación."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Revisión del estado de un trabajo de Azure Import/Export con archivos de registro de copia
Cuando el servicio Microsoft Azure Import/Export procesa las unidades asociadas a un trabajo de importación o exportación, escribe archivos de registro de copia en la cuenta de almacenamiento con origen o destino en la ubicación donde está importando o exportando blobs. El archivo de registro contiene el estado detallado de cada archivo que se importó o exportó. La dirección URL de cada archivo de registro de copia se devuelve al consultar el estado de un trabajo completado; vea el artículo de [obtención de trabajos](/rest/api/storageservices/Get-Job3) para obtener más información.  

## <a name="example-urls"></a>URL de ejemplo

Las siguientes son direcciones URL de ejemplo de los archivos de registro de copia de un trabajo de importación con dos unidades:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Vea [Formato del archivo de registro del servicio Import-Export](../storage-import-export-file-format-log.md) para consultar el formato de los registros de copia y la lista completa de códigos de estado.  
  
## <a name="next-steps"></a>Pasos siguientes
 
 * [Configuración de la herramienta Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Preparación de unidades de disco duro para un trabajo de importación](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparación de un trabajo de importación](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparación de un trabajo de exportación](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Solución de problemas de la herramienta Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
