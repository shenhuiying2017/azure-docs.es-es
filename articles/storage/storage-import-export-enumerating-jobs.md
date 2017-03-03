---
title: Lista de todos los trabajos de Azure Import/Export | Microsoft Docs
description: "Descubra cómo enumerar todos los trabajos del servicio Azure Import/Export de una suscripción."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 50fbd0d214c825137c3ac6873be27b9d2d53cee1
ms.openlocfilehash: 5bbe99110b86cb15d23eadc48c1ea45bbab13daf
ms.lasthandoff: 02/16/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumeración de los trabajos del servicio Azure Import/Export
Para enumerar todos los trabajos de una suscripción, llame a la operación [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List) (Enumerar trabajos). `List Jobs` devuelve una lista de trabajos, así como los siguientes atributos:

-   El tipo de trabajo (importación o exportación)

-   El estado actual del trabajo

-   La cuenta de almacenamiento asociada al trabajo

## <a name="see-also"></a>Otras referencias
 [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)

