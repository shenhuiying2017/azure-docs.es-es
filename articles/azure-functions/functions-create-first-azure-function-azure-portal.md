---
title: "Creación de su primera función desde Azure Portal | Microsoft Docs"
description: "Esto es Azure. Cree su primera función de Azure desde Azure Portal."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creación de su primera función en Azure Portal

En este tema se muestra cómo crear una sencilla función de Azure "Hola mundo", invocada por una solicitud HTTP. Antes de poder crear una función en Azure Portal, debe crear una instancia de Function App en Azure App Service para hospedar la ejecución de la función.

Para completar este inicio rápido, debe tener una cuenta de Azure. Hay disponibles [cuentas gratuitas](https://azure.microsoft.com/free/). También puede [probar Azure Functions](https://azure.microsoft.com/try/app-service/functions/) sin tener que registrarse en Azure.

## <a name="create-a-function-app"></a>Creación de una aplicación de función

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Para más información, consulte [Creación de una instancia de Function App desde Azure Portal](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Creación de una función
Mediante estos pasos se crea una función en la nueva instancia de Function App si se usa el inicio rápido de Azure Functions.

1. En la pestaña **Inicio rápido**, haga clic en **Webhook y API**, elija un lenguaje para su función y haga clic en **Crear una función**. Se crea una nueva función predefinida en el lenguaje elegido.  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (Opcional) En este momento del inicio rápido, puede realizar un recorrido rápido de las características de Azure Functions en el portal. Después de haber realizado u omitido el recorrido, podrá enviar una solicitud HTTP para probar la nueva función.


## <a name="test-the-function"></a>Prueba de la función
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


