<properties
	pageTitle="Operaciones de auditoría con el Administrador de recursos | Microsoft Azure"
	description="Use el registro de auditoría en el Administrador de recursos para revisar los errores y las acciones del usuario. Muestra PowerShell del Portal de Azure, CLI de Azure CLI y REST."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="tomfitz"/>

# Operaciones de auditoría con el Administrador de recursos

A través de los registros de auditoría puede determinar:

- qué operaciones se tomaron en los recursos en la suscripción
- quién inició la operación (aunque las operaciones iniciadas por un servicio back-end no devuelven un usuario como el autor de la llamada)
- cuándo tuvo lugar la operación
- el estado de la operación
- los valores de otras propiedades que podrían ayudarle en la investigación de la operación

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Este tema se centra en operaciones de auditoría. Para más información sobre el uso de registros de auditoría para la solución de problemas de una implementación, consulte [Troubleshooting resource group deployments in Azure (Solución de problemas de implementaciones de grupos de recursos en Azure)](resource-manager-troubleshoot-deployments-portal.md).

Puede recuperar información de los registros de auditoría a través del Portal de Azure, Azure PowerShell, CLI de Azure, API de REST de Insights o la [biblioteca .NET de Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Portal para ver registros de auditoría

1. Vea los registros de auditoría a través del portal, seleccione **Examinar** y **Registros de auditoría**.

    ![seleccionar registros de auditoría](./media/resource-group-audit/select-audit-logs.png)

2. En la hoja **Registros de auditoría**, verá un resumen de las operaciones recientes de todos los grupos de recursos de su suscripción. Incluye una representación gráfica del tiempo y el estado de las operaciones, así como una lista de las operaciones.

    ![mostrar acciones](./media/resource-group-audit/audit-summary.png)

3. Para buscar un tipo de acción determinado, puede filtrar las operaciones que se muestran en la hoja de registros de auditoría. Seleccione **Filtro** en la parte superior de la hoja.

    ![filtrar registros](./media/resource-group-audit/filter-logs.png)

4. En la hoja **Filtro**, puede seleccionar muchas condiciones diferentes para restringir el número de operaciones que se muestran. Por ejemplo, puede ver todas las acciones realizadas por un usuario concreto de la semana pasada.

    ![establecer opciones de filtro](./media/resource-group-audit/set-filter.png)

Después de actualizar la vista del registro de auditoría, solo verá las operaciones que cumplan la condición especificada. Estas configuraciones se conservarán la próxima vez que vea los registros de auditoría, por lo que podría tener que cambiar esos valores para ampliar la vista de las operaciones.

También puede filtrar automáticamente un recurso determinado seleccionando registros de auditoría desde esa hoja de recursos. En el portal, seleccione el recurso que desea auditar y seleccione **Registros de auditoría**.

![recurso de auditoría](./media/resource-group-audit/audit-by-resource.png)

Observe que el registro de auditoría se filtra automáticamente por el recurso seleccionado de la semana pasada.

![filtrar por recurso](./media/resource-group-audit/filtered-by-resource.png)

## PowerShell para ver registros de auditoría

1. Para recuperar las entradas de registro, ejecute el comando **Get-AzureRmLog**. Ofrezca parámetros adicionales para filtrar la lista de entradas. Si no especifica una hora de inicio y de finalización, se devuelven las entradas de la última hora. Por ejemplo, para recuperar las operaciones de un grupo de recursos durante la última hora ejecute:

        Get-AzureRmLog -ResourceGroup ExampleGroup

    En el ejemplo siguiente se muestra cómo usar el registro de auditoría para investigar operaciones llevadas a cabo durante un tiempo especificado. Las fechas inicial y final se especifican en un formato de fecha.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    También puede usar funciones de fecha para especificar el intervalo de fechas, como los últimos 14 días.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. En función de la hora de inicio que especifique, los comandos anteriores pueden devolver una lista larga de operaciones para el grupo de recursos. Puede filtrar los resultados para lo que busca ofreciendo criterios de búsqueda. Por ejemplo, si trata de investigar cómo se detuvo una aplicación web, podría ejecutar el siguiente comando.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
    En este ejemplo se muestra que someone@contoso.com ha realizado una acción de detención.
        
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK

3. Puede buscar las acciones realizadas por un usuario determinado, incluso para un grupo de recursos que ya no existe.

        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## CLI de Azure para ver registros de auditoría

1. Para recuperar las entradas del registro, ejecute el comando **azure group log show**.

        azure group log show ExampleGroup

2. Puede filtrar los resultados con una utilidad de JSON como [jq](http://stedolan.github.io/jq/download/). En el ejemplo siguiente se muestra cómo buscar operaciones que actualizaron un archivo de configuración web.

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. Puede buscar las acciones para un usuario determinado.

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## API de REST para ver registros de auditoría

Las operaciones REST para trabajar con el registro de auditoría forman parte de la [API de REST de Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar los eventos de registro de auditoría, vea [Lista de los eventos de administración de una suscripción](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Pasos siguientes

- Los registros de auditoría de Azure se pueden usar con Power BI para obtener mayor información sobre las acciones en su suscripción. Consulte [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Consulta y análisis de registros de auditoría de Azure en Power BI y más).
- Para información sobre cómo establecer directivas de seguridad, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).
- Para obtener más información sobre los comandos para la solución de problemas en implementaciones, consulte [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](resource-manager-troubleshoot-deployments-portal.md).
- Para aprender a evitar eliminaciones en un recurso para todos los usuarios, vea [Bloqueo de recursos con Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0615_2016-->