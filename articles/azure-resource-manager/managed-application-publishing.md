---
title: "Creación y publicación de una aplicación administrada del catálogo de servicios de Azure | Microsoft Docs"
description: "Se explica cómo crear una aplicación administrada de Azure diseñada para los miembros de su organización."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 39b74984ec2f89ed39753963de7fe3ff79577c9e
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicación de una aplicación administrada para consumo interno

Puede crear y publicar [aplicaciones administradas](managed-application-overview.md) de Azure que están diseñadas para los miembros de su organización. Por ejemplo, un departamento de TI puede publicar aplicaciones administradas que garantizan el cumplimiento de los estándares de la organización. Estas aplicaciones administradas están disponibles a través del catálogo de servicios y no en Azure Marketplace.

Para publicar una aplicación administrada en el catálogo de servicios:

* Cree un paquete .zip que contenga los tres archivos de plantilla necesarios.
* Decida qué usuario, grupo o aplicación necesita acceder al grupo de recursos en la suscripción del usuario.
* Cree la definición de aplicación administrada que apunta al paquete .zip y solicita acceso para la identidad.

## <a name="create-a-managed-application-package"></a>Creación de un paquete de aplicación administrada

El primer paso es crear los tres archivos de plantilla necesarios. Empaquete los tres archivos en un archivo ZIP y cárguelo en una ubicación accesible, como una cuenta de almacenamiento. Pase un vínculo a este archivo ZIP al crear la definición de aplicación administrada.

