---
title: "Uso de la herramienta de Azure Import/Export (versión 1) | Microsoft Docs"
description: "Descubra cómo usar la herramienta Import/Export a fin de preparar los discos duros para un trabajo de importación, así como reparar un trabajo de importación o de exportación."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: ca2d4bf2b1e1cb56910fa3ee7af6e45b6b77b450
ms.openlocfilehash: 7c628022a6de01de50f50256bd2ea0cdba2c2555


---

# <a name="using-the-azure-importexport-tool"></a>Uso de la herramienta de Azure Import/Export 

La herramienta de Azure Import/Export (WAImportExport.exe) se utiliza para crear y administrar trabajos del servicio Azure Import/Export y le permite transferir grandes volúmenes de datos a Azure Blob Storage o desde él.

Esta documentación está destinada a la versión más reciente de la herramienta de Azure Import/Export. Para obtener información sobre cómo usar la versión 1 de la herramienta, consulte [Uso de la herramienta de Azure Import/Export (versión 1)](storage-import-export-tool-how-to-v1.md).

En estos artículos, verá cómo usar la herramienta para realizar lo siguiente:  

- Instalar y configurar la herramienta de Import/Export.
- Preparar las unidades de disco duro para un trabajo en el que se importen los datos de las unidades a Azure Blob Storage.
- Revisar el estado de un trabajo con archivos de registro de copia. 
- Reparar un trabajo de importación. 
- Reparar un trabajo de exportación. 
- Solucionar problemas de la herramienta de Azure Import/Export, en el caso de que le haya surgido algún problema durante el proceso. 



<!--HONumber=Dec16_HO3-->


