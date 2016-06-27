<properties
   pageTitle="Implementación de recursos con la API de REST y plantilla | Microsoft Azure"
   description="Use Azure Resource Manager y la API de REST de Resource Manager para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/08/2016"
   ms.author="tomfitz"/>

# Implementación de recursos con las plantillas de Resource Manager y la API de REST de Resource Manager

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI de Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [API DE REST](resource-group-template-deploy-rest.md)

En este artículo se explica cómo usar la API de REST de Resource Manager con plantillas de Resource Manager para implementar sus recursos en Azure.

> [AZURE.TIP] Para obtener ayuda con la depuración de un error durante la implementación, consulte:
>
> - [Visualización de operaciones de implementación con la API de REST de Azure Resource Manager](resource-manager-troubleshoot-deployments-rest.md) para obtener información que le ayudará a solucionar el error
> - [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Implementación con la API de REST
1. Establezca los [encabezados y parámetros comunes](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluidos los tokens de autenticación.
2. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el identificador de la suscripción, el nombre del grupo de recursos y la ubicación que necesita para la solución. Para obtener más información, consulte [Crear un grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Valide la implementación antes de ejecutarla. Para ello, ejecute la operación de [validación de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790547.aspx). Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).

3. Cree una nueva implementación. Proporcione el identificador de suscripción, el nombre del grupo de recursos para implementar, el nombre de la implementación y un vínculo a su plantilla. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file). Para obtener más información acerca de la API de REST para crear un grupo de recursos, consulte [Creación de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx). Observe que el **modo** se establece en **Incremental**. Para ejecutar una implementación completa, establezca el **modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Si desea registrar el contenido de la respuesta y el de la solicitud, o ambos, incluya **debugSetting** en la solicitud.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Puede configurar la cuenta de almacenamiento para utilizar un token de firma de acceso compartido (SAS). Para más información, consulte [Delegar el acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obtenga el estado de la implementación de la plantilla. Para obtener más información, consulte [Obtener información acerca de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Pasos siguientes
- Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](virtual-machines/virtual-machines-windows-csharp-template.md) (Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
- Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
- Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
- Para obtener más información sobre el uso de una referencia KeyVault para pasar valores seguros, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0615_2016-->