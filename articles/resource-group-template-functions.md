<properties
   pageTitle="Funciones de la plantilla del Administrador de recursos de Azure"
   description="Describe las funciones que se van a utilizar en una plantilla del Administrador de recursos de Azure para implementar aplicaciones en Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz, ilygre"/>

# Funciones de la plantilla del Administrador de recursos de Azure

Este tema describe todas las funciones que puede utilizar en una plantilla del Administrador de recursos de Azure.

## base64

**base64 \(inputString\)**

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

**concat \(arg1, arg2, arg3, ...\)**

Combina varios valores de cadena y devuelve el valor de cadena resultante. Esta función puede tomar cualquier número de argumentos.

En el ejemplo siguiente se muestra cómo combinar varios valores para devolver un valor.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys \(resourceName o resourceIdentifier, [apiVersion]\)**

Devuelve las claves de una cuenta de almacenamiento. El valor de resourceId puede especificarse mediante la [función resourceId](./#resourceid) o mediante el formato **providerNamespace/resourceType/resourceName**. Puede utilizar la función para obtener los valores de primaryKey y secondaryKey.
  
| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Identificador único de una cuenta de almacenamiento.
| apiVersion | Sí | Versión de API de estado en tiempo de ejecución de un recurso.

En el ejemplo siguiente se muestra cómo se devuelven las claves de una cuenta de almacenamiento en la sección de salidas.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## parameters

**parámetros \(parameterName\)**

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

## provider

**proveedor \(providerNamespace, [resourceType]\)**

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

**referencia \(resourceName o resourceIdentifier, [apiVersion]\)**

Permite que una expresión derive su valor del estado de tiempo de ejecución de otro recurso.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Nombre o identificador único de un recurso.
| apiVersion | No | Versión de API de estado en tiempo de ejecución de un recurso. El parámetro debe utilizarse si el recurso no se ha aprovisionado en la misma plantilla.

La función **reference** deriva su valor desde un estado de tiempo de ejecución y, por tanto, no se puede utilizar en la sección de variables. Se puede utilizar en la sección de salidas de una plantilla.

Mediante el uso de la expresión de referencia, se declara que un recurso depende de otro recurso si el recurso al que se hace referencia se aprovisiona en la misma plantilla.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup\(\)**

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

**resourceId \([resourceGroupName], resourceType, resourceName1, [resourceName2]...\)**

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
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
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


## subscription

**subscription\(\)**

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

## variables

**variables \(variableName\)**

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| variable Name | Sí | El nombre de la variable que se va a devolver.


## Pasos siguientes
- [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md)
- [Operaciones avanzadas de plantilla](./resource-group-advanced-template.md)
- [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](./resouce-group-template-deploy.md)
- [Información general del Administrador de recursos de Azure](./resource-group-overview.md)

<!--HONumber=52-->
