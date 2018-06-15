---
title: Evento de inicio de eliminación de grupo de Azure Batch | Microsoft Docs
description: Referencia del evento de inicio de eliminación de grupo de Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 8737b9ff6452730ff5a55fa7324e37f0fe715433
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312066"
---
# <a name="pool-delete-start-event"></a>Evento de inicio de eliminación del grupo

 Este evento se genera cuando se inicia una operación de eliminación del grupo. Puesto que la eliminación de grupo es un evento asincrónico, puede esperar que se genere un evento completo de eliminación del grupo cuando se haya completado la operación de eliminación.

 En el siguiente ejemplo, se muestra el cuerpo de un evento de inicio de eliminación del grupo.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|id|string|El identificador del grupo.|