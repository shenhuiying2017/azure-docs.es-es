---
title: "Operaciones de implementación con Azure Resource Manager | Microsoft Docs"
description: "Se describe cómo ver las operaciones de implementación de Azure Resource Manager con el portal, PowerShell, CLI de Azure y API de REST."
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: tomfitz
ms.openlocfilehash: fb6b3b357fd1f66184e480115a9c863ba31ac193
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Visualización de operaciones de implementación con Azure Resource Manager


Puede ver las operaciones de una implementación mediante el Portal de Azure. Es posible que le resulte más interesante ver las operaciones si ha recibido un error durante la implementación; por ello, este artículo se centrará en la visualización de las operaciones en las que se han producido errores. El portal proporciona una interfaz que permite buscar los errores y determinar posibles correcciones con facilidad.

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="portal"></a>Portal
Para ver las operaciones de implementación, siga estos pasos:

1. En el grupo de recursos implicado en la implementación, examine el estado de la última implementación. Puede seleccionar este estado para obtener más detalles.
   
    ![Estado de la implementación](./media/resource-manager-deployment-operations/deployment-status.png)
2. Ve el historial de implementación reciente. Seleccione la implementación en la que se produjo un error.
   
    ![Estado de la implementación](./media/resource-manager-deployment-operations/select-deployment.png)
3. Seleccione el vínculo para ver una descripción del motivo del error de la implementación. En la imagen siguiente, el registro de DNS no es único.  
   
    ![ver la implementación con errores](./media/resource-manager-deployment-operations/view-error.png)
   
    Este mensaje de error debería ser suficiente para que comience la solución de problemas. Sin embargo, si necesita más detalles acerca de las tareas que se han completado, puede ver las operaciones como se muestra en los pasos siguientes.
4. Puede ver todas las operaciones de implementación en la hoja **Implementación** . Seleccione la operación que desea ver con mayor detalle.
   
    ![ver operaciones](./media/resource-manager-deployment-operations/view-operations.png)
   
    En este caso, puede ver que la cuenta de almacenamiento, la red virtual y el conjunto de disponibilidad se crearon correctamente. Se produjo un error en la dirección IP pública y no se han intentado otros recursos.
5. Puede ver los eventos de la implementación seleccionando **Eventos**.
   
    ![ver eventos](./media/resource-manager-deployment-operations/view-events.png)
6. Puede ver todos los eventos de la implementación y seleccionar cualquiera de ellos para más información. Observe también los identificadores de correlación. Este valor puede resultar útil al trabajar con el soporte técnico para solucionar un problema de implementación.
   
    ![consultar eventos](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Para obtener el estado general de una implementación, use el comando **Get-AzureRmResourceGroupDeployment** . 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   O bien, puede filtrar los resultados para obtener únicamente las implementaciones en las que se han producido errores.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Cada implementación incluye varias operaciones. Cada operación representa un paso del proceso de implementación. Para detectar qué salió mal con una implementación, normalmente es necesario ver los detalles de las operaciones de implementación. Puede ver el estado de las operaciones con **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Que devuelve varias operaciones con cada una de ellas en el formato siguiente:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Para más información acerca de las operaciones con errores, recupere las propiedades de aquellas operaciones con el estado **Error** .

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Que devuelve todas las operaciones con error con cada una de ellas en el formato siguiente:

  ```powershell
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
  ```

    Tenga en cuenta los valores serviceRequestId y trackingId de la operación. serviceRequestId puede resultar útil cuando se trabaja con el soporte técnico para solucionar un problema de implementación. Utilizará trackingId en el paso siguiente para centrarse en una operación determinada.
4. Para obtener el mensaje de estado de una operación con error determinada, use el siguiente comando:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Que devuelve:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Cada operación de implementación en Azure incluye el contenido de la solicitud y de la respuesta. El contenido de la solicitud es lo que se envía a Azure durante la implementación (por ejemplo, crear una máquina virtual, un disco del sistema operativo y otros recursos). El contenido de la respuesta es lo que Azure devolvió de la solicitud de implementación. Durante la implementación, se puede usar el parámetro **DeploymentDebugLogLevel** para especificar que la solicitud o la respuesta se conserven en el registro. 

  Esa información se obtiene del registro y se guarda localmente mediante los siguientes comandos de PowerShell:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Obtenga el estado general de una implementación con el comando **azure group deployment show** .

  ```azurecli
  azure group deployment show --resource-group ExampleGroup --name ExampleDeployment --json
  ```
  
  Uno de los valores devueltos es **correlationId**. Este valor se usa para realizar un seguimiento de eventos relacionados, y puede ser útil al colaborar con el soporte técnico para solucionar un problema de implementación.

  ```azurecli
  "properties": {
    "provisioningState": "Failed",
    "correlationId": "4002062a-a506-4b5e-aaba-4147036b771a",
  ```

2. Para ver las operaciones para una implementación, use:

  ```azurecli
  azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json
  ```

## <a name="rest"></a>REST

1. Obtenga información sobre la implementación con la operación [Obtener información sobre una implementación de plantilla](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) .

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    En la respuesta, observe en particular los elementos **provisioningState**, **correlationId** y **error**. **correlationId** se usa para realizar un seguimiento de eventos relacionados y puede ser útil al colaborar con el soporte técnico para solucionar un problema de implementación.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Obtenga información sobre las operaciones de implementación con la operación [Enumerar todas las operaciones de implementación de plantilla](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List) . 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    La respuesta incluye información de la solicitud o la respuesta según lo especificado en la propiedad **debugSetting** durante la implementación.

  ```json
  {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
  }
  ```


## <a name="next-steps"></a>Pasos siguientes
* Para obtener ayuda con la resolución de errores de implementación concretos, consulte [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obtener información sobre cómo usar los registros de actividad para supervisar otros tipos de acciones, vea [View activity logs to manage Azure resources](resource-group-audit.md) (Ver registros de actividad para administrar recursos de Azure).
* Para validar la implementación antes de ejecutarla, consulte [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](resource-group-template-deploy.md).

