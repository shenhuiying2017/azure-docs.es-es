---
title: "Creación de su primera función desde Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera función de Azure para su ejecución sin servidor mediante Azure Portal."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/07/2017
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 2b73b8b1c5773b471c49394b0cceb3916afb9a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creación de su primera función en Azure Portal

Azure Functions permite ejecutar el código en un entorno sin servidor sin necesidad de crear una máquina virtual o publicar una aplicación web. En este tema, aprenderá a usar Functions para crear una función "Hola mundo" en Azure Portal.

![Creación de una aplicación de función en Azure Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe tener una Function App para hospedar la ejecución de las funciones. Una Function App permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Después, cree una función en la nueva Function App.

## <a name="create-function"></a>Crear una función desencadenada por HTTP

1. Expanda la nueva Function App y haga clic en el botón **+** situado junto a **Funciones**.

2.  En la página **Get started quickly** (Empiece a trabajar rápidamente), seleccione **Webhook y API**, **Elija un lenguaje** para la función y haga clic en **Crear esta función**. 
   
    ![Inicio rápido de funciones en Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Se crea una función en el lenguaje elegido mediante la plantilla para una función desencadenada por HTTP. Puede ejecutar la función nueva mediante el envío de una solicitud HTTP.

## <a name="test-the-function"></a>Prueba de la función

1. En la nueva función, haga clic en **</> Get función URL** (</> Obtener URL de función), seleccione **default (Function key)** [predeterminada (tecla de función)] y, después, haga clic en **Copiar**. 

    ![Copiar la dirección URL de la función desde Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Pegue la dirección URL de la función en la barra de direcciones de su explorador. Anexe la cadena de consulta `&name=<yourname>` a esta dirección URL y presione la tecla `Enter` en el teclado para ejecutar la solicitud. A continuación, se muestra un ejemplo de la respuesta devuelta por la función en el navegador Edge:

    ![Respuesta de la función en el explorador.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    La dirección URL de la solicitud incluye una clave que, de forma predeterminada, es necesaria para tener acceso a la función a través de HTTP.   

3. Cuando se ejecuta la función, se escribe información de seguimiento en los registros. Para ver el resultado del seguimiento de la ejecución anterior, vuelva a la función en el portal y haga clic en la flecha hacia arriba que encontrará en la parte inferior de la pantalla para expandir **Registros**. 

   ![Visor de registros de las funciones en Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una Function App con una función simple desencadenada por HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información, vea [Enlaces HTTP y webhook en Azure Functions](functions-bindings-http-webhook.md).



