---
title: Expresiones y funciones en Azure Data Factory | Microsoft Docs
description: "En este artículo se proporciona información sobre las expresiones y las funciones que se pueden usar al crear entidades de Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 78f21576bb7d839e5b5c4d8c2b721e381d663406
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expresiones y funciones de Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-functions-variables.md)
> * [Versión 2: versión preliminar](control-flow-expression-language-functions.md)

En este artículo se proporciona información detallada sobre las expresiones y las funciones compatibles con Azure Data Factory (versión 2). 

## <a name="introduction"></a>Introducción
Los valores JSON de la definición pueden ser literales o expresiones que se evalúan en tiempo de ejecución. Por ejemplo:   
  
```json
"name": "value"
```

 o  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea [Funciones y variables en Data Factory V1](v1/data-factory-functions-variables.md).


## <a name="expressions"></a>Expresiones  
Las expresiones pueden aparecer en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Si un valor JSON es una expresión, se extrae el cuerpo de la expresión quitando el signo arroba (@). Si se necesita una cadena literal que empiece por @, debe convertirse con @@. Los ejemplos siguientes muestran cómo se evalúan las expresiones.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"parameters"|Se devuelven los caracteres de "parameters".|  
|"parameters[1]"|Se devuelven los caracteres de "parameters[1]".|  
|"@@"|Se devuelve una cadena de 1 carácter que contiene "@".|  
|" @"|Se devuelve una cadena de 2 caracteres que contienen " @".|  
  
 Las expresiones también pueden aparecer dentro de las cadenas mediante una característica llamada *interpolación de cadenas*, donde las expresiones se ajustan en `@{ ... }`. Por ejemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Con la interpolación de cadena, el resultado siempre es una cadena. Supongamos que se ha definido `myNumber` como `42` y `myString` como `foo`:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"@pipeline().parameters.myString"| Devuelve `foo` como una cadena.|  
