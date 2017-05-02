---
title: "Evento de inicio de eliminación del grupo: Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: feddca1e-c69c-4257-be44-a36172e77157
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: ef61238c6dc342173215ae628565a0b87a86bcad
ms.lasthandoff: 04/13/2017

---
# <a name="pool-delete-start-event"></a>Evento de inicio de eliminación del grupo
Cuerpo del registro del evento de inicio de eliminación del grupo

## <a name="remarks"></a>Comentarios
 Este evento se genera cuando se inicia una operación de eliminación del grupo. Puesto que la eliminación de grupo es un evento asincrónico, puede esperar que se genere un evento completo de eliminación del grupo cuando se haya completado la operación de eliminación.

 En el siguiente ejemplo, se muestra el cuerpo de un evento de inicio de eliminación del grupo.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|id|String|El identificador del grupo.|
