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
   ms.service="logic-apps"
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

## Detalles HTTP

El conector HTTP incluye una acción posible. A continuación, se incluye información acerca de cada una de las acciones, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

### Solicitud HTTP
Realice una solicitud de salida HTTP. Un asterisco (*) indica un campo obligatorio.

|Display Name (Nombre para mostrar)|Nombre de propiedad|Descripción|
|---|---|---|
|Método*|estático|Verbo HTTP que se usará|
|URI*|uri|Identificador URI de la solicitud HTTP|
|Encabezados|encabezados|Objeto JSON de los encabezados HTTP que incluir|
|Cuerpo|body|Cuerpo de la solicitud HTTP|
|Autenticación|authentication|[Detalles aquí](#authentication)|
<br>

**Detalles de salida**

Respuesta HTTP

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Encabezados|objeto|Encabezados de respuesta|
|Cuerpo|objeto|Objeto de respuesta|
|Código de estado|int|Código de estado HTTP|

## Autenticación

Logic Apps permite utilizar diferentes tipos de autenticación en los puntos de conexión HTTP. Esta autenticación se puede utilizar con los conectores HTTP, [HTTP + Swagger](./connectors-native-http-swagger.md) y [HTTP Webhook](./connectors-native-webhook.md). Los siguientes tipos de autenticación pueden configurarse:

* [Autenticación básica](#basic-authentication)
* [Autenticación ClientCertificate](#client-certificate-authentication)
* [Autenticación ActiveDirectoryOAuth](#azure-active-directory-oauth-authentication)

#### Autenticación básica

Se requiere el siguiente objeto de autenticación para la autenticación básica: un asterisco (*) significa que el campo es obligatorio.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Type*|type|Tipo de autenticación. En la autenticación básica, el valor debe ser `Basic`.|
|Username*|nombre de usuario|Nombre de usuario para autenticar.|
|Password*|contraseña|Contraseña para autenticar.|

>[AZURE.TIP] Si quiere utilizar una contraseña que no se pueda recuperar de la definición, use un parámetro `securestring` y la [función de definición de flujo de trabajo](http://aka.ms/logicappdocs) `@parameters()`.

Por tanto, debe crear un objeto similar al siguiente en el campo de autenticación:

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### Autenticación de certificados de clientes

Se requiere el siguiente objeto de autenticación para la autenticación de certificados de clientes: un asterisco (*) significa que el campo es obligatorio.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Type*|type|Tipo de autenticación. Para los certificados de cliente SSL, el valor debe ser `ClientCertificate`.|
|PFX*|pfx|Contenido codificado en base 64 del archivo PFX.|
|Password*|contraseña|Contraseña para acceder al archivo PFX.|

>[AZURE.TIP] Puede usar un parámetro `securestring` y la [función de definición de flujo de trabajo](http://aka.ms/logicappdocs) `@parameters()` para usar un parámetro que no se pueda leer en la definición después de guardarse.

Por ejemplo:

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Autenticación de OAuth de Azure Active Directory

Se requiere el siguiente objeto de autenticación para la autenticación de OAuth de Azure Active Directory: un asterisco (*) significa que el campo es obligatorio.

|Nombre de propiedad|Tipo de datos|Descripción|
|---|---|---|
|Type*|type|Tipo de autenticación. Para la autenticación ActiveDirectoryOAuth, el valor debe ser `ActiveDirectoryOAuth`.|
|Tenant*|tenant|Identificador del inquilino de Azure AD.|
|Audience*|audience|Se establece en `https://management.core.windows.net/`|
|Client ID*|clientId|Proporciona el identificador de cliente para la aplicación de Azure AD.|
|Secret*|secret|Secreto del cliente que solicita el token.|

>[AZURE.TIP] Puede usar un parámetro `securestring` y la [función de definición de flujo de trabajo](http://aka.ms/logicappdocs) `@parameters()` para usar un parámetro que no se pueda leer en la definición después de guardarse.

Por ejemplo:

```javascript
{
	"type": "ActiveDirectoryOAuth",
	"tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
	"audience": "https://management.core.windows.net/",
	"clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
	"secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

---

## Pasos siguientes

A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## Creación de una aplicación lógica

Pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ahora. Puede explorar los demás conectores disponibles en aplicaciones consultando nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->