|"@{pipeline().parameters.myString}"| Devuelve `foo` como una cadena.|  
|"@pipeline().parameters.myNumber"| Devuelve `42` como un *número*.|  
|"@{pipeline().parameters.myNumber}"| Devuelve `42` como una *cadena*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Devuelve la cadena `Answer is: 42`.|  
|"@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Devuelve la cadena `Answer is: 42`.|  
|"Answer is: @@{pipeline().parameters.myNumber}"| Devuelve la cadena `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Ejemplos

#### <a name="a-dataset-with-a-parameter"></a>Un conjunto de datos con un parámetro
En el ejemplo siguiente, BlobDataset toma un parámetro llamado **path**. Su valor se usa para establecer un valor para la propiedad **folderPath** mediante el uso de las siguientes expresiones: `@{dataset().path}`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Una canalización con un parámetro
En el ejemplo siguiente, la canalización toma los parámetros **inputPath** y **outputPath**. El valor de **path** para el conjunto de datos del blob con parámetros se establece mediante el uso de los valores de estos parámetros. La sintaxis utilizada aquí es: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Functions  
 Puede llamar a funciones dentro de expresiones. En las siguientes secciones se proporciona información sobre las funciones que se pueden usar en una expresión.  

## <a name="string-functions"></a>Funciones de cadena  
 Las siguientes funciones solo se aplican a las cadenas. También puede usar una serie de funciones de colección en las cadenas.  
  
|Nombre de función|DESCRIPCIÓN|  
|-------------------|-----------------|  
|concat|Combina cualquier número de cadenas. Por ejemplo, si parameter1 es `foo,`, la siguiente expresión devolvería `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Número de parámetro**: 1 ... *n*<br /><br /> **Nombre**: cadena *n*<br /><br /> **Descripción**: necesaria. Las cadenas para combinar en una sola cadena.|  
|substring|Devuelve un subconjunto de caracteres de una cadena. Por ejemplo, la siguiente expresión:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> devuelve:<br /><br /> `foo`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena desde la que se toma la subcadena.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: índice de inicio<br /><br /> **Descripción**: necesaria. El índice de donde comienza la subcadena en el parámetro 1.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: longitud<br /><br /> **Descripción**: necesaria. Longitud de la subcadena.|  
|replace|Reemplaza una cadena con una cadena determinada. Por ejemplo, la expresión:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> devuelve:<br /><br /> `the new string`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria.  Si el parámetro 2 se encuentra en el parámetro 1, la cadena que se busca en el parámetro 2 y se actualiza con el parámetro 3.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: cadena antigua<br /><br /> **Descripción**: necesaria. La cadena que se reemplazará por el parámetro 3 cuando se encuentra una coincidencia en el parámetro 1.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: cadena nueva<br /><br /> **Descripción**: necesaria. La cadena que se usa para reemplazar la cadena en el parámetro 2, cuando se encuentra una coincidencia en el parámetro 1.|  
|GUID| Genera una cadena única global (también conocida como GUID). Por ejemplo, se podría generar la siguiente salida `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Un especificador de formato único que indica [cómo dar formato al valor de este GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). El parámetro de formato puede ser "N", "D", "B", "P" o "X". Si no se proporciona el formato, se utiliza "D".|  
|toLower|Convierte una cadena a minúsculas. Por ejemplo, la siguiente expresión devuelve `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que se va a convertir a minúsculas. Si un carácter de la cadena no tiene un equivalente en minúsculas, se incluye sin cambios en la cadena devuelta.|  
|toUpper|Convierte una cadena a mayúsculas. Por ejemplo, la siguiente expresión devuelve `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que se va a convertir a mayúsculas. Si un carácter de la cadena no tiene un equivalente en mayúsculas, se incluye sin cambios en la cadena devuelta.|  
|indexof|Busca el índice de un valor dentro de una cadena, con independencia de mayúsculas o minúsculas. Por ejemplo, la siguiente expresión devuelve `7`: `indexof('hello, world.', 'world')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que puede contener el valor.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El valor para buscar el índice.|  
|lastindexof|Busca el último índice de un valor dentro de una cadena, con independencia de mayúsculas o minúsculas. Por ejemplo, la siguiente expresión devuelve `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que puede contener el valor.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El valor para buscar el índice.|  
|startswith|Comprueba si la cadena comienza con valor, con independencia de mayúsculas o minúsculas. Por ejemplo, la siguiente expresión devuelve `true`: `lastindexof('hello, world', 'hello')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que puede contener el valor.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El valor con el que comienza cadena.|  
|endswith|Comprueba si la cadena finaliza con un valor, con independencia de mayúsculas o minúsculas. Por ejemplo, la siguiente expresión devuelve `true`: `lastindexof('hello, world', 'world')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que puede contener el valor.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El valor con el que puede finalizar la cadena.|  
|split|Divide la cadena con un separador. Por ejemplo, la siguiente expresión devuelve `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que se divide.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El separador.|  
  
  
## <a name="collection-functions"></a>Funciones de colección  
 Estas funciones operan sobre colecciones como matrices, cadenas y, a veces, diccionarios.  
  
