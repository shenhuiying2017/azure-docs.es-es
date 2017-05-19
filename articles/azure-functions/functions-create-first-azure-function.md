---
title: "Creación de su primera función desde Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera función de Azure para su ejecución sin servidor mediante Azure Portal."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: abd508631787ba5f839a4ae2ea82e76c4bfab425
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creación de su primera función en Azure Portal

Azure Functions permite ejecutar el código en un entorno sin servidor sin necesidad de crear una máquina virtual o publicar una aplicación web. En este tema, aprenderá a usar Functions para crear una función "Hola mundo" en Azure Portal.

![Creación de una aplicación de función en Azure Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Debería tardar menos de cinco minutos en completar todos los pasos de este tema.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe tener una Function App para hospedar la ejecución de las funciones. Una Function App permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App creada correctamente.](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Después, cree una función en la nueva Function App.

## <a name="create-function"></a>Crear una función desencadenada por HTTP

1. Expanda la nueva Function App y haga clic en el botón **+** situado junto a **Funciones**.

2.  En la página **Empiece a trabajar rápidamente**, haga clic en **Webhook y API**, elija un lenguaje para su función y haga clic en **Crear esta función**. 
   
    ![Inicio rápido de funciones en Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Se crea una función en el lenguaje elegido mediante la plantilla para una función desencadenada por HTTP. Puede ejecutar la función nueva mediante el envío de una solicitud HTTP.

## <a name="test-the-function"></a>Prueba de la función

1. En la función nueva, haga clic en **</> Obtener la dirección URL de la función** y copie la **Dirección URL de la función**. 

    ![Copiar la dirección URL de la función desde Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Pegue la dirección URL de la solicitud HTTP en la barra de direcciones del explorador. Agregue la cadena de consulta `&name=<yourname>` a esta dirección URL y ejecute la solicitud. A continuación se muestra la respuesta en el explorador para la solicitud GET devuelta por la función:

    ![Respuesta de la función en el explorador.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    La dirección URL de la solicitud incluye una clave que, de forma predeterminada, es necesaria para tener acceso a la función a través de HTTP.   

## <a name="view-the-function-logs"></a>Ver los registros de función 

Cuando se ejecuta la función, se escribe información de seguimiento en los registros. Para ver el resultado del seguimiento de la ejecución anterior, vuelva a la función en el portal y haga clic en la flecha hacia arriba que encontrará en la parte inferior de la pantalla para expandir **Registros**. 

![Visor de registros de las funciones en Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Ha creado una Function App con una función simple desencadenada por HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obtener más información, vea [Enlaces HTTP y webhook en Azure Functions](functions-bindings-http-webhook.md).




