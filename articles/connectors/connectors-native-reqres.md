<properties
	pageTitle="Uso de acciones de solicitud y respuesta | Microsoft Azure"
	description="Información general del desencadenador y la acción de solicitud y respuesta en una aplicación lógica de Azure"
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Introducción a los componentes de solicitud y respuesta

Con los componentes de solicitud y respuesta de una aplicación lógica, puede responder en tiempo real a eventos.

Por ejemplo, puede:

- Responder a una solicitud HTTP con datos de una base de datos local a través de una aplicación lógica.
- Desencadenar una aplicación lógica desde un evento webhook externo.
- Llamar a una aplicación lógica con una acción de solicitud y respuesta desde otra aplicación lógica.

Para empezar a usar las acciones de solicitud y respuesta en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Uso del desencadenador de solicitud HTTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo configurar una solicitud HTTP en el diseñador de aplicaciones lógicas.

1. Agregue el desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica. También puede proporcionar un esquema JSON (mediante una herramienta como [JSONSchema.net](http://jsonschema.net)) para el cuerpo de solicitud. Esto permite al diseñador generar tokens para las propiedades de la solicitud HTTP.
2. Agregue otra acción para que pueda guardar la aplicación lógica.
3. Después de guardarla, puede obtener la dirección URL de la solicitud HTTP de la tarjeta de solicitud.
4. Una solicitud HTTP POST (puede utilizar una herramienta como [Postman](https://www.getpostman.com/)) a la dirección URL activará la aplicación lógica.

>[AZURE.NOTE] Si no define una acción de respuesta, se devolverá una respuesta `202 ACCEPTED` inmediatamente al llamador. Puede utilizar la acción de respuesta para personalizar una respuesta.

![Desencadenador de respuesta](./media/connectors-native-reqres/using-trigger.png)

## Uso de la acción de respuesta HTTP

La acción de respuesta HTTP solo es válida cuando se usa en un flujo de trabajo desencadenado por una solicitud HTTP. Si no define una acción de respuesta, se devolverá una respuesta `202 ACCEPTED` inmediatamente al llamador. Se puede agregar una acción de respuesta en cualquier paso del flujo de trabajo. La aplicación lógica solo mantiene la solicitud entrante abierta durante un minuto para una respuesta. Después de un minuto, si no se envía respuesta desde el flujo de trabajo (y existe una acción de respuesta en la definición), se devuelve una respuesta `504 GATEWAY TIMEOUT` al llamador.

A continuación se explica cómo agregar una acción de respuesta HTTP:

1. Seleccione el botón **Nuevo paso**.
2. Elija **Add an action** (Agregar una acción).
3. En el cuadro de búsqueda de acciones, escriba **Response** para mostrar la acción de respuesta.

	![Seleccionar la acción de respuesta](./media/connectors-native-reqres/using-action-1.png)

4. Agregue cualquier parámetro necesario para el mensaje de respuesta HTTP.

	![Completar la acción de respuesta](./media/connectors-native-reqres/using-action-2.png)

5. Haga clic en la esquina superior izquierda de la barra de herramientas para guardarla; la aplicación lógica se guardará y se publicará (activará).

## Desencadenador de solicitud

Aquí se muestran los detalles del desencadenador que admite este conector. Solo existe un único desencadenador de solicitud.

|Desencadenador|Description|
|---|---|
|Solicitud|Se produce cuando se recibe una solicitud HTTP.|

## Acción de respuesta

Aquí se muestran los detalles de la acción que admite este conector. Hay una única de acción de respuesta que solo puede usarse acompañada de un desencadenador de solicitud.

|Acción|Description|
|---|---|
|Respuesta|Devuelve una respuesta a la solicitud HTTP correlacionada.|

### Detalles de los desencadenadores y las acciones

En las tablas siguientes se describen los campos de entrada para el desencadenador y la acción, así como los detalles de salida correspondientes.

#### Desencadenador de solicitud
El siguiente es un campo de entrada para el desencadenador de una solicitud HTTP entrante.

|Nombre para mostrar|Nombre de propiedad|Description|
|---|---|---|
|Esquema JSON|schema|Esquema JSON del cuerpo de la solicitud HTTP|
<br>

**Detalles de salida**

Los detalles del resultado de la solicitud son los siguientes:

|Nombre de propiedad|Tipo de datos|Description|
|---|---|---|
|Encabezados|objeto|Encabezados de solicitud|
|Cuerpo|objeto|Objeto de solicitud|

#### Acción de respuesta

A continuación se muestran los campos de entrada para la acción de respuesta HTTP. Un * significa que es un campo obligatorio.

|Nombre para mostrar|Nombre de propiedad|Description|
|---|---|---|
|Código de estado*|statusCode|Código de estado HTTP|
|Encabezados|encabezados|Objeto JSON de cualquier encabezado de respuesta que incluir|
|Cuerpo|body|Cuerpo de la respuesta|

## Pasos siguientes

Ahora, pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los demás conectores disponibles en aplicaciones lógicas consultando nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->