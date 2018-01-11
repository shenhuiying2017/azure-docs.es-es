---
title: "Visualización de registros de actividad de Azure para supervisar recursos | Microsoft Docs"
description: Use los registros de actividad para revisar las acciones y los errores de los usuarios. Muestra PowerShell de Azure Portal, CLI de Azure CLI y REST.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
ms.openlocfilehash: ecfb7f726d5447710948405b2dd83fcd1db3dff2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Visualización de registros de actividad para auditar las acciones sobre los recursos
Mediante los registros de actividad, puede determinar:

* qué operaciones se realizaron en los recursos en la suscripción
* quién inició la operación (aunque las operaciones iniciadas por un servicio back-end no devuelven un usuario como el autor de la llamada)
* cuándo tuvo lugar la operación
* el estado de la operación
* los valores de otras propiedades que podrían ayudarle en la investigación de la operación

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

Puede recuperar información de los registros de actividad mediante el portal, PowerShell, la CLI de Azure, la API de REST de Insights o la [biblioteca .NET de Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portal
1. Para ver los registros de actividad mediante el portal, seleccione **Monitor**.
   
    ![seleccionar registro de actividad](./media/resource-group-audit/select-monitor.png)

   O bien, para filtrar automáticamente el registro de actividad por un determinado recurso o grupo de recursos, seleccione **Registro de actividad**. Observe que el registro de actividad se filtra automáticamente por el recurso seleccionado.
   
    ![filtrar por recurso](./media/resource-group-audit/filtered-by-resource.png)
2. En **Registro de actividad**, verá un resumen de operaciones recientes.
   
    ![mostrar acciones](./media/resource-group-audit/audit-summary.png)
3. Para restringir el número de operaciones que se muestran, seleccione diferentes condiciones. Por ejemplo, la siguiente imagen muestra los campos **Timespan** y **Evento iniciado por** modificados para ver las acciones llevadas a cabo por un determinado usuario o aplicación durante el mes pasado. Seleccione **Aplicar** para ver los resultados de la consulta.
   
    ![establecer opciones de filtro](./media/resource-group-audit/set-filter.png)

4. Si necesita volver a ejecutar la consulta más tarde, seleccione **Guardar** y asígnele un nombre.
   
    ![guardar consulta](./media/resource-group-audit/save-query.png)
5. Para ejecutar rápidamente una consulta, puede seleccionar una de las consultas integradas, como implementaciones con error.

    ![seleccionar consulta](./media/resource-group-audit/select-quick-query.png)

   La consulta seleccionada establece automáticamente los valores de filtro necesarios.

    ![ver errores de implementación](./media/resource-group-audit/view-failed-deployment.png)   

6. Seleccione una de las operaciones para ver un resumen del evento.

    ![ver operación](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. Para recuperar las entradas de registro, ejecute el comando **Get-AzureRmLog** . Ofrezca parámetros adicionales para filtrar la lista de entradas. Si no especifica una hora de inicio y de finalización, se devuelven las entradas de la última hora. Por ejemplo, para recuperar las operaciones de un grupo de recursos durante la última hora ejecute:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    En el ejemplo siguiente se muestra cómo usar el registro de actividad para investigar operaciones llevadas a cabo durante un tiempo especificado. Las fechas inicial y final se especifican en un formato de fecha.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    También puede usar funciones de fecha para especificar el intervalo de fechas, como los últimos 14 días.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. En función de la hora de inicio que especifique, los comandos anteriores pueden devolver una lista larga de operaciones para el grupo de recursos. Puede filtrar los resultados para lo que busca ofreciendo criterios de búsqueda. Por ejemplo, si trata de investigar cómo se detuvo una aplicación web, podría ejecutar el siguiente comando.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    En este ejemplo se muestra que someone@contoso.com ha realizado una acción de detención. 

  ```powershell 
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
  ```

3. Puede buscar las acciones realizadas por un usuario determinado, incluso para un grupo de recursos que ya no existe.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Puede filtrar por las operaciones con errores.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Para centrarse en un error, examine el mensaje de estado para esa entidad.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Que devuelve:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Azure CLI
* Para recuperar las entradas del registro, ejecute el comando **azure group log show** .

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>API de REST
Las operaciones REST para trabajar con el registro de actividad forman parte de la [API de REST de Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar eventos de registro de actividad, consulte [Lista de los eventos de administración de una suscripción](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Pasos siguientes
* Los registros de actividad de Azure se pueden usar con Power BI para obtener mayor información sobre las acciones en su suscripción. Consulte [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Consulta y análisis de registros de auditoría de Azure en Power BI y más).
* Para obtener información sobre cómo establecer directivas de seguridad, vea [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).
* Para aprender sobre los comandos para ver operaciones de implementación, consulte [Visualización de operaciones de implementación](resource-manager-deployment-operations.md).
* Para aprender a evitar eliminaciones en un recurso para todos los usuarios, consulte [Bloqueo de recursos con Azure Resource Manager](resource-group-lock-resources.md).
* Para ver la lista de operaciones disponibles para cada proveedor de Microsoft Azure Resource Manager, consulte [Operaciones del proveedor de recursos de Azure Resource Manager](~/articles/active-directory/role-based-access-control-resource-provider-operations.md)

