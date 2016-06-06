<properties
   pageTitle="Uso de funciones de Azure con aplicaciones lógicas | Microsoft Azure"
   description="Descubra cómo utilizar las funciones de Azure con aplicaciones lógicas"
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
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Uso de funciones de Azure con aplicaciones lógicas

Puede ejecutar fragmentos personalizados de C# o Node.js usando las funciones de Azure con una aplicación lógica. [Funciones de Azure](../azure-functions/functions-overview.md) es una oferta que permite ejecutar procesos sin servidor en Microsoft Azure. Esto resulta de utilidad en las aplicaciones lógicas para muchos de los escenarios siguientes:

* Dar formato a un valor de una acción (por ejemplo, convertir de DateTime a una cadena de fecha)
* Realizar cálculos dentro de un flujo de trabajo
* Ampliar la funcionalidad de aplicaciones lógicas a las funciones admitidas en C# o Node.js

## Creación de una función de Azure para aplicaciones lógicas

Se recomienda crear una nueva función de Azure en el portal de funciones mediante las plantillas de webhook de nodo genérico y de webhook de C# genérico. De este modo, se rellenará automáticamente una plantilla que acepta `application/json` de una aplicación lógica, y las funciones que utilizan estas plantillas se detectarán y mostrarán de manera automática en el Diseñador de aplicaciones lógicas, en la opción "Azure Functions in my region" (Funciones de Azure en mi región).

Las funciones de Webhook aceptan una solicitud y se pasan al método a través de una variable `data`. Puede acceder a las propiedades de la carga mediante una notación de puntos, como `data.foo`. Por ejemplo, una función de JavaScript sencilla que convierte un valor DateTime en una cadena de fecha tiene el aspecto siguiente:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## Realización de llamadas a funciones de Azure desde una aplicación lógica

En el diseñador, si hace clic en el menú desplegable de acciones, puede seleccionar "Azure Functions in my region" (Funciones de Azure en mi región). Tras esto, se mostrarán los contenedores de su suscripción y podrá elegir la función a la que desea llamar. Después de seleccionarla, deberá especificar un objeto de carga de entrada. Este es el mensaje que enviará la aplicación lógica a la función y debe ser un objeto JSON. Por ejemplo, si desea pasar la fecha "Última modificación" de un desencadenador de Salesforce, la carga de la función podría tener el aspecto siguiente:

![][1]

## Activación de aplicaciones lógicas desde una función de Azure

También se puede desencadenar una aplicación lógica desde una función. Para ello, simplemente cree una aplicación lógica con un desencadenador manual (obtenga información [aquí](app-service-logic-http-endpoint.md)). Después, desde la función de Azure, genere una solicitud HTTP POST a la dirección URL de desencadenador manual con la carga que desee enviar en la aplicación lógica.

### Creación de una función desde el Diseñador

También puede crear una función de webhook de node.js desde el Diseñador. En primer lugar, seleccione "Azure Functions in my region" (Funciones de Azure en mi región) y elija un contenedor para la función. Si todavía no dispone de un contenedor, tendrá que crearlo desde el [portal de funciones de Azure](https://functions.azure.com/signin). Después, puede seleccionar "Crear nuevo". Para generar una plantilla basada en los datos que desea calcular, especifique el objeto de contexto que tiene planeado pasar a una función. Debe ser un objeto JSON. Por ejemplo, si se pasa el contenido del archivo desde una acción de FTP, la carga de contexto tendría un aspecto similar al siguiente:

![][2]

>[AZURE.NOTE] Puesto que este objeto no se convierte en una cadena mediante la adición de comillas, se agregará el contenido directamente a la carga de JSON. Se producirá un error de espera si no es el token JSON (es decir, una cadena o un objeto o una matriz JSON). Para convertirlo en cadena, simplemente agregue comillas como en el anterior ejemplo de Salesforce.

El diseñador generará, después, una plantilla de función que puede crearse en línea. Las variables se crean previamente según el contexto que se va a pasar a la función.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0525_2016-->