---
title: "Creación de una función en Azure desencadenada por Webhook de GitHub | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante Webhook de GitHub."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Creación de una función desencadenada por Webhook de GitHub

Obtenga información sobre cómo crear una función que desencadena Webhook de GitHub. 

![Creación de una aplicación de función en Azure Portal](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

En este tema se requiere los recursos creados en el tema [Creación de su primera función desde Azure Portal](functions-create-first-azure-function.md).

También necesita una cuenta de GitHub. Puede [registrarse para obtener una cuenta gratuita de GitHub](https://github.com/join), en caso de que no tenga ninguna. 

Debería tardar menos de cinco minutos en completar todos los pasos de este tema.

## <a name="find-your-function-app"></a>Búsqueda de la aplicación de la función    

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/). 

2. En la barra de búsqueda en la parte superior del portal, escriba el nombre de la aplicación de la función y selecciónela en la lista.

## <a name="create-function"></a>Creación de una función desencadenada por Webhook de GitHub

1. En la aplicación de la función, haga clic en el botón  **+**  situado junto a **Funciones**, haga clic en la plantilla **GitHubWebHook** para el idioma que desee y haga clic en **Crear**.
   
    ![Cree una función desencadenada de webhook de GitHub en Azure Portal.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Haga clic en **</> Obtener la dirección URL de la función** y copie y guarde los valores. Haga lo mismo para **</> Obtener el secreto de GitHub**. Estos valores se utilizan para configurar el webhook en GitHub. 

    ![Revisar el código de la función](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
A continuación, va a crear un webhook en el repositorio de GitHub. 

## <a name="configure-the-webhook"></a>Configuración del webhook
1. En GitHub, navegue hasta un repositorio que le pertenezca. También puede utilizar los repositorios bifurcados.
 
2. Haga clic en **Configuración**, después en **Webhooks** y, finalmente, en **Agregar Webhook**.
   
    ![Agregar un webhook de GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Pegue la dirección URL y el secreto de la función en **Payload URL** (Dirección URL de carga) y **Secret** (Secreto) y seleccione **application/json** en **Content type** (Tipo de contenido).

4. Haga clic en **Let me select individual events** (Dejarme seleccionar eventos individuales), seleccione **Issue comment** (Emitir comentario) y haga clic en **Add webhook** (Agregar webhook).
   
    ![Establecer la dirección URL y el secreto del webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Ahora, el webhook está configurado para desencadenar la función cuando se agregue un nuevo comentario de problema. 

## <a name="test-the-function"></a>Prueba de la función
1. En el repositorio de GitHub, abra la pestaña **Issues** (Problemas) en una nueva ventana del explorador.

2. En la ventana nueva, haga clic en **New Issue** (Problema nuevo), escriba un título y haga clic en **Submit new issue** (Enviar problema nuevo). 

2. En el problema, escriba un comentario y haga clic en **Comment**(Comentario). 

3. En la ventana de GitHub, haga clic en **Edit** (Editar) junto al nuevo webhook, desplácese hacia abajo hasta **Recent Deliveries** (Entregas recientes) y compruebe que la solicitud de webhook la ha procesado la función. 
 
    ![Establecer la dirección URL y el secreto del webhook](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   La respuesta de la función debe contener `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


