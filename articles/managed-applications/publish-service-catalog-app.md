---
title: "Creación y publicación de una aplicación administrada del catálogo de servicios de Azure | Microsoft Docs"
description: "Se explica cómo crear una aplicación administrada de Azure diseñada para los miembros de su organización."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 46adcdf39625c85dc962a7541b68c5500cf920ee
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicación de una aplicación administrada para consumo interno

Puede crear y publicar [aplicaciones administradas](overview.md) de Azure que están diseñadas para los miembros de su organización. Por ejemplo, un departamento de TI puede publicar aplicaciones administradas que garantizan el cumplimiento de los estándares de la organización. Estas aplicaciones administradas están disponibles a través del catálogo de servicios y no en Azure Marketplace.

Para publicar una aplicación administrada en el catálogo de servicios:

* Cree una plantilla que defina los recursos que va a implementar con la aplicación administrada.
* Defina los elementos de la interfaz de usuario del portal cuando implemente la aplicación administrada.
* Cree un paquete .zip que contenga los archivos de plantilla necesarios.
* Decida qué usuario, grupo o aplicación necesita acceder al grupo de recursos en la suscripción del usuario.
* Cree la definición de aplicación administrada que apunta al paquete .zip y solicita acceso para la identidad.

Para este artículo, la aplicación administrada contiene solo una cuenta de almacenamiento. Está pensado para ilustrar los pasos necesarios para publicar una aplicación administrada. Para ver ejemplos completos, consulte [Sample projects for Azure managed applications](sample-projects.md) (Ejemplos de proyectos para aplicaciones administradas de Azure).

## <a name="create-the-resource-template"></a>Creación de la plantilla de recursos

Todas las definiciones de aplicaciones administradas contienen un archivo denominado **mainTemplate.json**. En él, se definen los recursos de Azure que desea aprovisionar. La plantilla no difiere de una plantilla habitual de Resource Manager.

Cree un archivo denominado **mainTemplate.json**. El nombre distingue mayúsculas de minúsculas.

Agregue el siguiente JSON al archivo. Define los parámetros para crear una cuenta de almacenamiento y especifica las propiedades de la cuenta de almacenamiento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString('storage'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Guarde el archivo mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Creación de la definición de la interfaz de usuario

Azure Portal usa el archivo **createUiDefinition.json** para generar la interfaz de usuario para los usuarios que crean la aplicación administrada. Defina cómo los usuarios proporcionan la entrad de cada parámetro. Puede usar opciones como una lista desplegable, un cuadro de texto, un cuadro de contraseña y otras herramientas de entrada. Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).

Cree un archivo denominado **createUiDefinition.json**. El nombre distingue mayúsculas de minúsculas.

Agregue el siguiente JSON al archivo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Guarde el archivo createUIDefinition.json.

## <a name="package-the-files"></a>Empaquetado de los archivos

Agregue los dos archivos a un archivo .zip denominado app.zip. Los dos archivos tienen que estar en el nivel raíz del archivo ZIP. Si los coloca en una carpeta, recibe un error al crear la definición de aplicación administrada que indica que los archivos requeridos no están presentes. 

Cargue el paquete en una ubicación accesible desde donde pueda consumirse. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Creación de la definición de aplicación administrada

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Creación de una aplicación o grupo de usuarios de Azure Active Directory

El siguiente paso consiste en seleccionar una aplicación o un grupo de usuarios para administrar los recursos en nombre del cliente. Este grupo de usuarios o esta aplicación tienen permisos en el grupo de recursos administrado, según el rol asignado. El rol puede ser cualquier rol de Control de acceso basado en roles (RBAC) integrado como propietario o colaborador. También puede conceder a un usuario individual permisos para administrar los recursos, pero este permiso se suele asignar a un grupo de usuarios. Para crear un grupo de usuarios de Active Directory, consulte [Creación de un grupo y adición de miembros en Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Necesita el identificador de objeto del grupo de usuarios que se usará para administrar los recursos. 

![Obtener identificador de grupo](./media/publish-service-catalog-app/get-group-id.png)

### <a name="get-the-role-definition-id"></a>Obtención del identificador de definición de rol

A continuación, necesita el identificador de definición de rol para el rol RBAC integrado que desea para conceder acceso al usuario, al grupo de usuarios o a la aplicación. Por lo general, se usa el rol Propietario, Colaborador o Lector. El comando siguiente muestra cómo obtener el identificador de definición de rol para el rol Propietario:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Creación de la definición de aplicación administrada

Si ya no dispone de un grupo de recursos para almacenar la definición de aplicación administrada, créelo ahora:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Ahora, cree el recurso de la definición de aplicación administrada.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "<group-id>:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application-by-using-the-portal"></a>Creación de una aplicación administrada con Azure Portal

Ahora, vamos a usar el portal para implementar la aplicación administrada. Puede consultar la interfaz de usuario que creó en el paquete.

1. Vaya a Azure Portal. Seleccione **+ Nuevo** y busque el **catálogo de servicios**.

   ![Búsqueda del catálogo de servicios](./media/publish-service-catalog-app/select-new.png)

1. Seleccione la **aplicación administrada del catálogo de servicios**.

   ![Selección del catálogo de servicios](./media/publish-service-catalog-app/select-service-catalog.png)

1. Seleccione **Crear**.

   ![Haga clic en Crear](./media/publish-service-catalog-app/select-create.png)

1. Busque la aplicación administrada que desea crear a partir de la lista de soluciones disponibles y selecciónela. Seleccione **Crear**.

   ![Búsqueda de la aplicación administrada](./media/publish-service-catalog-app/find-application.png)

1. Proporcione la información básica necesaria para la aplicación administrada. Especifique la suscripción y un nuevo grupo de recursos que contenga la aplicación administrada. Seleccione **Centro occidental de EE. UU.** como ubicación. Cuando haya terminado, seleccione **Aceptar**.

   ![Envío de parámetros de aplicaciones administradas](./media/publish-service-catalog-app/provide-basics.png)

1. Proporcione valores que sean específicos de los recursos de la aplicación administrada. Cuando haya terminado, seleccione **Aceptar**.

   ![Envío de parámetros de recursos](./media/publish-service-catalog-app/provide-resource-values.png)

1. La plantilla valida los valores que proporcionó. Si la validación es correcta, seleccione **Aceptar** para iniciar la implementación.

   ![Validación de aplicación administrada](./media/publish-service-catalog-app/validate.png)

Una vez que finalice la implementación, la aplicación administrada existirá en un grupo de recursos llamado applicationGroup. La cuenta de almacenamiento existe en un grupo de recursos llamado applicationGroup más un valor de cadena con hash.

## <a name="next-steps"></a>pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](overview.md).
* Para ver ejemplos de proyectos, consulte [Sample projects for Azure managed applications](sample-projects.md) (Ejemplos de proyectos para aplicaciones administradas de Azure).
* Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
