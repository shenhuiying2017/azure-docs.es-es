---
title: "Crear una función que se ejecuta según una programación en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear una función de Azure que se ejecuta según la programación que usted defina."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Cree una función en Azure que se desencadena mediante un temporizador

Obtenga información sobre cómo usar Azure Functions para crear una función que se ejecuta según la programación que usted defina. 

![Creación de una aplicación de función en Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Debería tardar menos de cinco minutos en completar todos los pasos de este tema.

## <a name="prerequisites"></a>Requisitos previos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

En este tema, creará una función desencadenada por el temporizador en la Function App existente. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Crear una función desencadenada por el temporizador

1. Expanda la Function App, haga clic en el botón **+** situado junto a **Funciones** y haga clic en la plantilla **TimerTrigger** para el idioma que quiera. Después, use la configuración especificada en la tabla y haga clic en **Crear**.

    | Configuración      |  Valor sugerido   | Descripción                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Asigne un nombre a la función** | TimerTriggerCSharp1 | Define el nombre de la función desencadenada por el temporizador.
    | **[Programación](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | [Expresión CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) de seis campos que programa la función para que se ejecute cada minuto. |

    Se crea una función en el lenguaje elegido que se ejecuta cada minuto. 

4. Vea la información de seguimiento que se escribe en los registros para comprobar la ejecución. 

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




