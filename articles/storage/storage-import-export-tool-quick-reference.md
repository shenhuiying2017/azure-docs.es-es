---
title: "Referencia rápida de los comandos de trabajos de importación de la herramienta de Azure Import/Export | Microsoft Docs"
description: "Referencia sobre los comandos de la herramienta de Azure Import/Export utilizados con frecuencia para trabajos de importación."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Referencia rápida de comandos usados con frecuencia para trabajos de importación

En este artículo se proporciona una referencia rápida para algunos comandos que se usan con frecuencia. Para obtener información detallada sobre el uso, consulte [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import.md) (Preparación de los discos duros para un trabajo de importación).

## <a name="import-job-quick-reference"></a>Referencia rápida de trabajos de importación

Para la primera sesión:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Segunda sesión:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

Anulación de la última sesión:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

Reanudación de la última sesión interrumpida:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

Adición de unidades a la última sesión:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Pasos siguientes

[Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)



<!--HONumber=Dec16_HO3-->


