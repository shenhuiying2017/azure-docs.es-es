---
title: "Creación de una función en Azure desencadenada por Webhook de GitHub | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante Webhook de GitHub."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: cdfb5db7b304a18d6945328abc0ca7ebf2f9ec6a
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Creación de una función desencadenada por Webhook de GitHub

Obtenga información sobre cómo crear una función que se desencadena mediante una solicitud de webhook HTTP con una carga específica de GitHub.

![Función desencadenada mediante un webhook de GitHub en Azure Portal](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Requisitos previos

+ Una cuenta de GitHub con un proyecto como mínimo.
+ Una suscripción de Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creación de una Function App de Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App creada correctamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

Después, cree una función en la nueva Function App.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Crear una función desencadenada de webhook de GitHub

1. Expanda su instancia de Function App y haga clic en el botón **+**, que se encuentra junto a **Functions**. Si se trata de la primera función de Function App, seleccione **Función personalizada**. Se muestra el conjunto completo de plantillas de funciones.

    ![Página de inicio rápido de Functions en Azure Portal](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. En el campo de búsqueda, escriba `github` y seleccione el idioma que desee para la plantilla del desencadenador de webhook de GitHub. 

     ![Elección de la plantilla de desencadenador de webhook de GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Escriba un **nombre** para la función y, después, seleccione **Crear**. 

     ![Configuración de una función desencadenada por webhook de GitHub en Azure Portal](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. En la función nueva, haga clic en **</> Obtener la dirección URL de la función** y copie y guarde los valores. Haga lo mismo para **</> Obtener el secreto de GitHub**. Estos valores se utilizan para configurar el webhook en GitHub.

    ![Revisar el código de la función](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

A continuación, va a crear un webhook en el repositorio de GitHub.

## <a name="configure-the-webhook"></a>Configuración del webhook

1. En GitHub, navegue hasta un repositorio que le pertenezca. También puede utilizar los repositorios bifurcados. Si necesita bifurcar un repositorio, use <https://github.com/Azure-Samples/functions-quickstart>.

1. Haga clic en **Configuración**, después en **Webhooks** y, finalmente, en **Agregar Webhook**.

    ![Agregar un webhook de GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Use la configuración especificada en la tabla y haga clic en **Agregar Webhook**.

    ![Establecer la dirección URL y el secreto del webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Configuración | Valor sugerido | Descripción |
|---|---|---|
| **Dirección URL de carga** | Valor copiado | Use el valor devuelto por **</> Obtener la dirección URL de la función**. |
| **Secreto**   | Valor copiado | Use el valor devuelto por **</> Obtener el secreto de GitHub**. |
| **Tipo de contenido** | application/json | La función espera una carga JSON. |
| Desencadenadores de eventos | Dejarme seleccionar eventos individuales | Solo interesa desencadenar en función de eventos de comentarios de problema.  |
| | Comentario de problema |  |

Ahora, el webhook está configurado para desencadenar la función cuando se agregue un nuevo comentario de problema.

## <a name="test-the-function"></a>Prueba de la función

1. En el repositorio de GitHub, abra la pestaña **Issues** (Problemas) en una nueva ventana del explorador.

1. En la ventana nueva, haga clic en **Problema nuevo**, escriba un título y haga clic en **Submit new issue** (Enviar problema nuevo).

1. En el problema, escriba un comentario y haga clic en **Comment**(Comentario).

    ![Agregue un comentario de problema de GitHub.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Vuelva al portal y vea los registros. Debería ver una entrada de seguimiento con el texto del nuevo comentario.

     ![Vea el texto del comentario en los registros.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una función que se ejecuta cuando se recibe una solicitud de un webhook de GitHub.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para más información sobre los desencadenadores de webhook, consulte [Enlaces HTTP y webhook en Azure Functions](functions-bindings-http-webhook.md).
