---
title: Variables del sistema en Azure Data Factory | Microsoft Docs
description: "En este artículo se describen las variables del sistema compatibles con Azure Data Factory. Puede usar estas variables en las expresiones a la hora de definir entidades de Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: shlo
ms.openlocfilehash: 274c003e0697ba08d010c3bf13724461a4b624ee
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variables del sistema compatibles con Azure Data Factory
En este artículo se describen las variables del sistema compatibles con Azure Data Factory. Puede usar estas variables en las expresiones a la hora de definir entidades de Data Factory. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Azure Data Factory: funciones y variables del sistema](v1/data-factory-functions-variables.md).


## <a name="pipeline-scope"></a>Ámbito de canalización:

| Nombre de la variable | Descripción |
| --- | --- |
| @pipeline().DataFactory |Nombre de la factoría de datos en la que se lleva a cabo la ejecución de la canalización | 
| @pipeline().Pipeline |Nombre de la canalización |
| @pipeline().RunId | Id. de la ejecución de canalización específica | 
| @pipeline().TriggerType | Tipo del desencadenador que ha invocado la canalización (Manual o Programador) | 
| @pipeline().TriggerId| Identificador del desencadenador que invoca la canalización |
| @pipeline().TriggerName| Nombre del desencadenador que invoca la canalización |
| @pipeline().TriggerTime| Hora a la que el desencadenador invocó la canalización. La hora del desencadenador es la hora desencadenada real, y no la hora programada. Por ejemplo, se devuelve `13:20:08.0149599Z` en lugar de `13:20:00.00Z` |

## <a name="trigger-scope"></a>Ámbito del desencadenador:

| Nombre de la variable | Descripción |
| --- | --- |
| trigger().scheduledTime |Hora a la que se programó el desencadenador para invocar la ejecución de la canalización. Por ejemplo, para un desencadenador que se activa cada 5 minutos, esta variable devolvería `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z` y `2017-06-01T22:29:00Z`, respectivamente.|
| trigger().startTime |Hora a la que **realmente** se activó el desencadenador para invocar la ejecución de la canalización. Por ejemplo, para un desencadenador que se activa cada 5 minutos, esta variable podría devolver algo parecido a `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z` y `2017-06-01T22:29:00.9935483Z`, respectivamente.|

## <a name="next-steps"></a>Pasos siguientes
Para información sobre cómo se usan estas variables en las expresiones, vea [Expression language & functions](control-flow-expression-language-functions.md) (Lenguaje de expresión y funciones). 
