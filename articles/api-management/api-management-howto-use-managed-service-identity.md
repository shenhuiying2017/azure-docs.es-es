---
title: "Cómo usar Azure Managed Service Identity en Azure API Management | Microsoft Docs"
description: Aprenda a usar Azure Managed Service Identity en API Management
services: api-management
documentationcenter: 
author: miaojiang
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 70bf207cc173caf7d8cae3c4c9111ee2f427405b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-use-azure-managed-service-identity-in-azure-api-management"></a>Uso de Azure Managed Service Identity en Azure API Management

> [!Note]
> Azure Managed Service Identity Azure API Management se encuentran actualmente en versión preliminar.

En este tema se muestra cómo crear una identidad de servicio administrada para una instancia de servicio de API Management y cómo acceder a otros recursos. Una identidad de servicio administrada generada por Azure Active Directory (AAD) permite a la instancia de API Management acceder de forma fácil y segura a otros recursos protegidos de AAD, como Azure Key Vault. Esta identidad de servicio administrada está administrada por Azure y no requiere que aprovisione o rote los secretos. Para más información sobre Azure Managed Service Identity, consulte [Managed Service Identity (MSI) para recursos de Azure](../active-directory/msi-overview.md).

## <a name="creating-an-api-management-instance-with-an-identity-using-an-azure-resource-manager-template"></a>Creación de una instancia de API Management con una identidad mediante una plantilla de Azure Resource Manager

Se puede crear una instancia de API Management con una identidad mediante la inclusión de la siguiente propiedad en la definición de recursos. 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Esto indica a Azure que debe crear y administrar la identidad para la instancia de API Management. 

Por ejemplo, una plantilla completa podría tener el aspecto siguiente:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "serviceName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The name of the api management service"
            }
        },
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "admin@contoso.com",
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "Contoso",
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "Developer",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "[parameters('serviceName')]",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "[parameters('sku')]",
                "capacity": "[parameters('skuCount')]"
            },
            "properties": {
                "publisherEmail": "[parameters('publisherEmail')]",
                "publisherName": "[parameters('publisherName')]"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```

## <a name="obtaining-a-certificate-from-azure-key-vault"></a>Obtención de un certificado en Azure Key Vault

En el ejemplo siguiente se muestra cómo obtener un certificado en Azure Key Vault. Contiene los pasos siguientes:

* Crear una instancia de API Management con una identidad
* Actualizar las directivas de acceso de una instancia de Azure Key Vault y permitir que la instancia de API Management obtenga secretos de ella
* Actualizar la instancia de API Management estableciendo un nombre de dominio personalizado mediante un certificado de la instancia de Key Vault

> [!Important]
> Si no se proporciona la versión del objeto del certificado, API Management obtiene automáticamente la versión más reciente del certificado una vez cargado a Key Vault. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre Azure Managed Service Identity
  * [Introducción a Managed Service Identity](../active-directory/msi-overview.md)
  * [Más información sobre las plantillas de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)

