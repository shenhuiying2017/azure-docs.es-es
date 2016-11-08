---
title: Plantilla del Administrador de recursos para un secreto en un almacén de claves | Microsoft Docs
description: Muestra el esquema del Administrador de recursos para implementar los secretos del almacén de claves mediante una plantilla.
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz

---
# Esquema de la plantilla del secreto del Almacén de claves
Crea un secreto que se almacena en un almacén de claves. Este tipo de recurso se implementa con frecuencia como un recurso secundario de [almacén de claves](resource-manager-template-keyvault.md).

## Formato de esquema
Para crear un secreto del almacén de claves, agregue el siguiente esquema a la plantilla. El secreto puede definirse como un recurso de secundario de un almacén de claves o como recurso de nivel superior. Se puede definir como un recurso secundario cuando el almacén de claves se implementa en la misma plantilla. Debe definir el secreto como un recurso de nivel superior cuando el almacén de claves no esté implementado en la misma plantilla, o cuando necesite crear varios secretos mediante un bucle en el tipo de recurso.

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## Valores
Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Valor |
| --- | --- |
| type |Enum<br />Obligatorio<br />**secrets** (cuando se implementa como un recurso secundario de almacén de claves) o<br /> **Microsoft.KeyVault/vaults/secrets** (cuando se implementa como un recurso de nivel superior)<br /><br />Tipo de recurso que se creará. |
| apiVersion |Enum<br />Obligatorio<br />**2015-06-01** o **2014-12-19-preview**<br /><br />Versión de la API que se usará para crear el recurso. |
| name |Cadena<br />Obligatorio<br />Una sola palabra cuando se implementa como un recurso secundario de un almacén de claves o en el formato **{nombre-almacén-claves}/{nombre-secreto}** cuando se implementa como un recurso de nivel superior que se agregará a un almacén de claves actual.<br /><br />Nombre del secreto que se va a crear. |
| propiedades |Objeto<br />Obligatorio<br />[Objeto de propiedades](#properties)<br /><br />Objeto que especifica el valor del secreto que se va a crear. |
| dependsOn |Matriz<br />Opcional<br />Lista separada por comas de nombres o identificadores únicos de recursos.<br /><br />Colección de recursos de los que depende este vínculo. Si se implementa el almacén de claves para el secreto en la misma plantilla, incluya el nombre del almacén de claves en este elemento para asegurarse de que se implementa en primer lugar. |

<a id="properties" />

### properties object
| Nombre | Valor |
| --- | --- |
| value |Cadena<br />Obligatorio<br /><br />Valor del secreto que se almacenará en el almacén de claves. Al pasar un valor para esta propiedad, use un parámetro de tipo **securestring**. |

## Ejemplos
El primer ejemplo implementa un secreto como un recurso secundario de un almacén de claves.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

En el segundo ejemplo se implementa el secreto como un recurso de nivel superior que se almacena en un almacén de claves existente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## Pasos siguientes
* Para obtener información general sobre almacenes de claves, consulte [Introducción al Almacén de claves de Azure](key-vault/key-vault-get-started.md).
* Para obtener un ejemplo de referencia de un secreto de almacén de claves al implementar plantillas, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0629_2016-->