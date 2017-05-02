---
title: "Análisis por lotes: Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 2fda4d9c-f782-4088-9320-656b450e3100
caps.latest.revision: 7
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 4b73637e0ec6798b701d5a13f96c504bcbb14cc7
ms.lasthandoff: 04/13/2017

---
# <a name="batch-analytics"></a>Análisis por lotes
Los temas de análisis por lotes contienen información de referencia para los eventos y las alertas disponibles para los recursos del servicio de Batch.

Vea [Registro de diagnóstico de Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obtener más información sobre cómo habilitar y consumir los registros de diagnóstico por lotes.

## <a name="diagnostic-logs"></a>Registros de diagnóstico

El servicio de Azure Batch genera los siguientes eventos de registro de diagnóstico mientras duren determinados recursos de Batch.

**Eventos del registro del servicio**
* [Creación del grupo](batch-pool-create-event.md)
* [Inicio de eliminación del grupo](batch-pool-delete-start-event.md)
* [Finalización de eliminación del grupo](batch-pool-delete-complete-event.md)
* [Inicio de cambio de tamaño del grupo](batch-pool-resize-start-event.md)
* [Finalización de cambio de tamaño del grupo](batch-pool-resize-complete-event.md)
* [Inicio de tarea](batch-task-start-event.md)
* [Finalización de tarea](batch-task-complete-event.md)
* [Error en la tarea](batch-task-fail-event.md)
