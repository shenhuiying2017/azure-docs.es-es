<properties
	pageTitle="Adición de la acción HTTP a las aplicaciones lógicas | Microsoft Azure"
	description="Información general de la acción HTTP con propiedades"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Introducción a la acción HTTP

Con la acción HTTP, puede ampliar los flujos de trabajo de su organización y comunicarse con cualquier punto de conexión a través de HTTP.

- Cree flujos de trabajo de aplicaciones lógicas que se activen (desencadenador) cuando un sitio web que administre deje de funcionar.
- Comuníquese con cualquier punto de conexión por HTTP para ampliar los flujos de trabajo a otros servicios.

Para empezar a usar la acción HTTP en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Uso de un desencadenador HTTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo configurar un desencadenador HTTP en el diseñador de aplicaciones lógicas.

1. Agregue el desencadenador HTTP a la aplicación lógica.
1. Rellene los parámetros para el punto de conexión HTTP que desee sondear.
1. Modificar el intervalo de periodicidad de la frecuencia con que se debe sondear.
1. Ahora se activará la aplicación lógica con cualquier contenido que se devuelva durante cada comprobación.

![Desencadenador HTTP](./media/connectors-native-http/using-trigger.png)

### Funcionamiento del desencadenador HTTP

El desencadenador HTTP realizará una llamada a un punto de conexión HTTP en un intervalo periódico. De forma predeterminada, cualquier código de respuesta HTTP inferior a 300 hará que la aplicación lógica se ejecute. Puede agregar una condición en la vista código que se evaluará después de la llamada HTTP para determinar si se debe activar la aplicación lógica. Este es un ejemplo de un desencadenador HTTP que se activará cada vez que el código de estado devuelto sea mayor o igual que `400`.

```javascript
"Http": 
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

En [MSDN se puede encontrar](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger) información completa acerca de los parámetros del desencadenador HTTP.

## Uso de una acción HTTP
	
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](connectors-overview.md).

1. Seleccione el botón **Nuevo paso**.
1. Elija **Agregar una acción**.
1. En el cuadro de búsqueda de acciones, escriba "HTTP" para mostrar la acción HTTP.

	![Seleccionar acción HTTP](./media/connectors-native-http/using-action-1.png)

1. Agregue cualquier parámetro necesario para la llamada HTTP.

	![Completar acción HTTP](./media/connectors-native-http/using-action-2.png)

1. Haga clic en Guardar en la esquina superior izquierda de la barra de herramientas; la aplicación lógica se guardará y se publicará (activará).

---

## Detalles técnicos

A continuación se muestran los detalles de los desencadenadores y las acciones que admite este conector.

## Desencadenadores HTTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md). El conector HTTP tiene un desencadenador.

|Desencadenador|Descripción|
|---|---|
|HTTP|Realizar una llamada HTTP y devolver el contenido de la respuesta.|

## Acciones HTTP

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](connectors-overview.md). El conector HTTP tiene una acción posible.

|Acción|Descripción|
|---|---|
|HTTP|Realizar una llamada HTTP y devolver el contenido de la respuesta.|

### Detalles de la acción

El conector HTTP incluye una acción posible. A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

#### Solicitud HTTP
Realice una solicitud de salida HTTP. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|Método*|estático|Verbo HTTP que se usará|
|URI*|uri|Identificador URI de la solicitud HTTP|
|Encabezados|encabezados|Objeto JSON de los encabezados HTTP que incluir|
|Cuerpo|body|Cuerpo de la solicitud HTTP|
<br>

**Detalles de salida**

Respuesta HTTP

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de respuesta|
|Cuerpo|objeto|Objeto de respuesta|
|Código de estado|int|Código de estado HTTP|

### Respuestas HTTP

Al realizar llamadas a diversas acciones, es posible que obtenga determinadas respuestas. A continuación se incluye una tabla que describe las respuestas y descripciones correspondientes.

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|

---

## Pasos siguientes

A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## Creación de una aplicación lógica

Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Explore los demás conectores disponibles en aplicaciones lógicas en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->