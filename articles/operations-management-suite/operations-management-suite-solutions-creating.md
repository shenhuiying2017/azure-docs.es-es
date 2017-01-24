---
title: "Creación de soluciones de administración en Operations Management Suite (OMS) | Microsoft Docs"
description: "Las soluciones de administración amplían la funcionalidad de Operations Management Suite (OMS) proporcionando escenarios de administración empaquetados que los clientes pueden agregar a su área de trabajo de OMS.  En este artículo se proporciona información sobre cómo crear soluciones de administración que se pueden usar en su propio entorno o ponerse a disposición de sus clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: ab33a7610b8e7bbf64e9f1bfde3753f95956a82f


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Creación de soluciones de administración en Operations Management Suite (OMS) (versión preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.  
>
>

Las soluciones de administración amplían la funcionalidad de Operations Management Suite (OMS) proporcionando escenarios de administración empaquetados que los clientes pueden agregar a su área de trabajo de OMS.  En este artículo se proporcionan detalles sobre cómo crear soluciones de administración propias que puede usar en su propio entorno o poner a disposición de los clientes a través de la comunidad.

## <a name="planning-your-management-solution"></a>Planeación de las soluciones de administración
Las soluciones de administración de OMS incluyen varios recursos que admiten un escenario de administración concreto.  Al planear la solución, debe centrarse en el escenario que está intentando lograr y todos los recursos necesarios para su funcionamiento.  Cada solución debe estar autocontenida y definir cada recurso que necesite, incluso si otras soluciones definen uno o más recursos.  Cuando se instala una solución de administración, se crea cada uno de los recursos a menos que ya existan, y se puede definir lo que ocurre en los recursos cuando se quita una solución.  

Por ejemplo, una solución de administración podría incluir un [runbook de Azure Automation](../automation/automation-intro.md) que recopila los datos en el repositorio de Log Analytics mediante una [programación](../automation/automation-schedules.md) y una [vista](../log-analytics/log-analytics-view-designer.md) que proporciona distintas visualizaciones de los datos recopilados.  La misma programación podría utilizarse en otra solución.  Como autor de la solución de administración, podría definir los tres recursos, pero especificar que el runbook y la vista deben eliminarse automáticamente cuando se quita la solución.    También podría definir la programación, pero especifique qué debe permanecer en su lugar si la solución se ha quitado en caso de que aún la use otra solución.

## <a name="management-solution-files"></a>Archivos de las soluciones de administración
Las soluciones de administración se implementan como [plantillas de administración de recursos](../azure-resource-manager/resource-manager-template-walkthrough.md).  La tarea principal para aprender a crear soluciones de administración es saber cómo [crear una plantilla](../azure-resource-manager/resource-group-authoring-templates.md).  En este artículo se proporcionan detalles únicos de plantillas que se usan para soluciones y sobre cómo definir recursos de solución típicos.

La estructura básica de un archivo de una solución de administración, que se muestra a continuación, es la misma que la de una [plantilla de Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format).  En cada una de las siguientes secciones se describen los elementos de nivel superior y su contenido en una solución.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parámetros
Los [parámetros](../azure-resource-manager/resource-group-authoring-templates.md#parameters) son valores que el usuario le debe proporcionar al instalar la solución de administración.  Hay parámetros estándar que tendrán todas las soluciones, y puede agregar parámetros adicionales según sea necesario para su solución particular.  La manera en que los usuarios proporcionarán valores de parámetros al instalar la solución dependerá del parámetro particular y de cómo se instala la solución.

Cuando un usuario instala su solución de administración mediante plantillas de [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) o [de inicio rápido de Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), se le pide que seleccione un [área de trabajo de OMS y una cuenta de Automation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Estos se usan para rellenar los valores de cada uno de los parámetros estándar.  Al usuario no se le pide que proporcione valores directamente para los parámetros estándar, pero se le pide que proporcione valores para cualquier parámetro adicional.

Cuando el usuario instala la solución [otro método](operations-management-suite-solutions.md#finding-and-installing-management-solutions), debe proporcionar un valor para todos los parámetros estándar y todos los parámetros adicionales.

A continuación se muestra un parámetro de ejemplo.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

En la tabla siguiente se describen los atributos de un parámetro.

| Atributo | Descripción |
|:--- |:--- |
| type |Tipo de datos para el parámetro. El control de entrada que se muestra para el usuario depende del tipo de datos.<br><br>bool: cuadro desplegable<br>string: cuadro de texto<br>int: cuadro de texto<br>Securestring: campo de contraseña<br> |
| categoría |Categoría opcional para el parámetro.  Los parámetros de la misma categoría se agrupan juntos. |
| control |Funcionalidad adicional para los parámetros de cadena.<br><br>datetime: se muestra el control de fecha y hora.<br>guid: el valor del GUID se genera automáticamente y no se muestra el parámetro. |
| Description |Descripción opcional del parámetro.  Se muestra en un globo de información junto al parámetro. |

### <a name="standard-parameters"></a>Parámetros estándar
En la tabla siguiente se enumeran los parámetros estándar de todas las soluciones de administración.  Estos valores se rellenan para el usuario en lugar de pedírseles cuando se instala la solución desde las plantillas de Azure Marketplace o de inicio rápido.  El usuario debe proporcionar los valores si la solución se instala con otro método.

> [!NOTE]
> La interfaz de usuario de las plantillas de Azure Marketplace y de inicio rápido espera los nombres de parámetro en la tabla.  Si usa nombres de parámetros diferentes, se le preguntarán al usuario y no se rellenarán automáticamente.
>
>

| Parámetro | Tipo | Descripción |
|:--- |:--- |:--- |
| accountName |string |Nombre de la cuenta de Automation de Azure. |
| pricingTier |string |Plan de tarifa del área de trabajo de Log Analytics y de la cuenta de Automation de Azure. |
| regionId |string |Región de la cuenta de Azure Automation. |
| solutionName |string |Nombre de la solución. |
| workspaceName |string |Nombre del área de trabajo de Log Analytics. |
| workspaceRegionId |string |Región del área de trabajo de Log Analytics. |

### <a name="sample"></a>Muestra
A continuación se muestra una entidad de parámetro de ejemplo para una solución.  Incluye todos los parámetros estándar y dos parámetros adicionales de la misma categoría.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Consulte los valores de parámetro de otros elementos de la solución con la sintaxis **parameters('nombre de parámetro')**.  Por ejemplo, para tener acceso al nombre de área de trabajo, use **parameters('workspaceName')**

## <a name="variables"></a>Variables
El elemento **Variables** incluye valores que usará en el resto de la solución de administración.  Estos valores no se exponen al usuario que instala la solución.  Están destinados a proporcionar al creador una única ubicación donde pueda administrar los valores que pueden utilizarse varias veces a lo largo de la solución. Debe colocar los valores específicos para su solución en variables en lugar de codificarlos en el elemento **resources**.  De este modo, el código es más legible y los valores se pueden cambiar fácilmente en versiones posteriores.

A continuación se muestra un ejemplo del elemento **variables** con parámetros típicos usados en las soluciones.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Consulte los valores de las variables a través de la solución con la sintaxis **variables('nombre de variable')**.  Por ejemplo, para tener acceso a la variable SolutionName, se usaría **variables('solutionName')**.

## <a name="resources"></a>Recursos
El elemento **resources** define los distintos recursos incluidos en la solución de administración.  Se trata de la parte más grande y compleja de la plantilla.  Los recursos se definen con la siguiente estructura.  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dependencias
El elemento **dependsOn** especifica una [dependencia](../azure-resource-manager/resource-group-define-dependencies.md) en otro recurso.  Cuando se instala la solución, un recurso no se crea hasta que no se hayan creado todas sus dependencias.  Por ejemplo, puede que su solución [inicie un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) cuando se instala mediante un [recurso de trabajo](operations-management-suite-solutions-resources-automation.md#automation-jobs).  El recurso de trabajo será dependiente del recurso de runbook para asegurarse de que el runbook se crea antes de que se cree el trabajo.

### <a name="oms-workspace-and-automation-account"></a>Área de trabajo de OMS y cuenta de Automation
La mayoría de las soluciones requieren que un [área de trabajo de OMS](../log-analytics/log-analytics-manage-access.md) contenga vistas y que una [cuenta de Automation](../automation/automation-security-overview.md#automation-account-overview) contenga runbooks y recursos relacionados.  Estos deben estar disponibles antes de que se creen los recursos de la solución y no se deben definir en la propia solución.  El usuario [especificará un área de trabajo y una cuenta](operations-management-suite-solutions.md#oms-workspace-and-automation-account) al implementar la solución, pero usted, como autor, debe tener en cuenta los siguientes puntos.

## <a name="solution-resource"></a>Recursos de solución
Cada solución requiere una entrada de recursos en el elemento **resources** que define la propia solución.  Esta tendrá un tipo de **Microsoft.OperationsManagement/solutions** y tener la siguiente estructura.  A continuación se muestra un ejemplo de un recurso de solución.  Sus distintos elementos se describen en las siguientes secciones.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nombre de la solución
El nombre de la solución debe ser único en su suscripción de Azure. El valor que se recomienda usar es el siguiente.  Este usa una variable denominada **SolutionName** para el nombre base y el parámetro **workspaceName** para asegurarse de que el nombre sea único.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Esto se resolvería en un nombre similar al siguiente.

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>Dependencias
El recurso de la solución debe tener un [dependencia](../azure-resource-manager/resource-group-define-dependencies.md) en todos los recursos de la solución, ya que estos deben existir antes de que se cree la solución.  Para ello, agregue una entrada para cada recurso en el elemento **dependsOn**.

### <a name="properties"></a>Propiedades
Este recurso de la solución tiene las propiedades de la tabla siguiente.  Esto incluye los recursos a los que hace referencia la solución y contenidos en ella, que define cómo se administra el recurso después de instalar la solución.  Cada recurso de la solución debe aparecer en una de las propiedades **referencedResources** o **containedResources**.

| Propiedad | Descripción |
|:--- |:--- |
| workspaceResourceId |Identificador del área de trabajo de OMS en el formulario *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nombre del espacio de trabajo\>*. |
| referencedResources |Lista de recursos de la solución que no se deben quitar cuando se quita la solución. |
| containedResources |Lista de recursos de la solución que debe quitarse cuando se quita la solución. |

El ejemplo anterior es para una solución con un runbook, una programación y una vista.  Se hace *referencia a la programación y al runbook * en el elemento **properties** de modo que no se quitan cuando se quita la solución.  La vista está *contenida*, por lo que se quita cuando se quita la solución.

### <a name="plan"></a>Plan
La entidad **plan** del recurso de la solución tiene las propiedades en la tabla siguiente.

| Propiedad | Description |
|:--- |:--- |
| name |Nombre de la solución. |
| versión |Versión de la solución según determine el autor. |
| product |Cadena única para identificar la solución. |
| publisher |Publicador de la solución. |

## <a name="other-resources"></a>Otros recursos:
Puede obtener los detalles y ejemplos de recursos que son comunes en las soluciones de administración en los siguientes artículos.

* [Vistas y paneles](operations-management-suite-solutions-resources-views.md)
* [Recursos de Automation](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Prueba de una solución de administración
Antes de implementar su solución de administración, se recomienda que la pruebe mediante [Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy).  Esto validará su archivo de solución y le ayudará a identificar los problemas antes de intentar implementarlo.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información en [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Búsqueda de [plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates) para obtener ejemplos de diferentes plantillas de Resource Manager.
* Vea los detalles para [agregar vistas a una solución de administración](operations-management-suite-solutions-resources-views.md).
* Vea los detalles para [agregar recursos de Automation a una solución de administración](operations-management-suite-solutions-resources-automation.md).



<!--HONumber=Jan17_HO1-->


