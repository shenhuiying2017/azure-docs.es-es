---
title: "Configuración de la herramienta Azure Import/Export | Microsoft Docs"
description: "Descubra cómo configurar herramienta de preparación y reparación de unidades del servicio Azure Import/Export."
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
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 6b5febd051d0b956c90cb14c260dda1881adac3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-the-azure-importexport-tool"></a>Configuración de la herramienta Azure Import/Export

La herramienta Microsoft Azure Import/Export es la herramienta de preparación y reparación de unidades de disco duro que se puede usar con el servicio Microsoft Azure Import/Export. Puede usar la herramienta para las siguientes funciones:

* Antes de crear un trabajo de importación, puede utilizar esta herramienta para copiar los datos en las unidades de disco duro que se van a enviar a un centro de datos de Azure.
* Cuando haya finalizado un trabajo de importación, puede usar esta herramienta para reparar los blobs que se hayan dañado, no se hayan encontrado o hayan entrado en conflicto con otros blobs.
* Después de recibir las unidades de un trabajo de exportación completado, puede utilizar esta herramienta para reparar los archivos dañados o no encontrados de las unidades.

## <a name="prerequisites"></a>Requisitos previos

Si va a **preparar unidades** para un trabajo de importación, se deben cumplir los siguientes requisitos previos:

* Debe contar con una suscripción de Azure activa.
* La suscripción debe incluir una cuenta de almacenamiento con suficiente espacio disponible para almacenar los archivos que se van a importar.
* Necesita al menos una de las claves de acceso para la cuenta de almacenamiento.
* Precisa un equipo (el "equipo de copia") con Windows 7, Windows Server 2008 R2 o un sistema operativo de Windows más reciente instalado.
* Se debe instalar .NET Framework 4 en el equipo de copia.
* Se debe habilitar BitLocker en el equipo de copia.
* Necesita una o más unidades de disco duro SATAII o III, o SSD, de 2,5 o 3,5 pulgadas conectadas a la máquina de copia.
* Se debe poder acceder a los archivos que se van a importar desde el equipo de copia, con independencia de si se encuentran en un recurso compartido de red o en una unidad de disco duro local.

Si está tratando de **reparar una importación** que ha generado un error parcial, precisa lo siguiente:

* Los archivos de registro de copia
* La clave de la cuenta de almacenamiento

Si está tratando de **reparar una exportación** que ha generado un error parcial, precisa lo siguiente:

* Los archivos de registro de copia
* Los archivos de manifiesto (opcionales)
* La clave de la cuenta de almacenamiento

## <a name="installing-the-azure-importexport-tool"></a>Instalación de la herramienta Azure Import/Export

En primer lugar, [descargue la herramienta de Azure Import/Export](https://www.microsoft.com/download/details.aspx?id=55280) y extráigala en un directorio del equipo; por ejemplo, `c:\WAImportExport`.

La herramienta Azure Import/Export se compone de los siguientes archivos:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

A continuación, abra una ventana del símbolo del sistema en **modo de administrador** y cambie al directorio que contiene los archivos extraídos.

Si desea obtener ayuda para el comando, ejecute la herramienta (`WAImportExport.exe`) sin parámetros:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Pasos siguientes

* [Preparación de unidades de disco duro para un trabajo de importación](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Vista previa de uso de disco para un trabajo de exportación](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Revisión del estado del trabajo con archivos de registro de copia](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparación de un trabajo de importación](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparación de un trabajo de exportación](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Solución de problemas de la herramienta Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
