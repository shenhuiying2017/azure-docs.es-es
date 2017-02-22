---
title: "Cancelación y eliminación de trabajos del servicio Azure Import/Export | Microsoft Docs"
description: "Descubra cómo cancelar y eliminar trabajos del servicio Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 640b814e4280f5cce029bea7324881bbfef4b1a4


---

# <a name="cancelling-and-deleting-jobs"></a>Cancelación y eliminación de trabajos
Puede solicitar la cancelación de un trabajo antes de que adquiera el estado `Packaging` llamando a la operación de [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) (Actualizar propiedades del trabajo) y establecer el elemento `CancelRequested` en `true`. El cancelará el trabajo en función de la mejor opción. Si las unidades están transfiriendo datos, es posible que estos sigan transmitiéndose incluso después de haber solicitado la cancelación.

 Un trabajo cancelado adquirirá el estado `Completed` y se conservará durante 90 días; transcurrido ese plazo, se eliminará.

 Para eliminar un trabajo, llame a la operación [Eliminar trabajo](/rest/api/storageimportexport/jobs#Jobs_Delete) antes de que este se haya procesado (*es decir*, mientras el trabajo muestre el estado `Creating`). También puede eliminar un trabajo cuando se encuentra en el estado `Completed`. Una vez eliminado un trabajo, ya no se podrá acceder a su información y estado mediante la API de REST o Azure Portal.

## <a name="see-also"></a>Otras referencias
 [Uso de la API de REST del servicio Azure Import/Export ](storage-import-export-using-the-rest-api.md)



<!--HONumber=Dec16_HO3-->


