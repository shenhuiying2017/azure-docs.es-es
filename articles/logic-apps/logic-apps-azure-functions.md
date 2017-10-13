---
title: "Código personalizado para Azure Logic Apps con Azure Functions | Microsoft Docs"
description: "Cree y ejecute código personalizado para Azure Logic Apps con Azure Functions"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Adición y ejecución de código personalizado para aplicaciones lógicas con Azure Functions

Para ejecutar fragmentos de código personalizados de C# o node.js en aplicaciones lógicas, puede crear funciones personalizadas a través de Azure Functions. 
[Azure Functions](../azure-functions/functions-overview.md) ofrece una computación sin servidor en Microsoft Azure y es útil para realizar estas tareas:

* Formato o proceso avanzados de los campos de Logic Apps.
* Realización de cálculos en un flujo de trabajo.
* Extensión de la funcionalidad de las aplicaciones lógicas con funciones que se admiten en C# o node.js.

## <a name="create-custom-functions-for-your-logic-apps"></a>Creación de funciones personalizadas para las aplicaciones lógicas

Se recomienda crear una función en el portal de Azure Functions mediante las plantillas **Generic Webhook - Node** o **Generic Webhook - C#**. El resultado crea una plantilla que se rellena automáticamente que acepta `application/json` de una aplicación lógica. Las funciones que se crean con estas plantillas se detectan automáticamente y aparecen en el Diseñador de aplicaciones lógicas en **Azure Functions in my region** (Azure Functions en mi región).

En Azure Portal, en el panel **Integrar** de una función, la plantilla debe mostrar que en **Modo** está seleccionado **Webhook** y en **Tipo de webhook** está seleccionado **JSON genérico**. 

Las funciones de Webhook aceptan una solicitud y las pasan al método mediante una variable `data` . Puede acceder a las propiedades de la carga mediante una notación de puntos, como `data.function-name`. Por ejemplo, una función de JavaScript simple que convierte un valor de DateTime en una cadena de fecha es similar al siguiente ejemplo:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Llamada a Azure Functions desde Logic Apps

Para enumerar los contenedores de una suscripción y seleccionar la función a la que se desea llamado, en el Diseñador de aplicaciones lógicas, haga clic en el menú **Actions** (Acciones) y selecciónelos en **Azure Functions in my Region** (Azure Functions en mi región).

Después de seleccionar la función, se le solicita que especifique un objeto de carga de entrada. Este objeto es el mensaje que la aplicación lógica envía a la función y debe ser un objeto JSON. Por ejemplo, si desea pasar la fecha de última modificación, **Last Modified Date**, de un desencadenador de Salesforce, la carga de la función podría ser similar a la de este ejemplo:

![Fecha de última modificación][1]

## <a name="trigger-logic-apps-from-a-function"></a>Desencadenamiento de aplicaciones lógicas desde una función

Una aplicación lógica se puede desencadenar desde dentro de una función. Consulte [Aplicaciones lógicas como puntos de conexión invocables](logic-apps-http-endpoint.md). Cree una aplicación lógica que tenga un desencadenador manual y, después, desde la función, genere una solicitud HTTP POST a la dirección URL del desencadenador manual con la carga que desee que se envíe a la aplicación lógica.

### <a name="create-a-function-from-logic-app-designer"></a>Creación de una función en el Diseñador de aplicaciones lógicas

Una función de webhook de node.js también se puede crear desde el diseñador. En primer lugar, seleccione **Azure Functions in my region** (Funciones de Azure en mi región) y elija un contenedor para la función. Si todavía no dispone de ningún contenedor, tendrá que crearlo en el [portal de Funciones de Azure](https://functions.azure.com/signin). Seleccione **Crear nuevo**.  

Para generar una plantilla basada en los datos que desea procesar, especifique el objeto de contexto que piensa pasar a una función, que debe ser un objeto JSON. Por ejemplo, si pasa el contenido del archivo de una acción de FTP, la carga del contexto será similar a la de este ejemplo:

![Carga de contexto][2]

> [!NOTE]
> Puesto que este objeto no se transmitió como una cadena, el contenido se agrega directamente a la carga de JSON. Sin embargo, si el objeto no es un token JSON (es decir, una cadena o un objeto o matriz JSON), se produce un error. Para trasmitir el objeto como una cadena, agregue comillas como se muestra en la primera ilustración de este artículo.
> 

A continuación, el diseñador genera una plantilla de función que puede crear en línea. Las variables se crean previamente de acuerdo con el contexto que se va a pasar a la función.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
