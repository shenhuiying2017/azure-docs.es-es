---
title: "Creación de un webhook o una función de API de Azure | Microsoft Docs"
description: "Use Azure Functions para crear una función sin servidor que se invoca mediante una llamada de API o WebHook."
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
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: a8f6d111a010666bf4aaaf05e061381cc8fffed0
ms.openlocfilehash: 23a65319fe1825e2ba51f2fd5a2d0b65ca499472


---
# <a name="create-a-webhook-or-api-azure-function"></a>Creación de un webhook o una función de API de Azure
Azure Functions es una experiencia de procesos a petición orientada a eventos que permite crear unidades de código programadas o desencadenadas que se implementan en diversos lenguajes de programación. Para más información acerca de Azure Functions, consulte [Información general sobre Azure Functions](functions-overview.md).

En este tema se muestra cómo crear una función de JavaScript que se invoca mediante un webhook de GitHub. La función nueva se crea a partir de una plantilla predefinida del portal de Funciones de Azure. También puede ver un vídeo de corta duración en el que podrá seguir la realización de estos pasos en el portal.

Los pasos generales de este tutorial también se pueden utilizar para crear una función en C# o F #, en lugar de JavaScript. 

## <a name="watch-the-video"></a>Visualización del vídeo
El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial 

>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

+ Una cuenta de Azure activa. Si aún no tiene ninguna, puede [registrarse para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/).  
 También puede usar la experiencia de las [funciones prueba](https://functions.azure.com/try) para completar este tutorial sin una cuenta de Azure.
+ Una cuenta de GitHub. Puede [registrarse para obtener una cuenta gratuita de GitHub](https://github.com/join), en caso de que no tenga ninguna. 

## <a name="create-a-webhook-triggered-function-from-the-template"></a>Creación de una función desencadenada por un webhook a partir de la plantilla
Una aplicación de función hospeda la ejecución de sus funciones en Azure. 

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.

2. Si tiene una aplicación de función existente, selecciónela en **Your function apps** (Sus aplicaciones de función) y haga clic en **Abrir**. Para crear una aplicación de función, escriba un **nombre** único para ella o acepte el que se genera, seleccione su **región** preferida y haga clic en **Crear y comenzar**. 

3. En la aplicación de función, haga clic en **+ Nueva función** > **Webhook de GitHub: JavaScript** > **Crear**. En este paso se crea una función con un nombre predeterminado basado en la plantilla especificada. Como alternativa, cree una función de C# o de F#.
   
    ![Crear una función desencadenada de webhook de GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. En la pestaña **Desarrollar**, observe la función express.js de ejemplo en la ventana **Código**. Esta función recibe una solicitud de GitHub de un webhook de comentario del problema, registra el texto del problema y envía una respuesta al webhook como `New GitHub comment: <Your issue comment text>`.

    ![Revisar el código de la función](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. Copie los valores de **Dirección URL de función** y **Secreto de GitHub** y guárdelos. Estos valores se usarán en la sección siguiente para configurar el webhook en GitHub. 

2. Haga clic en **Probar**, observe el cuerpo JSON predefinido de un comentario de problema en el **cuerpo de la solicitud** y haga clic en **Ejecutar**. 

    ![Función de webhook de prueba en el portal](./media/functions-create-a-web-hook-or-api-function/functions-test-webhook-in-portal.png)
   
    > [!NOTE]
    > Siempre puede probar el derecho de una función basada en plantilla en la pestaña **Desarrollar**; solo tiene que suministrar los datos JSON del cuerpo esperados y haga clic en botón **Ejecutar**. En este caso, la plantilla tiene un cuerpo predefinido para un comentario de problema. 

A continuación, creará el webhook real en el repositorio de GitHub.

## <a name="configure-the-webhook"></a>Configuración del webhook
1. En GitHub, navegue hasta un repositorio que le pertenezca. También puede utilizar los repositorios bifurcados.
 
2. Haga clic en **Configuración** > **Webhooks y servicios** > **Agregar webhook**.
   
    ![Agregar un webhook de GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   

3. Pegue la dirección URL y el secreto de la función en **Payload URL** (Dirección URL de carga) y **Secret** (Secreto) y seleccione **application/json** en **Content type** (Tipo de contenido).

4. Haga clic en **Let me select individual events** (Dejarme seleccionar eventos individuales), seleccione **Issue comment** (Emitir comentario) y haga clic en **Add webhook** (Agregar webhook).
   
    ![Establecer la dirección URL y el secreto del webhook](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

En este punto, el webhook de GitHub está configurado para desencadenar la función cuando se agregue un nuevo comentario de problema.  
Ahora, ha llegado el momento de probarlo.

## <a name="test-the-function"></a>Prueba de la función
1. En el repositorio de GitHub, abra la pestaña **Issues** (Problemas) en una nueva ventana del explorador.

2. En la ventana nueva, haga clic en **New Issue** (Problema nuevo), escriba un título y haga clic en **Submit new issue** (Enviar problema nuevo). También puede abrir un problema existente.

2. En el problema, escriba un comentario y haga clic en **Comment**(Comentario). 

3. En la ventana de GitHub, haga clic en **Edit** (Editar) junto a la nueva webhook, desplácese hacia abajo hasta **Recent Deliveries** (Entregas recientes) y compruebe que se ha enviado una solicitud de webhook y que el cuerpo de la respuesta es `New GitHub comment: <Your issue comment text>`.

3. En el portal de Functions, desplácese hacia abajo hasta los registros y vea que la función se ha desencadenado y que el valor `New GitHub comment: <Your issue comment text>` está escrito en los registros de streaming.

## <a name="next-steps"></a>Pasos siguientes
Consulte estos temas para más información sobre Azure Functions.

* [Azure Functions developer reference](functions-reference.md)  
  (Referencia para desarrolladores de Funciones de Azure) contiene las referencias del programador para codificar las funciones.
* [Testing Azure Functions](functions-test-a-function.md)  
  describe las diversas herramientas y técnicas para probar sus funciones.
* [How to scale Azure Functions](functions-scale.md)  
  Trata los planes de servicio disponibles con Azure Functions, incluido el plan de hospedaje de Consumo, y cómo elegir el plan adecuado.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->


