<properties
pageTitle="Aprenda a utilizar el conector de Salesforce en las aplicaciones lógicas | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. El conector de Salesforce proporciona una API para trabajar con objetos de Salesforce."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# Introducción al conector de Salesforce

El conector de Salesforce proporciona una API para trabajar con objetos de Salesforce.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión con el conector de Salesforce

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.

### Creación de una conexión con el conector de Salesforce

>[AZURE.INCLUDE [Pasos para crear una conexión al conector de Salesforce](../../includes/connectors-create-api-salesforce.md)]

## Uso de un desencadenador del conector de Salesforce

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Pasos para crear un desencadenador de Salesforce](../../includes/connectors-create-api-salesforce-trigger.md)]

## Agregar una condición 
>[AZURE.INCLUDE [Pasos para crear una condición de Salesforce](../../includes/connectors-create-api-salesforce-condition.md)]

## Uso de una acción del conector de Salesforce

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Pasos para crear una acción de Salesforce](../../includes/connectors-create-api-salesforce-action.md)]

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores del conector de Salesforce

El conector de Salesforce tiene el siguiente desencadenador o desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[Cuando se crea un objeto](connectors-create-api-salesforceconnector.md#when-an-object-is-created)|Esta operación desencadena un flujo al crear un objeto.|
|[Cuando se modifica un objeto](connectors-create-api-salesforceconnector.md#when-an-object-is-modified)|Esta operación desencadena un flujo al modificar un objeto.|


## Acciones del conector de Salesforce

El conector de Salesforce tiene las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Obtener objetos](connectors-create-api-salesforceconnector.md#get-objects)|Esta operación obtiene objetos de un determinado tipo de objeto como 'Cliente potencial'.|
|[Crear objeto](connectors-create-api-salesforceconnector.md#create-object)|Esta operación crea un objeto.|
|[Obtener objeto](connectors-create-api-salesforceconnector.md#get-object)|Esta operación obtiene un objeto.|
|[Eliminar objeto](connectors-create-api-salesforceconnector.md#delete-object)|Esta operación elimina un objeto.|
|[Actualizar objeto](connectors-create-api-salesforceconnector.md#update-object)|Esta operación actualiza un objeto.|
|[Obtener tipos de objeto](connectors-create-api-salesforceconnector.md#get-object-types)|Esta operación enumera los tipos de objeto disponibles.|
### Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:



### Obtener objetos
Esta operación obtiene objetos de un determinado tipo de objeto como 'Cliente potencial'.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto de Salesforce como 'Cliente potencial'|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ItemsList


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|




### Crear objeto
Esta operación crea un objeto.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto como 'Cliente potencial'|
|item*|Objeto|Objeto que se va a crear|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

Elemento


| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|




### Obtener objeto
Esta operación obtiene un objeto.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto de Salesforce como 'Cliente potencial'|
|id*|Id. de objeto|Identificador del objeto que se va a obtener|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

Elemento


| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|




### Eliminar objeto
Esta operación elimina un objeto.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto como 'Cliente potencial'|
|id*|Id. de objeto|Identificador del objeto que se va a eliminar|

El símbolo * indica que la propiedad es obligatoria.




### Actualizar objeto
Esta operación actualiza un objeto.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto como 'Cliente potencial'|
|id*|Id. de objeto|Identificador del objeto que se va a actualizar|
|item*|Objeto|Objeto con propiedades cambiadas|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

Elemento


| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|




### Cuando se crea un objeto
Esta operación desencadena un flujo al crear un objeto.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto como 'Cliente potencial'|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ItemsList


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|




### Cuando se modifica un objeto
Esta operación desencadena un flujo al modificar un objeto.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Tipo de objeto|Tipo de objeto como 'Cliente potencial'|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ItemsList


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|




### Obtener tipos de objeto
Esta operación enumera los tipos de objeto disponibles.


No hay parámetros para esta llamada

#### Detalles de salida

TablesList


| Nombre de propiedad | Tipo de datos | 
|---|---|
|value|array|



## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

|Name|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido.|
|default|Error en la operación.|






## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
