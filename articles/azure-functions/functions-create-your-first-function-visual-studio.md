---
title: "Creación de la primera función en Azure mediante Visual Studio | Microsoft Docs"
description: "Cree y publique una función simple desencadenada por HTTP en Azure mediante Herramientas de Azure Functions para Visual Studio."
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: azure functions, funciones, procesamiento de eventos, proceso, arquitectura sin servidor
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/05/2017
ms.author: rachelap, glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: be7a9979ba7e6aa26c60b24bcc892ca35af3c1fc
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="create-your-first-function-using-visual-studio"></a>Creación de la primera función mediante Visual Studio

Azure Functions permite ejecutar el código en un entorno sin servidor sin necesidad de crear una máquina virtual o publicar una aplicación web.

> [!IMPORTANT]
> En este tema se utiliza una versión preliminar de Visual Studio para completar sus pasos. Asegúrese de que ha instalado [Visual Studio 2017 Preview versión 15.3](https://www.visualstudio.com/vs/preview/) antes de continuar.

En este tema, aprenderá a usar Herramientas de Azure Functions para Visual Studio 2017 para crear y probar una función de "Hola mundo" localmente. Luego publicará el código de función en Azure.

![Código Azure Functions en un proyecto de Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, instale:

* [Versión 15.3 de Visual Studio 2017 Preview](https://www.visualstudio.com/vs/preview/), incluyendo la carga de trabajo **Desarrollo de Azure**.

    ![Instalar Visual Studio 2017 con la cargas de trabajo Desarrollo de Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-functions-tools-for-visual-studio-2017"></a>Instalación de Herramientas de Azure Functions para Visual Studio 2017

Antes de comenzar, debe descargar e instalar Herramientas de Azure Functions para Visual Studio 2017. Estas herramientas solo pueden utilizarse con la versión 15.3 de Visual Studio 2017 Preview, o cualquier versión posterior. Si ya ha instalado Herramientas de Azure Functions, puede omitir esta sección.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Creación de un proyecto de Azure Functions en Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Ahora que ha creado el proyecto, puede crear la primera función.

## <a name="create-the-function"></a>Creación de la función

En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Agregar** > **Nuevo elemento**. Seleccione **Función de Azure** y haga clic en **Agregar**.

Seleccione **HttpTrigger**, escriba un **nombre de función**, seleccione **Anónimo** en **Derechos de acceso**y haga clic en **Crear**. Cualquier cliente puede acceder a la función que se crea mediante una solicitud HTTP. 

![Crear una nueva función de Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Ahora que ha creado una función desencadenada por HTTP, puede probarla en el equipo local.

## <a name="test-the-function-locally"></a>Prueba local de la función

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

Copie la dirección URL de la función de los resultados del runtime de Azure Functions.  

![Runtime local de Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

 Pegue la dirección URL de la solicitud HTTP en la barra de direcciones del explorador. Agregue la cadena de consulta `&name=<yourname>` a esta dirección URL y ejecute la solicitud. A continuación se muestra la respuesta en el explorador para la solicitud GET local devuelta por la función: 

![Respuesta de localhost de la función en el explorador](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

Para detener la depuración, haga clic en el botón **Detener** de la barra de herramientas de Visual Studio.

Después de comprobar que la función se ejecuta correctamente en el equipo local es el momento de publicar el proyecto en Azure.

## <a name="publish-the-project-to-azure"></a>Publicar el proyecto en Azure

Debe tener una aplicación de función en la suscripción de Azure para poder publicar el proyecto. Las aplicaciones de función se pueden crear directamente desde Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Prueba de una función en Azure

Copie la URL base de la aplicación de función de la página de perfil de publicación. Reemplace la parte `localhost:port` de la dirección URL que usó al probar la función localmente por la nueva URL base. Como antes, asegúrese de que agrega la cadena de consulta `&name=<yourname>` a esta dirección URL y ejecute la solicitud.

La dirección URL que llama a la función desencadenada por HTTP es similar a la siguiente:

    http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

Pegue la dirección URL de la solicitud HTTP en la barra de direcciones del explorador. A continuación se muestra la respuesta en el explorador para la solicitud GET remota devuelta por la función: 

![Respuesta de la función en el explorador](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>Pasos siguientes

Ha usado Visual Studio para crear una aplicación de función en C# con una función simple desencadenada por HTTP. 

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Para más información acerca de la realización de pruebas y la depuración locales mediante Azure Functions Core Tools, consulte [Codificación y comprobación de las funciones de Azure en un entorno local](functions-run-local.md). Para más información sobre el desarrollo de funciones como bibliotecas de clases .NET, consulte [Utilizar bibliotecas de clases de .NET con Azure Functions](functions-dotnet-class-library.md). 


