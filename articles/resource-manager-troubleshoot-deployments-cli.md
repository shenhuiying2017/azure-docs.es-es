<properties
   pageTitle="Visualización de operaciones de implementación con la CLI de Azure | Microsoft Azure"
   description="Describe cómo usar la CLI de Azure para detectar los problemas de la implementación de Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Visualización de operaciones de implementación con la CLI de Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI de Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API DE REST](resource-manager-troubleshoot-deployments-rest.md)

Si recibe un error al implementar recursos en Azure, quizás desee conocer más detalles acerca de las operaciones de implementación ejecutadas. La CLI de Azure proporciona comandos que permiten buscar los errores y determinar las posibles correcciones.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Puede evitar algunos errores si valida la plantilla y la infraestructura antes de la implementación. También puede registrar información adicional sobre solicitudes y respuestas durante la implementación que podría resultarle de ayuda más adelante para solucionar los problemas. Para obtener más información sobre la validación, así como del registro de la información de solicitudes y respuestas, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy-cli.md) (Implementación de un grupo de recursos con la plantilla de Azure Resource Manager).

## Uso de registros de auditoría para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver los errores de una implementación, siga estos pasos:

1. Para ver los registros de auditoría, ejecute el comando **azure group log show**. Puede incluir la opción **--last- deployment** para recuperar solo el registro de la implementación más reciente.

        azure group log show ExampleGroup --last-deployment

2. El comando **azure group log show** puede devolver mucha información. Para solucionar problemas, lo habitual es centrarse en las operaciones que produjeron un error. El script siguiente usa la opción **--json** y la utilidad [jq](https://stedolan.github.io/jq/) de JSON para buscar en el registro errores de implementación.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
            mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
            "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
        },
        ...

    Puede ver que **properties** incluye información de json sobre la operación con error.

    Puede usar las opciones **--verbose** y **-vv** para ver más información de los registros. Use la opción **--verbose** para mostrar los pasos que recorren las operaciones en `stdout`. Para ver un historial completo de la solicitud, use la opción **-vv**. Los mensajes suelen ofrecer pistas fundamentales sobre la causa de cualquier error.

3. Para centrarse en el mensaje de estado de las entradas con errores, use el siguiente comando:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## Uso de operaciones de implementación para solucionar problemas

1. Obtenga el estado general de una implementación con el comando **azure group deployment show**. En el ejemplo siguiente la implementación produjo un error.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Para ver el mensaje de las operaciones con errores de una implementación, use:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## Pasos siguientes

- Para obtener ayuda con la resolución de errores de implementación concretos, consulte [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para obtener más información acerca de cómo usar los registros de auditoría para supervisar otros tipos de acciones, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
- Para validar la implementación antes de ejecutarla, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy.md) (Implementación de un grupo de recursos con la plantilla de Azure Resource Manager).

<!---HONumber=AcomDC_0615_2016-->