<properties
	pageTitle="Incorporación de la API Usuarios de Office 365 a sus aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API Usuarios de Office 365 con parámetros de la API de REST"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introducción a la API Usuarios de Office 365

El proveedor de conexiones de Usuarios de Office 365 le permite acceder a los perfiles de usuario de su organización con su cuenta de Office 365. Puede realizar diversas acciones, como obtener su perfil, el perfil de un usuario, el administrador de un usuario o informes directos, así como actualizar un perfil de usuario.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [API de Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Con Usuarios de Office 365, puede:

* Usarlo para crear aplicaciones lógicas
* Usarlo para crear aplicaciones versátiles

Para obtener información sobre cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Hablemos de acciones y desencadenadores

La API Usuarios de Office 365 puede usarse como una acción; no hay desencadenadores. Todas las API admiten datos en formato JSON y XML.

 La API Usuarios de Office 365 tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de Usuarios de Office 365
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|MyProfile|Recupera el perfil del usuario actual|
|UserProfile|Recupera un perfil de usuario específico|
|Manager|Recupera el perfil de usuario para el administrador del usuario especificado|
|DirectReports|Obtener informes directos|
|SearchUser|Recupera los resultados de búsqueda de los perfiles de usuario|
## Creación de una conexión a Usuarios de Office 365
Para usar la API Usuarios de Office 365, cree primero una **conexión** y después proporcione los detalles para estas propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporcionar las credenciales de Office365|


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de API de REST de Usuarios de Office 365
#### Esta documentación corresponde a la versión: 1.0


### Obtener mi perfil 


 Recupera el perfil del usuario actual ```GET: /users/me```

No hay parámetros para esta llamada
#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener perfil de usuario 


 Recupera un perfil de usuario específico ```GET: /users/{userId}```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener administrador 


 Recupera el perfil de usuario para el administrador del usuario especificado ```GET: /users/{userId}/manager```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener informes directos 


 Obtener informes directos ```GET: /users/{userId}/directReports```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|userId|cadena|yes|path|Ninguna|Nombre principal o identificador de correo electrónico del usuario|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Buscar usuarios 


 Recupera los resultados de búsqueda de los perfiles de usuario ```GET: /users```



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|searchTerm|cadena|no|query|Ninguna|Cadena de búsqueda (se aplica a: nombre para mostrar, nombre dado, apellidos, correo, sobrenombre de correo y nombre principal del usuario)|


#### Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|202|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Internal Server Error|
|default|Error en la operación.|
------



## Definiciones de objeto: 

 **User**: clase de modelo de usuario.

Propiedades obligatorias para User:

Id

**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|DisplayName|cadena|
|GivenName|cadena|
|Surname|cadena|
|Mail|cadena|
|MailNickname|cadena|
|TelephoneNumber|cadena|
|AccountEnabled|boolean|
|Id|cadena|
|UserPrincipalName|cadena|
|Departamento|cadena|
|JobTitle|cadena|


## Pasos siguientes
Después de agregar la API de Office 365 a PowerApps Enterprise, [conceda permisos a los usuarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar dicha API en sus aplicaciones.

[Cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->