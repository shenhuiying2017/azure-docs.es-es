---
title: "Esquema del lenguaje de definición de flujo de trabajo - Azure Logic Apps | Microsoft Docs"
description: "Definición de flujos de trabajo basados en el esquema de definición de flujo de trabajo para Azure Logic Apps"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 6befc5b26f2b01113f1aa813125b33eb66ad6f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Esquema del lenguaje de definición de flujo de trabajo - Azure Logic Apps

Una definición de flujo de trabajo contiene la lógica real que se ejecuta como parte de la aplicación lógica. Esta definición incluye uno o más desencadenadores que inician la aplicación lógica y una o varias acciones que va a realizar la aplicación lógica.  
  
## <a name="basic-workflow-definition-structure"></a>Estructura de la definición de flujo de trabajo básica

Esta es la estructura básica de una definición de flujo de trabajo:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> La documentación de [Management API de REST de flujos de trabajo](https://docs.microsoft.com/rest/api/logic/workflows) contiene información sobre cómo crear y administrar flujos de trabajo de aplicación lógica.
  
|Nombre del elemento|Obligatorio|Descripción|  
|------------------|--------------|-----------------|  
|$schema|No|Especifica la ubicación del archivo de esquema JSON que describe la versión del idioma de definición. Esta ubicación es necesaria cuando se hace referencia a una definición externamente. En este documento, la ubicación es: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|No|Especifica la versión de definición. Cuando se implementa un flujo de trabajo mediante la definición, puede usar este valor para asegurarse de que se utiliza la definición correcta.|  
|parameters|No|Especifica los parámetros que se utilizan para introducir datos en la definición. Se puede definir un máximo de 50 parámetros.|  
|Desencadenadores|No|Especifica información de los desencadenadores que inician el flujo de trabajo. Se pueden definir 10 desencadenadores como máximo.|  
|actions|No|Especifica las acciones que se realizan mientras se ejecuta el flujo. Se puede definir un máximo de 250 acciones.|  
|outputs|No|Especifica la información sobre el recurso implementado. Se puede definir un máximo de 10 salidas.|  
  
## <a name="parameters"></a>Parámetros

Esta sección especifica todos los parámetros que se usan en la definición de flujo de trabajo durante la implementación. Todos los parámetros se deben declarar en esta sección antes de que se puedan utilizar en otras secciones de la definición.  
  
En el ejemplo siguiente se muestra la estructura de una definición de parámetro:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Nombre del elemento|Obligatorio|Descripción|  
|------------------|--------------|-----------------|  
|type|Sí|**Tipo**: string <p> **Declaración**: `"parameters": {"parameter1": {"type": "string"}` <p> **Especificación**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: securestring <p> **Declaración**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **Especificación**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Tipo**: int <p> **Declaración**: `"parameters": {"parameter1": {"type": "int"}}` <p> **Especificación**: `"parameters": {"parameter1": {"value" : 5}}` <p> **Tipo**: bool <p> **Declaración**: `"parameters": {"parameter1": {"type": "bool"}}` <p> **Especificación**: `"parameters": {"parameter1": { "value": true }}` <p> **Tipo**: array <p> **Declaración**: `"parameters": {"parameter1": {"type": "array"}}` <p> **Especificación**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Tipo**: object <p> **Declaración**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Especificación**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Tipo**: secureobject <p> **Declaración**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Especificación**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Nota:** Los tipos `securestring` y `secureobject` no se devuelven en las operaciones `GET`. Todas las contraseñas, claves y secretos deben usar este tipo.|  
|defaultValue|No|Especifica el valor predeterminado del parámetro cuando no se especifica ningún valor en el momento de creación del recurso.|  
|allowedValues|No|Especifica una matriz de valores permitidos para el parámetro.|  
|metadata|No|Especifica información adicional sobre el parámetro, como una descripción legible o datos de tiempo de diseño que usa Visual Studio u otras herramientas.|  
  
En este ejemplo se muestra cómo puede usar un parámetro en la sección de cuerpo de una acción:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Los parámetros también pueden utilizarse en las salidas.  
  
## <a name="triggers-and-actions"></a>Desencadenadores y acciones  

Los desencadenadores y acciones especifican las llamadas que pueden participar en la ejecución de flujo de trabajo. Para más información acerca de esta sección, consulte [Acciones y desencadenadores de flujo de trabajo](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Salidas  

Las salidas especifican la información que se puede devolver desde una ejecución de flujo de trabajo. Por ejemplo, si tiene un estado o un valor específico del que se va a realizar el seguimiento para cada ejecución, puede incluir esos datos en las salidas de ejecución. Los datos aparecen en Mangement API de REST para la ejecución y en la interfaz de usuario de administración para dicha ejecución en Azure Portal. También puede enviar estas salidas a otros sistemas externos como Power BI para crear paneles. Las salidas *no* se usan para responder a las solicitudes entrantes en la API de REST de servicio. Para responder a una solicitud entrante usando el tipo de acción `response`, este es un ejemplo:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Nombre del elemento|Obligatorio|Descripción|  
|------------------|--------------|-----------------|  
|key1|Sí|Especifica el identificador de clave de la salida. Reemplace **key1** por un nombre que desea utilizar para identificar el resultado.|  
|value|Sí|Especifica el valor de la salida.|  
|type|Sí|Especifica el tipo para el valor especificado. Los tipos de valores posibles son: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Expresiones  

Los valores JSON de la definición pueden ser literales o pueden ser expresiones que se evalúan cuando se usa la definición. Por ejemplo:  
  
```json
"name": "value"
```

 o  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Algunas expresiones obtienen sus valores de acciones en tiempo de ejecución que pueden no existir al comienzo de la ejecución. Puede usar **funciones** para ayudar a recuperar algunos de estos valores.  
  
Las expresiones pueden aparecer en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Cuando se ha determinado que un valor JSON es una expresión, se extrae el cuerpo de la expresión quitando el signo de arroba (@). Si se necesita una cadena literal que empiece por @, debe convertirse mediante el uso de @@. Los ejemplos siguientes muestran cómo se evalúan las expresiones.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"parameters"|Se devuelven los caracteres de "parameters".|  
|"parameters[1]"|Se devuelven los caracteres de "parameters[1]".|  
|"@@"|Se devuelve una cadena de 1 carácter que contiene "@".|  
|" @"|Se devuelve una cadena de 2 caracteres que contienen " @".|  
  
Con la *interpolación de cadena*, las expresiones también pueden aparecer dentro de cadenas donde las expresiones se ajustan en `@{ ... }`. Por ejemplo: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

El resultado siempre es una cadena, lo que hace esta característica similar a la función `concat`. Supongamos que ha definido `myNumber` como `42` y `myString` como `sampleString`:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"@parameters("myString")"|Devuelve `sampleString` como una cadena.|  
|"@{parameters("myString")}"|Devuelve `sampleString` como una cadena.|  
|"@parameters("myNumber")"|Devuelve `42` como un *número*.|  
|"@{parameters("myNumber")}"|Devuelve `42` como una *cadena*.|  
|"Answer is: @{parameters("myNumber")}"|Devuelve la cadena `Answer is: 42`.|  
|"@concat("Answer is: ", string(parameters("myNumber")))"|Devuelve la cadena `Answer is: 42`.|  
|"Answer is: @@{parameters("myNumber")}"|Devuelve la cadena `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operadores  

Los operadores son los caracteres que se pueden usar en expresiones o funciones. 
  
|operador|Descripción|  
|--------------|-----------------|  
|.|El operador punto permite hacer referencia a las propiedades de un objeto.|  
|?|El operador de signo de interrogación permite hacer referencia a propiedades null de un objeto un error en tiempo de ejecución. Por ejemplo, puede utilizar esta expresión para controlar las salidas del desencadenador null: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|Las comillas simples son la única manera de incluir literales de cadena. No se puede usar comillas dobles dentro de las expresiones porque esta signo de puntuación está en conflicto con la oferta JSON que contiene toda la expresión.|  
|[]|Los corchetes se pueden utilizar para obtener un valor de una matriz con un índice específico. Por ejemplo, si tiene una acción que pasa `range(0,10)` en la función `forEach`, puede usar esta función para obtener elementos de las matrices:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funciones  

También puede llamar a funciones dentro de expresiones. En la tabla siguiente muestra las funciones que pueden utilizarse en una expresión.  
  
|Expression|Evaluación|  
|----------------|----------------|  
|"@function("Hello")"|Llama al miembro de la función de la definición con la cadena literal Hello como primer parámetro.|  
|"@function("It"s Cool!")"|Llama al miembro de la función de la definición con la cadena literal "It"s Cool!" como primer parámetro.|  
|"@function().prop1"|Devuelve el valor de la propiedad prop1 del miembro `myfunction` de la definición.|  
|"@function("Hello").prop1"|Llama al miembro de la función de la definición con la cadena literal "Hello" como primer parámetro y devuelve la propiedad prop1 del objeto.|  
|"@function(parameters("Hello"))"|Evalúa el parámetro Hello y pasa el valor a la función.|  
  
### <a name="referencing-functions"></a>Referencias a funciones  

Puede utilizar estas funciones para hacer referencia a salidas de otras acciones en la aplicación lógica o a los valores que se pasan cuando se crea la aplicación lógica. Por ejemplo, puede hacer referencia a los datos de un paso para usarlos en otro.  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|parameters|Devuelve un valor de parámetro que se define en la definición. <p>`parameters('password')` <p> **Número de parámetro**: 1 <p> **Nombre**: parámetro <p> **Descripción**: necesaria. El nombre del parámetro cuyos valores desea.|  
|action|Permite que una expresión derive su valor de otros pares de valor y nombre JSON o la salida de la acción en tiempo de ejecución actual. La propiedad representada por propertyPath en el ejemplo siguiente es opcional. Si no se especifica propertyPath, la referencia es el objeto de acción completa. Esta función solo puede utilizarse dentro de las condiciones do-until de una acción. <p>`action().outputs.body.propertyPath`|  
|actions|Permite que una expresión derive su valor de otros pares de valor y nombre JSON o la salida de la acción en tiempo de ejecución. Estas expresiones declaran explícitamente que una acción depende de otra. La propiedad representada por propertyPath en el ejemplo siguiente es opcional. Si no se especifica propertyPath, la referencia es el objeto de acción completa. Puede usar este elemento o el elemento conditions para especificar las dependencias, pero no es necesario usar ambos para el mismo recurso dependiente. <p>`actions('myAction').outputs.body.propertyPath` <p> **Número de parámetro**: 1 <p> **Nombre**: nombre de acción <p> **Descripción**: necesaria. El nombre de la acción cuyos valores desea. <p> Las propiedades disponibles en el objeto de acción son: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Consulte la [API de REST](http://go.microsoft.com/fwlink/p/?LinkID=850646) para más información sobre las propiedades.|
|trigger|Permite que una expresión derive su valor de otros pares de valor y nombre JSON o la salida del desencadenador en tiempo de ejecución. La propiedad representada por propertyPath en el ejemplo siguiente es opcional. Si no se especifica propertyPath, la referencia es el objeto de desencadenador completo. <p>`trigger().outputs.body.propertyPath` <p>Cuando se utiliza dentro de las entradas del desencadenador, la función devuelve las salidas de la ejecución anterior. Sin embargo, cuando se utiliza dentro de una condición del desencadenador, la función `trigger` devuelve las salidas de la ejecución anterior. <p> Las propiedades disponibles en el objeto de desencadenador son: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Consulte la [API de REST](http://go.microsoft.com/fwlink/p/?LinkID=850644) para más información sobre las propiedades.|
|actionOutputs|Esta función es una abreviatura de `actions('actionName').outputs` <p> **Número de parámetro**: 1 <p> **Nombre**: nombre de acción <p> **Descripción**: necesaria. El nombre de la acción cuyos valores desea.|  
|actionBody y body|Estas funciones son una abreviatura de `actions('actionName').outputs.body` <p> **Número de parámetro**: 1 <p> **Nombre**: nombre de acción <p> **Descripción**: necesaria. El nombre de la acción cuyos valores desea.|  
|triggerOutputs|Esta función es una abreviatura de `trigger().outputs`|  
|triggerBody|Esta función es una abreviatura de `trigger().outputs.body`|  
|item|Cuando se utiliza dentro de una acción de repetición, esta función devuelve el elemento que se encuentra en la matriz de esta iteración de la acción. Por ejemplo, si tiene una acción que se ejecuta para cada elemento de una matriz de mensajes, puede usar esta sintaxis: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Funciones de colección  

Estas funciones operan sobre colecciones y normalmente se aplican a matrices, cadenas y, a veces, a diccionarios.  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|contains|Devuelve true si el diccionario contiene una clave, la lista contiene un valor o la cadena contiene una subcadena. Por ejemplo, esta función devuelve `true`: <p>`contains('abacaba','aca')` <p> **Número de parámetro**: 1 <p> **Nombre**: dentro de la colección <p> **Descripción**: necesaria. La colección en donde buscar. <p> **Número de parámetro**: 2 <p> **Nombre**: objeto de búsqueda <p> **Descripción**: necesaria. El objeto que se va a buscar **dentro de la colección**.|  
|length|Devuelve el número de elementos de una matriz o cadena. Por ejemplo, esta función devuelve `3`:  <p>`length('abc')` <p> **Número de parámetro**: 1 <p> **Nombre**: colección <p> **Descripción**: necesaria. La colección para la que se va a obtener la longitud.|  
|empty|Devuelve true si el objeto, matriz o cadena están vacíos. Por ejemplo, esta función devuelve `true`: <p>`empty('')` <p> **Número de parámetro**: 1 <p> **Nombre**: colección <p> **Descripción**: necesaria. La colección para comprobar si está vacía.|  
|intersección|Devuelve una única matriz u objeto que tiene elementos comunes entre las matrices o los objetos en los que se pasan. Por ejemplo, esta función devuelve `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>Los parámetros de la función pueden ser un conjunto de objetos o un conjunto de matrices (no una combinación de ambos). Si hay dos objetos con el mismo nombre, el último objeto con ese nombre aparece en el objeto final. <p> **Número de parámetro**: 1 ... *n* <p> **Nombre**: colección *n* <p> **Descripción**: necesaria. Las colecciones para evaluar. El objeto debe encontrarse en todas las colecciones en las que se ha pasado para que aparezca en el resultado.|  
|union|Devuelve una única matriz o un objeto con todos los elementos que se encuentran en la matriz u objeto pasados a esta función. Por ejemplo, esta función devuelve `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>Los parámetros de la función pueden ser un conjunto de objetos o un conjunto de matrices (no una combinación de los mismos). Si hay dos objetos con el mismo nombre en la salida final, el último objeto con ese nombre aparece en el objeto final. <p> **Número de parámetro**: 1 ... *n* <p> **Nombre**: colección *n* <p> **Descripción**: necesaria. Las colecciones para evaluar. Un objeto que aparece en cualquiera de las colecciones también aparece en el resultado.|  
|first|Devuelve el primer elemento de la matriz o de la cadena en la que ha pasado. Por ejemplo, esta función devuelve `0`: <p>`first([0,2,3])` <p> **Número de parámetro**: 1 <p> **Nombre**: colección <p> **Descripción**: necesaria. La colección de donde tomar el primer objeto.|  
|last|Devuelve el último elemento de la matriz o de la cadena en la que ha pasado. Por ejemplo, esta función devuelve `3`: <p>`last('0123')` <p> **Número de parámetro**: 1 <p> **Nombre**: colección <p> **Descripción**: necesaria. La colección de donde tomar el último objeto.|  
|take|Devuelve los primeros elemento **Count** de la matriz o de la cadena en la que ha pasado. Por ejemplo, esta función devuelve `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Número de parámetro**: 1 <p> **Nombre**: colección <p> **Descripción**: necesaria. La colección desde donde se toman los primeros objetos **Count**. <p> **Número de parámetro**: 2 <p> **Nombre**: recuento <p> **Descripción**: necesaria. El número de objetos que se toman de la **colección**. Debe ser un entero positivo.|  
|skip|Devuelve los elementos en la matriz que comienza en el índice **Count**. Por ejemplo, esta función devuelve `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Número de parámetro**: 1 <p> **Nombre**: colección <p> **Descripción**: necesaria. La colección desde donde omitir los primeros objetos **Count**. <p> **Número de parámetro**: 2 <p> **Nombre**: recuento <p> **Descripción**: necesaria. El número de objetos que se van a quitar de la parte delantera de la **colección**. Debe ser un entero positivo.|  
|join|Devuelve una cadena con cada elemento de una matriz unido por un delimitador, por ejemplo, devuelve `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. La colección de donde se van a unir los elementos.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: delimitador<br /><br /> **Descripción**: necesaria. La cadena con la que delimitar los elementos.|  
  
### <a name="string-functions"></a>Funciones de cadena

Las siguientes funciones solo se aplican a las cadenas. También puede utilizar algunas funciones de colección en las cadenas.  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|concat|Combina cualquier número de cadenas. Por ejemplo, si el parámetro 1 es `p1`, esta función devuelve `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Número de parámetro**: 1 ... *n* <p> **Nombre**: cadena *n* <p> **Descripción**: necesaria. Las cadenas para combinar en una sola cadena.|  
|substring|Devuelve un subconjunto de caracteres de una cadena. Por ejemplo, esta función devuelve `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena desde la que se toma la subcadena. <p> **Número de parámetro**: 2 <p> **Nombre**: índice de inicio <p> **Descripción**: necesaria. El índice de donde comienza la subcadena en el parámetro 1. <p> **Número de parámetro**: 3 <p> **Nombre**: longitud <p> **Descripción**: necesaria. Longitud de la subcadena.|  
|replace|Reemplaza una cadena con una cadena determinada. Por ejemplo, esta función devuelve `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que se busca en el parámetro 2 y se actualiza con el parámetro 3, cuando el parámetro 2 se encuentra en el parámetro 1. <p> **Número de parámetro**: 2 <p> **Nombre**: cadena antigua <p> **Descripción**: necesaria. La cadena que desea reemplazar con el parámetro 3, cuando se encuentra una coincidencia en el parámetro 1. <p> **Número de parámetro**: 3 <p> **Nombre**: cadena nueva <p> **Descripción**: necesaria. La cadena que se usa para reemplazar la cadena en el parámetro 2, cuando se encuentra una coincidencia en el parámetro 1.|  
|GUID|Esta función genera una cadena única global (GUID). Por ejemplo, esta función puede generar este GUID: `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Número de parámetro**: 1 <p> **Nombre**: formato <p> **Descripción**: opcional. Un especificador de formato único que indica [cómo dar formato al valor de este GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). El parámetro de formato puede ser "N", "D", "B", "P" o "X". Si no se proporciona el formato, se utiliza "D".|  
|toLower|Convierte una cadena a minúsculas. Por ejemplo, esta función devuelve `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que se va a convertir a minúsculas. Si un carácter de la cadena no tiene un equivalente en minúsculas, el carácter se incluye sin cambios en la cadena devuelta.|  
|toUpper|Convierte una cadena a mayúsculas. Por ejemplo, esta función devuelve `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que se va a convertir a mayúsculas. Si un carácter de la cadena no tiene un equivalente en mayúsculas, el carácter se incluye sin cambios en la cadena devuelta.|  
|indexof|Busca el índice de un valor dentro de una cadena, con independencia de mayúsculas o minúsculas. Por ejemplo, esta función devuelve `7`: <p>`indexof('hello, world.', 'world')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que puede contener el valor. <p> **Número de parámetro**: 2 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El valor para buscar el índice.|  
|lastindexof|Busca el último índice de un valor dentro de una cadena, con independencia de mayúsculas o minúsculas. Por ejemplo, esta función devuelve `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que puede contener el valor. <p> **Número de parámetro**: 2 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El valor para buscar el índice.|  
|startswith|Comprueba si la cadena comienza con valor, con independencia de mayúsculas o minúsculas. Por ejemplo, esta función devuelve `true`: <p>`startswith('hello, world', 'hello')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que puede contener el valor. <p> **Número de parámetro**: 2 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El valor con el que comienza cadena.|  
|endswith|Comprueba si la cadena finaliza con un valor, con independencia de mayúsculas o minúsculas. Por ejemplo, esta función devuelve `true`: <p>`endswith('hello, world', 'world')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que puede contener el valor. <p> **Número de parámetro**: 2 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El valor con el que puede finalizar la cadena.|  
|split|Divide la cadena con un separador. Por ejemplo, esta función devuelve `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que se divide. <p> **Número de parámetro**: 2 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El separador.|  
  
### <a name="logical-functions"></a>Funciones lógicas  

Estas funciones son útiles en las condiciones y pueden usarse para evaluar cualquier tipo de lógica.  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|equals|Devuelve true si dos valores son iguales. Por ejemplo, si el parámetro 1 es someValue, esta función devuelve `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Número de parámetro**: 1 <p> **Nombre**: objeto 1 <p> **Descripción**: necesaria. El objeto que se va a comparar con el **objeto 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: objeto 2 <p> **Descripción**: necesaria. El objeto que se va a comparar con el **objeto 1**.|  
|less|Devuelve true si el primer argumento es inferior al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, esta función devuelve `true`: <p>`less(10,100)` <p> **Número de parámetro**: 1 <p> **Nombre**: objeto 1 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior al **objeto 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: objeto 2 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es superior al **objeto 1**.|  
|lessOrEquals|Devuelve true si el primer argumento es inferior o igual al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, esta función devuelve `true`: <p>`lessOrEquals(10,10)` <p> **Número de parámetro**: 1 <p> **Nombre**: objeto 1 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior o igual al **objeto 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: objeto 2 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es superior o igual al **objeto 1**.|  
|greater|Devuelve true si el primer argumento es superior al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, esta función devuelve `false`:  <p>`greater(10,10)` <p> **Número de parámetro**: 1 <p> **Nombre**: objeto 1 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es superior al **objeto 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: objeto 2 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior al **objeto 1**.|  
|greaterOrEquals|Devuelve true si el primer argumento es superior o igual al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, esta función devuelve `false`: <p>`greaterOrEquals(10,100)` <p> **Número de parámetro**: 1 <p> **Nombre**: objeto 1 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es superior o igual al **objeto 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: objeto 2 <p> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior o igual al **objeto 1**.|  
|y|Devuelve true si ambos parámetros son true. Ambos argumentos deben ser valores booleanos. Por ejemplo, esta función devuelve `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Número de parámetro**: 1 <p> **Nombre**: booleano 1 <p> **Descripción**: necesaria. El primer argumento debe ser `true`. <p> **Número de parámetro**: 2 <p> **Nombre**: booleano 2 <p> **Descripción**: necesaria. El segundo argumento debe ser `true`.|  
|o|Devuelve true si alguno de los parámetros es true. Ambos argumentos deben ser valores booleanos. Por ejemplo, esta función devuelve `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Número de parámetro**: 1 <p> **Nombre**: booleano 1 <p> **Descripción**: necesaria. El primer argumento debe ser `true`. <p> **Número de parámetro**: 2 <p> **Nombre**: booleano 2 <p> **Descripción**: necesaria. El segundo argumento debe ser `true`.|  
|not|Devuelve true si los parámetros son `false`. Ambos argumentos deben ser valores booleanos. Por ejemplo, esta función devuelve `true`: <p>`not(contains('200 Success','Fail'))` <p> **Número de parámetro**: 1 <p> **Nombre**: booleano <p> **Descripción**: devuelve true si los parámetros son `false`. Ambos argumentos deben ser valores booleanos. Esta función devuelve `true`: `not(contains('200 Success','Fail'))`|  
|if|Devuelve un valor especificado en función de si la expresión dio como resultado `true` o `false`.  Por ejemplo, esta función devuelve `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Número de parámetro**: 1 <p> **Nombre**: expresión <p> **Descripción**: necesaria. Un valor booleano que determina qué valor debe devolver la expresión. <p> **Número de parámetro**: 2 <p> **Nombre**: true <p> **Descripción**: necesaria. Valor que se devuelve si la expresión es `true`. <p> **Número de parámetro**: 3 <p> **Nombre**: false <p> **Descripción**: necesaria. Valor que se devuelve si la expresión es `false`.|  
  
### <a name="conversion-functions"></a>Funciones de conversión  

Estas funciones se utilizan para convertir en cada uno de los tipos nativos del idioma:  
  
- cadena  
  
- integer  
  
- float  
  
- boolean  
  
- arrays  
  
- dictionaries  

-   forms  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|int|Convierte el parámetro en un entero. Por ejemplo, esta función devuelve 100 como un número, en lugar de una cadena: <p>`int('100')` <p> **Número de parámetro**: 1 <p> **Nombre**: valor <p> **Descripción**: necesaria. El valor que se convierte en un entero.|  
|cadena|Convierte el parámetro en una cadena. Por ejemplo, esta función devuelve `'10'`: <p>`string(10)` <p>También puede convertir un objeto en una cadena. Por ejemplo, si el parámetro `myPar` es un objeto con una propiedad `abc : xyz`; después, esta función devuelve `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Número de parámetro**: 1 <p> **Nombre**: valor <p> **Descripción**: necesaria. El valor que se convierte en una cadena.|  
|json|Convierte el parámetro en un valor de tipo JSON y es el opuesto de `string()`. Por ejemplo, esta función devuelve `[1,2,3]` como una matriz, en lugar de una cadena: <p>`json('[1,2,3]')` <p>Del mismo modo, puede convertir una cadena en un objeto. Por ejemplo, esta función devuelve `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que se convierte en un valor de tipo nativo. <p>La función `json()` es compatible también con la entrada XML. Por ejemplo, el valor del parámetro de: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>se convierte a este JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Convierte el argumento del parámetro en un número de punto flotante. Por ejemplo, esta función devuelve `10.333`: <p>`float('10.333')` <p> **Número de parámetro**: 1 <p> **Nombre**: valor <p> **Descripción**: necesaria. El valor que se convierte en un número de punto flotante.|  
|booleano|Convierte el parámetro en un booleano. Por ejemplo, esta función devuelve `false`: <p>`bool(0)` <p> **Número de parámetro**: 1 <p> **Nombre**: valor <p> **Descripción**: necesaria. El valor que se convierte en un booleano.|  
|coalesce|Devuelve el primer objeto no null en los argumentos pasados. **Nota**: Una cadena vacía no es null. Por ejemplo, si no se definen los parámetros 1 y 2, esta función devuelve `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Número de parámetro**: 1 ... *n* <p> **Nombre**: objeto*n* <p> **Descripción**: necesaria. Los objetos para comprobar si hay valores null.|  
|base64|Devuelve la representación de base64 de la cadena de entrada. Por ejemplo, esta función devuelve `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena 1 <p> **Descripción**: necesaria. Cadena que se va a codificar en representación base64.|  
|base64ToBinary|Devuelve una representación binaria de una cadena codificada en base64. Por ejemplo, esta función devuelve la representación binaria de `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. Cadena codificada en base64|  
|base64ToString|Devuelve una representación de cadena de una cadena codificada en base64. Por ejemplo, esta función devuelve `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. Cadena codificada en base64|  
|Binary|Devuelve una representación binaria de un valor.  Por ejemplo, esta función devuelve la representación binaria de `some string`: <p>`binary('some string')` <p> **Número de parámetro**: 1 <p> **Nombre**: valor <p> **Descripción**: necesaria. El valor que se convierte en binario.|  
|dataUriToBinary|Devuelve una representación binaria de un identificador URI de datos. Por ejemplo, esta función devuelve la representación binaria de `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El identificador URI para convertir en representación binaria.|  
|dataUriToString|Devuelve una representación de cadena de un identificador URI de datos. Por ejemplo, esta función devuelve `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena<p> **Descripción**: necesaria. El identificador URI de datos para convertir en representación de cadena.|  
|dataUri|Devuelve un identificador URI de datos de un valor. Por ejemplo, esta función devuelve este identificador URI de datos`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Número de parámetro**: 1<p> **Nombre**: valor<p> **Descripción**: necesaria. El valor que se convierte en un identificador URI de datos.|  
|decodeBase64|Devuelve una representación de cadena de una cadena en base64 de entrada. Por ejemplo, esta función devuelve `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: devuelve una representación de cadena de una cadena en base64 de entrada.|  
|encodeUriComponent|Cadena de escape de la cadena de dirección URL que se pasa. Por ejemplo, esta función devuelve `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena de escape de los caracteres no seguros de la dirección URL.|  
|decodeUriComponent|Anulación de la cadena de escape de la cadena de dirección URL que se pasa. Por ejemplo, esta función devuelve `You Are:Cool/Awesome`: <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena para decodificar los caracteres no seguros de la dirección URL.|  
|decodeDataUri|Devuelve una representación binaria de una cadena de identificador URI de datos de entrada. Por ejemplo, esta función devuelve la representación binaria de `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena <p> **Descripción**: necesaria. El dataURI para descodificar en una representación binaria.|  
|uriComponent|Devuelve una representación codificada de un identificador URI de un valor. Por ejemplo, esta función devuelve `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Número de parámetro**: 1<p> **Nombre**: cadena <p> **Descripción**: necesaria. La cadena que se va a codificar como URI.|  
|uriComponentToBinary|Devuelve una representación binaria de una cadena codificada como URI. Por ejemplo, esta función devuelve la representación binaria de `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Número de parámetro**: 1 <p> **Nombre**: cadena<p> **Descripción**: necesaria. Cadena codificada en URI.|  
|uriComponentToString|Devuelve una representación de cadena de una cadena codificada como URI. Por ejemplo, esta función devuelve `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Número de parámetro**: 1<p> **Nombre**: cadena<p> **Descripción**: necesaria. Cadena codificada en URI.|  
|xml|Devuelve una representación XML del valor. Por ejemplo, esta función devuelve contenido XML representado por `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>La función `xml()` es compatible también con la entrada JSON del objeto. Por ejemplo, el parámetro `{ "abc": "xyz" }` se convierte en contenido XML: `\<abc>xyz\</abc>` <p> **Número de parámetro**: 1<p> **Nombre**: valor<p> **Descripción**: necesaria. El valor que se va a convertir en XML.|  
|xpath|Devuelve una matriz de nodos XML que coinciden con la expresión xpath de un valor que se evalúa como la expresión xpath. <p> **Ejemplo 1** <p>Supone que el valor del parámetro `p1` es una representación de cadena de este XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Este código: `xpath(xml(parameters('p1'), '/lab/robot/name')` <p>devuelve <p>`[ <name>R1</name>, <name>R2</name> ]` <p>mientras que este otro: <p>`xpath(xml(parameters('p1'), ' sum(/lab/robot/parts)')` <p>devuelve <p>`13` <p> <p> **Ejemplo 2** <p>Dato el contenido XML siguiente: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Este código: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>o este código: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>devuelve <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>Y este código: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>devuelve <p>``xyz`` <p> **Número de parámetro**: 1 <p> **Nombre**: Xml <p> **Descripción**: necesaria. El código XML en el que se va a evaluar la expresión XPath. <p> **Número de parámetro**: 2 <p> **Nombre**: XPath <p> **Descripción**: necesaria. La expresión XPath que se va a evaluar.|  
|array|Convierte el parámetro en una matriz. Por ejemplo, esta función devuelve `["abc"]`: <p>`array('abc')` <p> **Número de parámetro**: 1 <p> **Nombre**: valor <p> **Descripción**: necesaria. El valor que se convierte en una matriz.|
|createArray|Crea una matriz a partir de los parámetros. Por ejemplo, esta función devuelve `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Número de parámetro**: 1 ... *n* <p> **Nombre**: cualquiera *n* <p> **Descripción**: necesaria. Los valores que se van a combinar en una matriz.|
|triggerFormDataValue|Devuelve un valor único que coincida con el nombre de clave desde los datos del formulario o la salida del desencadenador codificado por formulario.  Si hay varias coincidencias, se producirá un error.  Por ejemplo, se devolverá lo siguiente `bar`: `triggerFormDataValue('foo')`<br /><br />**Número de parámetro**: 1<br /><br />**Nombre**: nombre de clave<br /><br />**Descripción**: necesaria. El nombre de clave del valor de datos de formulario que se va a devolver.|
|triggerFormDataMultiValues|Devuelve una matriz de valores que coincidan con el nombre de clave desde los datos del formulario o la salida del desencadenador codificado por formulario.  Por ejemplo, se devolverá lo siguiente `["bar"]`: `triggerFormDataValue('foo')`<br /><br />**Número de parámetro**: 1<br /><br />**Nombre**: nombre de clave<br /><br />**Descripción**: necesaria. El nombre de clave de los valores de datos de formulario que se van a devolver.|
|triggerMultipartBody|Devuelve el cuerpo de una parte de una salida de varias partes del desencadenador.<br /><br />**Número de parámetro**: 1<br /><br />**Nombre**: índice<br /><br />**Descripción**: necesaria. El índice del elemento que se va a recuperar.|
|formDataValue|Devuelve un valor único que coincida con el nombre de clave desde los datos del formulario o la salida de la acción codificada por formulario.  Si hay varias coincidencias, se producirá un error.  Por ejemplo, se devolverá lo siguiente `bar`: `formDataValue('someAction', 'foo')`<br /><br />**Número de parámetro**: 1<br /><br />**Nombre**: nombre de acción<br /><br />**Descripción**: necesaria. El nombre de la acción con datos de formulario o respuesta codificada del formulario.<br /><br />**Número de parámetro**: 2<br /><br />**Nombre**: nombre de clave<br /><br />**Descripción**: necesaria. El nombre de clave del valor de datos de formulario que se va a devolver.|
|formDataMultiValues|Devuelve una matriz de valores que coincida con el nombre de clave desde los datos del formulario o la salida de la acción codificada por formulario.  Por ejemplo, se devolverá lo siguiente `["bar"]`: `formDataMultiValues('someAction', 'foo')`<br /><br />**Número de parámetro**: 1<br /><br />**Nombre**: nombre de acción<br /><br />**Descripción**: necesaria. El nombre de la acción con datos de formulario o respuesta codificada del formulario.<br /><br />**Número de parámetro**: 2<br /><br />**Nombre**: nombre de clave<br /><br />**Descripción**: necesaria. El nombre de clave de los valores de datos de formulario que se van a devolver.|
|multipartBody|Devuelve el cuerpo de una parte de una salida de varias partes de una acción.<br /><br />**Número de parámetro**: 1<br /><br />**Nombre**: nombre de acción<br /><br />**Descripción**: necesaria. El nombre de la acción con una respuesta de varias partes.<br /><br />**Número de parámetro**: 2<br /><br />**Nombre**: índice<br /><br />**Descripción**: necesaria. El índice del elemento que se va a recuperar.|

### <a name="math-functions"></a>Funciones matemáticas  

Estas funciones pueden utilizarse para ambos tipos de números: **enteros** y **flotantes**.  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|agregar|Devuelve el resultado de sumar dos números. Por ejemplo, esta función devuelve `20.333`: <p>`add(10,10.333)` <p> **Número de parámetro**: 1 <p> **Nombre**: sumando 1 <p> **Descripción**: necesaria. El número para agregar a **sumando 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: sumando 2 <p> **Descripción**: necesaria. El número para agregar a **sumando 1**.|  
|sub|Devuelve el resultado de restar dos números. Por ejemplo, esta función devuelve `-0.333`: <p>`sub(10,10.333)` <p> **Número de parámetro**: 1 <p> **Nombre**: minuendo <p> **Descripción**: necesaria. El número que se resta del **sustraendo**. <p> **Número de parámetro**: 2 <p> **Nombre**: sustraendo <p> **Descripción**: necesaria. El número que se va a quitar del **minuendo**.|  
|mul|Devuelve el resultado de multiplicar dos números. Por ejemplo, esta función devuelve `103.33`: <p>`mul(10,10.333)` <p> **Número de parámetro**: 1 <p> **Nombre**: multiplicando 1 <p> **Descripción**: necesaria. El número que se va a multiplicar con el **multiplicando 2**. <p> **Número de parámetro**: 2 <p> **Nombre**: multiplicando 2 <p> **Descripción**: necesaria. El número que se va a multiplicar con el **multiplicando 1**.|  
|div|Devuelve el resultado de dividir dos números. Por ejemplo, esta función devuelve `1.0333`: <p>`div(10.333,10)` <p> **Número de parámetro**: 1 <p> **Nombre**: dividendo <p> **Descripción**: necesaria. El número que se va a dividir por el **divisor**. <p> **Número de parámetro**: 2 <p> **Nombre**: divisor <p> **Descripción**: necesaria. El número por el que se divide el **dividendo**.|  
|mod|Devuelve el resto después de dividir los dos números (módulo). Por ejemplo, esta función devuelve `2`: <p>`mod(10,4)` <p> **Número de parámetro**: 1 <p> **Nombre**: dividendo <p> **Descripción**: necesaria. El número que se va a dividir por el **divisor**. <p> **Número de parámetro**: 2 <p> **Nombre**: divisor <p> **Descripción**: necesaria. El número por el que se divide el **dividendo**. Después de la división, se toma el resto.|  
|Min|Hay dos patrones diferentes para llamar a esta función. <p>Aquí `min` toma una matriz y la función devuelve `0`: <p>`min([0,1,2])` <p>Como alternativa, esta función puede tomar una lista de valores separada por comas y también devuelve `0`: <p>`min(0,1,2)` <p> **Nota**: Todos los valores deben ser números, por lo que si el parámetro es una matriz, esta debe tener solo números. <p> **Número de parámetro**: 1 <p> **Nombre**: recopilación o valor <p> **Descripción**: necesaria. Una matriz de valores para encontrar el valor mínimo o el primer valor de un conjunto. <p> **Número de parámetro**: 2 ... *n* <p> **Nombre**: valor *n* <p> **Descripción**: opcional. Si el primer parámetro es un valor, se pueden pasar valores adicionales y se devuelve el mínimo de todos los valores pasados.|  
|max|Hay dos patrones diferentes para llamar a esta función. <p>Aquí `max` toma una matriz y la función devuelve `2`: <p>`max([0,1,2])` <p>Como alternativa, esta función puede tomar una lista de valores separada por comas y también devuelve `2`: <p>`max(0,1,2)` <p> **Nota**: Todos los valores deben ser números, por lo que si el parámetro es una matriz, esta debe tener solo números. <p> **Número de parámetro**: 1 <p> **Nombre**: recopilación o valor <p> **Descripción**: necesaria. Una matriz de valores para encontrar el valor máximo o el primer valor de un conjunto. <p> **Número de parámetro**: 2 ... *n* <p> **Nombre**: valor *n* <p> **Descripción**: opcional. Si el primer parámetro es un valor, se pueden pasar valores adicionales y se devuelve el valor máximo de todos los valores pasados.|  
|range|Genera una matriz de enteros a partir de un número determinado. Se define la longitud de la matriz devuelta. <p>Por ejemplo, esta función devuelve `[3,4,5,6]`: <p>`range(3,4)` <p> **Número de parámetro**: 1 <p> **Nombre**: índice de inicio <p> **Descripción**: necesaria. El primer entero de la matriz. <p> **Número de parámetro**: 2 <p> **Nombre**: recuento <p> **Descripción**: necesaria. Este valor es el número de enteros que se encuentra en la matriz.|  
|rand|Genera un entero aleatorio dentro del intervalo especificado (primer extremo incluido). Por ejemplo, esta función puede devolver `0` o "1": <p>`rand(0,2)` <p> **Número de parámetro**: 1 <p> **Nombre**: mínimo <p> **Descripción**: necesaria. El entero más bajo que se puede devolver. <p> **Número de parámetro**: 2 <p> **Nombre**: máximo <p> **Descripción**: necesaria. Este valor es el siguiente entero más alto después del entero máximo que se podría devolver.|  
  
### <a name="date-functions"></a>Funciones de fecha  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|utcnow|Devuelve la marca de tiempo actual como una cadena, por ejemplo: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Número de parámetro**: 1 <p> **Nombre**: formato <p> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|addseconds|Agrega un número entero de segundos a una marca de tiempo de cadena que se pasa. El número de segundos puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Número de parámetro**: 1 <p> **Nombre**: marca de tiempo <p> **Descripción**: necesaria. Una cadena que contiene la hora. <p> **Número de parámetro**: 2 <p> **Nombre**: segundos <p> **Descripción**: necesaria. El número de segundos que se agregan. Puede ser negativo para restar los segundos. <p> **Número de parámetro**: 3 <p> **Nombre**: formato <p> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|addminutes|Agrega un número entero de minutos a una marca de tiempo de cadena que se pasa. El número de minutos puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Número de parámetro**: 1 <p> **Nombre**: marca de tiempo <p> **Descripción**: necesaria. Una cadena que contiene la hora. <p> **Número de parámetro**: 2 <p> **Nombre**: minutos <p> **Descripción**: necesaria. El número de minutos que se van a agregar. Puede ser negativo para restar los minutos. <p> **Número de parámetro**: 3 <p> **Nombre**: formato <p> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|addhours|Agrega un número entero de horas a una marca de tiempo de cadena que se pasa. El número de horas puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Número de parámetro**: 1 <p> **Nombre**: marca de tiempo <p> **Descripción**: necesaria. Una cadena que contiene la hora. <p> **Número de parámetro**: 2 <p> **Nombre**: horas <p> **Descripción**: necesaria. El número de horas que se van a agregar. Puede ser negativo para restar las horas. <p> **Número de parámetro**: 3 <p> **Nombre**: formato <p> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|adddays|Agrega un número entero de días a una marca de tiempo de cadena que se pasa. El número de días puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-02-23T13:27:36Z`: <p>`addseconds('2015-03-15T13:27:36Z', -20)` <p> **Número de parámetro**: 1 <p> **Nombre**: marca de tiempo <p> **Descripción**: necesaria. Una cadena que contiene la hora. <p> **Número de parámetro**: 2 <p> **Nombre**: días <p> **Descripción**: necesaria. El número de días que se van a agregar. Puede ser negativo para restar los días. <p> **Número de parámetro**: 3 <p> **Nombre**: formato <p> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|formatDateTime|Devuelve una cadena en formato de fecha. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-02-23T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Número de parámetro**: 1 <p> **Nombre**: fecha <p> **Descripción**: necesaria. Una cadena que contiene la fecha. <p> **Número de parámetro**: 2 <p> **Nombre**: formato <p> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|startOfHour|Devuelve el inicio de la hora a una marca de tiempo de cadena que se pasa. Por ejemplo: `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.<br /><br />**Número de parámetro**: 2<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|startOfDay|Devuelve el inicio del día a una marca de tiempo de cadena que se pasa. Por ejemplo: `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.<br /><br />**Número de parámetro**: 2<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").| 
|startOfMonth|Devuelve el inicio del mes a una marca de tiempo de cadena que se pasa. Por ejemplo: `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.<br /><br />**Número de parámetro**: 2<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").| 
|dayOfWeek|Devuelve el día del componente de la semana de una marca de tiempo de cadena.  El valor del domingo es 0, el del lunes es 1 y así sucesivamente. Por ejemplo: `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.| 
|dayOfMonth|Devuelve el día del componente del mes de una marca de tiempo de cadena. Por ejemplo: `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.| 
|dayOfYear|Devuelve el día del componente del año de una marca de tiempo de cadena. Por ejemplo: `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.| 
|ticks|Devuelve la propiedad ticks de una marca de tiempo de cadena. Por ejemplo: `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Es una cadena que contiene la hora.| 
  
### <a name="workflow-functions"></a>Funciones de flujo de trabajo  

Estas funciones le ayudarán a obtener información sobre el mismo flujo de trabajo en tiempo de ejecución.  
  
|Nombre de función|Descripción|  
|-------------------|-----------------|  
|listCallbackUrl|Devuelve una cadena de llamadas para invocar la acción o el desencadenador. <p> **Nota**: Esta función solo puede utilizarse en **httpWebhook** y **apiConnectionWebhook**, no en **manual**, **recurrence**, **http** o **apiConnection**. <p>Por ejemplo, la función `listCallbackUrl()` devuelve: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|flujo de trabajo|Esta función proporciona todos los detalles del mismo propio flujo de trabajo en tiempo de ejecución. <p> Las propiedades disponibles en el objeto de flujo de trabajo son: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> El valor de la propiedad `run` es un objeto con las siguientes propiedades: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Consulte la [API de REST](http://go.microsoft.com/fwlink/p/?LinkID=525617) para más información sobre las propiedades.<p> Por ejemplo, para obtener el nombre de la ejecución actual, use la expresión `"@workflow().run.name"`. |

## <a name="next-steps"></a>Pasos siguientes

[Acciones y desencadenadores de flujo de trabajo](logic-apps-workflow-actions-triggers.md)
