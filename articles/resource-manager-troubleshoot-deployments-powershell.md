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
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# Visualización de operaciones de implementación con Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Puede ver las operaciones de una implementación mediante Azure PowerShell. Es posible que le resulte más interesante ver las operaciones si ha recibido un error durante la implementación; por ello, este artículo se centrará en la visualización de las operaciones en las que se han producido errores. PowerShell proporciona cmdlets que permiten buscar los errores y determinar posibles correcciones con facilidad.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Puede evitar algunos errores si valida la plantilla y la infraestructura antes de la implementación. También puede registrar información adicional sobre solicitudes y respuestas durante la implementación que podría resultarle de ayuda más adelante para solucionar los problemas. Para obtener más información sobre la validación, así como del registro de la información de solicitudes y respuestas, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy.md) (Implementación de un grupo de recursos con la plantilla de Azure Resource Manager).

## Uso de operaciones de implementación para solucionar problemas

1. Para obtener el estado general de una implementación, use el comando **Get-AzureRmResourceGroupDeployment**. Puede filtrar los resultados para obtener únicamente las implementaciones en las que se han producido errores.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Que devuelve las implementaciones erróneas en el formato siguiente:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Cada implementación normalmente se compone de varias operaciones y cada operación representa un paso en el proceso de implementación. Para detectar qué salió mal con una implementación, normalmente es necesario ver los detalles de las operaciones de implementación. Puede ver el estado de las operaciones con **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Que devuelve varias operaciones con cada una de ellas en el formato siguiente:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Para más información acerca de las operaciones con errores, recupere las propiedades de aquellas operaciones con el estado **Error**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Que devuelve todas las operaciones con error con cada una de ellas en el formato siguiente:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Anote el identificador de seguimiento de la operación. Lo utilizará en el paso siguiente para centrarse en una operación determinada.

4. Para obtener el mensaje de estado de una operación con error determinada, use el siguiente comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Que devuelve:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## Uso de registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver los errores de una implementación, siga estos pasos:

1. Para recuperar las entradas de registro, ejecute el comando **Get-AzureRmLog**. Puede usar los parámetros **ResourceGroup** y **Status** para devolver solo los eventos que produjeron errores en un único grupo de recursos. Si no especifica una hora de inicio y de finalización, se devuelven las entradas de la última hora. Por ejemplo, para recuperar las operaciones con errores de la última hora, ejecute lo siguiente:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Puede especificar un intervalo de tiempo determinado. En el ejemplo siguiente, buscaremos las acciones con errores del día anterior.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    También puede establecer una hora de inicio y de finalización para las acciones con errores:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si este comando devuelve demasiadas entradas y propiedades, puede centrarse en sus esfuerzos de auditoría recuperando la propiedad **Properties**. También incluiremos el parámetro **DetailedOutput** para ver los mensajes de error.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Que devuelve las propiedades de las entradas de registro en el siguiente formato:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. En función de estos resultados, vamos a centrarnos en el segundo elemento. Puede restringir los resultados examinando el mensaje de estado para esa entrada.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Que devuelve:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación concretos, consulte [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para más información acerca de cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
- Para validar la implementación antes de ejecutarla, consulte [Implementación de recursos con plantillas de Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->