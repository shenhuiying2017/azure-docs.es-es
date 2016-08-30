<properties
   pageTitle="Funciones de la plantilla del Administrador de recursos | Microsoft Azure"
   description="Describe las funciones que se van a usar en una plantilla del Administrador de recursos de Azure para recuperar valores, trabajar con cadenas y valores numéricos y recuperar información de implementación."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/11/2016"
   ms.author="tomfitz"/>

# Funciones de la plantilla del Administrador de recursos de Azure

Este tema describe todas las funciones que puede utilizar en una plantilla de Azure Resource Manager.

Las funciones de plantilla y sus parámetros no distinguen mayúsculas de minúsculas. Por ejemplo, el Administrador de recursos resuelve **variables('var1')** y **VARIABLES('VAR1')** de la misma manera. Cuando se evalúa, a menos que la función modifique expresamente las mayúsculas (como toUpper o toLower), la función conserva las mayúsculas. Es posible que determinados tipos de recursos tengan requisitos de mayúsculas independientemente de cómo se evalúen las funciones.

## Funciones numéricas

El Administrador de recursos ofrece las siguientes funciones para trabajar con números enteros:

- [agregar](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### agregar

**add(operand1, operand2)**

Devuelve la suma de los dos enteros especificados.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Primer entero que se va a agregar.
| operand2 | Sí | Segundo entero que se va a agregar.

El ejemplo siguiente agrega dos parámetros.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Devuelve el índice actual de un bucle de iteración.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| Offset | No | Cantidad que se va a agregar al valor de la iteración actual.

Esta función siempre se usa con un objeto **copy**. Para ver una descripción completa de cómo usar **copyIndex**, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md).

En el ejemplo siguiente se muestra un bucle de copia y el valor de índice incluido en el nombre.

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />
### div

**div(operand1, operand2)**

Devuelve la división de enteros de los dos enteros especificados.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Entero que se va a dividir.
| operand2 | Sí | Entero que se usa para dividir. No puede ser 0.

En el ejemplo siguiente se divide un parámetro por otro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />
### int

**int(valueToConvert)**

Convierte el valor especificado en entero.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| valueToConvert | Sí | Valor que se convierte en entero. Solo puede ser de tipo cadena o entero.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en entero.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Devuelve el resto de la división de enteros de los dos enteros especificados.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Entero que se va a dividir.
| operand2 | Sí | Entero que se usa para dividir, no puede ser 0.

En el ejemplo siguiente se devuelve el resultado de dividir un parámetro por otro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />
### mul

**mul(operand1, operand2)**

Devuelve la multiplicación de los dos enteros especificados.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Primer entero que se va a multiplicar.
| operand2 | Sí | Segundo entero que se va a multiplicar.

En el ejemplo siguiente se multiplica un parámetro por otro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### sub

**sub(operand1, operand2)**

Devuelve la resta de los dos enteros especificados.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Entero del que se resta.
| operand2 | Sí | Entero que se resta.

En el ejemplo siguiente se resta un parámetro de otro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## Funciones de cadena

El Administrador de recursos ofrece las siguientes funciones para trabajar con cadenas:

