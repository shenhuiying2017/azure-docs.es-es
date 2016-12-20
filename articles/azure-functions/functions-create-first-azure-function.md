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
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: d40304cd4f7ede4e37d32f66330ac712d325f898
ms.openlocfilehash: ea7d8acec83103c981bad279507dd7ead5f72370


---
# <a name="create-your-first-azure-function"></a>Creación de su primera función de Azure
## <a name="overview"></a>Información general
Azure Functions es una experiencia de proceso a petición basada en eventos que amplía la plataforma de aplicaciones existente de Azure con funcionalidades para implementar código desencadenado por eventos que tienen lugar en otros servicios de Azure, productos de SaaS y sistemas locales. Con Azure Functions, sus aplicaciones se escalan en función de la demanda y solo hay que pagar por los recursos consumidos. Azure Functions permite crear unidades de código programadas o desencadenadas que se implementan en varios lenguajes de programación. Para más información acerca de Azure Functions, consulte [Información general sobre Azure Functions](functions-overview.md).

En este tema se muestra cómo utilizar el inicio rápido de Azure Functions en el portal para crear una sencilla función de Node.js "hello world" invocada por un desencadenador de HTTP. También puede ver un vídeo de corta duración en el que podrá seguir la realización de estos pasos en el portal.

## <a name="watch-the-video"></a>Visualización del vídeo
El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Creación de una función desde el inicio rápido
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Siga estos pasos para crear una aplicación de función con la nueva función. La nueva aplicación de función se crea con una configuración predeterminada. Para obtener un ejemplo de cómo crear la aplicación de función de forma explícita, consulte [el tutorial de inicio rápido de Azure Functions](functions-create-first-azure-function-azure-portal.md).

Para poder crear la primera función, es necesario tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [tiene a su disposición la creación de una cuenta gratis](https://azure.microsoft.com/free/).

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.
2. Escriba un **Nombre** único para la nueva aplicación de función o acepte el que se ha generado, seleccione la **Región** y, a continuación, haga clic en **Create + get started** (Crear + comenzar). 
3. En la pestaña **Inicio rápido**, haga clic en **Webhook y API**, **JavaScript** y **Crear una función**. Se crea una función predefinida de Node.js. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) En este momento del inicio rápido, puede realizar un recorrido rápido de las características de Azure Functions en el portal. Después de haber realizado u omitido el recorrido, podrá probar la nueva función mediante el desencadenador de HTTP.

## <a name="test-the-function"></a>Prueba de la función
Puesto que los inicios rápidos de Azure Functions contienen código funcional, puede probar la nueva función inmediatamente.

1. En la pestaña **Desarrollar**, revise la ventana **Código** y observe que este código Node.js espera una solicitud HTTP con un valor de *nombre* pasado en el cuerpo del mensaje o en una cadena de consulta. Cuando se ejecuta la función, este valor se devuelve en el mensaje de respuesta.
   
2. Haga clic en **Probar** para mostrar el panel de solicitud de prueba HTTP integrado para la función.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. En el cuadro de texto **Cuerpo de la solicitud**, cambie el valor de la propiedad *name* a su nombre y haga clic en **Ejecutar**. Verá que una solicitud HTTP de prueba desencadena la ejecución, la información se escribe en los registros de streaming y se muestra la respuesta "hello" en **Salida**.
 
3. Para desencadenar la ejecución de la misma función desde otra ventana o pestaña del explorador, copie el valor de la **dirección URL de función** de la pestaña **Desarrollar** y péguelo en la barra de direcciones. Después, agregue el valor de la cadena de consulta `&name=yourname` a la dirección URL y presione Entrar. La misma información se escribe en los registros y el explorador muestra la respuesta "hello" como antes.

## <a name="next-steps"></a>Pasos siguientes
Este inicio rápido demuestra una ejecución sencilla de una función desencadenada de HTTP básica. Para más información sobre el uso de Azure Functions en sus aplicaciones, consulte los temas siguientes:

* [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)  
   contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.
* [Prueba de Azure Functions](functions-test-a-function.md)  
   describe las diversas herramientas y técnicas para probar sus funciones.
* [How to scale Azure Functions](functions-scale.md)  
  Aborda los planes de servicio disponibles con Azure Functions, incluido el plan de servicio dinámico, y cómo elegir el plan adecuado. 
* [¿Qué es Servicios de aplicaciones de Azure?](../app-service/app-service-value-prop-what-is.md)  
   Azure Functions aprovecha la plataforma Azure App Service para obtener una funcionalidad básica, como implementaciones, variables de entorno y diagnósticos. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO3-->


