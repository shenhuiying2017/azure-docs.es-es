---
title: "Enumeración de los trabajos del servicio Azure Import/Export | Microsoft Docs"
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
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>Enumeración de trabajos
Para enumerar todos los trabajos de una suscripción, llame a la operación [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List) (Enumerar trabajos). `List Jobs` devuelve una lista de trabajos, así como los siguientes atributos:

-   El tipo de trabajo (importación o exportación)

-   El estado actual del trabajo

-   La cuenta de almacenamiento asociada al trabajo

## <a name="see-also"></a>Otras referencias
 [Uso de la API de REST del servicio Azure Import/Export ](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


