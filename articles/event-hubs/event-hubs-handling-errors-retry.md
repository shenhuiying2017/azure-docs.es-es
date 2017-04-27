---
title: Procedimientos recomendados de Azure Event Hubs para controlar errores | Microsoft Docs
description: Control de errores y reintentos en Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>🔧 Procedimientos recomendados de Event Hubs para controlar errores y reintentos

> [!NOTE]
> 
> Este tema no se ha escrito aún. 
>
> Agradecemos que nos haga llegar sus comentarios para determinar el ámbito y el enfoque aplicables a este contenido. Puede realizar un seguimiento del estado y proporcionar comentarios en este [tema de GitHub](https://github.com/Azure/azure-event-hubs/issues/305).
> 
> Obtenga más información acerca de cómo puede hacer sus aportaciones en [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md).

En este artículo se tratarán lo siguiente:

- ¿Qué tipos de errores existen?
- ¿Qué ocurre cuando se recibe un error?
- ¿Con qué tipos de errores se debería recurrir a los reintentos? ¿Con cuáles no?
- Directiva de reintentos personalizados

