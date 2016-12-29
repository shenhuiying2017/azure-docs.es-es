---
title: Plantilla de Resource Manager para Key Vault | Microsoft Docs
description: Muestra el esquema del Administrador de recursos para implementar los almacenes de claves mediante una plantilla.
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: f72ae06c2e31de5d8a1121a9e265c23f016fffe9


---
# <a name="key-vault-template-schema"></a>Esquema de la plantilla del Almacén de claves
Crea un almacén de claves.

## <a name="schema-format"></a>Formato de esquema
Para crear un almacén de claves, agregue el siguiente esquema a la sección de recursos de la plantilla.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valores
Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Valor |
| --- | --- |
| type |Enum<br />Obligatorio<br />**Microsoft.KeyVault/vaults**<br /><br />Tipo de recurso que se creará. |
| apiVersion |Enum<br />Obligatorio<br />**2015-06-01** o **2014-12-19-preview**<br /><br />Versión de la API que se usará para crear el recurso. |
| Nombre |String<br />Obligatorio<br />Un nombre que único en Azure.<br /><br />Nombre del almacén de claves que se creará. Considere la posibilidad de usar la función [uniqueString](resource-group-template-functions.md#uniquestring) junto con su convención de nomenclatura para crear un nombre único, tal y como se muestra en el ejemplo siguiente. |
| location |String<br />Obligatorio<br />Región válida para almacenes de claves. Para determinar las regiones válidas, consulte [Regiones admitidas](resource-manager-supported-services.md#supported-regions).<br /><br />Región donde se hospedará el almacén de claves. |
| propiedades |Objeto<br />Obligatorio<br />[properties object](#properties)<br /><br />Objeto que especifica el tipo de almacén de claves que se creará. |
| resources |Matriz<br />Opcional<br />Valores permitidos: [recursos secretos de almacén de claves](resource-manager-template-keyvault-secret.md)<br /><br />Recursos secundarios para el almacén de claves. |

<a id="properties" />

### <a name="properties-object"></a>properties object
| Nombre | Valor |
| --- | --- |
| enabledForDeployment |Booleano<br />Opcional<br />**true** o **false**<br /><br />Especifica si el almacén está habilitado para la implementación de Service Fabric o máquinas virtuales. |
| enabledForTemplateDeployment |Booleano<br />Opcional<br />**true** o **false**<br /><br />Especifica si el almacén está habilitado para usarse en implementaciones de plantilla de Azure Resource Manager. Para obtener más información, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption |Booleano<br />Opcional<br />**true** o **false**<br /><br />Especifica si el almacén está habilitado para el cifrado de volúmenes. |
| tenantId |String<br />Obligatorio<br />**Globally-unique identifier**<br /><br />Identificador del inquilino para la suscripción. Puede recuperarlo con el cmdlet de PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) o el comando de la CLI de Azure **azure account show** . |
| accessPolicies |Matriz<br />Obligatorio<br />[objeto accessPolicies](#accesspolicies)<br /><br />Matriz de hasta 16 objetos que especifican los permisos para el usuario o la entidad de servicio. |
| sku |Objeto<br />Obligatorio<br />[objeto de SKU](#sku)<br /><br />SKU para el almacén de claves. |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies object
| Nombre | Valor |
| --- | --- |
| tenantId |String<br />Obligatorio<br />**Globally-unique identifier**<br /><br />Identificador del inquilino de Azure Active Directory que contiene el **objectId** en esta directiva de acceso. |
| objectId |String<br />Obligatorio<br />**Globally-unique identifier**<br /><br />Identificador de objeto del usuario de Azure Active Directory o de la entidad de servicio que tendrá acceso al almacén. Puede recuperar el valor de los cmdlets de PowerShell [Get-AzureRMADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) o [Get-AzureRMADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) o de los comandos de la CLI de Azure**azure ad user** o **azure ad sp**. |
| permisos |Objeto<br />Obligatorio<br />[objeto permissions](#permissions)<br /><br />Permisos concedidos en este almacén al objeto de Active Directory. |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions object
| Nombre | Valor |
| --- | --- |
| claves |Matriz<br />Obligatorio<br />**all**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**<br /><br />Permisos concedidos en las claves de este almacén a este objeto de Active Directory. Este valor debe especificarse como una matriz de uno o más valores permitidos. |
| secrets |Matriz<br />Obligatorio<br />**all**, **delete**, **get**, **list**, **set**<br /><br />Permisos concedidos en los secretos de este almacén a este objeto de Active Directory. Este valor debe especificarse como una matriz de uno o más valores permitidos. |

<a id="sku" />

### <a name="propertiessku-object"></a>properties.sku object
| Nombre | Valor |
| --- | --- |
| Nombre |Enum<br />Obligatorio<br />**standard** o **premium** <br /><br />Nivel de servicio del almacén de claves que se usará.  Standard admite secretos y claves protegidas mediante software.  Premium incorpora compatibilidad para claves protegidas con HSM. |
| family |Enum<br />Obligatorio<br />**A** <br /><br />Familia de SKU que se usará. |

## <a name="examples"></a>Ejemplos
En el ejemplo siguiente se implementa un almacén de claves y un secreto.

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

## <a name="quickstart-templates"></a>Plantillas de inicio rápido
La siguiente plantilla de inicio rápido implementa un almacén de claves.

* [Create Key Vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información general sobre almacenes de claves, consulte el artículo de [introducción a Azure Key Vault](../key-vault/key-vault-get-started.md).
* Para obtener un ejemplo de referencia de un secreto de almacén de claves al implementar plantillas, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).




<!--HONumber=Nov16_HO3-->


