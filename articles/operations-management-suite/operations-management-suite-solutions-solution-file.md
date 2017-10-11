---
title: "Creación de soluciones de administración en Operations Management Suite (OMS) | Microsoft Docs"
description: "Las soluciones de administración amplían la funcionalidad de Operations Management Suite (OMS) proporcionando escenarios de administración empaquetados que los clientes pueden agregar a su área de trabajo de OMS.  En este artículo se proporciona información sobre cómo crear soluciones de administración que se pueden usar en su propio entorno o ponerse a disposición de sus clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee3462c13101d18921dc488b08c79e1e4e02ff3a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="creating-a-management-solution-file-in-operations-management-suite-oms-preview"></a>Creación de archivos de solución de administración en Operations Management Suite (OMS) (versión preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.  

Las soluciones de administración en Operations Management Suite (OMS) se implementan como [plantillas de Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).  La tarea principal para aprender a crear soluciones de administración es saber cómo [crear una plantilla](../azure-resource-manager/resource-group-authoring-templates.md).  En este artículo se proporcionan detalles únicos de plantillas que se usan para soluciones y sobre cómo configurar recursos de solución típicos.


## <a name="tools"></a>Herramientas

Puede usar cualquier editor de texto para trabajar con archivos de solución, pero se recomienda aprovechar las características proporcionadas en Visual Studio o Visual Studio Code como se describe en los siguientes artículos.