* **applianceMainTemplate.json**: este archivo define los recursos de Azure que se aprovisionan como parte de la aplicación administrada. La plantilla no difiere de una plantilla habitual de Resource Manager. Por ejemplo, para crear una cuenta de almacenamiento con una aplicación administrada, el archivo applianceMainTemplate.json contiene:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **mainTemplate.json**: los usuarios implementan esta plantilla al crear la aplicación administrada. Define el recurso de la aplicación administrada, que es un tipo de recurso Microsoft.Solutions/appliances. Este archivo contiene todos los parámetros que necesita para los recursos en applianceMainTemplate.json.

  Establezca dos propiedades importantes en esta plantilla. En primer lugar, la propiedad **applianceDefinitionId** es el identificador de la definición de aplicación administrada. Cree la definición más adelante en este tema. Al configurar este valor, debe decidir qué suscripción y grupo de recursos usar para almacenar las definiciones de aplicación administrada. Además, debe decidir un nombre para la definición. El identificador tiene el formato:

  `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Solutions/applianceDefinitions/<definition-name>`

  En segundo lugar, la propiedad **managedResourceGroupId** es el identificador del grupo de recursos donde se crean los recursos de Azure. Puede asignar un valor para este nombre de grupo de recursos o dejar que el usuario facilite un nombre. El formato del identificador es:

  `/subscriptions/<subscription-id>/resourceGroups/<resoure-group-name>`.

  En el ejemplo siguiente se muestra un archivo mainTemplate.json. Especifica un grupo de recursos para los recursos implementados. El identificador de la definición está establecido para usar una definición con nombre **storageApp** en un grupo de recursos denominado **managedApplicationGroup**. Puede cambiar estos valores para usar nombres distintos. Proporcione su propio identificador de suscripción en el identificador de definición.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "variables": {
        "managedRGId": "[concat(resourceGroup().id,'-application-resources')]",
        "managedAppName": "[concat('managedStorage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/appliances",
            "name": "[variables('managedAppName')]",
            "apiVersion": "2016-09-01-preview",
            "location": "[resourceGroup().location]",
            "kind": "ServiceCatalog",
            "properties": {
                "managedResourceGroupId": "[variables('managedRGId')]",
                "applianceDefinitionId": "/subscriptions/<subscription-id>/resourceGroups/managedApplicationGroup/providers/Microsoft.Solutions/applianceDefinitions/storageApp",
                "parameters": {
                    "storageAccountNamePrefix": {
                        "value": "[parameters('storageAccountNamePrefix')]"
                    }
                }
            }
        }
    ]
  }
  ```

* **applianceCreateUiDefinition.json**: Azure Portal usa este archivo para generar la interfaz de usuario para los usuarios que crean la aplicación administrada. Defina cómo los usuarios proporcionan la entrad de cada parámetro. Puede usar opciones como una lista desplegable, un cuadro de texto, un cuadro de contraseña y otras herramientas de entrada. Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).

  En el ejemplo siguiente se muestra un archivo applianceCreateUiDefinition.json que permite a los usuarios especificar el prefijo del nombre de la cuenta de almacenamiento a través de un cuadro de texto.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

Después de que todos los archivos necesarios estén listos, empaquételos como un archivo ZIP. Los tres archivos deben estar en el nivel raíz del archivo ZIP. Si los coloca en una carpeta, recibe un error al crear la definición de aplicación administrada que indica que los archivos requeridos no están presentes. Cargue el paquete en una ubicación accesible desde donde pueda consumirse. En el resto de este artículo se asume que el archivo ZIP se encuentra en un contenedor de blobs de almacenamiento de acceso público.

## <a name="create-an-azure-active-directory-user-group-or-application"></a>Creación de una aplicación o grupo de usuarios de Azure Active Directory

El segundo paso consiste en seleccionar una aplicación o un grupo de usuarios para administrar los recursos en nombre del cliente. Este grupo de usuarios o esta aplicación tienen permisos en el grupo de recursos administrado, según el rol asignado. El rol puede ser cualquier rol de Control de acceso basado en roles (RBAC) integrado como propietario o colaborador. También puede conceder a un usuario individual permisos para administrar los recursos, pero este permiso se suele asignar a un grupo de usuarios. Para crear un grupo de usuarios de Active Directory, consulte [Creación de un grupo y adición de miembros en Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Necesita el identificador de objeto del grupo de usuarios que se usará para administrar los recursos. En el ejemplo siguiente se muestra cómo obtener el identificador de objeto a partir del nombre para mostrar del grupo:

```azurecli-interactive
az ad group show --group exampleGroupName
```

El comando de ejemplo devuelve el siguiente resultado:

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

Para recuperar solo el identificador de objeto, use:

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

## <a name="get-the-role-definition-id"></a>Obtención del identificador de definición de rol

A continuación, necesita el identificador de definición de rol para el rol RBAC integrado que desea para conceder acceso al usuario, al grupo de usuarios o a la aplicación. Por lo general, se usa el rol Propietario, Colaborador o Lector. El comando siguiente muestra cómo obtener el identificador de definición de rol para el rol Propietario:


```azurecli-interactive
az role definition list --name owner
```

El comando devuelve el siguiente resultado:

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

Se necesita el valor de la propiedad "name" del ejemplo anterior. Puede recuperar solo esa propiedad con:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

## <a name="create-the-managed-application-definition"></a>Creación de la definición de aplicación administrada

Si ya no dispone de un grupo de recursos para almacenar la definición de aplicación administrada, créelo ahora:

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

Ahora, cree el recurso de la definición de aplicación administrada.

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

Los parámetros que se usan en el ejemplo anterior son:

* **resource-group**: el nombre del grupo de recursos donde se creó la definición de aplicación administrada.
* **lock-level**: el tipo de bloqueo aplicado al grupo de recursos administrado. Impide que el cliente realice operaciones no deseadas en este grupo de recursos. Actualmente, ReadOnly es el único nivel de bloqueo admitido. Cuando se especifica ReadOnly, el cliente solo puede leer los recursos presentes en el grupo de recursos administrado.
* **authorizations**: describe el identificador de entidad de seguridad y el identificador de definición de rol que se usan para conceder el permiso al grupo de recursos administrado. Se especifica en el formato `<principalId>:<roleDefinitionId>`. También se pueden especificar varios valores para esta propiedad. Si se necesitan varios valores, se deben especificar de esta forma `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Los valores van separados por un espacio.
* **package-file-uri**: la ubicación del paquete de aplicación administrada que contiene los archivos de plantilla, que puede ser una instancia de Azure Storage Blob.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](managed-application-overview.md).
* Para ver ejemplos de los archivos, consulte los [ejemplos de aplicaciones administradas](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
* Para información sobre cómo publicar aplicaciones administradas en Azure Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
* Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).

