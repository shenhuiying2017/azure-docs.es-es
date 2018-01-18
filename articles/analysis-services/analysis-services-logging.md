---
title: "Registro de diagnóstico de Azure Analysis Services | Microsoft Docs"
description: "Obtenga información acerca de cómo configurar el registro de diagnóstico de Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/29/2017
ms.author: owend
ms.openlocfilehash: 02c25de980b399812676285ad3f87f60af93265f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="setup-diagnostic-logging"></a>Configuración del registro de diagnóstico

Una parte importante de cualquier solución de Analysis Services es la supervisión de cómo se comportan los servidores en cuanto al rendimiento. Con los [registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), puede supervisar los registros y enviarlos a [Azure Storage](https://azure.microsoft.com/services/storage/), difundirlos a [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) y exportarlos a [Log Analytics](https://azure.microsoft.com/services/log-analytics/), que forma parte de [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Registro de diagnóstico a Storage, Event Hubs u Operations Management Suite a través de Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>¿Qué se registra?

Puede seleccionar las categorías **Motor**, **Servicio** y **Métricas**.

### <a name="engine"></a>Motor

Al seleccionar **Motor**, se registran todos los [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). No puede seleccionar eventos individuales. 

|Categorías de XEvent |Nombre del evento  |
|---------|---------|
|Auditoría de seguridad    |   Audit Login      |
|Auditoría de seguridad    |   Audit Logout      |
|Auditoría de seguridad    |   Audit Server Starts And Stops      |
|Informes de progreso     |   Progress Report Begin      |
|Informes de progreso     |   Progress Report End      |
|Informes de progreso     |   Progress Report Current      |
|Consultas     |  Query Begin       |
|Consultas     |   Query End      |
|Comandos:     |  Command Begin       |
|Comandos:     |  Command End       |
|Errores y advertencias     |   Error      |
|Descubra     |   Discover End      |
|Notificación     |    Notificación     |
|Sesión     |  Session Initialize       |
|Bloqueos    |  Deadlock       |
|Procesamiento de consultas     |   VertiPaq SE Query Begin      |
|Procesamiento de consultas     |   VertiPaq SE Query End      |
|Procesamiento de consultas     |   VertiPaq SE Query Cache Match      |
|Procesamiento de consultas     |   Direct Query Begin      |
|Procesamiento de consultas     |  Direct Query End       |

### <a name="service"></a>Servicio

|Nombre de la operación  |Ocurre cuando  |
|---------|---------|
|CreateGateway     |   El usuario configura una puerta de enlace en el servidor      |
|ResumeServer     |    Se reanuda un servidor     |
|SuspendServer    |   Se pausa un servidor      |
|DeleteServer     |    Eliminación de un servidor     |
|RestartServer    |     El usuario reinicia un servidor a través de SSMS o PowerShell    |
|GetServerLogFiles    |    El usuario exporta el registro de servidor a través de PowerShell     |
|ExportModel     |   El usuario exporta un modelo en el portal con Abrir en Visual Studio     |

### <a name="all-metrics"></a>Todas las métricas

La categoría Métricas registra las mismas [métricas de servidor](analysis-services-monitor.md#server-metrics) que se muestran en Métricas.

## <a name="setup-diagnostics-logging"></a>Configuración del registro de diagnósticos

### <a name="azure-portal"></a>Azure Portal

1. En [Azure Portal](https://portal.azure.com) > servidor, haga clic en **Registros de diagnóstico** en el panel de navegación izquierdo y luego en **Activar diagnósticos**.

    ![Activar el registro de diagnóstico para Azure Cosmos DB en Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. En **Configuración de diagnóstico**, haga lo siguiente: 

    * **Nombre**. Escriba un nombre para los registros que quiere crear.

    * **Archivar en una cuenta de almacenamiento**. Para usar esta opción, necesita una cuenta de almacenamiento existente a la cual conectarse. Vea [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md). Siga las instrucciones para crear una cuenta de uso general de Resource Manager. Luego, vuelva a esta página en el portal para seleccionar la cuenta de almacenamiento. Las cuentas de almacenamiento recién creadas pueden tardar unos minutos en aparecer en el menú desplegable.
    * **Transmitir a un centro de eventos**. Para usar esta opción, necesita un espacio de nombres de Event Hubs y un centro de eventos al que conectarse. Para crear un espacio de nombres de Event Hubs, consulte [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](../event-hubs/event-hubs-create.md). Luego, vuelva a esta página en el portal para seleccionar el espacio de nombres de Event Hubs y el nombre de la directiva.
    * **Enviar a Log Analytics**. Para usar esta opción, use un área de trabajo existente o cree una nueva área de trabajo de Log Analytics siguiendo los pasos necesarios para [crear una nueva área de trabajo](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) en el portal. Para más información acerca de cómo ver los registros de Log Analytics, vea [Visualización de los registros de Log Analytics](#view-in-loganalytics).

    * **Motor**. Seleccione esta opción para registrar xEvents. Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Los registros se eliminan automáticamente una vez expira el período de retención.
    * **Servicio**. Seleccione esta opción para registrar los eventos del nivel de servicio. Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Los registros se eliminan automáticamente una vez expira el período de retención.
    * **Métricas**. Seleccione esta opción para almacenar los datos detallados en [Métricas](analysis-services-monitor.md#server-metrics). Si va a archivar en una cuenta de almacenamiento, puede seleccionar el período de retención para los registros de diagnóstico. Los registros se eliminan automáticamente una vez expira el período de retención.

3. Haga clic en **Save**(Guardar).

    Si recibe un error que indica que "no se pudieron actualizar los diagnósticos para \<nombre de área de trabajo>. La suscripción \<identificador de suscripción> no está registrada para usar microsoft.insights.", siga las instrucciones para [solucionar problemas de Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) a fin de registrar la cuenta; a continuación, vuelva a intentar este procedimiento.

    Si desea cambiar el modo en que se guardan los registros de diagnóstico en algún momento, puede volver a esta página para modificar la configuración.

### <a name="powershell"></a>PowerShell

Estos son los comandos básicos para comenzar. Si desea obtener ayuda paso a paso para configurar el registro en una cuenta de almacenamiento con PowerShell, vea el tutorial más adelante en este artículo.

Para habilitar las métricas y los registros de diagnóstico con PowerShell, use los siguientes comandos:

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

- Para habilitar la transmisión de registros de diagnóstico a un centro de eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   El identificador de regla de Azure Service Bus es una cadena con este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Puede obtener el identificador de recurso de su área de trabajo de Log Analytics con el comando siguiente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="rest-api"></a>API DE REST

Aprenda a [cambiar la configuración de diagnóstico con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Plantilla de Resource Manager

Lea sobre cómo [habilitar la configuración de diagnóstico al crear recursos con la plantilla de Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Administración de los registros

Los registros normalmente estarán disponibles en un par de horas tras la configuración del registro. Es decisión suya administrar los registros en la cuenta de almacenamiento:

* Utilice los métodos de control de acceso de Azure estándar para proteger los registros mediante la restricción de quién puede tener acceso a ellos.
* Elimine los registros que ya no desee mantener en la cuenta de almacenamiento.
* Asegúrese de establecer un período de retención porque los registros antiguos se eliminan de la cuenta de almacenamiento.

## <a name="view-logs-in-log-analytics"></a>Visualización de los registros de Log Analytics

Los eventos del servidor y las métricas se integran con xEvents en Log Analytics para realizar un análisis en paralelo. Log Analytics también puede configurarse para recibir eventos de otros servicios de Azure proporcionando una vista holística de los datos de registro de diagnóstico en la arquitectura.

Para ver los datos de diagnóstico en Log Analytics, abra la página Búsqueda de registros desde el menú de la izquierda o el área Administración, como se muestra a continuación.

![Opciones de búsqueda de registros en Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Ahora que ha habilitado la recopilación de datos, en **Búsqueda de registros**, haga clic en **Todos los datos recopilados**.

En **Tipo**, haga clic en **AzureDiagnostics** y, a continuación, haga clic en **Aplicar**. AzureDiagnostics incluye eventos de motor y de servicio. Tenga en cuenta que una consulta de Log Analytics se crea sobre la marcha. El campo EventClass\_s contiene los nombres de xEvent, lo que puede resultar familiar si se ha usado xEvents para el registro local.

Haga clic en **EventClass\_s** o en uno de los nombres de evento y Log Analytics continúa generando una consulta. Asegúrese de guardar las consultas para reutilizarlas más adelante.

Asegúrese de consultar Operations Management Suite, que proporciona un sitio Web con consulta mejorada, funcionalidades de alerta en los datos de Log Analytics y paneles.

### <a name="queries"></a>Consultas

Existen cientos de consultas que puede utilizar. Estas son algunas para comenzar.
Para más información sobre cómo utilizar el nuevo lenguaje de consulta de Búsqueda de registros, consulte [Descripción de las búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Query devuelve las consultas enviadas a Azure Analysis Services que han tardado más de cinco minutos (300 000 milisegundos) en completarse.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identifique las réplicas con el escalado horizontal.

    ```
    search * | summarize count() by ServerName_s
    ```
    Cuando se usa el escalado horizontal, puede identificar las réplicas de solo lectura porque los valores del campo ServerName\_s tienen el número de instancia de la réplica anexado al nombre. El campo de recurso contiene el nombre de recurso de Azure, que coincide con el nombre del servidor que los usuarios ven. El campo IsQueryScaleoutReadonlyInstance_s es igual a true para las réplicas.



> [!TIP]
> ¿Tiene una gran consulta de Log Analytics que desea compartir? Si tiene una cuenta de GitHub, puede agregarla a este artículo. Basta con hacer clic en **Editar** en la parte superior derecha de esta página.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Tutorial: Activación del registro con PowerShell
En este tutorial rápido, se crea una cuenta de almacenamiento en la misma suscripción y grupo de recursos que el servidor de Analysis Services. A continuación, se usa Set-AzureRmDiagnosticSetting para activar el registro y se envía la salida a la nueva cuenta de almacenamiento.

### <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, necesitará los recursos siguientes:

* Un servidor de Azure Analysis Services. Para obtener instrucciones sobre cómo crear un recurso de servidor, consulte [Creación de un servidor en Azure Portal](analysis-services-create-server.md) o [Creación de un servidor de Azure Analysis Services mediante PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Conexión a las suscripciones

Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:  

```powershell
Login-AzureRmAccount
```

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtiene todas las suscripciones asociadas a esta cuenta y, de forma predeterminada, usa la primera.

Si tiene varias suscripciones, es posible que deba especificar la que se usó para crear su instancia de Azure Key Vault. Escriba lo siguiente para ver las suscripciones de su cuenta:

```powershell
Get-AzureRmSubscription
```

A continuación, para especificar la suscripción asociada a la cuenta de Azure Analysis Services que registrará, escriba:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si tiene varias suscripciones asociadas a su cuenta, es importante especificar la suscripción.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Creación de una cuenta de almacenamiento nueva para los registros

Puede usar una cuenta de almacenamiento existente para los registros, siempre que sea en la misma suscripción que el servidor. Para este tutorial, creará una nueva cuenta de almacenamiento dedicada a los registros de Analysis Services. Para facilitar el proceso, almacenamos los detalles de la cuenta de almacenamiento en una variable denominada **sa**.

También puede utilizar el mismo grupo de recursos que la carpeta que contenga el servidor de Analysis Services. Reemplace los valores de `awsales_resgroup`, `awsaleslogs` y `West Central US` con los suyos:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificación de la cuenta del servidor para los registros

Establezca el nombre de la cuenta en una variable denominada **account**, donde ResourceName es el nombre de la cuenta.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Habilitación del registro

Para habilitar el registro, use el cmdlet Set-AzureRmDiagnosticSetting, junto con las variables para la nueva cuenta de almacenamiento, la cuenta de servidor y la categoría. Ejecute el siguiente comando y establezca la marca **-Enabled** en **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

El resultado debe parecerse a este:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Así se confirma que el registro está habilitado ahora para el servidor y se guarda la información en la cuenta de almacenamiento.

También puede establecer una directiva de retención para los registros, de forma que los más antiguos se eliminen automáticamente. Por ejemplo, establezca la directiva de retención estableciendo la marca **-RetentionEnabled** en **$true** y el parámetro **-RetentionInDays** en **90**. Los registros de más de 90 días se eliminan automáticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>pasos siguientes

Más información sobre los [registros de diagnóstico de recursos Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Consulte [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) en la Ayuda de PowerShell.