---
title: "Creación de su primera función de Azure | Microsoft Docs"
description: "Cree su primera función de Azure, una aplicación sin servidor, en menos de dos minutos."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>Creación de su primera función de Azure
## <a name="overview"></a>Información general
Azure Functions es una experiencia de proceso a petición basada en eventos que amplía la plataforma de aplicaciones existente de Azure con funcionalidades para implementar código desencadenado por eventos que tienen lugar en otros servicios de Azure, productos de SaaS y sistemas locales. Con Azure Functions, sus aplicaciones se escalan en función de la demanda y solo hay que pagar por los recursos consumidos. Azure Functions permite crear unidades de código programadas o desencadenadas que se implementan en varios lenguajes de programación. Para más información acerca de Azure Functions, consulte [Información general sobre Azure Functions](functions-overview.md).

En este tema se muestra cómo utilizar el inicio rápido de Azure Functions en el portal para crear una sencilla función JavaScript "hello world" que invoca un desencadenador de HTTP. También puede ver un vídeo de corta duración en el que podrá seguir la realización de estos pasos en el portal.

## <a name="watch-the-video"></a>Visualización del vídeo
El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Creación de una función desde el inicio rápido
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Siga estos pasos para crear una aplicación de función con la nueva función. La nueva aplicación de función se crea con una configuración predeterminada. Para obtener un ejemplo de cómo crear la aplicación de función de forma explícita, consulte [el tutorial de inicio rápido de Azure Functions](functions-create-first-azure-function-azure-portal.md).

Para poder crear la primera función, es necesario tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [tiene a su disposición la creación de una cuenta gratis](https://azure.microsoft.com/free/).

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.
2. Escriba un **nombre** único para la nueva aplicación de función o acepte el que se ha generado automáticamente, seleccione la **región** que prefiera y haga clic en **Create + get started** (Crear + comenzar). Tenga en cuenta que debe especificar un nombre válido, que contenga solo letras, números y guiones. El carácter de subrayado (**_**) no se permite.
3. En la pestaña **Inicio rápido**, haga clic en **Webhook y API**, **JavaScript** y **Crear una función**. Se crea una función predefinida de JavaScript. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) En este momento del inicio rápido, puede realizar un recorrido rápido de las características de Azure Functions en el portal. Después de haber realizado u omitido el recorrido, podrá probar la nueva función mediante el desencadenador de HTTP.

## <a name="test-the-function"></a>Prueba de la función
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


