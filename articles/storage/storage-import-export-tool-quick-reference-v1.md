---
title: "Referencia rápida de los comandos de trabajos de importación de la herramienta Azure Import/Export | Microsoft Docs"
description: "Referencia sobre los comandos de la herramienta de Azure Import/Export utilizados con frecuencia para trabajos de importación. Se refiere a la versión 1 de la herramienta Import/Export."
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
ms.sourcegitcommit: 0c58a94a553a22ac06bfdfd8032879f4a4a87fe5
ms.openlocfilehash: e1c440ee165d148b59f29035b853cd8e13a44e7c


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Referencia rápida de comandos usados con frecuencia para trabajos de importación
En esta sección se proporcionan referencias rápidas para algunos comandos que se usan con frecuencia. Para obtener información detallada sobre el uso, vea [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md) (Preparación de los discos duros para un trabajo de importación).  

## <a name="prepare-the-disks-when-data-already-copied-to-the-disks"></a>Preparación de los discos los datos ya están copiados en los discos
 A continuación se indica un comando de ejemplo para preparar un disco cuando ya se han copiado datos en la unidad de disco duro que aún no se han cifrado con BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Copia de un único directorio en una unidad de disco duro  
 A continuación se indica un comando de ejemplo para copiar un único directorio de origen en una unidad de disco duro que aún no se cifrado con BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-wwo-directories-to-a-hard-drive"></a>Copia de dos directorios en una unidad de disco duro  
 Para copiar dos directorios de origen en una unidad, necesitará dos comandos.  
  
 El primer comando especifica el directorio de registro, la clave de cuenta de almacenamiento, la letra de la unidad de destino y los requisitos `format/encrypt`, además de los parámetros comunes:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 El segundo comando especifica el archivo de diario, un nuevo identificador de sesión y las ubicaciones de origen y de destino:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Copia de un archivo grande en una unidad de disco duro en una segunda sesión de copia  
 A continuación se especifica un comando de ejemplo que copia un único archivo grande en una unidad que se ha preparado en una sesión de copia anterior:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="see-also"></a>Consulte también  
 [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)



<!--HONumber=Dec16_HO3-->