- [base64](#base64)
- [concat](#concat)
- [length](#lengthstring)
- [padLeft](#padleft)
- [replace](#replace)
- [skip](#skipstring)
- [split](#split)
- [cadena](#string)
- [substring](#substring)
- [take](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [trim](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)


<a id="base64" />
### base64

**base64 (inputString)**

Devuelve la representación de base64 de la cadena de entrada.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| inputString | Sí | Valor de cadena que se va a devolver como una representación de base64.

En el ejemplo siguiente se muestra cómo utilizar la función de base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat - cadena

**concat (string1, string2, string3...)**

Combina varios valores de cadena y devuelve la cadena concatenada.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| string1 | Sí | Valor de cadena para concatenar.
| cadenas adicionales | No | Valores de cadena para concatenar.

Esta función puede tomar cualquier número de argumentos y puede aceptar cadenas o matrices para los parámetros. Para obtener un ejemplo de concatenación de matrices, consulte [concat - matriz](#concatarray).

En el ejemplo siguiente se muestra cómo combinar varios valores de cadena para devolver un valor concatenado.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### length - cadena

**length(string)**

Devuelve el número de caracteres de una cadena.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| string | Sí | Valor de cadena que se va a utilizar para obtener el número de caracteres.

Para obtener un ejemplo del uso de length con una matriz, consulte [length - matriz](#length).

En el ejemplo siguiente se devuelve el número de caracteres de una cadena.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### padLeft

**padLeft(valueToPad, totalLength, paddingCharacter)**

Devuelve una cadena alineada a la derecha agregando caracteres a la izquierda hasta alcanzar la longitud total especificada.
  
| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| valueToPad | Sí | La cadena o el entero que se va a alinear a la derecha.
| totalLength | Sí | El número total de caracteres de la cadena devuelta.
| paddingCharacter | No | El carácter que se va a usar para el relleno a la izquierda hasta alcanza la longitud total. El valor predeterminado es un espacio.

En el ejemplo siguiente se muestra cómo rellenar el valor del parámetro proporcionado por el usuario agregando el carácter cero hasta que la cadena llegue a 10 caracteres. Si el valor del parámetro original tiene más de 10 caracteres, no se agrega ningún carácter.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

**replace(originalString, oldCharacter, newCharacter)**

Devuelve una nueva cadena con todas las instancias de un carácter de la cadena especificada sustituidas por otro carácter.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| originalString | Sí | Cadena que tiene todas las instancias de un carácter sustituido por otro carácter.
| oldCharacter | Sí | El carácter que se va a quitar de la cadena original.
| newCharacter | Sí | El carácter que se va a agregar en lugar del carácter eliminado.

En el ejemplo siguiente se muestra cómo quitar todos los guiones de la cadena proporcionada por el usuario.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### skip - cadena
**skip(originalValue, numberToSkip)**

Devuelve una cadena con todos los caracteres después del número especificado en la cadena.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| originalValue | Sí | Cadena que se usará para la omisión.
| numberToSkip | Sí | Número de caracteres que se van a omitir. Si este valor es 0 o un valor inferior, se devuelven todos los caracteres de la cadena. Si es mayor que la longitud de la cadena, se devuelve una cadena vacía. 

Para obtener un ejemplo del uso de skip con una matriz, consulte [skip - matriz](#skip).

En el ejemplo siguiente se omite el número especificado de caracteres de la matriz.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />
### split

**split(inputString, delimiterString)**

**split(inputString, delimiterArray)**

Devuelve una matriz de cadenas que contiene las subcadenas de la cadena de entrada que están delimitadas por los delimitadores especificados.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| inputString | Sí | La cadena que se va a dividir.
| delimiter | Sí | Delimitador que se va a usar, puede ser una cadena o una matriz de cadenas.

En el ejemplo siguiente la cadena de entrada se divide con una coma.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

En el ejemplo siguiente se divide la cadena de entrada con una coma o un punto y coma.

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />
### string

**string(valueToConvert)**

Convierte el valor especificado en cadena.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| valueToConvert | Sí | El valor que se convierte en cadena. Se puede convertir cualquier tipo de valor, incluidos objetos y matrices.

En el siguiente ejemplo los valores del parámetro proporcionados por el usuario se convierten en cadenas.

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

Devuelve una subcadena que empieza en la posición de carácter especificada y que contiene el número especificado de caracteres.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| stringToParse | Sí | La cadena original desde la que se extrae la subcadena.
| startIndex | No | La posición de carácter inicial basado en cero de la subcadena.
| length | No | El número de caracteres de la subcadena.

En el ejemplo siguiente se extraen los tres primeros caracteres de un parámetro.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### take - cadena
**take(originalValue, numberToTake)**

Devuelve una cadena con el número especificado de caracteres desde el principio de la cadena.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| originalValue | Sí | Cadena de la que se van a tomar los caracteres.
| numberToTake | Sí | Número de caracteres que se van a tomar. Si este valor es 0 o un valor inferior, se devolverá una cadena vacía. Si es mayor que la longitud de la cadena especificada, se devuelven todos los caracteres de la cadena.

Para obtener un ejemplo del uso de take con una matriz, consulte [take - matriz](#take).

En el ejemplo siguiente se toma el número especificado de caracteres de la cadena.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />
### toLower

**toLower(stringToChange)**

Convierte la cadena especificada a minúsculas.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| stringToChange | Sí | La cadena que se va a convertir a minúsculas.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario a minúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(stringToChange)**

Convierte la cadena especificada a mayúsculas.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| stringToChange | Sí | La cadena que se va a convertir a mayúsculas.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario a mayúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### trim

**trim (stringToTrim)**

Quita todos los caracteres de espacio en blanco iniciales y finales de la cadena especificada.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| stringToTrim | Sí | La cadena que se recortará.

En el ejemplo siguiente se recortan los caracteres de espacio en blanco del valor de parámetro proporcionado por el usuario.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (baseString...)**

Crea una cadena única basada en los valores proporcionados como parámetros.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| baseString | Sí | Cadena utilizada en la función hash para crear una cadena única.
| parámetros adicionales según sea necesario | No | Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad.

Esta función es útil cuando se debe crear un nombre único para un recurso. Proporciona valores de parámetros que representan el nivel de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación.

El valor devuelto no es una cadena aleatoria, sino que es el resultado de una función hash. El valor devuelto tiene 13 caracteres. No se garantiza que sea único global. Puede que desee combinar el valor con un prefijo de su convención de nomenclatura para crear un nombre que sea más fácil de reconocer. En el ejemplo siguiente se muestra el formato del valor devuelto. Por supuesto, el valor real variará en función de los parámetros proporcionados.

    tcvhiyu5h2o5o

En los ejemplos siguientes se muestra cómo utilizar uniqueString a fin de crear un valor único para niveles de uso común.

Único basado en la suscripción

    "[uniqueString(subscription().subscriptionId)]"

Único basado en el grupo de recursos

    "[uniqueString(resourceGroup().id)]"

Único basado en la implementación de un grupo de recursos

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
En el ejemplo siguiente se muestra cómo crear un nombre único para una cuenta de almacenamiento basada en el grupo de recursos.

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

Crea un URI absoluto mediante la combinación de la cadena de relativeUri y baseUri.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| baseUri | Sí | La cadena de uri base.
| relativeUri | Sí | La cadena de uri relativo que se agregará a la cadena de uri base.

El valor del parámetro **baseUri** puede incluir un archivo específico, pero al construir el identificador URI, solo se usa la ruta de acceso base. Por ejemplo, al pasar **http://contoso.com/resources/azuredeploy.json** como parámetro baseUri, se obtiene como resultado un identificador URI base de **http://contoso.com/resources/**.

En el ejemplo siguiente se muestra cómo construir un vínculo a una plantilla anidada en función del valor de la plantilla principal.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Funciones de matriz

El Administrador de recursos ofrece varias funciones para trabajar con valores de matriz:

- [concat](#concatarray)
- [length](#length)
- [skip](#skip)
- [take](#take)

Para obtener una matriz de valores de cadena delimitada por un valor, consulte [split](#split).

<a id="concatarray" />
### concat - matriz

**concat (array1, array2, array3...)**

Combina varias matrices y devuelve la matriz concatenada.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| array1 | Sí | Matriz que se va a concatenar.
| matrices adicionales | No | Matrices que se van a concatenar.

Esta función puede tomar cualquier número de argumentos y puede aceptar cadenas o matrices para los parámetros. Para obtener un ejemplo de concatenación de valores de cadena, consulte [concat - cadena](#concat).

En el ejemplo siguiente se muestra cómo combinar dos matrices.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### length - matriz

**length(array)**

Devuelve el número de elementos de una matriz.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| array | Sí | Matriz que se puede usar para obtener el número de elementos.

Puede usar esta función con una matriz para especificar el número de iteraciones al crear recursos. En el ejemplo siguiente, el parámetro **siteNames** debería hacer referencia a una matriz de nombres que se usará al crear los sitios web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Para más información sobre cómo usar esta función con una matriz, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

Para obtener un ejemplo del uso de length con un valor de cadena, consulte [length - cadena](#lengthstring).

<a id="skip" />
### skip - matriz
**skip(originalValue, numberToSkip)**

Devuelve una matriz con todos los elementos después del número especificado en la matriz.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| originalValue | Sí | Matriz que se usará para la omisión.
| numberToSkip | Sí | Número de elementos que se van a omitir. Si este valor es 0 o un valor inferior, se devolverán todos los elementos de la matriz. Si es mayor que la longitud de la matriz, se devuelve una matriz vacía. 

Para obtener un ejemplo del uso de skip con una cadena, consulte [skip - cadena](#skipstring).

En el ejemplo siguiente se omite el número especificado de elementos de la matriz.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />
### take - matriz
**take(originalValue, numberToTake)**

Devuelve una matriz con el número especificado de elementos desde el principio de la matriz.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| originalValue | Sí | Matriz de la que se van a tomar los elementos.
| numberToTake | Sí | El número de elementos que se van a tomar. Si este valor es 0 o un valor inferior, se devolverá una matriz vacía. Si es mayor que la longitud de la matriz especificada, se devuelven todos los elementos de la matriz.

Para obtener un ejemplo del uso de take con una cadena, consulte [take - cadena](#takestring).

En el ejemplo siguiente se toma el número especificado de elementos de la matriz.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## Funciones con valores de implementación

El Administrador de recursos ofrece las siguientes funciones para obtener valores de las secciones de la plantilla y valores relacionados con la implementación:

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Para obtener valores de recursos, grupos de recursos o suscripciones, consulte [Funciones de recursos](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Devuelve información sobre la operación de implementación actual.

Esta función devuelve el objeto pasado durante la implementación. Las propiedades del objeto devuelto varían en función de si el objeto de implementación se ha pasado como un vínculo o como un objeto en línea.

Cuando se pasa el objeto de implementación en línea, como cuando se usa el parámetro **-TemplateFile** en Azure PowerShell para orientarlo a un archivo local, el objeto devuelto tiene el formato siguiente:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Cuando el objeto se pasa como un vínculo, como cuando se usa el parámetro **-TemplateUri** para orientarlo a un objeto remoto, se devuelve el objeto en el formato siguiente.

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

En el ejemplo siguiente se muestra cómo usar deployment() para establecer un vínculo con otra plantilla basada en el identificador URI de la plantilla principal.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### parameters

**parámetros (parameterName)**

Devuelve un valor de parámetro. El nombre del parámetro especificado debe definirse en la sección de parámetros de la plantilla.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| parameterName | Sí | El nombre del parámetro que se va a devolver.

En el ejemplo siguiente se muestra un uso simplificado de la función de los parámetros.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### variables

**variables (variableName)**

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| variable Name | Sí | El nombre de la variable que se va a devolver.

En el ejemplo siguiente se utiliza un valor de variable.

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## Funciones de recursos

El Administrador de recursos ofrece las siguientes funciones para obtener valores de recursos:

- [listKeys y list{Value}](#listkeys)
- [providers](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [suscripción](#subscription)

Para obtener valores de parámetro, variables o la implementación actual, consulte [Funciones con valores de implementación](#deployment-value-functions).

<a id="listkeys" /> <a id="list" />
### listKeys y list{Value}

**listKeys (resourceName o resourceIdentifier, apiVersion)**

**list{Value} (resourceName o resourceIdentifier, apiVersion)**

Devuelve los valores para cualquier tipo de recurso que admite la operación list. El uso más común es **listKeys**.
  
| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Identificador único para el recurso.
| apiVersion | Sí | Versión de API de estado en tiempo de ejecución de un recurso.

Cualquier operación que comienza por **list** se puede usar como función en la plantilla. Entre las operaciones disponibles se incluyen no solo **listKeys**, como sino también operaciones como **list**, **listAdminKeys** y **listStatus**. Para determinar qué tipos de recursos tienen una operación de lista, use el siguiente comando de PowerShell.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

O bien, recupere la lista con CLI de Azure. En el ejemplo siguiente se recuperan todas las operaciones de **apiapps** y se usa la utilidad JSON [jq](http://stedolan.github.io/jq/download/) para filtrar las operaciones list.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

El valor de resourceId puede especificarse mediante la [función resourceId](./#resourceid) o mediante el formato **{providerNamespace}/{resourceType}/{resourceName}**.

En el ejemplo siguiente se muestra cómo se devuelven las claves principal y secundaria de una cuenta de almacenamiento en la sección de salidas.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

El objeto devuelto desde listKeys tiene el formato siguiente:

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />
### providers

**providers (providerNamespace, [resourceType])**

Devuelve información acerca de un proveedor de recursos y sus tipos de recursos admitidos. Si no proporciona un tipo de recurso, la función devuelve todos los tipos admitidos para el proveedor de recursos.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| providerNamespace | Sí | Espacio de nombres del proveedor
| resourceType | No | El tipo de recurso en el espacio de nombres especificado.

Se devuelve cada tipo admitido en el formato siguiente. No se garantiza la ordenación de la matriz.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

En el ejemplo siguiente se muestra cómo utilizar la función de proveedor:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Devuelve un objeto que representa el estado de tiempo de ejecución de otro recurso.

| Parámetro | Obligatorio | Description
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Nombre o identificador único de un recurso.
| apiVersion | No | Versión de la API del recurso especificado. Incluya este parámetro cuando el recurso no esté aprovisionado en la misma plantilla.

La función **reference** deriva su valor desde un estado de tiempo de ejecución y, por tanto, no se puede utilizar en la sección de variables. Se puede utilizar en la sección de salidas de una plantilla.

Mediante el uso de la función de referencia, se declara implícitamente que un recurso depende de otro recurso si el recurso al que se hace referencia se aprovisiona en la misma plantilla. No tiene que usar también la propiedad **dependsOn**. La función no se evalúa hasta que el recurso al que se hace referencia complete la implementación.

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento que se implementa en la misma plantilla.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento que no se implementa en esta plantilla, pero que existe dentro del mismo grupo de recursos conforme se implementan los recursos.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
			"type" : "object"
		}
	}

Puede recuperar un valor concreto del objeto devuelto, como el URI del punto de conexión del blob, como se muestra en el ejemplo siguiente.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento en otro grupo de recursos.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Las propiedades en el objeto devuelto varían según el tipo de recurso.

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Devuelve un objeto que representa el grupo de recursos actual.

El objeto devuelto está en el formato siguiente:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

En el ejemplo siguiente se utiliza la ubicación del grupo de recursos para asignar la ubicación de un sitio web.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Devuelve el identificador único de un recurso.
      
| Parámetro | Obligatorio | Description
| :---------------: | :------: | :----------
| subscriptionId | No | El valor predeterminado es la suscripción actual. Especifique este valor cuando necesite recuperar un recurso en otra suscripción.
| resourceGroupName | No | El valor predeterminado es el grupo de recursos actual. Especifique este valor cuando necesite recuperar un recurso en otro grupo de recursos.
| resourceType | Sí | Tipo de recurso, incluido el espacio de nombres del proveedor de recursos.
| resourceName1 | Sí | Nombre del recurso.
| resourceName2 | No | Siguiente segmento de nombre de recurso si el recurso está anidado.

Utilice esta función cuando el nombre del recurso sea ambiguo o no esté aprovisionado dentro de la misma plantilla. El identificador se devuelve con el formato siguiente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

En el ejemplo siguiente se muestra cómo recuperar los identificadores de recursos para un sitio web y una base de datos. El sitio web existe en un grupo de recursos denominado **myWebsitesGroup** y la base de datos existe en el grupo de recursos actual para esta plantilla.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
A menudo, necesitará utilizar esta función cuando se usa una cuenta de almacenamiento o red virtual en un grupo de recursos alternativo. La cuenta de almacenamiento o la red virtual puede utilizarse en varios grupos de recursos; por lo tanto, no es deseable su eliminación cuando se elimina un único grupo de recursos. En el ejemplo siguiente se muestra cómo un recurso de un grupo de recursos externos se puede utilizar fácilmente:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### suscripción

**subscription()**

Devuelve detalles acerca de la suscripción en el formato siguiente.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

En el ejemplo siguiente se muestra la función de suscripción a la que se llama en la sección de salidas.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Pasos siguientes
- Para obtener una descripción de las secciones de una plantilla del Administrador de recursos de Azure, vea [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para combinar varias plantillas, vea [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).
- Para iterar una cantidad de veces determinada al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
- Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md)

<!---HONumber=AcomDC_0817_2016-->