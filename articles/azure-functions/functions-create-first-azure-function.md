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
ms.date: 09/08/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e446766c0f3d19a8cce585d708e8e7a809593408


---
# <a name="create-your-first-azure-function"></a>Creación de su primera función de Azure
## <a name="overview"></a>Información general
Azure Functions es una experiencia de proceso a petición basada en eventos que amplía la plataforma de aplicaciones existente de Azure con funcionalidades para implementar código desencadenado por eventos que tienen lugar en otros servicios de Azure, productos de SaaS y sistemas locales. Con Funciones de Azure, sus aplicaciones escalan en función de la demanda y solo hay que pagar por los recursos consumidos. Funciones de Azure permite crear unidades de código programadas o desencadenadas que se implementan en varios lenguajes de programación. Para más información acerca de Azure Functions, consulte [Información general sobre Azure Functions](functions-overview.md).

En este tema se muestra cómo utilizar el inicio rápido de Azure Functions en el portal para crear una sencilla función de Node.js "hello world" invocada por un desencadenador de HTTP. También puede ver un vídeo de corta duración en el que podrá seguir la realización de estos pasos en el portal.

## <a name="watch-the-video"></a>Visualización del vídeo
El siguiente vídeo muestra cómo realizar los pasos básicos de este tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Creación de una función desde el inicio rápido
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Siga estos pasos para crear una nueva aplicación de función, así como la nueva función. La nueva aplicación de función se crea con una configuración predeterminada. Para obtener un ejemplo de cómo crear la aplicación de función de forma explícita, consulte [el tutorial de inicio rápido de Azure Functions](functions-create-first-azure-function-azure-portal.md).

Para poder crear la primera función, es necesario tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [tiene a su disposición la creación de una cuenta gratis](https://azure.microsoft.com/free/).

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.
2. Escriba un **Nombre** único para la nueva aplicación de función o acepte el que se ha generado, seleccione la **Región** y, a continuación, haga clic en **Create + get started** (Crear + comenzar). 
3. En la pestaña **Inicio rápido**, haga clic en **Webhook y API**, **JavaScript** y **Crear una función**. Se crea una función predefinida de Node.js. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) En este momento del inicio rápido, puede realizar un recorrido rápido de las características de Azure Functions en el portal.    Una vez que haya realizado u omitido el recorrido, podrá probar la nueva función mediante el desencadenador de HTTP.

## <a name="test-the-function"></a>Prueba de la función
Puesto que los inicios rápidos de Azure Functions contienen código funcional, puede probar la nueva función inmediatamente.

1. En la pestaña **Desarrollar**, revise la ventana **Código** y observe que este código Node.js espera una solicitud HTTP con un valor de *nombre* pasado en el cuerpo del mensaje o en una cadena de consulta. Cuando se ejecuta la función, este valor se devuelve en el mensaje de respuesta.
   
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)
2. Desplácese hacia abajo hasta el cuadro de texto **Cuerpo de la solicitud**, cambie el valor de la propiedad *name* a su nombre y haga clic en **Ejecutar**. Verá que se desencadena la ejecución a partir de una solicitud HTTP de prueba, se escribe información en los registros de streaming y se muestra la respuesta "hello" en **Output**(salida). 
3. Para desencadenar la ejecución de la misma función desde otra ventana o pestaña del explorador, copie el valor de la **dirección URL de función** de la pestaña **Desarrollar** y péguelo en la barra de direcciones; a continuación, agregue el valor de la cadena de consulta `&name=yourname` y presione ENTRAR. La misma información se escribe en los registros y el explorador muestra la respuesta "hello" como antes.

## <a name="next-steps"></a>Pasos siguientes
Este inicio rápido demuestra una ejecución muy sencilla de una función desencadenada de HTTP básica. Consulte estos temas para más información acerca de cómo aprovechar el potencial de Funciones de Azure en sus aplicaciones.

* [Azure Functions developer reference](functions-reference.md)  
   contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.
* [Prueba de Azure Functions](functions-test-a-function.md)  
   describe las diversas herramientas y técnicas para probar sus funciones.
* [How to scale Azure Functions](functions-scale.md)  
   aborda los planes de servicio disponibles con Azure Functions, incluido el plan de servicio dinámico, y cómo elegir el plan adecuado. 
* [¿Qué es Servicios de aplicaciones de Azure?](../app-service/app-service-value-prop-what-is.md)  
   Funciones de Azure aprovecha la plataforma de Servicio de aplicaciones de Azure para obtener funcionalidad básica como implementaciones, variables de entorno y diagnósticos. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!---HONumber=Nov16_HO2-->


