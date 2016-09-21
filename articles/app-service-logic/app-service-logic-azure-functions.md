<properties
   pageTitle="Uso de funciones de Azure con aplicaciones lógicas | Microsoft Azure"
   description="Descubra cómo utilizar las funciones de Azure con aplicaciones lógicas"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="jehollan"/>

# Uso de funciones de Azure con aplicaciones lógicas

Puede ejecutar fragmentos personalizados de C# o node.js mediante Funciones de Azure desde dentro de una aplicación lógica. [Funciones de Azure](../azure-functions/functions-overview.md) ofrece la computación sin servidor en Microsoft Azure. Esto resulta útil para realizar las tareas siguientes:

* Dar formato al valor de una acción (por ejemplo, la conversión de fecha y hora a una cadena de fecha).
* Realizar cálculos dentro de un flujo de trabajo
* Extender la funcionalidad de las aplicaciones lógicas con funciones que se admiten en C# o node.js.

## Creación de una función para aplicaciones lógicas

Se recomienda crear una función en el portal de Azure Functions mediante las plantillas de **webhook de nodo genérico** y de **webhook de C# genérico**. Esto rellena automáticamente una plantilla que acepta `application/json` desde una aplicación lógica. Las funciones que usan estas plantillas se detectan automáticamente y se muestran en el diseñador de aplicaciones lógicas en **Azure Functions in my region** (Funciones de Azure en mi región).

Las funciones de Webhook aceptan una solicitud y las pasan al método mediante una variable `data`. Puede acceder a las propiedades de la carga mediante una notación de puntos, como `data.foo`. Por ejemplo, una función de JavaScript simple que convierte un valor de DateTime en una cadena de fecha es similar al siguiente ejemplo:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## Llamadas a Funciones de Azure desde una aplicación lógica

En el diseñador, si hace clic en el menú **Acciones**, puede seleccionar **Azure Functions in my region** (Funciones de Azure en mi región). Esto muestra una lista de los contenedores de su suscripción y le permite elegir la función a la que desea llamar.

Después de seleccionar la función, deberá especificar un objeto de carga de entrada. Este es el mensaje que la aplicación lógica enviará a la función y debe ser un objeto JSON. Por ejemplo, si desea pasar la fecha **Última modificación** de un desencadenador de Salesforce, la carga de la función podría ser similar a esta:

![Última fecha de modificación][1]

## Desencadenamiento de aplicaciones lógicas desde una función

También se puede desencadenar una aplicación lógica desde una función. Para ello, simplemente cree una aplicación lógica con un desencadenador manual. Para más información, consulte [Aplicaciones lógicas como puntos de conexión invocables](app-service-logic-http-endpoint.md). Después, desde la función, genere una solicitud HTTP POST a la dirección URL del desencadenador manual con la carga que desea enviar a la aplicación lógica.

### Creación de una función desde el diseñador

También puede crear una función de webhook de node.js desde el Diseñador. En primer lugar, seleccione **Azure Functions in my region** (Funciones de Azure en mi región) y elija un contenedor para la función. Si todavía no dispone de ningún contenedor, tendrá que crearlo en el [portal de Funciones de Azure](https://functions.azure.com/signin). Seleccione **Crear nuevo**.

Para generar una plantilla basada en los datos que desea procesar, especifique el objeto de contexto que piensa pasar a una función. Debe ser un objeto JSON. Por ejemplo, si se pasa el contenido del archivo de una acción de FTP, la carga de contexto será similar a esta:

![Carga de contexto][2]

>[AZURE.NOTE] Puesto que este objeto no se convierte en una cadena, se agregará el contenido directamente a la carga de JSON. Sin embargo, generará un error si no es el token JSON (es decir, una cadena o un objeto o matriz JSON). Para convertirlo en una cadena, simplemente agregue comillas como se muestra en la primera ilustración de este artículo.

A continuación, el diseñador genera una plantilla de función que puede crear en línea. Las variables se crean previamente de acuerdo con el contexto que se va a pasar a la función.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0907_2016-->