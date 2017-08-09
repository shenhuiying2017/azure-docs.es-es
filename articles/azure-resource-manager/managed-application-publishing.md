---
title: "Creación y publicación de una aplicación administrada de Azure | Microsoft Docs"
description: "Muestra cómo un ISV o asociado crea una aplicación administrada de Azure"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 623d728cf70a5aa0b91b6acd9e314ca5c009c0aa
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="create-and-publish-service-catalog-managed-application"></a>Creación y publicación de una aplicación administrada del catálogo de servicios

Tal y como se describe en el artículo de [introducción a las aplicaciones administradas](managed-application-overview.md), hay dos escenarios en la experiencia completa. Uno es el publicador o el ISV que desea crear una aplicación administrada para que la utilicen los usuarios. El segundo es el usuario o el consumidor de la aplicación administrada. Este artículo se centra en el primer escenario y explica cómo un ISV puede crear y publicar una aplicación administrada. 

Para crear una aplicación administrada, debe crear:

* un paquete que contiene los archivos de plantilla
* el usuario, grupo o aplicación que tiene acceso al grupo de recursos en la suscripción del cliente
* la definición de aplicación

Para ver ejemplos de los archivos, consulte los [ejemplos de aplicaciones administradas](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).

## <a name="create-managed-application-package"></a>Creación de un paquete de aplicación administrada

El primer paso es crear el paquete de aplicación administrada que contiene los archivos de plantilla principales. El publicador o el ISV crean tres archivos. Debe empaquetar los tres archivos en un archivo .zip y cargarlo en una ubicación accesible.

* El primer archivo se denomina **applianceMainTemplate.json**. Este archivo de plantilla define los recursos reales que se aprovisionan como parte de la aplicación administrada. Por ejemplo, para crear una cuenta de almacenamiento con una aplicación administrada, el archivo applianceMainTemplate.json contiene: 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {       
          }
      }],
      "outputs": {      
      }
  }
  ```

* El segundo archivo que debe crear el publicador es **mainTemplate.json**. El archivo de plantilla solo contiene el recurso de aplicación (Microsoft.Solutions/appliances). También contiene todos los parámetros que son necesarios para los recursos en applianceMainTemplate.json. 

  Hay dos propiedades importantes que son necesarias como entrada durante la creación de la aplicación administrada. La propiedad **managedResourceGroupId** es el identificador del grupo de recursos donde se crean los recursos definidos en applianceMainTemplate.json. El formato del identificador es: `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  La propiedad **applianceDefinitionId** es el identificador del recurso de definición de aplicación administrada. El identificador está en el formato: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  Proporcione parámetros para ambos valores, de modo que el consumidor pueda especificarlos al crear una aplicación administrada. En el ejemplo siguiente, los dos parámetros que corresponden a estas propiedades son managedByResourceGroup y applianceDefinitonId.

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }             
              }
          }
      }]
  }
  ```

* El tercer archivo necesario en el paquete es **createUiDefinition.json**. Azure Portal usa este archivo para generar la interfaz de usuario para los consumidores que crean la aplicación administrada. Defina los parámetros para la aplicación administrada y cómo los consumidores pueden obtener la entrada para cada parámetro. Puede usar opciones como un selector desplegable, un cuadro de texto, un cuadro de contraseña y otras herramientas de entrada. Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).

Una vez que estén listos todos los archivos necesarios, cargue el paquete en una ubicación accesible desde donde pueda consumirse.


## <a name="create-azure-ad-user-group-or-application"></a>Creación de un grupo de usuarios o una aplicación de Azure AD
A continuación, cree un grupo de usuarios o una aplicación que desee usar para administrar los recursos en nombre del cliente. Este grupo de usuarios o esta aplicación tiene permisos en el grupo de recursos administrado, según la descripción del rol. El rol podría ser cualquier rol RBAC integrado, como **Propietario** o **Colaborador**. También se puede conceder a un usuario individual permisos para administrar los recursos, pero se suele asignar este permiso al uso de un grupo de usuarios. Para crear un grupo de usuarios de Active Directory, use:

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

También puede usar un grupo existente. Se necesita el identificador de objeto del grupo de usuarios existente o recién creado. En el ejemplo siguiente se muestra cómo obtener el identificador de objeto a partir del nombre para mostrar que se usó para crear el grupo.

```azurecli
az ad group show --group "groupName"
```

Ejemplo:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

Que devuelve la siguiente salida:

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
Se necesita el valor de objectId de arriba. 

## <a name="get-the-role-definition-id"></a>Obtención del identificador de definición de rol

A continuación, necesita el identificador de definición de rol para el rol RBAC integrado que desea para conceder acceso al usuario, al grupo de usuarios o a la aplicación. Por lo general, le interesaría el rol "Propietario", "Colaborador" o "Lector". El comando siguiente muestra cómo obtener el identificador de definición de rol para el rol "Propietario":

```azurecli
az role definition list --name owner
```

Que devuelve la siguiente salida:

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

Se necesita el valor de la propiedad "name" del ejemplo anterior.


## <a name="create-the-managed-application-definition"></a>Creación de la definición de aplicación administrada

El último paso es crear el recurso de definición de la aplicación administrada. Una vez que haya creado el paquete de aplicación y las autorizaciones, cree la definición de aplicación mediante el comando siguiente: 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

Los parámetros que se usan en el ejemplo anterior son:

- resource-group: el nombre del grupo de recursos donde se crea la definición de la aplicación.
- lock-level: el tipo de bloqueo se aplica al grupo de recursos administrado. Impide que el cliente realice operaciones no deseadas en este grupo de recursos. Actualmente, **ReadOnly** es el único nivel de bloqueo admitido. Cuando se especifica ReadOnly, el cliente solo puede leer los recursos presentes en el grupo de recursos administrado.
- authorizations: describe el identificador de entidad de seguridad y el identificador de definición de rol que se usan para conceder el permiso al grupo de recursos administrado. Se especifica en el formato `<principalId>:<roleDefinitionId>`. También se pueden especificar varios valores para esta propiedad. Si se necesitan varios valores, se deben especificar de esta forma `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Los valores van separados por un espacio.
- package-file-uri: la ubicación del paquete de aplicación que contiene los archivos de plantilla, que puede ser una instancia de Azure Storage Blob. 


## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](managed-application-overview.md).
* Para ver ejemplos de los archivos, consulte los [ejemplos de aplicaciones administradas](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
* Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
* Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).

