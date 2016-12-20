---
title: Recursos de Automation en soluciones de OMS | Microsoft Docs
description: "Normalmente, las soluciones de OMS incluyen runbooks en Azure Automation para automatizar procesos como la recopilación y el procesamiento de datos de supervisión.  En este artículo se describe cómo incluir runbooks y sus recursos relacionados en una solución."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>Recursos de Automation en soluciones de OMS (vista preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.   
> 
> 

[Normalmente, las soluciones de administración de OMS](operations-management-suite-solutions.md) incluyen runbooks en Azure Automation para automatizar procesos como la recopilación y el procesamiento de datos de supervisión.  Además de los runbooks, las cuentas de Automation incluyen recursos como variables y programaciones que admiten los runbooks que se usan en la solución.  En este artículo se describe cómo incluir runbooks y sus recursos relacionados en una solución.

> [!NOTE]
> En los ejemplos de este artículo se usan parámetros y variables que son necesarios o comunes para las soluciones de administración y se describen en [Creating management solutions in Operations Management Suite (OMS) (Creación de soluciones de administración en Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 
> 
> 

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con la manera de [crear una solución de administración](operations-management-suite-solutions-creating.md) y la estructura de un archivo de solución.

## <a name="samples"></a>Muestras
Puede obtener plantillas de Resource Manager de ejemplo para recursos de Automation de las [plantillas de inicio rápido en GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Cuenta de Automation
Todos los recursos de Azure Automation están incluidos en una [cuenta de Automation](../automation/automation-security-overview.md#automation-account-overview).  Como se describe en [el área de trabajo de OMS y la cuenta de Automation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account), la cuenta de Automation no está incluida en la solución de administración pero debe existir antes de que se instale la solución.  Si no está disponible, se producirá un error en la instalación de la solución.

El nombre de su cuenta de Automation es el nombre de cada recurso de Automation.  Esto se hace en la solución con el parámetro **accountName** como se muestra en el siguiente ejemplo de un recurso de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Los recursos de [runbook de Azure Automation](../automation/automation-runbook-types.md) tienen un tipo de **Microsoft.Automation/automationAccounts/runbooks** y tienen las propiedades en la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| runbookType |Especifica los tipos del runbook. <br><br> Script: script de PowerShell <br>PowerShell: flujo de trabajo de PowerShell <br> GraphPowerShell: runbook de script de PowerShell gráfico <br> GraphPowerShellWorkflow: runbook de flujo de trabajo de PowerShell gráfico |
| logProgress |Especifica si se deben generar [registros de progreso](../automation/automation-runbook-output-and-messages.md) para el runbook. |
| logVerbose |Especifica si se deben generar [registros detallados](../automation/automation-runbook-output-and-messages.md) para el runbook. |
| Description |Descripción opcional del runbook. |
| publishContentLink |Especifica el contenido del runbook. <br><br>uri: URI del contenido del runbook.  Será un archivo. ps1 para los runbooks de PowerShell y script, y un archivo de runbook gráfico exportado para un runbook gráfico.  <br> version: versión del runbook para su propio seguimiento. |

A continuación se muestra un ejemplo de un recurso de runbook.  En este caso, recupera el runbook desde la [Galería de PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Inicio de un runbook
Hay dos métodos para iniciar un runbook en una solución de administración.

* Para iniciar el runbook cuando se instala la solución, cree un [recurso de trabajo](#automation-jobs) tal como se describe a continuación.
* Para iniciar el runbook en una programación, cree un [recurso de programación](#schedules) tal como se describe a continuación. 

## <a name="automation-jobs"></a>Trabajos de automatización
Para iniciar un runbook cuando se instale la solución de administración, se crea un recurso de **trabajo**.  Los recursos de trabajo tienen un tipo de **Microsoft.Automation/automationAccounts/jobs** y tienen las propiedades en la tabla siguiente.

| Propiedad | Description |
|:--- |:--- |
| runbook |Entidad **name** única con el nombre del runbook. |
| parameters |Entidad de cada parámetro que necesita el runbook. |

El trabajo incluye el nombre del runbook y los valores de los parámetros que se deben enviar al runbook.  El trabajo debe [depender del](operations-management-suite-solutions-creating.md#resources) runbook que se inicia, ya que el runbook debe crearse antes del trabajo.  También se pueden crear dependencias en otros trabajos para runbooks que se deben completar antes del que esté en curso.

El nombre de un recurso de trabajo debe contener un GUID que suele asignar un parámetro.  Puede obtener más información sobre los parámetros GUID en [Creating solutions in Operations Management Suite (OMS) (Creación de soluciones en Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md#parameters).  

A continuación se muestra un ejemplo de un recurso de trabajo que inicia un runbook cuando se instala la solución de administración.  Uno de los runbooks debe completarse antes de que este se inicie para que tenga dependencias en los trabajos de ese runbook.  Los detalles del trabajo se proporcionan mediante parámetros y variables en lugar de especificarse directamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificados
[Los certificados de Azure Automation](../automation/automation-certificates.md) tienen un tipo de **Microsoft.Automation/automationAccounts/certificates** y tienen las propiedades en la tabla siguiente.

| Propiedad | Description |
|:--- |:--- |
| base64Value |Valor Base 64 del certificado. |
| thumbprint |Huella digital del certificado. |

A continuación se muestra un ejemplo de un recurso de certificado.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenciales
[Las credenciales de Azure Automation](../automation/automation-credentials.md) tienen un tipo de **Microsoft.Automation/automationAccounts/credentials** y tienen las propiedades en la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| userName |Nombre de usuario de la credencial. |
| contraseña |Contraseña de la credencial. |

A continuación se muestra un ejemplo de un recurso de credencial.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Programaciones
[Las programaciones de Azure Automation](../automation/automation-schedules.md) tienen un tipo de **Microsoft.Automation/automationAccounts/schedules** y tienen las propiedades en la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Description |Descripción opcional de la programación. |
| startTime |Especifica la hora de inicio de una programación como un objeto DateTime. Se puede proporcionar una cadena si esta se puede convertir en un valor DateTime válido. |
| isEnabled |Especifica si la programación está habilitada. |
| interval |El tipo de intervalo de la programación.<br><br>day<br>hour |
| frequency |Frecuencia con la que la programación se debe activar en cantidad de días u horas. |

A continuación se muestra un ejemplo de un recurso de programación.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variables
[Las variables de Azure Automation](../automation/automation-variables.md) tienen un tipo de **Microsoft.Automation/automationAccounts/variables** y tienen las propiedades en la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Description |Descripción opcional de la variable. |
| isEncrypted |Especifica si se debe cifrar la variable. |
| type |Tipo de datos de la variable. |
| value |Valor de la variable. |

A continuación se muestra un ejemplo de un recurso de variable.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Módulos
La solución de administración no necesita definir los [módulos globales](../automation/automation-integration-modules.md) que usan los runbooks porque siempre estarán disponibles.  No es necesario que incluya un recurso para ningún otro módulo que usan los runbooks y el runbook tiene que depender del recurso de módulo para asegurarse de que este se cree antes que el runbook.

[Los módulos de integración](../automation/automation-integration-modules.md) tienen un tipo de **Microsoft.Automation/automationAccounts/modules** y tienen las propiedades en la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| contentLink |Especifica el contenido del módulo. <br><br>uri: URI del contenido del runbook.  Será un archivo. ps1 para los runbooks de PowerShell y script, y un archivo de runbook gráfico exportado para un runbook gráfico.  <br> version: versión del runbook para su propio seguimiento. |

A continuación se muestra un ejemplo de un recurso de módulo.

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Actualización de módulos
Si actualiza una solución de administración que incluye un runbook que usa una programación y la nueva versión de la solución tiene un nuevo módulo que usa ese runbook, el runbook puede usar la versión anterior del módulo.  Debe incluir los siguientes runbooks en la solución y crear un trabajo para ejecutarlos antes que cualquier otro runbook.  Así se asegurará de que todos los módulos se actualizan según sea necesario antes de que se carguen los runbooks.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantizará que la versión de todos los módulos que usan los runbooks en la solución sea la más reciente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) volverá a registrar todos los recursos de programación para asegurarse de que los runbooks vinculados a ellos usen los módulos más recientes.

A continuación se muestra un ejemplo de los elementos necesarios de una solución para admitir la actualización del módulo.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Pasos siguientes
* [Agregar una vista a la solución](operations-management-suite-solutions-resources-views.md) para visualizar los datos recopilados.




<!--HONumber=Nov16_HO3-->


