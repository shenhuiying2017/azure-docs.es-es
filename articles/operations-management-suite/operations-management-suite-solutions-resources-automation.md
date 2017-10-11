---
title: Recursos de Azure Automation en soluciones de OMS | Microsoft Docs
description: "Normalmente, las soluciones de OMS incluyen runbooks en Azure Automation para automatizar procesos como la recopilación y el procesamiento de datos de supervisión.  En este artículo se describe cómo incluir runbooks y sus recursos relacionados en una solución."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1909183a33ed03d8165671cff25cc8b83b77733
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="adding-azure-automation-resources-to-an-oms-management-solution-preview"></a>Incorporación de recursos de Azure Automation a una solución de administración de OMS (versión preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.   


[Normalmente, las soluciones de administración de OMS](operations-management-suite-solutions.md) incluyen runbooks en Azure Automation para automatizar procesos como la recopilación y el procesamiento de datos de supervisión.  Además de los runbooks, las cuentas de Automation incluyen recursos como variables y programaciones que admiten los runbooks que se usan en la solución.  En este artículo se describe cómo incluir runbooks y sus recursos relacionados en una solución.

> [!NOTE]
> En los ejemplos de este artículo se usan parámetros y variables que son necesarios o comunes para las soluciones de administración y se describen en [Creating management solutions in Operations Management Suite (OMS) (Creación de soluciones de administración en Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con la información siguiente.

- Cómo [crear una solución de administración](operations-management-suite-solutions-creating.md).
- La estructura de un [archivo de solución](operations-management-suite-solutions-solution-file.md).
- Cómo [crear plantillas de Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Cuenta de Automation
Todos los recursos de Azure Automation están incluidos en una [cuenta de Automation](../automation/automation-security-overview.md#automation-account-overview).  Como se describe en [el área de trabajo de OMS y la cuenta de Automation](operations-management-suite-solutions.md#oms-workspace-and-automation-account), la cuenta de Automation no está incluida en la solución de administración pero debe existir antes de que se instale la solución.  Si no está disponible, se producirá un error en la instalación de la solución.

El nombre de cada recurso de Automation incluye el nombre de su cuenta de Automation.  Esto se hace en la solución con el parámetro **accountName** como se muestra en el siguiente ejemplo de un recurso de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Debe incluir cualquier runbook usado por la solución en el archivo de solución de modo que se creen al instalarse la solución.  No puede contener el cuerpo del runbook en la plantilla, sin embargo, por lo que debe publicar el runbook en una ubicación pública a la que pueda tener acceso cualquier usuario que instale su solución.

Los recursos de [runbook de Azure Automation](../automation/automation-runbook-types.md) tienen un tipo de **Microsoft.Automation/automationAccounts/runbooks** y tienen la estructura siguiente. Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


En la tabla siguiente se describen las propiedades para los runbooks.

| Propiedad | Descripción |
|:--- |:--- |
| runbookType |Especifica los tipos del runbook. <br><br> Script: script de PowerShell <br>PowerShell: flujo de trabajo de PowerShell <br> GraphPowerShell: runbook de script de PowerShell gráfico <br> GraphPowerShellWorkflow: runbook de flujo de trabajo de PowerShell gráfico |
| logProgress |Especifica si se deben generar [registros de progreso](../automation/automation-runbook-output-and-messages.md) para el runbook. |
| logVerbose |Especifica si se deben generar [registros detallados](../automation/automation-runbook-output-and-messages.md) para el runbook. |
| Description |Descripción opcional del runbook. |
| publishContentLink |Especifica el contenido del runbook. <br><br>uri: URI del contenido del runbook.  Será un archivo. ps1 para los runbooks de PowerShell y script, y un archivo de runbook gráfico exportado para un runbook gráfico.  <br> version: versión del runbook para su propio seguimiento. |


## <a name="automation-jobs"></a>Trabajos de automatización
Cuando se inicia un runbook en Azure Automation, se crea un trabajo de automatización.  Puede agregar un recurso de trabajo de automatización a la solución para iniciar un runbook automáticamente al instalarse la solución de administración.  Este método se suele usar para iniciar runbooks que se emplean para la configuración inicial de la solución.  Para iniciar un runbook a intervalos regulares, cree una [programación](#schedules) y un [programa de trabajos](#job-schedules)

Los recursos de trabajo tienen un tipo de **Microsoft.Automation/automationAccounts/jobs** y tienen la estructura siguiente.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

En la tabla siguiente se describen las propiedades para los trabajos de automatización.

| Propiedad | Description |
|:--- |:--- |
| runbook |Entidad name única con el nombre del runbook que se va a iniciar. |
| parameters |Entidad de cada valor del parámetro que necesita el runbook. |

El trabajo incluye el nombre del runbook y los valores de los parámetros que se deben enviar al runbook.  El trabajo debe [depender del](operations-management-suite-solutions-solution-file.md#resources) runbook que se inicia, ya que el runbook debe crearse antes del trabajo.  Si tiene varios runbooks que deben iniciarse, puede definir su orden teniendo un trabajo dependiente de cualquier otro trabajo que deba ejecutarse primero.

El nombre de un recurso de trabajo debe contener un GUID que suele asignar un parámetro.  Puede obtener más información sobre los parámetros GUID en [Creating solutions in Operations Management Suite (OMS) (Creación de soluciones en Operations Management Suite (OMS)](operations-management-suite-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificados
Los [certificados de Azure Automation](../automation/automation-certificates.md) tienen un tipo de **Microsoft.Automation/automationAccounts/certificates** y tienen la estructura siguiente. Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



En la tabla siguiente se describen las propiedades para los recursos de certificados.

| Propiedad | Description |
|:--- |:--- |
| base64Value |Valor Base 64 del certificado. |
| thumbprint |Huella digital del certificado. |



## <a name="credentials"></a>Credenciales
Las [credenciales de Azure Automation](../automation/automation-credentials.md) tienen un tipo de **Microsoft.Automation/automationAccounts/credentials** y tienen la estructura siguiente.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

En la tabla siguiente se describen las propiedades para los recursos de credenciales.

| Propiedad | Descripción |
|:--- |:--- |
| userName |Nombre de usuario de la credencial. |
| contraseña |Contraseña de la credencial. |


## <a name="schedules"></a>Programaciones
Las [programaciones de Azure Automation](../automation/automation-schedules.md) tienen un tipo de **Microsoft.Automation/automationAccounts/schedules** y tienen la estructura siguiente. Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

En la tabla siguiente se describen las propiedades para los recursos de programación.

| Propiedad | Descripción |
|:--- |:--- |
| Description |Descripción opcional de la programación. |
| startTime |Especifica la hora de inicio de una programación como un objeto DateTime. Se puede proporcionar una cadena si esta se puede convertir en un valor DateTime válido. |
| isEnabled |Especifica si la programación está habilitada. |
| interval |El tipo de intervalo de la programación.<br><br>day<br>hour |
| frequency |Frecuencia con la que la programación se debe activar en cantidad de días u horas. |

Las programaciones deben tener una hora de inicio con un valor mayor que la hora actual.  No puede proporcionar este valor con una variable, ya que no tendría forma de saber cuándo se va a instalar.

Use una de las dos estrategias siguientes al usar recursos de programación en una solución.

- Use un parámetro para la hora de inicio de la programación.  Este pedirá al usuario que proporcione un valor cuando instale la solución.  Si tiene varias programaciones, podría usar un valor del parámetro único para más de una.
- Cree las programaciones mediante un runbook que se inicie al instalarse la solución.  De este modo, ya no es necesario que el usuario especifique una hora, pero usted no puede contener la programación en su solución, por lo que se quitará al quitarse también la solución.


### <a name="job-schedules"></a>Programaciones del trabajo
Los recursos de programación de trabajo vinculan un runbook con una programación.  Tienen un tipo de **Microsoft.Automation/automationAccounts/jobSchedules** y tienen la estructura siguiente.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


En la tabla siguiente se describen las propiedades para las programaciones de trabajo.

| Propiedad | Descripción |
|:--- |:--- |
| nombre de programación |Entidad **name** única con el nombre de la programación. |
| nombre de runbook  |Entidad **name** única con el nombre del runbook.  |



## <a name="variables"></a>Variables
Las [variables de Azure Automation](../automation/automation-variables.md) tienen un tipo de **Microsoft.Automation/automationAccounts/variables** y tienen la estructura siguiente.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

En la tabla siguiente se describen las propiedades para los recursos de variables.

| Propiedad | Descripción |
|:--- |:--- |
| Description | Descripción opcional de la variable. |
| isEncrypted | Especifica si se debe cifrar la variable. |
| type | Esta propiedad no tiene actualmente ningún efecto.  El tipo de datos de la variable se determinará por el valor inicial. |
| value | Valor de la variable. |

> [!NOTE]
> La propiedad **type** no tiene actualmente ningún efecto en la variable que se está creando.  El tipo de datos de la variable se determinará por el valor.  

Si establece el valor inicial de la variable, este debe configurarse con el tipo de datos correcto.  La tabla siguiente muestra los tipos de datos permitidos y su sintaxis.  Tenga en cuenta que es previsible que los valores en formato JSON vayan siempre entre comillas, incluyendo todos los caracteres especiales.  Por ejemplo, un valor de cadena se especificaría con comillas alrededor de la cadena (mediante el carácter de escape [\\]) mientras que un valor numérico se especificaría con un conjunto de comillas.

| Tipo de datos | Descripción | Ejemplo | Se resuelve como |
|:--|:--|:--|:--|
| cadena   | Incluya el valor entre comillas dobles.  | "\"Hello world\"" | "Hello world" |
| numeric  | Valor numérico con comillas simples.| "64" | 64 |
| boolean  | **true** o **false** entre comillas.  Tenga en cuenta que este valor debe ir en minúsculas. | "true" | true |
| datetime | Valor de fecha serializado.<br>Puede usar el cmdlet ConvertTo-Json de PowerShell para generar este valor para una fecha determinada.<br>Ejemplo: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Módulos
La solución de administración no necesita definir los [módulos globales](../automation/automation-integration-modules.md) que usan los runbooks porque siempre estarán disponibles en la cuenta de Automation.  Debe incluir un recurso para cualquier otro módulo usado por los runbooks.

Los [módulos de integración](../automation/automation-integration-modules.md) tienen un tipo de **Microsoft.Automation/automationAccounts/modules** y tienen la estructura siguiente.  Aquí se incluyen las variables y los parámetros habituales para que pueda copiar y pegar este fragmento de código en su archivo de solución y cambiar los nombres de parámetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


En la tabla siguiente se describen las propiedades para los recursos de módulos.

| Propiedad | Descripción |
|:--- |:--- |
| contentLink |Especifica el contenido del módulo. <br><br>uri: URI del contenido del módulo.  Será un archivo. ps1 para los runbooks de PowerShell y script, y un archivo de runbook gráfico exportado para un runbook gráfico.  <br> version: versión del módulo para su propio seguimiento. |

El runbook debe depender del recurso de módulos para garantizar que se cree antes que el runbook.

### <a name="updating-modules"></a>Actualización de módulos
Si actualiza una solución de administración que incluye un runbook que usa una programación y la nueva versión de la solución tiene un nuevo módulo que usa ese runbook, el runbook puede usar la versión anterior del módulo.  Debe incluir los siguientes runbooks en la solución y crear un trabajo para ejecutarlos antes que cualquier otro runbook.  Así se asegurará de que todos los módulos se actualizan según sea necesario antes de que se carguen los runbooks.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantizará que la versión de todos los módulos que usan los runbooks en la solución sea la más reciente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) volverá a registrar todos los recursos de programación para asegurarse de que los runbooks vinculados a ellos usen los módulos más recientes.




## <a name="sample"></a>Muestra
A continuación se muestra un ejemplo de una solución que incluye los siguientes recursos:

- Runbook.  Este es un runbook de ejemplo almacenado en un repositorio de GitHub público.
- Trabajo de automatización que inicia el runbook cuando se instala la solución.
- Programación y programa de trabajos para iniciar el runbook a intervalos regulares.
- Certificado.
- Credencial.
- Variable.
- Módulo.  Este es el [módulo OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) para escribir datos en Log Analytics. 

En el ejemplo se utilizan variables de [parámetros de solución estándar](operations-management-suite-solutions-solution-file.md#parameters) que se suelen utilizar en una solución en lugar de codificar valores en las definiciones de recursos.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Pasos siguientes
* [Agregar una vista a la solución](operations-management-suite-solutions-resources-views.md) para visualizar los datos recopilados.
