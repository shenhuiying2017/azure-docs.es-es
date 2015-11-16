<properties
   pageTitle="Funciones de la plantilla del Administrador de recursos | Microsoft Azure"
   description="Describe las funciones que se van a usar en una plantilla del Administrador de recursos de Azure para recuperar valores, trabajar con cadenas y valores numéricos y recuperar información de implementación."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/02/2015"
   ms.author="tomfitz"/>

# Funciones de la plantilla del Administrador de recursos de Azure

Este tema describe todas las funciones que puede utilizar en una plantilla del Administrador de recursos de Azure.

Las funciones de plantilla y sus parámetros no distinguen mayúsculas de minúsculas. Por ejemplo, el Administrador de recursos resuelve **variables('var1')** y **VARIABLES('VAR1')** de la misma manera.

## agregar

**add(operand1, operand2)**

Devuelve la suma de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Primer operando que se va a usar.
| operand2 | Sí | Segundo operando que se va a usar.


## base64

**base64 (inputString)**

Devuelve la representación de base64 de la cadena de entrada.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| inputString | Sí | Valor de cadena que se va a devolver como una representación de base64.

En el ejemplo siguiente se muestra cómo utilizar la función de base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat (arg1, arg2, arg3, ...)**

Combina varios valores de cadena y devuelve el valor de cadena resultante. Esta función puede tomar cualquier número de argumentos.

En el ejemplo siguiente se muestra cómo combinar varios valores para devolver un valor.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## copyIndex

**copyIndex(offset)**

Devuelve el índice actual de un bucle de iteración. Para obtener ejemplos de cómo usar esta función, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

## deployment

**deployment()**

Devuelve información sobre la operación de implementación actual.

La información sobre la implementación se devuelve como un objeto con las siguientes propiedades:

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

En el ejemplo siguiente se muestra cómo se devuelve la información de implementación en la sección de salidas.

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## div

**div(operand1, operand2)**

Devuelve la división de enteros de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Número que se va a dividir.
| operand2 | Sí | Número que se usa para dividir, tiene que ser distinto de 0.

## int

**int(valueToConvert)**

Convierte el valor especificado en entero.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| valueToConvert | Sí | Valor que se convierte en entero. Solo puede ser de tipo cadena o entero.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en entero.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }

## length

**length(array)**

Devuelve el número de elementos de una matriz. Normalmente, sirve para especificar el número de iteraciones al crear recursos. Para obtener un ejemplo de cómo usar esta función, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

## listKeys

**listKeys (resourceName o resourceIdentifier, apiVersion)**

Devuelve las claves de una cuenta de almacenamiento. El valor de resourceId puede especificarse mediante la [función resourceId](./#resourceid) o mediante el formato **providerNamespace/resourceType/resourceName**. Puede utilizar la función para obtener los valores de primaryKey y secondaryKey.
  
| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Identificador único de una cuenta de almacenamiento.
| apiVersion | Sí | Versión de API de estado en tiempo de ejecución de un recurso.

En el ejemplo siguiente se muestra cómo se devuelven las claves de una cuenta de almacenamiento en la sección de salidas.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

## mod

**mod(operand1, operand2)**

Devuelve el resto de la división de enteros de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Número que se va a dividir.
| operand2 | Sí | Número que se usa para dividir, tiene que ser distinto de 0.


## mul

**mul(operand1, operand2)**

Devuelve la multiplicación de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Primer operando que se va a usar.
| operand2 | Sí | Segundo operando que se va a usar.


## padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

Devuelve una cadena alineada a la derecha agregando caracteres a la izquierda hasta alcanzar la longitud total especificada.
  
| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToPad | Sí | La cadena que se va a alinear a la derecha.
| totalLength | Sí | El número total de caracteres de la cadena devuelta.
| paddingCharacter | Sí | El carácter que se va a usar para el relleno a la izquierda hasta alcanza la longitud total.

En el ejemplo siguiente se muestra cómo rellenar el valor del parámetro proporcionado por el usuario agregando el carácter cero hasta que la cadena llegue a 10 caracteres. Si el valor del parámetro original tiene más de 10 caracteres, no se agrega ningún carácter.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }


## parameters

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

## providers

**providers (providerNamespace, [resourceType])**

Se devuelve información acerca de un proveedor de recursos y sus tipos de recursos admitidos. Si no se proporciona el tipo, se devuelven todos los tipos admitidos.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| providerNamespace | Sí | Espacio de nombres del proveedor
| resourceType | No | El tipo de recurso en el espacio de nombres especificado.

Se devuelve cada tipo admitido en el formato siguiente:

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

## reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Permite que una expresión derive su valor del estado de tiempo de ejecución de otro recurso.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Nombre o identificador único de un recurso.
| apiVersion | No | Versión de API de estado en tiempo de ejecución de un recurso. El parámetro debe utilizarse si el recurso no se ha aprovisionado en la misma plantilla.

La función **reference** deriva su valor desde un estado de tiempo de ejecución y, por tanto, no se puede utilizar en la sección de variables. Se puede utilizar en la sección de salidas de una plantilla.

