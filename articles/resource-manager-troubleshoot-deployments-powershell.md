<properties
   pageTitle="Visualización de operaciones de implementación con PowerShell | Microsoft Azure"
   description="Describe cómo usar Azure PowerShell para detectar los problemas de la implementación de Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="05/19/2016"
   ms.author="tomfitz"/>

# Visualización de operaciones de implementación con Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Si recibe un error al implementar recursos en Azure, quizás desee conocer más detalles acerca de las operaciones de implementación ejecutadas. Azure PowerShell proporciona cmdlets que permiten buscar los errores y determinar posibles correcciones con facilidad.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Puede evitar algunos errores si valida la plantilla y la infraestructura antes de la implementación. También puede registrar información adicional sobre solicitudes y respuestas durante la implementación que podría resultarle de ayuda más adelante para solucionar los problemas. Para obtener más información sobre la validación, así como del registro de la información de solicitudes y respuestas, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy.md) (Implementación de un grupo de recursos con la plantilla de Azure Resource Manager).

## Uso de registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver los errores de una implementación, siga estos pasos:

1. Para recuperar las entradas de registro, ejecute el comando **Get-AzureRmLog**. Puede usar los parámetros **ResourceGroup** y **Status** para devolver solo los eventos que produjeron errores en un único grupo de recursos. Si no especifica una hora de inicio y de finalización, se devuelven las entradas de la última hora. Por ejemplo, para recuperar las operaciones con errores de la última hora, ejecute lo siguiente:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Puede especificar un intervalo de tiempo determinado. En el ejemplo siguiente, buscaremos las acciones con errores de los últimos 14 días.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    También puede establecer una hora de inicio y de finalización para las acciones con errores:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si este comando devuelve demasiadas entradas y propiedades, puede centrarse en sus esfuerzos de auditoría recuperando la propiedad **Properties**. También incluiremos el parámetro **DetailedOutput** para ver los mensajes de error.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    Que devuelve las propiedades de las entradas de registro en el siguiente formato:
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. Además, puede restringir los resultados examinando el mensaje de estado de una entrada en concreto.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    Que devuelve el mensaje de estado en el formato siguiente:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## Uso de operaciones de implementación para solucionar problemas

1. Para obtener el estado general de una implementación, use el comando **Get-AzureRmResourceGroupDeployment**. Puede filtrar los resultados para obtener únicamente las implementaciones en las que se han producido errores.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Que devuelve las implementaciones erróneas en el formato siguiente:
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. Cada implementación normalmente se compone de varias operaciones y cada operación representa un paso en el proceso de implementación. Para detectar qué salió mal con una implementación, normalmente es necesario ver los detalles de las operaciones de implementación. Puede ver el estado de las operaciones con **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
        
    Que devuelve las operaciones en el formato siguiente:
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. Para obtener más información sobre la operación, recupere el objeto **Properties**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    Que devuelve la propiedades de operación en el formato siguiente:
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. Para centrarse en el mensaje de estado de las operaciones con errores, use el siguiente comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    Que devuelve el mensaje de estado en el formato siguiente:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación concretos, consulte [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para obtener más información acerca de cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
- Para validar la implementación antes de ejecutarla, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy.md) (Implementación de un grupo de recursos con la plantilla de Azure Resource Manager).

<!---HONumber=AcomDC_0615_2016-->