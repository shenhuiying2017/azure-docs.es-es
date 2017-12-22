---
title: "Implementación de recursos con la API de REST y plantilla | Microsoft Docs"
description: Use Azure Resource Manager y la API de REST de Resource Manager para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
ms.openlocfilehash: 46856a25fb57bb2c5a3c1aeae13c11655e1a58a5
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementación de recursos con las plantillas de Resource Manager y la API de REST de Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI de Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API DE REST](resource-group-template-deploy-rest.md)
> 
> 

En este artículo se explica cómo usar la API de REST de Resource Manager con plantillas de Resource Manager para implementar sus recursos en Azure.  

> [!TIP]
> Para obtener ayuda con la depuración de un error durante la implementación, consulte:
> 
> * [Visualización de operaciones de implementación](resource-manager-deployment-operations.md) para obtener información que lo ayude a solucionar el error
> * [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación
> 
> 

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Implementación con la API de REST
1. Establezca los [encabezados y parámetros comunes](https://docs.microsoft.com/rest/api/index), incluidos los tokens de autenticación.
2. Si no tiene un grupo de recursos existente, puede crear uno. Especifique el identificador de la suscripción, el nombre del nuevo grupo de recursos y la ubicación que necesita para la solución. Para obtener más información, consulte [Crear un grupo de recursos](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate).
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. Valide la implementación antes de ejecutarla. Para ello, ejecute la operación de [validación de una implementación de plantilla](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate). Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).
4. Cree una implementación. Especifique el identificador de suscripción, el nombre del grupo de recursos, el nombre de la implementación y un vínculo a su plantilla. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](#parameter-file). Para obtener más información acerca de la API de REST para crear un grupo de recursos, consulte [Creación de una implementación de plantilla](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Observe que el **modo** se establece en **Incremental**. Para ejecutar una implementación completa, establezca el **modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0"
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0"
              }
            }
          }
   
      Si desea registrar el contenido de la respuesta y el de la solicitud, o ambos, incluya **debugSetting** en la solicitud.
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      Puede configurar la cuenta de almacenamiento para utilizar un token de firma de acceso compartido (SAS). Para obtener más información, consulte [Delegating Access with a Shared Access Signature](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)(Delegación del acceso con una firma de acceso compartido).
5. Obtenga el estado de la implementación de la plantilla. Para obtener más información, consulte [Obtener información acerca de una implementación de plantilla](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>Archivo de parámetros

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre el control de operaciones asincrónicas de REST, vea [Seguimiento de las operaciones asincrónicas de Azure](resource-manager-async-operations.md).
* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