|Nombre de función|DESCRIPCIÓN|  
|-------------------|-----------------|  
|contains|Devuelve true si el diccionario contiene una clave, la lista contiene un valor o la cadena contiene una subcadena. Por ejemplo, la siguiente expresión devuelve `true:``contains('abacaba','aca')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: dentro de la colección<br /><br /> **Descripción**: necesaria. La colección en donde buscar.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: objeto de búsqueda<br /><br /> **Descripción**: necesaria. El objeto que se va a buscar **dentro de la colección**.|  
|length|Devuelve el número de elementos de una matriz o cadena. Por ejemplo, la siguiente expresión devuelve `3`: `length('abc')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. Colección de la que se obtendrá la longitud.|  
|empty|Devuelve true si el objeto, matriz o cadena están vacíos. Por ejemplo, la siguiente expresión devuelve `true`:<br /><br /> `empty('')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. La colección para comprobar si está vacía.|  
|intersección|Devuelve una única matriz u objeto con los elementos comunes entre las matrices o los objetos en los que se pasan. Por ejemplo, esta función devuelve `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Los parámetros de la función pueden ser un conjunto de objetos o un conjunto de matrices (no una combinación de los mismos). Si hay dos objetos con el mismo nombre, el último objeto con ese nombre aparece en el objeto final.<br /><br /> **Número de parámetro**: 1 ... *n*<br /><br /> **Nombre**: colección *n*<br /><br /> **Descripción**: necesaria. Las colecciones para evaluar. El objeto debe encontrarse en todas las colecciones en las que se ha pasado para que aparezca en el resultado.|  
|union|Devuelve una única matriz o un objeto con todos los elementos que se encuentran en la matriz u objeto pasados. Por ejemplo, esta función devuelve `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Los parámetros de la función pueden ser un conjunto de objetos o un conjunto de matrices (no una combinación de los mismos). Si hay dos objetos con el mismo nombre en la salida final, el último objeto con ese nombre aparece en el objeto final.<br /><br /> **Número de parámetro**: 1 ... *n*<br /><br /> **Nombre**: colección *n*<br /><br /> **Descripción**: necesaria. Las colecciones para evaluar. En el resultado aparece un objeto que aparece en cualquiera de las colecciones.|  
|first|Devuelve el primer elemento de la matriz o de la cadena en la que ha pasado. Por ejemplo, esta función devuelve `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. La colección de donde tomar el primer objeto.|  
|last|Devuelve el último elemento de la matriz o de la cadena en la que ha pasado. Por ejemplo, esta función devuelve `3`:<br /><br /> `last('0123')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. La colección de donde tomar el último objeto.|  
|take|Devuelve los primeros elementos **Count** de la matriz o de la cadena que se ha pasado. Por ejemplo, esta función devuelve `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. Colección de la que se tomarán los primeros objetos **Count**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: recuento<br /><br /> **Descripción**: necesaria. El número de objetos que se toman de la **colección**. Debe ser un entero positivo.|  
|skip|Devuelve los elementos en la matriz que comienzan en el índice **Count**. Por ejemplo, esta función devuelve `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: colección<br /><br /> **Descripción**: necesaria. La colección desde donde omitir los primeros objetos **Count**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: recuento<br /><br /> **Descripción**: necesaria. El número de objetos que se van a quitar de la parte delantera de la **colección**. Debe ser un entero positivo.|  
  
## <a name="logical-functions"></a>Funciones lógicas  
 Estas funciones son útiles en las condiciones y se pueden usar para evaluar cualquier tipo de lógica.  
  
|Nombre de función|DESCRIPCIÓN|  
|-------------------|-----------------|  
|equals|Devuelve true si dos valores son iguales. Por ejemplo, si parameter1 es foo, la siguiente expresión devolvería `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: objeto 1<br /><br /> **Descripción**: necesaria. El objeto que se va a comparar con el **objeto 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: objeto 2<br /><br /> **Descripción**: necesaria. El objeto que se va a comparar con el **objeto 1**.|  
|less|Devuelve true si el primer argumento es inferior al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, la siguiente expresión devuelve `true`: `less(10,100)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: objeto 1<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior al **objeto 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: objeto 2<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es superior al **objeto 1**.|  
|lessOrEquals|Devuelve true si el primer argumento es inferior o igual al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, la siguiente expresión devuelve `true`: `lessOrEquals(10,10)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: objeto 1<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior o igual al **objeto 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: objeto 2<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es superior o igual al **objeto 1**.|  
|greater|Devuelve true si el primer argumento es superior al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, la siguiente expresión devuelve `false`: `greater(10,10)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: objeto 1<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es superior al **objeto 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: objeto 2<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior al **objeto 1**.|  
|greaterOrEquals|Devuelve true si el primer argumento es superior o igual al segundo. Tenga en cuenta que solo pueden ser valores de tipo integer, float o string. Por ejemplo, la siguiente expresión devuelve `false`: `greaterOrEquals(10,100)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: objeto 1<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es superior o igual al **objeto 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: objeto 2<br /><br /> **Descripción**: necesaria. El objeto que se va a comprobar si es inferior o igual al **objeto 1**.|  
|y|Devuelve true si ambos parámetros son true. Ambos argumentos deben ser valores booleanos. Lo siguiente devuelve `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: booleano 1<br /><br /> **Descripción**: necesaria. El primer argumento debe ser `true`.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: booleano 2<br /><br /> **Descripción**: necesaria. El segundo argumento debe ser `true`.|  
|o|Devuelve true si cualquiera de los parámetros es true. Ambos argumentos deben ser valores booleanos. Lo siguiente devuelve `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: booleano 1<br /><br /> **Descripción**: necesaria. El primer argumento debe ser `true`.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: booleano 2<br /><br /> **Descripción**: necesaria. El segundo argumento debe ser `true`.|  
|not|Devuelve true si el parámetro es `false`. Ambos argumentos deben ser valores booleanos. Lo siguiente devuelve `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: booleano<br /><br /> **Descripción**: Devuelve true si el parámetro es `false`. Ambos argumentos deben ser valores booleanos. Lo siguiente devuelve `true`: `not(contains('200 Success','Fail'))`|  
|if|Devuelve un valor especificado en función de si la expresión ha proporcionado resultados en `true` o `false`.  Por ejemplo, la siguiente expresión devuelve `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: expresión<br /><br /> **Descripción**: necesaria. Un valor booleano que determina qué valor devuelve la expresión.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: true<br /><br /> **Descripción**: necesaria. Valor que se devuelve si la expresión es `true`.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: false<br /><br /> **Descripción**: necesaria. Valor que se devuelve si la expresión es `false`.|  
  
