---
title: "Creación de una función en Azure Portal | Microsoft Docs"
description: "Cree su primera función de Azure, una aplicación sin servidor, en menos de dos minutos."
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
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ab0b218a99ab3ff98edfa075eabbd3eb2c2bd1d4
ms.openlocfilehash: 996fc80ff926117dc12180efe1949b3dbeba3f91


---
# <a name="create-a-function-from-the-azure-portal"></a>Creación de una función en Azure Portal
## <a name="overview"></a>Información general
Azure Functions es una experiencia de proceso a petición basada en eventos que amplía la plataforma de aplicaciones existente de Azure con funcionalidades para implementar código desencadenado por eventos que tienen lugar en otros servicios de Azure, productos de SaaS y sistemas locales. Con Azure Functions, sus aplicaciones se escalan en función de la demanda y solo hay que pagar por los recursos consumidos. Azure Functions permite crear unidades de código programadas o desencadenadas que se implementan en varios lenguajes de programación. Para más información acerca de Azure Functions, consulte [Información general sobre Azure Functions](functions-overview.md).

En este tema se muestra cómo utilizar Azure Portal para crear una sencilla función de Azure de Node.js, "hello world", invocada por un desencadenador de HTTP. Antes de poder crear una función en Azure Portal, debe crear explícitamente una aplicación de función en Azure App Service. Para que la aplicación de función se cree de forma automática, consulte el [otro tutorial de inicio rápido de Azure Functions](functions-create-first-azure-function.md), que es una experiencia de inicio rápido más sencilla e incluye un vídeo.

## <a name="create-a-function-app"></a>Creación de una aplicación de función
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Si ya no tiene una cuenta de Azure, consulte la experiencia [Probar Functions](https://functions.azure.com/try) o [cree una cuenta gratis de Azure](https://azure.microsoft.com/free/). Siga estos pasos para crear una aplicación de función en Azure Portal.

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. Haga clic en **+Nuevo**  >  **Compute** > **Function App**, seleccione su **Suscripción**, escriba un **Nombre de la aplicación** único, que identifica la aplicación de función, y especifique la siguiente configuración:
   
   * **[Grupo de recursos](../azure-resource-manager/resource-group-overview.md)**: seleccione **Crear nuevo** y escriba un nombre para el nuevo grupo de recursos. También puede elegir un grupo de recursos existente; sin embargo, es posible que no pueda crear un plan de App Service basado en el consumo para la aplicación de función.
   * **[Plan de hospedaje](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)** puede ser cualquiera de los siguientes: 
     * **Plan de consumo**: el tipo de plan predeterminado para Azure Functions. Al elegir un plan de consumo, también debe elegir la ubicación en el campo **Ubicación** y establecer el valor de **Asignación de memoria** (en MB). Para más información sobre cómo afecta la asignación de memoria a los costos, consulte [Precios de Functions](https://azure.microsoft.com/pricing/details/functions/). 
     * **Plan de App Service**: este plan requiere que se cree un **plan de App Service/ubicación** o se seleccione uno existente. Esta configuración determina la [ubicación, características, costo y recursos de procesos](https://azure.microsoft.com/pricing/details/app-service/) asociados a la aplicación.  
   * **Cuenta de almacenamiento**: cada aplicación de función requiere una cuenta de almacenamiento. Puede elegir una cuenta de almacenamiento existente o crear una. 
     
     ![Creación de una aplicación de función en Azure Portal](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

    Tenga en cuenta que debe especificar un **nombre de aplicación** válido, que contenga solo letras, números y guiones. El carácter de subrayado (**_**) no se permite.

3. Haga clic en **Crear** para aprovisionar e implementar la nueva función de aplicación.  

Ahora que se ha aprovisionado la aplicación de función, puede crear la primera función.

## <a name="create-a-function"></a>Creación de una función
Mediante estos pasos se crea una función desde el inicio rápido de Azure Functions.

1. En la pestaña **Inicio rápido**, haga clic en **Webhook y API**, **JavaScript** y **Crear una función**. Se crea una función predefinida de Node.js. 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (Opcional) En este momento del inicio rápido, puede realizar un recorrido rápido de las características de Azure Functions en el portal.    Después de haber realizado u omitido el recorrido, podrá probar la nueva función mediante el desencadenador de HTTP.

## <a name="test-the-function"></a>Prueba de la función
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->


