<properties
   pageTitle="Creación de un enlace web o una función de API de Azure| Microsoft Azure"
   description="Use las funciones de Azure para crear una función que se invoque mediante una llamada de API o webhook."
   services="azure-functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/30/2016"
   ms.author="glenga"/>
   
# Creación de un webhook o una función de API de Azure

Funciones de Azure es una experiencia de procesos a petición orientada a eventos que permite crear unidades de código programadas o desencadenadas que se implementan en diversos lenguajes de programación. Para más información acerca de Funciones de Azure, consulte [Información general sobre Funciones de Azure](functions-overview.md).

En este tema se muestra cómo crear una nueva función de Node.js que se invoca mediante un webhook de GitHub. La función nueva se crea a partir de una plantilla predefinida del portal de Funciones de Azure. También puede ver un vídeo de corta duración en el que podrá seguir la realización de estos pasos en el portal.

## Visualización del vídeo

El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial.

[AZURE.VIDEO create-a-web-hook-or-api-azure-function]

##Creación de una función desencadenada por un webhook a partir de la plantilla

Una aplicación de función hospeda la ejecución de sus funciones en Azure. Para poder crear una función, tiene que tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [tiene a su disposición gratuitas](https://azure.microsoft.com/free/).

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.

2. Si tiene una aplicación de función existente, selecciónela en **Your function apps** (Sus aplicaciones de función) y haga clic en **Abrir**. Para crear una aplicación de función, escriba un **nombre** único para ella o acepte el que se genera, seleccione su **región** preferida y haga clic en **Create + get started** (Crear y comenzar).

3. En la aplicación de función, haga clic en **+ Nueva función** > **GitHub Webhook - Node** (Webhook de GitHub: nodo) > **Crear**. Se crea una función con un nombre predeterminado basado en la plantilla especificada.

	![Crear función de webhook de GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png)

4. En la pestaña **Desarrollar**, observe la función express.js de ejemplo en la ventana **Código**. Esta función recibe una solicitud de GitHub de un webhook de comentario del problema, registra el texto del problema y envía una respuesta al webhook como `New GitHub comment: <Your issue comment text>`.


	![Crear función de webhook de GitHub](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png)

5. Copie los valores de **Function URL** (URL de función) y **GitHub Secret** (Secreto de GitHub). Los necesitará al crear el webhook en GitHub.

6. Desplácese hacia abajo hasta **Ejecutar**, observe el cuerpo JSON predefinido de un comentario de problema en el cuerpo de la solicitud y haga clic en **Ejecutar**.
 
	Siempre puede probar el derecho de una función basada en plantilla en la pestaña **Desarrollar**; solo tiene que suministrar los datos JSON del cuerpo esperados y haga clic en botón **Ejecutar**. En este caso, la plantilla tiene un cuerpo predefinido para un comentario de problema.
 
A continuación, creará el webhook real en el repositorio de GitHub.

##Configuración del webhook

1. En GitHub, vaya a un repositorio que tenga; esto incluye todos los repositorios que haya forjado.
 
2. Haga clic en **Settings** (Configuración) > **Webhooks & services** (Webhooks y servicios) > **Add webhook** (Agregar webhook).

	![Crear función de webhook de GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)

3. Pegue la dirección URL y el secreto de la función en **Payload URL** (URL de carga) y **Secret** (Secreto), a continuación haga clic en **Let me select individual events** (Dejarme seleccionar eventos individuales), seleccione **Issue comment** (Comentario de problema) y haga clic en **Add webhook** (Agregar webhook).

	![Crear función de webhook de GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png)

En este punto, el webhook de GitHub está configurado para desencadenar la función cuando se agregue un nuevo comentario de problema. Ahora, ha llegado el momento de probarlo.

##Prueba de la función

1. En el repositorio de GitHub, abra la pestaña **Issues** (Problemas) en una nueva ventana de explorador, haga clic en **New Issue** (Nuevo problema), escriba un título y haga clic en **Submit new issue** (Enviar nuevo problema). También puede abrir un problema existente.

2. En el problema, escriba un comentario y haga clic en **Comment** (Comentario). En este punto, puede volver a su webhook en GitHub y en **Recent Deliveries** (Entregas recientes) ver que se ha enviado una solicitud de webhook y que el cuerpo de respuesta es `New GitHub comment: <Your issue comment text>`.

3. En el portal de Functions, desplácese hacia abajo hasta los registros y vea que la función se ha desencadenado y que el valor `New GitHub comment: <Your issue comment text>` está escrito en los registros de streaming.


##Pasos siguientes

Consulte estos temas para más información sobre Azure Functions.

+ [Azure Functions developer reference](functions-reference.md) (Referencia para desarrolladores de Funciones de Azure) contiene las referencias del programador para codificar las funciones.
+ [Testing Azure Functions](functions-test-a-function.md) (Prueba de Funciones de Azure) describe las diversas herramientas y técnicas para probar sus funciones.
+ [How to scale Azure Functions](functions-scale.md) (Cómo escalar Funciones de Azure) aborda los planes de servicio disponibles con Funciones de Azure, incluido el plan de servicio dinámico, y cómo elegir el plan adecuado.


[AZURE.INCLUDE [Nota de introducción](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0907_2016-->