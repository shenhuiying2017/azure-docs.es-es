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

Con los componentes de solicitud y respuesta de una aplicación lógica, puede responder en tiempo real a eventos, por ejemplo:

- Responder a una solicitud HTTP con datos de una base de datos local a través de una aplicación lógica.
- Desencadenar una aplicación lógica desde un evento webhook externo.
- Llamar a una aplicación lógica con una acción de solicitud y respuesta desde otra aplicación lógica.

Para empezar a usar las acciones de solicitud y respuesta en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Uso del desencadenador de solicitud HTTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo configurar una solicitud HTTP en el diseñador de aplicaciones lógicas.

1. Agregue el desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica.
	- También puede proporcionar un esquema JSON (mediante una herramienta como [jsonschema.net](http://jsonschema.net)) para el cuerpo de solicitud. Esto permitirá al diseñador generar tokens para las propiedades de la solicitud HTTP.
1. Agregue otra acción para que pueda guardar la aplicación lógica.
1. Después de guardar, puede obtener la dirección URL de la solicitud HTTP de la tarjeta de solicitud.
1. Una solicitud HTTP POST (puede utilizar una herramienta como [Postman](https://www.getpostman.com/)) a la dirección URL activará la aplicación lógica.

>[AZURE.NOTE] Si no define una acción de respuesta, se devolverá una respuesta `202 ACCEPTED` inmediatamente al llamador. Puede utilizar la acción de respuesta para personalizar una respuesta.

![Desencadenador de respuesta](./media/connectors-native-reqres/using-trigger.png)

## Uso de la acción de respuesta HTTP
	
La acción de respuesta HTTP solo es válida cuando se usa en un flujo de trabajo desencadenado por una solicitud HTTP. Si no define una acción de respuesta, se devolverá una respuesta `202 ACCEPTED` inmediatamente al llamador. Una acción de respuesta se puede agregar en cualquier paso del flujo de trabajo. La aplicación lógica solo mantendrá la solicitud entrante abierta durante un minuto para una respuesta. Después de un minuto, si no se envía respuesta desde el flujo de trabajo (y existe una acción de respuesta en la definición), se devolverá una respuesta `504 GATEWAY TIMEOUT` al llamador. A continuación se explica cómo agregar una acción de respuesta HTTP:

1. Seleccione el botón **Nuevo paso**.
1. Elija **Agregar una acción**.
1. En el cuadro de búsqueda de acciones, escriba "Response" para mostrar la acción de respuesta.

	![Seleccionar acción de respuesta](./media/connectors-native-reqres/using-action-1.png)

1. Agregue cualquier parámetro necesario para el mensaje de respuesta HTTP.

	![Completar acción de respuesta](./media/connectors-native-reqres/using-action-2.png)

1. Haga clic en Guardar en la esquina superior izquierda de la barra de herramientas; la aplicación lógica se guardará y se publicará (activará).

---

## Detalles técnicos

A continuación se muestran los detalles de los desencadenadores y las acciones que admite este conector.

## Desencadenador de solicitud

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md). Solo existe un único desencadenador de solicitud.

|Desencadenador|Descripción|
|---|---|
|Solicitud|Cuando se recibe una solicitud HTTP|

## Acción de respuesta

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](connectors-overview.md). Hay una única de acción de respuesta que solo puede usarse acompañada de un desencadenador de solicitud.

|Acción|Descripción|
|---|---|
|Respuesta|Devolver una respuesta a la solicitud HTTP correlacionada|

### Detalles de la acción

A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

#### Desencadenador de solicitud
Desencadenador de una solicitud HTTP entrante. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|Esquema JSON|schema|Esquema JSON del cuerpo de la solicitud HTTP|
<br>

**Detalles de salida**

Solicitud

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de solicitud|
|Cuerpo|objeto|Objeto de solicitud|

#### Acción de respuesta

Respuesta HTTP. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|Código de estado*|statusCode|Código de estado HTTP|
|Encabezados|encabezados|Objeto JSON de cualquier encabezado de respuesta que incluir|
|Cuerpo|body|Cuerpo de la respuesta|

## Pasos siguientes

A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## Creación de una aplicación lógica

Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Explore los demás conectores disponibles en aplicaciones lógicas en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->