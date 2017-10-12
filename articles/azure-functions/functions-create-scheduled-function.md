---
title: "Crear una función que se ejecuta según una programación en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear una función de Azure que se ejecuta según la programación que usted defina."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 476e103c7101621e116c5155241f56f1cb9036df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Cree una función en Azure que se desencadena mediante un temporizador

Obtenga información sobre cómo usar Azure Functions para crear una función que se ejecuta según la programación que usted defina.

![Creación de una aplicación de función en Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

+ Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creación de una Function App de Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App creada correctamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

Después, cree una función en la nueva Function App.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Creación de una función desencadenada por un temporizador

1. Expanda su instancia de Function App y haga clic en el botón **+**, que se encuentra junto a **Functions**. Si se trata de la primera función de Function App, seleccione **Función personalizada**. Se muestra el conjunto completo de plantillas de funciones.

    ![Página de inicio rápido de Functions en Azure Portal](./media/functions-create-scheduled-function/add-first-function.png)

2. Seleccione la plantilla **TimerTrigger** del idioma que desee. Luego, use la configuración que se especifica en la tabla:

    ![Creación de una función desencadenada mediante un temporizador en Azure Portal.](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

    | Configuración | Valor sugerido | Descripción |
    |---|---|---|
    | **Asigne un nombre a la función** | TimerTriggerCSharp1 | Define el nombre de la función desencadenada por el temporizador. |
    | **[Programación](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | [Expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) de seis campos que programa la función para que se ejecute cada minuto. |

2. Haga clic en **Crear**. Se crea una función en el lenguaje elegido que se ejecuta cada minuto.

3. Vea la información de seguimiento que se escribe en los registros para comprobar la ejecución.

    ![Visor de registros de las funciones en Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Ahora puede cambiar la programación de la función para que se ejecute con menos frecuencia, por ejemplo, una vez cada hora. 

## <a name="update-the-timer-schedule"></a>Actualizar la programación del temporizador

1. Expanda la función y haga clic en **Integrar**. Aquí es donde se definen los enlaces de entrada y salida de la función y se establece la programación. 

2. Escriba el nuevo valor de **Programación** `0 0 */1 * * *` y, después, haga clic en **Guardar**.  

![Programación del temporizador de actualización de funciones en Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Ahora tiene una función que se ejecuta una vez cada hora. 

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una función que se ejecuta según una programación.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información sobre los desencadenadores de temporizador, vea [Programación de la ejecución de código con Azure Functions](functions-bindings-timer.md).