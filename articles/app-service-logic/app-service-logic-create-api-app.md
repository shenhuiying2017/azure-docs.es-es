<properties 
	pageTitle="Crear una API para Aplicaciones lógicas" 
	description="Creación de una API personalizada para usar con aplicaciones lógicas" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
    
# Creación de una API personalizada para usar con aplicaciones lógicas

Si desea ampliar la plataforma Aplicaciones lógicas, hay muchas maneras para poder llamar a API o sistemas que no están disponibles, como uno de nuestros numerosos conectores sin necesidad de configuración. Se puede llamar a uno de esos métodos para crear una aplicación de API desde dentro de un flujo de trabajo de la Aplicación lógica.

## Herramientas útiles

Para que las API funcionen mejor con Aplicaciones lógicas, se recomienda generar un documento [swagger](http://swagger.io) que detalla los parámetros y operaciones admitidos para la API. Hay muchas bibliotecas (como [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) que generarán automáticamente el documento swagger. También puede usar [TRex](https://github.com/nihaue/TRex) para ayudar a anotar el documento swagger para que funcione bien con Aplicaciones lógicas (mostrar nombres, tipos de propiedades, etc.). Para ver algunos ejemplos de Aplicaciones de API integradas para Aplicaciones lógicas, asegúrese de consultar nuestro [repositorio de GitHub](http://github.com/logicappsio) o [blog](http://aka.ms/logicappsblog).

## Acciones

La acción básica de una Aplicación lógica es un controlador que aceptará una solicitud HTTP y devolverá una respuesta (normalmente, 200). Sin embargo, hay patrones diferentes que puede seguir para ampliar las acciones según sus necesidades.

De forma predeterminada, el motor de Aplicación lógica superará el tiempo de espera de una solicitud después de un minuto. Sin embargo, puede hacer que su API ejecute acciones que tardan más tiempo, y que el motor espere a que terminen, siguiendo un patrón async o webhook descrito a continuación.

Para acciones estándar, basta con escribir un método de solicitud HTTP en la API que se expone mediante swagger. Puede ver ejemplos de aplicaciones de API que funcionan con Logic Apps en nuestra [repositorio de GitHub](https://github.com/logicappsio). A continuación se muestran varias formas de conseguir patrones comunes con un conector personalizado.

### Patrón de acciones de larga duración: asincrónico

Al ejecutar un paso o una tarea largos, lo primero que debe hacer es asegurarse de que el motor sabe que no se ha agotado el tiempo de espera. También debe comunicarse con el motor para que sepa cuando ha terminado la tarea y, finalmente, debe devolver datos relevantes para el motor para poder seguir con el flujo de trabajo. Para completarlo mediante una API, siga el flujo que aparece a continuación. Estos pasos son desde el punto de vista de la API personalizada:

1. Cuando se recibe una solicitud, se devuelve una respuesta inmediatamente (antes de realizar el trabajo). Esta respuesta será una respuesta `202 ACCEPTED`, que permite al motor saber que ha obtenido los datos, ha aceptado la carga útil y ahora se está procesando. La respuesta 202 debe contener los siguientes encabezados:
 * Encabezado `location` (obligatorio): se trata de una ruta absoluta a la dirección URL que Logic Apps puede usar para comprobar el estado del trabajo.
 * `retry-after` (opcional, con el valor predeterminado 20 para las acciones). Se trata del número de segundos que el motor debe esperar antes de sondear la dirección URL de encabezado de ubicación para comprobar el estado.

2. Una vez comprobado el estado del trabajo, realice las siguientes comprobaciones:
 * Si el trabajo ha terminado: devuelve una respuesta `200 OK`, con la carga útil de la respuesta.
 * Si el trabajo todavía se está procesando: devuelve otra respuesta `202 ACCEPTED`, con los mismos encabezados que la respuesta inicial.

Este patrón permite ejecutar tareas extremadamente largas en un subproceso de la API personalizada, pero mantiene una conexión activa con el motor de Aplicaciones lógicas para que no agote el tiempo de espera o continúe antes de que finalice el trabajo. Al agregarlo a la Aplicación lógica, es importante tener en cuenta que no es necesario hacer nada en la definición de la Aplicación lógica para continuar sondeando y comprobando el estado. En cuanto el motor ve una respuesta 202 ACCEPTED con un encabezado de ubicación válida, respetará el patrón asincrónico y continuará sondeando el encabezado de ubicación hasta que se devuelve una respuesta que no es 202.

Puede ver un ejemplo de este patrón en GitHub: [aquí](https://github.com/jeffhollan/LogicAppsAsyncResponseSample).

### Acciones de Webhook

Durante el flujo de trabajo, puede hacer que la Aplicación lógica haga una pausa y espere a recibir una devolución de llamada. Esta devolución de llamada tiene el formato de una solicitud HTTP POST. Para implementar este patrón, debe proporcionar dos puntos de conexión en el controlador: subscribe y unsubscribe.

Con "subscribe", la Aplicación lógica creará y registrará una URL de devolución de llamada que su API puede almacenar y devolver la llamada con listo como HTTP POST. El contenido y los encabezados se pasarán a la Aplicación lógica y se podrán usar en el resto del flujo de trabajo. El motor de Aplicación lógica llamará a la ejecución del punto subscribe en cuanto llegue a ese paso.

Si se canceló la ejecución, el motor de Aplicación lógica hará una llamada al punto de conexión "unsubscribe". A continuación, la API puede anular el registro de la URL de devolución de llamada según sea necesario.

Actualmente, el diseñador de Aplicación lógica no admite la detección de un punto de conexión de webhook mediante swagger, por lo que, para utilizar este tipo de acción debe agregar la acción "Webhook" y especificar la dirección URL, los encabezados y el cuerpo de la solicitud. Puede usar la función del flujo de trabajo `@listCallbackUrl()` en cualquiera de esos campos según sea necesario para pasar la URL de devolución de llamada.

Puede ver un ejemplo del patrón de webhook en GitHub: [aquí](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## Desencadenadores

Además de las acciones, puede hacer que la API personalizada funcione como un desencadenador para una Aplicación lógica. Hay dos patrones que puede seguir para desencadenar una Aplicación lógica:

### Desencadenadores de sondeo

Los desencadenadores de sondeo actúan de forma muy parecida a las acciones de larga duración: asincrónico antes descrita. El motor de Aplicación lógica llamará el punto de conexión del desencadenador una vez transcurrido un período determinado (según la SKU, 15 segundos para Premium, 1 minuto para Standard y 1 hora para gratis).

Si no hay ningún dato disponible, el desencadenador devuelve una respuesta `202 ACCEPTED`, con un encabezado `location` y `retry-after`. Sin embargo, para los desencadenadores se recomienda que el encabezado `location` contenga un parámetro de consulta de `triggerState`. Se trata de un identificador para que la API sepa cuándo se activó por última vez la Aplicación lógica. Si hay datos disponibles, el desencadenador devuelve una respuesta `200 OK`, con la carga útil de contenido. Esto desencadenará la Aplicación lógica.

Por ejemplo, si yo estaba sondeando para ver si un archivo estaba disponible, usted podría crear un desencadenador de sondeo que haría lo siguiente:

* Si se recibió una solicitud sin triggerState, la API devolverá una respuesta `202 ACCEPTED` con un encabezado `location` que tiene un elemento triggerState de la hora actual y un valor `retry-after` de 15.
* Si se recibió una solicitud con triggerState:
 * Compruebe si se agregó algún archivo después de la fecha y hora de triggerState.
  * Si hay un archivo, devuelve una respuesta `200 OK` con la carga de contenido, aumenta triggerState a la fecha y hora del archivo que he devuelto y establece el valor de `retry-after` en 15.
  * Si hay varios archivos, puedo devolver uno cada vez con una respuesta `200 OK`, aumentar mi elemento triggerState en el encabezado `location` y establecer `retry-after` en 0. Esto indicará al motor que hay más datos disponibles y los solicitará inmediatamente en el encabezado `location` especificado.
  * Si no hay ningún archivo disponible, devuelve una respuesta `202 ACCEPTED` y deja el elemento triggerState `location` igual. Establece `retry-after` en 15.

Puede ver un ejemplo de un desencadenador de sondeo en GitHub: [aquí](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers).

### Desencadenadores de Webhook

Los desencadenadores de Webhook funcionan de forma muy parecida a las acciones de Webhook antes descritas. El motor de Aplicación lógica llamará al punto de conexión "subscribe" siempre que se agregue y guarde un desencadenador de webhook. La API puede registrar la dirección URL de webhook y llamarla mediante HTTP POST cada vez que los datos están disponibles. La carga de contenido y los encabezados se pasarán a la ejecución de la Aplicación lógica.

Si alguna vez se elimina un desencadenador webhook (la Aplicación de lógica completa o solo el desencadenador de webhook), el motor realizará una llamada a la dirección URL "unsubscribe", donde la API puede anular el registro de la dirección URL de devolución de llamada y detener los procesos según sea necesario.

Actualmente, el diseñador de Aplicación lógica no admite la detección de un desencadenador de webhook mediante swagger, por lo que, para utilizar este tipo de acción debe agregar el desencadenador "Webhook" y especificar la dirección URL, los encabezados y el cuerpo de la solicitud. Puede usar la función del flujo de trabajo `@listCallbackUrl()` en cualquiera de esos campos según sea necesario para pasar la URL de devolución de llamada.

Puede ver un ejemplo de un desencadenador de webhook en GitHub: [aquí](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers).

<!---HONumber=AcomDC_0803_2016-->