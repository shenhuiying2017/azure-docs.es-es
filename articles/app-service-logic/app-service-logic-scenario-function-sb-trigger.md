<properties
   pageTitle="Escenario de aplicación lógica: desencadenador de bus de servicio de función de Azure | Microsoft Azure"
   description="Vea cómo se utiliza Funciones de Azure como desencadenador de bus de servicio para Aplicaciones lógicas"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# Escenario de aplicación lógica: desencadenador de bus de servicio de función de Azure

Funciones de Azure puede utilizarse como desencadenador de Aplicaciones lógicas, siempre que sea preciso implementar agentes de escucha o tareas de ejecución prolongada. Por ejemplo, puede crear una función de Azure que escuche en una cola y que activa inmediatamente una aplicación lógica como desencadenador de push.

## Generación de la aplicación lógica

En este ejemplo se ejecutará una función para cada aplicación lógica que sea preciso que se desencadene. Por lo tanto, primero es preciso crear una aplicación lógica con un desencadenador de solicitud HTTP (la función de Azure llamará a dicho  
punto de conexión cada vez que se reciba un mensaje de la cola).

1. Abra una aplicación lógica nueva y seleccione el desencadenador **Manual - When an HTTP Request is Received** (Manual: cuando se recibe una solicitud HTTP).  
    * Opcionalmente, puede especificar un esquema de JSON que el mensaje de la cola tendrá a través de una herramienta como [jsonschema.net](http://jsonschema.net) y péguelo en el desencadenador. Esto permitirá al diseñador conocer la forma de los datos y hacer fluir fácilmente las propiedades a través del flujo de trabajo.
1. Agregue todos los pasos que desea que sucedan después de que se reciba un mensaje de la cola. Por ejemplo, puede enviar un mensaje de correo electrónico a través de Office 365.  
1. Guarde la aplicación lógica para generar la dirección URL de devolución de llamada para el desencadenador a esta aplicación lógica. La dirección URL aparecerá en la tarjeta del desencadenador.

![][1]

## Creación de la función de Azure

A continuación, es preciso que cree una función de Azure que actúe como desencadenador y escuche a la cola.

1. Abra la [Portal de Funciones de Azure](https://functions.azure.com/signin) y seleccione **Nueva función** con una plantilla **ServiceBusQueueTrigger - C#**.

    ![][2]

2. Configure la conexión a la cola de bus de servicio (que va a utilizar el agente de escucha `OnMessageReceive()` del SDK del Bus de servicio)
3. Escriba una función simple que llame al punto de conexión de la aplicación lógica (anterior) con el mensaje de la cola. Este es un ejemplo completo de una función con un tipo de contenido de mensaje de `application/json`, pero puede cambiarse si es necesario.

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

Para probarlo, agregue un mensaje de cola a través de una herramienta como [Explorador del Bus de servicio](https://github.com/paolosalvatori/ServiceBusExplorer) y vea si la aplicación lógica se activa inmediatamente después de que la función recibe el mensaje.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->