## <a name="conversion-functions"></a>Funciones de conversión  
 Estas funciones se utilizan para convertir en cada uno de los tipos nativos del idioma:  
  
-   cadena  
  
-   integer  
  
-   float  
  
-   boolean  
  
-   arrays  
  
-   dictionaries  
  
|Nombre de función|DESCRIPCIÓN|  
|-------------------|-----------------|  
|int|Convierte el parámetro en un entero. Por ejemplo, la siguiente expresión devuelve 100 como un número, en lugar de una cadena: `int('100')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: valor<br /><br /> **Descripción**: necesaria. El valor que se convierte en un entero.|  
|cadena|Convierte el parámetro en una cadena. Por ejemplo, la siguiente expresión devuelve `'10'`: `string(10)`. También puede convertir un objeto en una cadena. Por ejemplo, si el parámetro **foo** es un objeto con una propiedad `bar : baz` se devolvería `{"bar" : "baz"}` `string(pipeline().parameters.foo)`.<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: valor<br /><br /> **Descripción**: necesaria. El valor que se convierte en una cadena.|  
|json|Convierte el parámetro en un valor de tipo JSON. Es lo contrario de string(). Por ejemplo, la siguiente expresión devuelve `[1,2,3]` como una matriz, en lugar de una cadena:<br /><br /> `parse('[1,2,3]')`<br /><br /> Del mismo modo, puede convertir una cadena en un objeto. Por ejemplo, `json('{"bar" : "baz"}')` devuelve:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena que se convierte en un valor de tipo nativo.<br /><br /> La función json también es compatible con la entrada XML. Por ejemplo, el valor del parámetro de:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> se convierte en el siguiente JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Convierte el argumento del parámetro en un número de punto flotante. Por ejemplo, la siguiente expresión devuelve `10.333`: `float('10.333')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: valor<br /><br /> **Descripción**: necesaria. El valor que se convierte en un número de punto flotante.|  
|booleano|Convierte el parámetro en un booleano. Por ejemplo, la siguiente expresión devuelve `false`: `bool(0)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: valor<br /><br /> **Descripción**: necesaria. El valor que se convierte en un booleano.|  
|coalesce|Devuelve el primer objeto no null en los argumentos pasados. Nota: Una cadena vacía no es null. Por ejemplo, si no se definen los parámetros 1 y 2, se devolverá `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Número de parámetro**: 1 ... *n*<br /><br /> **Nombre**: objeto*n*<br /><br /> **Descripción**: necesaria. Los objetos que deben comprobar si hay valores `null`.|  
|base64|Devuelve la representación de base64 de la cadena de entrada. Por ejemplo, la siguiente expresión devuelve `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena 1<br /><br /> **Descripción**: necesaria. Cadena que se va a codificar en representación base64.|  
|base64ToBinary|Devuelve una representación binaria de una cadena codificada en base64. Por ejemplo, la siguiente expresión devuelve la representación binaria de alguna cadena: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. Cadena codificada en base64|  
|base64ToString|Devuelve una representación de cadena de una cadena codificada en base64. Por ejemplo, la siguiente expresión devuelve alguna cadena: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. Cadena codificada en base64|  
|Binary|Devuelve una representación binaria de un valor.  Por ejemplo, la siguiente expresión devuelve una representación binaria de alguna cadena: `binary(‘some string’).`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: valor<br /><br /> **Descripción**: necesaria. El valor que se convierte en binario.|  
|dataUriToBinary|Devuelve una representación binaria de un identificador URI de datos. Por ejemplo, la siguiente expresión devuelve la representación binaria de alguna cadena: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El identificador URI para convertir en representación binaria.|  
|dataUriToString|Devuelve una representación de cadena de un identificador URI de datos. Por ejemplo, la siguiente expresión devuelve alguna cadena: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br />**Descripción**: necesaria. El identificador URI de datos para convertir en representación de cadena.|  
|dataUri|Devuelve un identificador URI de datos de un valor. Por ejemplo, la siguiente expresión devuelve datos: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Número de parámetro**: 1<br /><br />**Nombre**: valor<br /><br />**Descripción**: necesaria. El valor que se convierte en un identificador URI de datos.|  
|decodeBase64|Devuelve una representación de cadena de una cadena en base64 de entrada. Por ejemplo, la siguiente expresión devuelve `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: devuelve una representación de cadena de una cadena en base64 de entrada.|  
|encodeUriComponent|Cadena de escape de la cadena de dirección URL que se pasa. Por ejemplo, la siguiente expresión devuelve `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena de escape de los caracteres no seguros de la dirección URL.|  
|decodeUriComponent|Anulación de la cadena de escape de la cadena de dirección URL que se pasa. Por ejemplo, la siguiente expresión devuelve `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. La cadena para decodificar los caracteres no seguros de la dirección URL.|  
|decodeDataUri|Devuelve una representación binaria de una cadena de identificador URI de datos de entrada. Por ejemplo, la siguiente expresión devuelve la representación binaria de `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br /> **Descripción**: necesaria. El dataURI para descodificar en una representación binaria.|  
|uriComponent|Devuelve una representación codificada de un identificador URI de un valor. Por ejemplo, la siguiente expresión devuelve `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Detalles de los parámetros: Número: 1; Nombre: Cadena; Descripción: Necesario. La cadena que se va a codificar como URI.|  
|uriComponentToBinary|Devuelve una representación binaria de una cadena codificada como URI. Por ejemplo, la siguiente expresión devuelve una representación binaria de `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: cadena<br /><br />**Descripción**: necesaria. Cadena codificada en URI.|  
|uriComponentToString|Devuelve una representación de cadena de una cadena codificada como URI. Por ejemplo, la siguiente expresión devuelve `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Número de parámetro**: 1<br /><br />**Nombre**: cadena<br /><br />**Descripción**: necesaria. Cadena codificada en URI.|  
|xml|Devuelve una representación XML del valor. Por ejemplo, la siguiente expresión devuelve un contenido xml representado por `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. La función xml también es compatible con la entrada de objeto JSON. Por ejemplo, el parámetro `{ "abc": "xyz" }` se convierte en contenido XML: `\<abc>xyz\</abc>`.<br /><br /> **Número de parámetro**: 1<br /><br />**Nombre**: valor<br /><br />**Descripción**: necesaria. El valor que se va a convertir en XML.|  
|xpath|Devuelve una matriz de nodos XML que coinciden con la expresión xpath de un valor que se evalúa como la expresión xpath.<br /><br />  **Ejemplo 1**<br /><br /> Supone que el valor del parámetro "p1" es una representación de cadena del siguiente XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Este código: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> devolvería<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> mientras que<br /><br /> 2. Este código: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> devolvería<br /><br /> `13`<br /><br /> <br /><br /> **Ejemplo 2**<br /><br /> Dato el contenido XML siguiente:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Este código: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> o<br /><br /> 2. Este código: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> devuelve<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> y<br /><br /> 3. Este código: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> devuelve<br /><br /> ``bar``<br /><br /> **Número de parámetro**: 1<br /><br />**Nombre**: Xml<br /><br />**Descripción**: necesaria. El código XML en el que se va a evaluar la expresión XPath.<br /><br /> **Número de parámetro**: 2<br /><br />**Nombre**: XPath<br /><br />**Descripción**: necesaria. La expresión XPath que se va a evaluar.|  
|array|Convierte el parámetro en una matriz.  Por ejemplo, la siguiente expresión devuelve `["abc"]`: `array('abc')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: valor<br /><br /> **Descripción**: necesaria. El valor que se convierte en una matriz.|
|createArray|Crea una matriz a partir de los parámetros.  Por ejemplo, la siguiente expresión devuelve `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Número de parámetro**: 1 ... n<br /><br /> **Nombre**: Cualquier n<br /><br /> **Descripción**: necesaria. Los valores que se van a combinar en una matriz.|

## <a name="math-functions"></a>Funciones matemáticas  
 Estas funciones pueden utilizarse para ambos tipos de números: **enteros** y **flotantes**.  
  
|Nombre de función|DESCRIPCIÓN|  
|-------------------|-----------------|  
|agregar|Devuelve el resultado de la suma de los dos números. Por ejemplo, esta función devuelve `20.333`: `add(10,10.333)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: sumando 1<br /><br /> **Descripción**: necesaria. El número para agregar a **sumando 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: sumando 2<br /><br /> **Descripción**: necesaria. El número para agregar a **sumando 1**.|  
|sub|Devuelve el resultado de la resta de los dos números. Por ejemplo, esta función devuelve `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: minuendo<br /><br /> **Descripción**: necesaria. El número que se resta del **sustraendo**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: sustraendo<br /><br /> **Descripción**: necesaria. El número que se va a quitar del **minuendo**.|  
|mul|Devuelve el resultado de la multiplicación de los dos números. Por ejemplo, la siguiente expresión devuelve `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: multiplicando 1<br /><br /> **Descripción**: necesaria. El número que se va a multiplicar con el **multiplicando 2**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: multiplicando 2<br /><br /> **Descripción**: necesaria. El número que se va a multiplicar con el **multiplicando 1**.|  
|div|Devuelve el resultado de la división de los dos números. Por ejemplo, la siguiente expresión devuelve `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: dividendo<br /><br /> **Descripción**: necesaria. El número que se va a dividir por el **divisor**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: divisor<br /><br /> **Descripción**: necesaria. El número por el que se divide el **dividendo**.|  
|mod|Devuelve el resultado del resto después de hacer la división de los dos números (módulo). Por ejemplo, la siguiente expresión devuelve `2`:<br /><br /> `mod(10,4)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: dividendo<br /><br /> **Descripción**: necesaria. El número que se va a dividir por el **divisor**.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: divisor<br /><br /> **Descripción**: necesaria. El número por el que se divide el **dividendo**. Después de la división, se toma el resto.|  
|Min|Hay dos patrones diferentes para llamar a esta función: `min([0,1,2])` Aquí min toma una matriz. Esta expresión devuelve `0`. Como alternativa, esta función puede tomar una lista de valores separados por comas: `min(0,1,2)` Esta función también devuelve 0. Nota: Todos los valores deben ser números, por lo que si el parámetro es una matriz, solo debe contener números.<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: recopilación o valor<br /><br /> **Descripción**: necesaria. Puede ser una matriz de valores para buscar el valor mínimo o el primer valor de un conjunto.<br /><br /> **Número de parámetro**: 2 ... *n*<br /><br /> **Nombre**: valor *n*<br /><br /> **Descripción**: opcional. Si el primer parámetro es un valor, se pueden pasar valores adicionales y se devuelve el mínimo de todos los valores pasados.|  
|max|Hay dos patrones diferentes para llamar a esta función: `max([0,1,2])`<br /><br /> Aquí max toma una matriz. Esta expresión devuelve `2`. Como alternativa, esta función puede tomar una lista de valores separados por comas: `max(0,1,2)` Esta función devuelve 2. Nota: Todos los valores deben ser números, por lo que si el parámetro es una matriz, solo debe contener números.<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: recopilación o valor<br /><br /> **Descripción**: necesaria. Puede ser una matriz de valores para buscar el valor máximo o el primer valor de un conjunto.<br /><br /> **Número de parámetro**: 2 ... *n*<br /><br /> **Nombre**: valor *n*<br /><br /> **Descripción**: opcional. Si el primer parámetro es un valor, se pueden pasar valores adicionales y se devuelve el máximo de todos los valores pasados.|  
|range| Genera una matriz de enteros a partir de un número determinado y se define la longitud de la matriz devuelta. Por ejemplo, esta función devuelve `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: índice de inicio<br /><br /> **Descripción**: necesaria. Es el primer entero de la matriz.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: recuento<br /><br /> **Descripción**: necesaria. Número de enteros que hay en la matriz.|  
|rand| Genera un entero aleatorio dentro del intervalo especificado (ambos extremos incluidos). Por ejemplo, esto podría devolver `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: mínimo<br /><br /> **Descripción**: necesaria. El entero más bajo que se podría devolver.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: máximo<br /><br /> **Descripción**: necesaria. El entero más alto que se podría devolver.|  
  
