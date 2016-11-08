---
title: Aprenda a utilizar el conector de Salesforce en las aplicaciones lógicas | Microsoft Docs
description: Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El conector de Salesforce proporciona una API para trabajar con objetos de Salesforce.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe

---
# <a name="get-started-with-the-salesforce-connector"></a>Introducción al conector de Salesforce
El conector de Salesforce proporciona una API para trabajar con objetos de Salesforce.

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-salesforce-connector"></a>Conexión con el conector de Salesforce
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.  

### <a name="create-a-connection-to-salesforce-connector"></a>Creación de una conexión con el conector de Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Uso de un desencadenador del conector de Salesforce
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Agregar una condición
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Uso de una acción del conector de Salesforce
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>Detalles técnicos
Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## <a name="salesforce-connector-triggers"></a>Desencadenadores del conector de Salesforce
El conector de Salesforce tiene el siguiente desencadenador o desencadenadores:  

| Desencadenador | Descripción |
| --- | --- |
| [Cuando se crea un objeto](connectors-create-api-salesforce.md#when-an-object-is-created) |Esta operación desencadena un flujo al crear un objeto. |
| [Cuando se modifica un objeto](connectors-create-api-salesforce.md#when-an-object-is-modified) |Esta operación desencadena un flujo al modificar un objeto. |

## <a name="salesforce-connector-actions"></a>Acciones del conector de Salesforce
El conector de Salesforce tiene las siguientes acciones:

| Acción | Descripción |
| --- | --- |
| [Obtener objetos](connectors-create-api-salesforce.md#get-objects) |Esta operación obtiene objetos de un determinado tipo de objeto como 'Cliente potencial'. |
| [Crear objeto](connectors-create-api-salesforce.md#create-object) |Esta operación crea un objeto. |
| [Obtener objeto](connectors-create-api-salesforce.md#get-object) |Esta operación obtiene un objeto. |
| [Eliminar objeto](connectors-create-api-salesforce.md#delete-object) |Esta operación elimina un objeto. |
| [Actualizar objeto](connectors-create-api-salesforce.md#update-object) |Esta operación actualiza un objeto. |
| [Obtener tipos de objeto](connectors-create-api-salesforce.md#get-object-types) |Esta operación enumera los tipos de objeto disponibles. |

### <a name="action-details"></a>Detalles de la acción
Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:

### <a name="get-objects"></a>Obtener objetos
Esta operación obtiene objetos de un determinado tipo de objeto como 'Cliente potencial'. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto de Salesforce como 'Cliente potencial' |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

### <a name="create-object"></a>Crear objeto
Esta operación crea un objeto. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Cliente potencial' |
| item* |Objeto |Objeto que se va a crear |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| ItemInternalId |cadena |

### <a name="get-object"></a>Obtener objeto
Esta operación obtiene un objeto. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto de Salesforce como 'Cliente potencial' |
| id* |Id. de objeto |Identificador del objeto que se va a obtener |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-object"></a>Eliminar objeto
Esta operación elimina un objeto. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Cliente potencial' |
| id* |Id. de objeto |Identificador del objeto que se va a eliminar |

El símbolo * indica que la propiedad es obligatoria.

### <a name="update-object"></a>Actualizar objeto
Esta operación actualiza un objeto. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Cliente potencial' |
| id* |Id. de objeto |Identificador del objeto que se va a actualizar |
| item* |Objeto |Objeto con propiedades cambiadas |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| ItemInternalId |cadena |

### <a name="when-an-object-is-created"></a>Cuando se crea un objeto
Esta operación desencadena un flujo al crear un objeto. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Cliente potencial' |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

### <a name="when-an-object-is-modified"></a>Cuando se modifica un objeto
Esta operación desencadena un flujo al modificar un objeto. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Tipo de objeto |Tipo de objeto como 'Cliente potencial' |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

### <a name="get-object-types"></a>Obtener tipos de objeto
Esta operación enumera los tipos de objeto disponibles. 

No hay parámetros para esta llamada

#### <a name="output-details"></a>Detalles de salida
TablesList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Respuestas HTTP
Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP: 

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido. |
| default |Error en la operación. |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!--HONumber=Oct16_HO2-->


