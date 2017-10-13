---
title: "Implementación de un área de trabajo de Machine Learning mediante Azure Resource Manager | Microsoft Docs"
description: "Cómo implementar un área de trabajo de Machine Learning de Azure mediante la plantilla de Azure Resource Manager"
services: machine-learning
documentationcenter: 
author: ahgyger
manager: haining
editor: garye
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/15/2017
ms.author: ahgyger
ms.openlocfilehash: e3cbcb8118aa05e554b2493506280d0e24706059
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Implementación del área de trabajo de Machine Learning mediante Azure Resource Manager
## <a name="introduction"></a>Introducción
El uso de una plantilla de implementación de Azure Resource Manager le permite ahorrar tiempo proporcionándole una manera escalable de implementar componentes interconectados con un mecanismo de validación y reintento. Para configurar áreas de trabajo de Azure Machine Learning, por ejemplo, debe configurar primero una cuenta de Almacenamiento de Azure y, a continuación, implementar el área de trabajo. Imagine que tiene que hacer esto manualmente para cientos de áreas de trabajo. Una alternativa más sencilla es utilizar una plantilla de Azure Resource Manager para implementar un área de trabajo de Azure Machine Learning y todas sus dependencias. Este artículo le guiará por este proceso paso a paso. Para ver una introducción excelente sobre Azure Resource Manager, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Paso a paso: Creación de un área de trabajo de Machine Learning
Crearemos un grupo de recursos de Azure e implementaremos una nueva cuenta de Almacenamiento de Azure y una nueva área de trabajo de Azure Machine Learning mediante una plantilla de Resource Manager. Una vez completada la implementación, se imprimirá información importante acerca de las áreas de trabajo que se crearon (la clave principal, el identificador y la dirección URL del área de trabajo).

### <a name="create-an-azure-resource-manager-template"></a>Creación de una plantilla de Azure Resource Manager
Un área de trabajo de Machine Learning requiere una cuenta de almacenamiento de Azure para almacenar el conjunto de datos vinculado a ella.
La siguiente plantilla usa el nombre del grupo de recursos para generar el nombre de la cuenta de almacenamiento y el nombre del área de trabajo.  También utiliza el nombre de la cuenta de almacenamiento como una propiedad al crear el área de trabajo.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Guarde esta plantilla como archivo mlworkspace.json en c:\temp.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementación del grupo de recursos basado en la plantilla
* Abra PowerShell
* Instale los módulos de Azure Resource Manager y Azure Service Management  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estos pasos permiten descargar e instalar los módulos necesarios para completar los pasos restantes. Esto solo debe realizarse una vez en el entorno donde se ejecutan los comandos de PowerShell.   

* Autentíquese en Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Este paso debe repetirse para cada sesión. Una vez autenticado, aparecerá la información de la suscripción.

![Cuenta de Azure][1]

Ahora que tenemos acceso a Azure, podemos crear el grupo de recursos.

* Crear un grupo de recursos

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Compruebe que se ha aprovisionado correctamente el grupo de recursos. **ProvisioningState** debe ser "Succeeded" (Correcto).
La plantilla utiliza el nombre del grupo de recursos para generar el nombre de la cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe tener entre 3 y 24 caracteres, y usar solo números y letras minúsculas.

![El grupos de recursos][2]

* Mediante la implementación del grupo de recursos, implemente una nueva área de trabajo de Machine Learning.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Una vez completada la implementación, resulta sencillo tener acceso a las propiedades del área de trabajo que implementó. Por ejemplo, puede tener acceso al token de clave principal.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Otra manera de recuperar los tokens del área de trabajo existente es usar el comando Invoke-AzureRmResourceAction. Por ejemplo, puede enumerar los tokens principales y secundarios de todas las áreas de trabajo.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Una vez aprovisionada el área de trabajo, también puede automatizar muchas tareas de Estudio de aprendizaje automático de Microsoft Azure mediante el [módulo de PowerShell para Aprendizaje automático de Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [creación de plantillas de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Eche un vistazo al [repositorio de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates). 
* Vea este vídeo acerca de [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