Mediante el uso de la expresión de referencia, se declara implícitamente que un recurso depende de otro recurso si el recurso al que se hace referencia se aprovisiona en la misma plantilla. No tiene que usar también la propiedad **dependsOn**. La expresión no se evalúa hasta que el recurso al que se hace referencia complete la implementación.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

**replace(originalString, oldCharacter, newCharacter)**

Devuelve una nueva cadena con todas las instancias de un carácter de la cadena especificada sustituidas por otro carácter.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| originalString | Sí | La cadena que tendrá todas las instancias de un carácter sustituido por otro carácter.
| oldCharacter | Sí | El carácter que se va a quitar de la cadena original.
| newCharacter | Sí | El carácter que se va a agregar en lugar del carácter eliminado.

En el ejemplo siguiente se muestra cómo quitar todos los guiones de la cadena proporcionada por el usuario.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

## resourceGroup

**resourceGroup()**

Devuelve un objeto estructurado que representa el grupo de recursos actual. El objeto tendrá el siguiente formato:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
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

## resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Devuelve el identificador único de un recurso. Utilice esta función cuando el nombre del recurso sea ambiguo o no esté aprovisionado dentro de la misma plantilla. El identificador se devuelve con el formato siguiente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parámetro | Obligatorio | Descripción
| :---------------: | :------: | :----------
| resourceGroupName | No | Nombre del grupo de recursos opcional. El valor predeterminado es el grupo de recursos actual. Especifique este valor cuando se recupere un recurso en otro grupo de recursos.
| resourceType | Sí | Tipo de recurso, incluido el espacio de nombres del proveedor de recursos.
| resourceName1 | Sí | Nombre del recurso.
| resourceName2 | No | Siguiente segmento de nombre de recurso si el recurso está anidado.

En el ejemplo siguiente se muestra cómo recuperar los identificadores de recursos para un sitio web y una base de datos. El sitio web existe en un grupo de recursos denominado **myWebsitesGroup** y la base de datos existe en el grupo de recursos actual para esta plantilla.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
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

## split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

Devuelve una matriz de cadenas que contiene las subcadenas de la cadena de entrada que están delimitadas por los delimitadores enviados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| inputString | Sí | Cadena que se va a dividir.
| delimiter | Sí | Delimitador que se va a usar, puede ser una cadena o una matriz de cadenas.

En el ejemplo siguiente la cadena de entrada se divide con una coma.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

## cadena

**string(valueToConvert)**

Convierte el valor especificado en cadena.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| valueToConvert | Sí | Valor que se convierte en cadena. Solo puede ser de tipo booleano, entero o cadena.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en cadena.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

## sub

**sub(operand1, operand2)**

Devuelve la resta de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Número del que se va a restar.
| operand2 | Sí | Número que se va a restar.


## subscription

**subscription()**

Devuelve detalles acerca de la suscripción en el formato siguiente.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

En el ejemplo siguiente se muestra la función de suscripción a la que se llama en la sección de salidas.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## toLower

**toLower(stringToChange)**

Convierte la cadena especificada a minúsculas.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToChange | Sí | La cadena que se va a convertir a minúsculas.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario a minúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

## toUpper

**toUpper(stringToChange)**

Convierte la cadena especificada a mayúsculas.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToChange | Sí | La cadena que se va a convertir a mayúsculas.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario a mayúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }


## uniqueString

**uniqueString (stringForCreatingUniqueString,...)**

Realiza un hash de 64 bits de las cadenas proporcionadas para crear una cadena única. Esta función es útil cuando se debe crear un nombre único para un recurso. Proporciona valores de parámetros que representan el nivel de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Sí | Cadena base utilizada en la función hash para crear una cadena única.
| parámetros adicionales según sea necesario | No | Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad.

El valor devuelto no es una cadena completamente aleatoria, sino que es el resultado de una función hash. El valor devuelto tiene 13 caracteres. No se garantiza que sea único global. Puede que desee combinar el valor con un prefijo de su convención de nomenclatura para crear un nombre más descriptivo.

En los ejemplos siguientes se muestra cómo utilizar uniqueString para crear un valor único para diferentes niveles de uso común.

Único basado en la suscripción

    "[uniqueString(subscription().subscriptionId)]"

Único basado en el grupo de recursos

    "[uniqueString(resourceGroup().id)]"

Único basado en la implementación de un grupo de recursos

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
En el ejemplo siguiente se muestra cómo crear un nombre único para una cuenta de almacenamiento basada en el grupo de recursos.

    "resources": [{ 
        "name": "[concat('ContosoStorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...


## variables

**variables (variableName)**

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| variable Name | Sí | El nombre de la variable que se va a devolver.


## Pasos siguientes
- Para obtener una descripción de las secciones de una plantilla del Administrador de recursos de Azure, vea [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para combinar varias plantillas, vea [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).
- Para iterar una cantidad de veces específica al crear un tipo de recurso, consulte [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
- Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!---HONumber=Nov15_HO2-->