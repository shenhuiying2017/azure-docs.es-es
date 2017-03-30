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
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Creación de su primera función de Azure

En este tema se muestra cómo utilizar la guía de inicio rápido de Azure Functions en el portal para crear una sencilla función "hello world" invocada por una solicitud HTTP. Para más información acerca de Azure Functions, consulte [Información general sobre Azure Functions](functions-overview.md).

Antes de empezar, debe tener una cuenta de Azure. Hay disponibles [cuentas gratuitas](https://azure.microsoft.com/free/). También puede [probar Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sin tener que registrarse en Azure.

## <a name="create-a-function-from-the-portal-quickstart"></a>Creación de una función desde guía de inicio rápido en el portal

1. Vaya al [portal de Azure Functions](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure. 
 
2. Escriba un **nombre** único para la nueva aplicación de función o acepte el que se ha generado automáticamente, seleccione la **región** que prefiera y haga clic en **Create + get started** (Crear + comenzar). Un nombre válido solo puede contener letras, números y guiones. El carácter de subrayado (**_**) no se permite.

3. En la pestaña **Inicio rápido**, haga clic en **Webhook y API**, elija un lenguaje para su función y haga clic en **Crear una función**. Se crea una nueva función predefinida en el lenguaje elegido. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Opcional) En este momento del inicio rápido, puede realizar un recorrido rápido de las características de Azure Functions en el portal. Después de haber realizado u omitido el recorrido, podrá enviar una solicitud HTTP para probar la nueva función.

## <a name="test-the-function"></a>Prueba de la función
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Visualización del vídeo
El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


