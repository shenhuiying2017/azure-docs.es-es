---
title: "Solución de problemas de la herramienta Azure Import/Export | Microsoft Docs"
description: "Obtenga información sobre problemas comunes observados al usar la herramienta Import/Export de Azure y cómo abordarlos."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Solución de problemas de la herramienta Azure Import/Export
La herramienta Microsoft Azure Import/Export devuelve mensajes de error si encuentra problemas. En este tema se enumeran algunos problemas comunes con los que los usuarios se pueden encontrar.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Se produce un error en una sesión de copia, ¿qué debo hacer?  
 Cuando se produce un error en una sesión de copia, tiene dos opciones:  
  
 Si el error es recuperable, por ejemplo, si el recurso compartido de red estuvo sin conexión durante un breve período y ahora vuelve a tener conexión, puede reanudar la sesión de copia. Si el error no es recuperable, por ejemplo, si especificó el directorio de archivos de origen incorrecto en los parámetros de la línea de comandos, debe anular la sesión de copia. Vea [Preparación de unidades de disco duro para un trabajo de importación](../storage-import-export-tool-preparing-hard-drives-import-v1.md) para más información sobre cómo reanudar y anular sesiones de copia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>No puedo reanudar o anular una sesión de copia.  
 Si la sesión de copia es la primera sesión de copia de una unidad, entonces el mensaje de error debe indicar: "La primera sesión de copia se no se puede reanudar o anular". En este caso, puede eliminar el archivo de diario antiguo y volver a ejecutar el comando.  
  
 Si una sesión de copia no es la primera para una unidad, siempre se puede reanudar o anular.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>He perdido el archivo de diario, ¿todavía puedo crear el trabajo?  
 El archivo de diario para una unidad contiene toda la información de copia de datos a esta unidad, y es necesario para agregar más archivos a la unidad y se utilizará para crear un trabajo de importación. Si el archivo de diario se pierde, tendrá que rehacer todas las sesiones de copia de la unidad.  
  
## <a name="next-steps"></a>Pasos siguientes
 
* [Configuración de la herramienta Azure Import/Export](../storage-import-export-tool-setup-v1.md)   
* [Preparación de unidades de disco duro para un trabajo de importación](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisión del estado del trabajo con archivos de registro de copia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparación de un trabajo de importación](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparación de un trabajo de exportación](../storage-import-export-tool-repairing-an-export-job-v1.md)
