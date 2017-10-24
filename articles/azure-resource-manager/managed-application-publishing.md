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
ms.date: 10/09/2017
ms.author: gauravbh
ms.openlocfilehash: 3ff2108d08bacc4bc5f79a768b9c131aa7e6fceb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicación de una aplicación administrada para consumo interno

Puede crear y publicar [aplicaciones administradas](managed-application-overview.md) de Azure que están diseñadas para los miembros de su organización. Por ejemplo, un departamento de TI puede publicar aplicaciones administradas que garantizan el cumplimiento de los estándares de la organización. Estas aplicaciones administradas están disponibles a través del catálogo de servicios y no en Azure Marketplace.

Para publicar una aplicación administrada en el catálogo de servicios:

* Cree un paquete ZIP que contenga los dos archivos de plantilla necesarios.
* Decida qué usuario, grupo o aplicación necesita acceder al grupo de recursos en la suscripción del usuario.
* Cree la definición de aplicación administrada que apunta al paquete .zip y solicita acceso para la identidad.

## <a name="create-a-managed-application-package"></a>Creación de un paquete de aplicación administrada

El primer paso es crear los dos archivos de plantilla necesarios. Empaquete los dos archivos en un archivo ZIP y cárguelo en una ubicación accesible, como una cuenta de almacenamiento. Pase un vínculo a este archivo ZIP al crear la definición de aplicación administrada.

* **mainTemplate.json**: este archivo define los recursos de Azure que se aprovisionan como parte de la aplicación administrada. La plantilla no difiere de una plantilla habitual de Resource Manager. Por ejemplo, para crear una cuenta de almacenamiento con una aplicación administrada, el archivo mainTemplate.json contiene:

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

* **createUiDefinition.json**: Azure Portal usa este archivo para generar la interfaz de usuario para los usuarios que crean la aplicación administrada. Defina cómo los usuarios proporcionan la entrad de cada parámetro. Puede usar opciones como una lista desplegable, un cuadro de texto, un cuadro de contraseña y otras herramientas de entrada. Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).

  En el ejemplo siguiente se muestra un archivo createUiDefinition.json que permite a los usuarios especificar el prefijo del nombre de la cuenta de almacenamiento a través de un cuadro de texto.

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

Después de que todos los archivos necesarios estén listos, empaquételos como un archivo ZIP. Los dos archivos tienen que estar en el nivel raíz del archivo ZIP. Si los coloca en una carpeta, recibe un error al crear la definición de aplicación administrada que indica que los archivos requeridos no están presentes. Cargue el paquete en una ubicación accesible desde donde pueda consumirse. En el resto de este artículo se asume que el archivo ZIP se encuentra en un contenedor de blobs de almacenamiento de acceso público.

Puede utilizar la CLI de Azure o Azure Portal para crear una aplicación administrada para el catálogo de servicios. En este artículo se muestran ambos enfoques.

## <a name="create-managed-application-with-azure-cli"></a>Creación de una aplicación administrada con la CLI de Azure

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Creación de una aplicación o grupo de usuarios de Azure Active Directory

El siguiente paso consiste en seleccionar una aplicación o un grupo de usuarios para administrar los recursos en nombre del cliente. Este grupo de usuarios o esta aplicación tienen permisos en el grupo de recursos administrado, según el rol asignado. El rol puede ser cualquier rol de Control de acceso basado en roles (RBAC) integrado como propietario o colaborador. También puede conceder a un usuario individual permisos para administrar los recursos, pero este permiso se suele asignar a un grupo de usuarios. Para crear un grupo de usuarios de Active Directory, consulte [Creación de un grupo y adición de miembros en Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

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

### <a name="get-the-role-definition-id"></a>Obtención del identificador de definición de rol

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

### <a name="create-the-managed-application-definition"></a>Creación de la definición de aplicación administrada

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

## <a name="create-managed-application-with-portal"></a>Creación de una aplicación administrada con Azure Portal

1. Si es necesario, cree un grupo de usuarios de Azure Active Directory para administrar los recursos. Para más información consulte [Creación de un grupo y adición de miembros en Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).
1. En la esquina superior izquierda, seleccione **+ Nuevo**.

   ![Nuevo servicio](./media/managed-application-publishing/new.png)

1. Busque **catálogo de servicios**.

1. En los resultados, desplácese hasta que encuentre **Definición de aplicación administrada del catálogo de servicios**. Selecciónelo.

   ![Busque las definiciones de aplicación administrada](./media/managed-application-publishing/select-managed-apps-definition.png)

1. Seleccione **Crear** para iniciar el proceso de creación de la definición de la aplicación administrada.

   ![Creación de la definición de aplicación administrada](./media/managed-application-publishing/create-definition.png)

1. Proporcione valores para nombre, nombre para mostrar, descripción, ubicación, suscripciones y grupo de recursos. Para el identificador URI del archivo de paquete, proporcione la ruta de acceso al archivo ZIP que creó.

   ![Proporcionar valores](./media/managed-application-publishing/fill-application-values.png)

1. Cuando llegue a la sección de Autenticación y Nivel de bloqueo, seleccione **Agregar autorización**.

   ![Agregar autorización](./media/managed-application-publishing/add-authorization.png)

1. Seleccione un grupo de Azure Active Directory para administrar los recursos y seleccione **Aceptar**.

   ![Agregar grupo de autorización](./media/managed-application-publishing/add-auth-group.png)

1. Cuando haya proporcionado todos los valores, seleccione **Crear**.

   ![Creación de aplicación administrada](./media/managed-application-publishing/create-app.png)

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](managed-application-overview.md).
* Para ver ejemplos de los archivos, consulte los [ejemplos de aplicaciones administradas](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
* Para información sobre cómo publicar aplicaciones administradas en Azure Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
* Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