- [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Trabajo con plantillas de Azure Resource Manager en Visual Studio Code](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>sección Estructura
La estructura básica de un archivo de una solución de administración, que se muestra a continuación, es la misma que la de una [plantilla de Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format).  En cada una de las siguientes secciones se describen los elementos de nivel superior y su contenido en una solución.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parámetros
Los [parámetros](../azure-resource-manager/resource-group-authoring-templates.md#parameters) son valores que el usuario le debe proporcionar al instalar la solución de administración.  Hay parámetros estándar que tendrán todas las soluciones, y puede agregar parámetros adicionales según sea necesario para su solución particular.  La manera en que los usuarios proporcionarán valores de parámetros al instalar la solución dependerá del parámetro particular y de cómo se instala la solución.

Cuando un usuario instala su solución de administración mediante plantillas de [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) o [de inicio rápido de Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), se le pide que seleccione un [área de trabajo de OMS y una cuenta de Automation](operations-management-suite-solutions.md#oms-workspace-and-automation-account).  Estos se usan para rellenar los valores de cada uno de los parámetros estándar.  Al usuario no se le pide que proporcione valores directamente para los parámetros estándar, pero se le pide que proporcione valores para cualquier parámetro adicional.

Cuando el usuario instala la solución [otro método](operations-management-suite-solutions.md#finding-and-installing-management-solutions), debe proporcionar un valor para todos los parámetros estándar y todos los parámetros adicionales.

A continuación se muestra un parámetro de ejemplo.  

    "startTime": {
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
| solutionName |string |Nombre de la solución.  Si va a implementar la solución a través de plantillas de inicio rápido, debe definir solutionName como parámetro para poder definir una cadena en lugar de pedir al usuario que especifique una. |
| workspaceName |string |Nombre del área de trabajo de Log Analytics. |
| workspaceRegionId |string |Región del área de trabajo de Log Analytics. |


Esta es la estructura de los parámetros estándar que puede copiar y pegar en el archivo de solución.  

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
        }
    }


Consulte los valores de parámetro de otros elementos de la solución con la sintaxis **parameters('nombre de parámetro')**.  Por ejemplo, para tener acceso al nombre de área de trabajo, use **parameters('workspaceName')**

## <a name="variables"></a>Variables
[Variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) son valores que usará en el resto de la solución de administración.  Estos valores no se exponen al usuario que instala la solución.  Están destinados a proporcionar al creador una única ubicación donde pueda administrar los valores que pueden utilizarse varias veces a lo largo de la solución. Debe colocar los valores específicos para su solución en variables en lugar de codificarlos de forma rígida en el elemento **resources**.  De este modo, el código es más legible y los valores se pueden cambiar fácilmente en versiones posteriores.

A continuación se muestra un ejemplo del elemento **variables** con parámetros típicos usados en las soluciones.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Consulte los valores de las variables a través de la solución con la sintaxis **variables('nombre de variable')**.  Por ejemplo, para tener acceso a la variable SolutionName, se usaría **variables('SolutionName')**.

También puede definir variables complejas en varios conjuntos de valores.  Estas son especialmente útiles en soluciones de administración cuando se definen varias propiedades para diferentes tipos de recursos.  Por ejemplo, puede reestructurar las variables de solución mostradas anteriormente a la siguiente.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

En este caso, consulte los valores de las variables a través de la solución con la sintaxis **variables('nombre de variable').property**.  Por ejemplo, para tener acceso a la variable SolutionName, se usaría **variables('Solution').Name**.

## <a name="resources"></a>Recursos
[Recursos](../azure-resource-manager/resource-group-authoring-templates.md#resources) define los diferentes recursos que instalará y configurará la solución de administración.  Se trata de la parte más grande y compleja de la plantilla.  Puede obtener la estructura y la descripción completa de los elementos de recurso en [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Se detallan distintos recursos que se definirán normalmente en otros artículos en esta documentación. 


### <a name="dependencies"></a>Dependencias
El elemento **dependsOn** especifica una [dependencia](../azure-resource-manager/resource-group-define-dependencies.md) en otro recurso.  Cuando se instala la solución, un recurso no se crea hasta que no se hayan creado todas sus dependencias.  Por ejemplo, puede que su solución [inicie un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) cuando se instala mediante un [recurso de trabajo](operations-management-suite-solutions-resources-automation.md#automation-jobs).  El recurso de trabajo será dependiente del recurso de runbook para asegurarse de que el runbook se crea antes de que se cree el trabajo.

### <a name="oms-workspace-and-automation-account"></a>Área de trabajo de OMS y cuenta de Automation
La mayoría de las soluciones requieren que un [área de trabajo de OMS](../log-analytics/log-analytics-manage-access.md) contenga vistas y que una [cuenta de Automation](../automation/automation-security-overview.md#automation-account-overview) contenga runbooks y recursos relacionados.  Estos deben estar disponibles antes de que se creen los recursos de la solución y no se deben definir en la propia solución.  El usuario [especificará un área de trabajo y una cuenta](operations-management-suite-solutions.md#oms-workspace-and-automation-account) al implementar la solución, pero usted, como autor, debe tener en cuenta los siguientes puntos.

## <a name="solution-resource"></a>Recursos de solución
Cada solución requiere una entrada de recursos en el elemento **resources** que define la propia solución.  Esto tendrá un tipo de **Microsoft.OperationsManagement/solutions** y tener la siguiente estructura. Esto incluye [parámetros estándar](#parameters) y [variables](#variables) que se usan normalmente para definir las propiedades de la solución.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspacename'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Dependencias
El recurso de la solución debe tener un [dependencia](../azure-resource-manager/resource-group-define-dependencies.md) en todos los recursos de la solución, ya que estos deben existir antes de que se cree la solución.  Para ello, agregue una entrada para cada recurso en el elemento **dependsOn**.

### <a name="properties"></a>Propiedades
Este recurso de la solución tiene las propiedades de la tabla siguiente.  Esto incluye los recursos a los que hace referencia la solución y contenidos en ella, que define cómo se administra el recurso después de instalar la solución.  Cada recurso de la solución debe aparecer en una de las propiedades **referencedResources** o **containedResources**.

| Propiedad | Descripción |
|:--- |:--- |
| workspaceResourceId |Identificador del área de trabajo de Log Analytics en el formulario *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nombre del espacio de trabajo\>*. |
| referencedResources |Lista de recursos de la solución que no se deben quitar cuando se quita la solución. |
| containedResources |Lista de recursos de la solución que debe quitarse cuando se quita la solución. |

El ejemplo anterior es para una solución con un runbook, una programación y una vista.  Se hace *referencia a la programación y al runbook* en el elemento **properties** de modo que no se quitan cuando se quita la solución.  La vista está *contenida*, por lo que se quita cuando se quita la solución.

### <a name="plan"></a>Plan
La entidad **plan** del recurso de la solución tiene las propiedades en la tabla siguiente.

| Propiedad | Description |
|:--- |:--- |
| name |Nombre de la solución. |
| versión |Versión de la solución según determine el autor. |
| product |Cadena única para identificar la solución. |
| publisher |Publicador de la solución. |



## <a name="sample"></a>Muestra
Puede ver ejemplos de archivos de solución con un recurso de la solución en las siguientes ubicaciones.

- [Recursos de Automation](operations-management-suite-solutions-resources-automation.md#sample)
- [Recursos de búsqueda y alerta](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Pasos siguientes
* [Incorporación de búsquedas y alertas guardadas](operations-management-suite-solutions-resources-searches-alerts.md) a la solución de administración.
* [Incorporación de vistas](operations-management-suite-solutions-resources-views.md) a la solución de administración.
* [Incorporación de runbooks de y otros recursos Automation](operations-management-suite-solutions-resources-automation.md) a la solución de administración.
* Obtenga más información en [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Búsqueda de [plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates) para obtener ejemplos de diferentes plantillas de Resource Manager.
