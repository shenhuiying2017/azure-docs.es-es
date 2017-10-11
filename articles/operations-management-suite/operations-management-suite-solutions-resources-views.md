---
title: "Vistas de las soluciones de administración en Operations Management Suite (OMS) | Microsoft Docs"
description: "Normalmente, las soluciones de administración en Operations Management Suite (OMS) incluyen una o más vistas para visualizar los datos.  En este artículo se describe cómo exportar una vista creada por el Diseñador de vistas e incluirla en una solución de administración. "
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 533b5564a805e0b41f2b1a4ad92e12b133220952
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Vistas de las soluciones de administración en Operations Management Suite (OMS) (versión preliminar)
> [!NOTE]
> La versión de la documentación para crear soluciones de administración de OMS está actualmente en fase preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.    
>
>

[Normalmente, las soluciones de administración en Operations Management Suite (OMS)](operations-management-suite-solutions.md) incluyen una o más vistas para visualizar los datos.  En este artículo se describe cómo exportar una vista creada por el [Diseñador de vistas](../log-analytics/log-analytics-view-designer.md) e incluirla en una solución de administración.  

> [!NOTE]
> En los ejemplos de este artículo se usan parámetros y variables que son necesarios o comunes para las soluciones de administración y se describen en [Creating management solutions in Operations Management Suite (OMS) (Creación de soluciones de administración en Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md).
>
>

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con la manera de [crear una solución de administración](operations-management-suite-solutions-creating.md) y la estructura de un archivo de solución.

## <a name="overview"></a>Información general
Para incluir una vista en una solución de administración, se crea un **recurso** para ella en el [archivo de solución](operations-management-suite-solutions-creating.md).  Sin embargo, el JSON que describe la configuración detallada de la vista detallada suele ser complejo y no algo que el autor de una solución típica podría crear manualmente.  El método más común es crear la vista mediante el [Diseñador de vistas](../log-analytics/log-analytics-view-designer.md), exportarla y luego agregar su configuración detallada a la solución.

Los pasos básicos para agregar una vista a una solución son los siguientes.  Cada uno de los pasos se describe en detalle en las secciones que aparecen a continuación.

1. Exportar la vista a un archivo.
2. Crear el recurso de la vista en la solución.
3. Agregue los detalles de la vista.

## <a name="export-the-view-to-a-file"></a>Exportar la vista a un archivo
Siga las instrucciones del [Diseñador de vistas de Log Analytics](../log-analytics/log-analytics-view-designer.md) para exportar una vista a un archivo.  El archivo exportado tendrá el formato JSON con los mismo [elementos que el archivo de solución](operations-management-suite-solutions-solution-file.md).  

El elemento **resources** de la vista tendrá un recurso con un tipo de **Microsoft.OperationalInsights/workspaces** que representa el área de trabajo de OMS.  Este elemento tendrá un subelemento con un tipo de **vistas** que representa la vista y contiene su configuración detallada.  Copiará los detalles de este elemento y luego copiará el elemento en la solución.

## <a name="create-the-view-resource-in-the-solution"></a>Crear el recurso de la vista en la solución
Agregue el siguiente recurso de vista al elemento **resources** del archivo de solución.  Usa las variables que se describen a continuación y que también debe agregar.  Tenga en cuenta que las propiedades **Dashboard** y **OverviewTile** son marcadores de posición que sobrescribirá con las propiedades correspondientes desde el archivo de vista exportado.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Agregue las siguientes variables al elemento variables del archivo de solución y reemplace los valores por los de la solución.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Tenga en cuenta que podría copiar todo el recurso de vista desde el archivo de vista exportado, pero necesitaría realizar los siguientes cambios para que funcionara en su solución.  

* El **tipo** del recurso de vista debe cambiar de **vistas** a **Microsoft.OperationalInsights/workspaces**.
* La propiedad **name** del recurso de vista se debe cambiar para incluir el nombre del área de trabajo.
* La dependencia en el área de trabajo debe quitarse porque el recurso de área de trabajo no está definido en la solución.
* La propiedad **DisplayName** se debe agregar a la vista.  Los valores **Id**, **Name** y **DisplayName** deben coincidir.
* Los nombres de parámetro deben cambiarse para coincidir con el conjunto de parámetros necesario.
* Las variables deben definirse en la solución y usarse en las propiedades adecuadas.

## <a name="add-the-view-details"></a>Agregar los detalles de la vista
El recurso de vista del archivo de vista exportado contendrá dos elementos en el elemento **properties** denominado **Dashboard** y **OverviewTile** que contienen la configuración detallada de la vista.  Copie los dos elementos y su contenido en el elemento **propiedades** del recurso de vista del archivo de solución.

## <a name="example"></a>Ejemplo
Por ejemplo, en el ejemplo siguiente se muestra un archivo de solución simple con una vista.  Los puntos suspensivos (...) se muestran para el contenido **Dashboard** y **OverviewTile** por motivos de espacio.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Pasos siguientes
* Obtener información detallada sobre cómo crear [soluciones de administración](operations-management-suite-solutions-creating.md).
* Incluir [runbooks de Automation en la solución de administración](operations-management-suite-solutions-resources-automation.md).