## <a name="date-functions"></a>Funciones de fecha  
  
|Nombre de función|DESCRIPCIÓN|  
|-------------------|-----------------|  
|utcnow|Devuelve la marca de tiempo actual como una cadena. Por ejemplo: `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|addseconds|Agrega un número entero de segundos a una marca de tiempo de cadena que se pasa. El número de segundos puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Una cadena que contiene la hora.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: segundos<br /><br /> **Descripción**: necesaria. El número de segundos que se agregan. Podría ser negativo para restar los segundos.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|addminutes|Agrega un número entero de minutos a una marca de tiempo de cadena que se pasa. El número de minutos puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Una cadena que contiene la hora.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: minutos<br /><br /> **Descripción**: necesaria. El número de minutos que se van a agregar. Podría ser negativo para restar los minutos.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|addhours|Agrega un número entero de horas a una marca de tiempo de cadena que se pasa. El número de horas puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Una cadena que contiene la hora.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: horas<br /><br /> **Descripción**: necesaria. El número de horas que se van a agregar. Podría ser negativo para restar las horas.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|adddays|Agrega un número entero de días a una marca de tiempo de cadena que se pasa. El número de días puede ser positivo o negativo. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-02-23T13:27:36Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -20)`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: marca de tiempo<br /><br /> **Descripción**: necesaria. Una cadena que contiene la hora.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: días<br /><br /> **Descripción**: necesaria. El número de días que se van a agregar. Podría ser negativo para restar los días.<br /><br /> **Número de parámetro**: 3<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  
|formatDateTime|Devuelve una cadena en formato de fecha. De forma predeterminada, el resultado es una cadena en formato ISO 8601 ("o"), a menos que se proporcione un especificador de formato. Por ejemplo: `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Número de parámetro**: 1<br /><br /> **Nombre**: fecha<br /><br /> **Descripción**: necesaria. Una cadena que contiene la fecha.<br /><br /> **Número de parámetro**: 2<br /><br /> **Nombre**: formato<br /><br /> **Descripción**: opcional. Ya sea un [único carácter especificador de formato](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) o un [patrón de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica cómo dar formato al valor de esta marca de tiempo. Si no se proporciona el formato, se utiliza el formato ISO 8601 ("o").|  

## <a name="next-steps"></a>pasos siguientes
Para obtener una lista de las variables del sistema que se pueden usar en las expresiones, vea [Variables del sistema](control-flow-system-variables.md).