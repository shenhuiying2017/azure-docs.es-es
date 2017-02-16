---
title: "Escenario de aplicación lógica: creación de un desencadenador de Service Bus de Azure Functions| Microsoft Docs"
description: "Uso de Funciones de Azure para crear un desencadenador de Bus de servicio para una aplicación lógica"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 83e93d155ca0b51227c91cf5ceb7ec75032a7c9c


---
# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Escenario de aplicación lógica: creación de un desencadenador de Bus de servicio de Azure mediante Funciones de Azure
Puede utilizar Funciones de Azure para crear un desencadenador para una aplicación lógica cuando necesite implementar un agente de escucha o una tarea de ejecución prolongada. Por ejemplo, puede crear una función de Azure que escuche en una cola y que active inmediatamente una aplicación lógica como desencadenador de push.

## <a name="build-the-logic-app"></a>Compilación de la aplicación lógica
En este ejemplo, se ejecuta una función para cada aplicación lógica que necesite desencadenarse. En primer lugar, cree una aplicación lógica que tenga un desencadenador de solicitud HTTP. La función llama a ese punto de conexión siempre que se reciba un mensaje en cola.  

1. Cree una aplicación lógica nueva y seleccione el desencadenador **Manual - When an HTTP Request is Received** (Manual: cuando se recibe una solicitud HTTP).  
   Opcionalmente, puede especificar un esquema JSON para usar con el mensaje de cola mediante una herramienta como [jsonschema.net](http://jsonschema.net). Pegue el esquema en el desencadenador. Esto ayuda al diseñador a conocer la forma de los datos y hacer fluir más fácilmente las propiedades a través del flujo de trabajo.
2. Agregue los pasos adicionales que desee que tengan lugar después de recibir un mensaje en cola. Por ejemplo, enviar un correo electrónico a través de Office 365.  
3. Guarde la aplicación lógica para generar la dirección URL de devolución de llamada para el desencadenador a esta aplicación lógica. La dirección URL aparece en la tarjeta de desencadenador.

![La dirección URL de devolución de la llamada aparece en la tarjeta de desencadenador.][1]

## <a name="build-the-function"></a>Compilación de la función
A continuación, es preciso que cree una función que actúe como desencadenador y escuche a la cola.

1. En el [portal de Azure Functions](https://functions.azure.com/signin), seleccione **Nueva función** y, luego, seleccione la plantilla **ServiceBusQueueTrigger - C#**.
   
    ![portal de Funciones de Azure][2]
2. Configure la conexión a la cola de Bus de servicio (que va a utilizar el agente de escucha `OnMessageReceive()` del SDK del Bus de servicio de Azure).
3. Escriba una función simple para llamar al punto de conexión de la aplicación lógica (creado anteriormente) usando el mensaje de cola como desencadenador. Este es un ejemplo completo de una función. El ejemplo utiliza un tipo de contenido de mensaje `application/json` , pero puede cambiarlo si es necesario.
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para probar, agregue un mensaje en cola a través de una herramienta como el [Explorador de Bus de servicio](https://github.com/paolosalvatori/ServiceBusExplorer). Consulte la aplicación lógica inmediatamente después de que la función reciba el mensaje.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png



<!--HONumber=Jan17_HO3-->


