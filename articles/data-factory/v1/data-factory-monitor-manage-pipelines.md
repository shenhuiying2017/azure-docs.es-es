---
title: "Supervisión y administración de canalizaciones usando Azure Portal y PowerShell | Microsoft Docs"
description: "Obtenga información sobre el uso de Azure Portal y Azure PowerShell para supervisar y administrar las factorías de datos y las canalizaciones de Azure que haya creado."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d9e7b1d020a99e939ea01c43c7e5e935188b212e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Supervisión y administración de canalizaciones de Azure Data Factory mediante Azure Portal y PowerShell
> [!div class="op_single_selector"]
> * [Uso de Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Uso de la aplicación de supervisión y administración](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, consulte el artículo sobre [la supervisión y administración de canalizaciones en Data Factory en la versión 2](../monitor-visually.md).

> [!IMPORTANT]
> La aplicación de supervisión y administración proporciona una mejor compatibilidad con la supervisión y la administración de las canalizaciones de datos y la solución de problemas. Para más información sobre el uso de la aplicación, consulte [Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación de supervisión y administración](data-factory-monitor-manage-app.md). 


En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante Azure Portal y PowerShell. También se ofrece información sobre cómo crear alertas y recibir notificaciones cuando se produzcan errores.

## <a name="understand-pipelines-and-activity-states"></a>Descripción de las canalizaciones y los estados de actividad
Con Azure Portal, puede:

* Ver una factoría de datos como un diagrama.
* Ver las actividades en una canalización.
* Ver conjuntos de datos de entrada y salida.

En esta sección se describen también las transiciones de sectores de un conjunto de datos de un estado a otro.   

### <a name="navigate-to-your-data-factory"></a>Navegación hasta la factoría de datos
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Factorías de datos** en el menú de la izquierda. Si no ve está opción, haga clic en **Más servicios >** y luego en **Factorías de datos**, en la categoría **INTELIGENCIA Y ANÁLISIS**.

   ![Examinar todo -> Factorías de datos](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. En la hoja **Factorías de datos**, seleccione la factoría de datos que le interese.

    ![Selección de la factoría de datos](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Verá la página principal de la factoría de datos.

   ![Hoja Factoría de datos](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vista de diagrama de la factoría de datos
La vista **Diagrama** de una factoría de datos ofrece un panel único para supervisar y administrar la factoría de datos y sus recursos. Haga clic en **Diagrama** en la página principal de la factoría de datos para ver la vista de **diagrama**.

![Vista Diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Puede acercar, alejar, hacer zoom para ajustar, hacer zoom al 100 %, bloquear el diseño del diagrama y colocar automáticamente canalizaciones y conjuntos de datos. También puede ver la información de linaje de datos (es decir, se muestran los elementos ascendentes y descendentes de los elementos seleccionados).

### <a name="activities-inside-a-pipeline"></a>Actividades en una canalización
1. Haga clic con el botón derecho en la canalización y luego en **Abrir canalización** para ver todas las actividades de la canalización junto con los conjuntos de datos de entrada y salida para las actividades. Esta característica resulta útil cuando la canalización incluye más de una actividad y se quiere entender el linaje operativo de una sola canalización.

    ![Menú Abrir canalización](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. En el ejemplo siguiente, verá una actividad de copia en la canalización con una entrada y una salida. 

    ![Actividades en una canalización](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Puede navegar de nuevo a la página principal haciendo clic en el vínculo **Data Factory** situado en la ruta de navegación en la esquina superior izquierda.

    ![Navegación hacia atrás a la factoría de datos](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Visualización del estado de cada actividad dentro de una canalización
Puede ver el estado actual de una actividad viendo el estado de cualquiera de los conjuntos de datos que genera la actividad.

Al hacer doble clic en **OutputBlobTable** en la vista **Diagrama**, puede observar todos los segmentos generados por distintas ejecuciones de actividades dentro de una canalización. Puede ver que la actividad de copia se ejecutó correctamente durante las últimas ocho horas y generó los segmentos en el estado **Listo**.  

![Estado de la canalización](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Los segmentos de conjunto de datos en una factoría de datos pueden tener uno de los siguientes estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">DESCRIPCIÓN</th>
</tr>
<tr>
    <td rowspan="8">En espera</td><td>ScheduleTime</td><td>No ha llegado la hora para que se ejecute el segmento.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Las dependencias de nivel superior no están listas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>No están disponibles los recursos de proceso.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas las instancias de actividad están ocupadas con la ejecución de otros sectores.</td>
</tr>
<tr>
<td>ActivityResume</td><td>La actividad está en pausa y no puede ejecutar los segmentos hasta que se reanude.</td>
</tr>
<tr>
<td>Retry</td><td>Se está volviendo a intentar la ejecución de la actividad.</td>
</tr>
<tr>
<td>Validación</td><td>Aún no ha iniciado la validación.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>La validación está esperando un reintento.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validating</td><td>Validación en curso.</td>
</tr>
<td>-</td>
<td>El segmento se está procesando.</td>
</tr>
<tr>
<td rowspan="4">Con error</td><td>TimedOut</td><td>La ejecución de la actividad tardó más tiempo del permitido por la actividad.</td>
</tr>
<tr>
<td>Canceled</td><td>El segmento se ha cancelado por una acción del usuario.</td>
</tr>
<tr>
<td>Validación</td><td>Error de validación.</td>
</tr>
<tr>
<td>-</td><td>No se pudo puede generar o validar el segmento.</td>
</tr>
<td>Ready</td><td>-</td><td>El segmento está listo para su uso.</td>
</tr>
<tr>
<td>Skipped</td><td>None</td><td>El segmento se está procesando.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Un segmento existía con un estado distinto, pero se ha restablecido.</td>
</tr>
</table>



Puede ver los detalles sobre un segmento haciendo clic en la hoja **Segmentos actualizados recientemente**.

![Detalles de segmento](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Si el segmento se ejecutó varias veces, aparecen varias filas en la lista **Ejecuciones de actividad** . Para ver detalles sobre una ejecución de actividad, haga clic en la entrada de la ejecución en la lista **Ejecuciones de actividades** . La lista muestra todos los archivos de registro junto con un mensaje de error, si hubiera alguno. Esta característica resulta muy útil para ver y depurar registros sin tener que salir de la factoría de datos.

![Detalles de ejecución de actividad](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**. Esta característica resulta muy útil cuando el segmento tiene el estado **En espera** y se quiere saber cuáles son las dependencias ascendentes por las que el segmento está esperando.

![Segmentos ascendentes que no están listos](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado del conjunto de datos
Cuando se implementa una factoría de datos y las canalizaciones tienen un período activo válido, los segmentos del conjunto de datos pasan de un estado a otro. Actualmente, el estado del segmento se ajusta al siguiente diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

El flujo de transición de estado del conjunto de datos de la factoría de datos es el siguiente: En espera -> En curso /En curso (Validando) -> Listo/Error.

El segmento se inicia con un estado **En espera**, mientras se espera a que se cumplan las condiciones previas que deben cumplirse antes de su ejecución. Luego, la actividad comienza a ejecutarse y el segmento pasa al estado **En curso**. La ejecución de esta actividad se completará correctamente o dará error. El segmento se marca como **Listo** o **Error** según el resultado de la ejecución.

El usuario puede restablecer el segmento para que vuelva del estado **Listo** o **Error** al estado **En espera**. El usuario también puede marcar el estado del segmento como **Omitir**, lo que impide que la actividad se ejecute, y no se procesa el segmento.

## <a name="pause-and-resume-pipelines"></a>Pausa y reanudación de canalizaciones
Puede administrar las canalizaciones usando Azure PowerShell. Por ejemplo, puede pausar y reanudar canalizaciones ejecutando cmdlets de Azure PowerShell. 

> [!NOTE] 
> La vista de diagrama no admite las funciones de pausa y reanudación de las canalizaciones. Si quiere usar una interfaz de usuario, utilice la aplicación de supervisión y administración. Para más información sobre el uso de la aplicación, consulte el artículo [Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación de supervisión y administración](data-factory-monitor-manage-app.md). 

Puede pausar o suspender canalizaciones con el cmdlet **Suspend-AzureRmDataFactoryPipeline** de Powershell. Este cmdlet es útil cuando no desea ejecutar canalizaciones hasta que se solucione un problema. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por ejemplo: 

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Tras solucionar el problema con la canalización, se puede reanudar la canalización suspendida mediante el siguiente comando de PowerShell:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por ejemplo: 

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Depuración de canalizaciones
Azure Data Factory ofrece amplias funcionalidades a través de Azure Portal y Azure PowerShell para depurar y solucionar problemas de las canalizaciones.

> [NOTA} Es mucho más fácil solucionar errores mediante la aplicación de supervisión y administración. Para más información sobre el uso de la aplicación, consulte el artículo [Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación de supervisión y administración](data-factory-monitor-manage-app.md). 

### <a name="find-errors-in-a-pipeline"></a>Búsqueda de errores en una canalización
Si falla la ejecución de actividad en una canalización, el conjunto de datos generado por la canalización tiene un estado de error debido al fallo. Puede depurar y solucionar los errores en Azure Data Factory con los métodos siguientes.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Uso de Azure Portal para depurar un error
1. En la hoja **Tabla**, haga clic en el segmento problemático cuyo **Estado** sea **Error**.

   ![Hoja Tabla con segmentos con problemas](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. En la hoja **Segmento de datos**, haga clic en la ejecución de actividad que falló.

   ![Segmento de datos con un error](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. En la hoja **Detalles de la ejecución de actividad**, puede descargar los archivos asociados al procesamiento de HDInsight. Haga clic en **Descargar** correspondiente a Status/stderr para descargar el archivo de registro de errores que contiene detalles sobre el error.

   ![Hoja Detalles de ejecución de actividad con errores](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Uso de PowerShell para depurar un error
1. Inicie **PowerShell**.
2. Ejecute el comando **Get-AzureRmDataFactorySlice** para ver los segmentos y sus estados. Debería ver un segmento con el estado: **Error**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por ejemplo: 

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Reemplace **StartDateTime** por la hora de inicio de la canalización. 
3. Ahora, ejecute el cmdlet **Get-AzureRmDataFactoryRun** para obtener detalles sobre la ejecución de actividad para el segmento.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por ejemplo: 

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    El valor de StartDateTime es la hora de inicio del segmento con error o con problemas que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
4. Debería ver una salida con detalles sobre el error similar a la siguiente:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Puede ejecutar el cmdlet **Save-AzureRmDataFactoryLog** con el valor de identificador que ve en la salida y descargar los archivos de registro mediante la opción **-DownloadLogsoption** del cmdlet.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Repetición de la ejecución de errores en una canalización

> [!IMPORTANT]
> Es más fácil solucionar los errores y volver a ejecutar los sectores erróneos con la aplicación de supervisión y administración. Para más información sobre el uso de la aplicación, consulte [Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación de supervisión y administración](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Uso del Portal de Azure
Tras solucionar los problemas y depurar los errores de una canalización, puede volver a ejecutar los elementos con fallos; para ello, vaya al segmento de error y haga clic en el botón **Ejecutar** de la barra de comandos.

![Repetición de ejecución de un segmento con errores](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

En caso de que el segmento no se valide debido a un error de directiva (por ejemplo que los datos no estén disponibles), puede corregir el error y volver a validarlo haciendo clic en el botón **Validar** de la barra de comandos.

![Corrección de errores y validación](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Uso de Azure PowerShell
Puede volver a ejecutar errores mediante el cmdlet **Set-AzureRmDataFactorySliceStatus**. Consulte el tema [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) para obtener información sobre la sintaxis y otros detalles del cmdlet.

**Ejemplo:**

En el siguiente ejemplo, el estado de todos los segmentos de la tabla "DAWikiAggregatedData" se establece en "En espera" en la factoría de datos de Azure "WikiADF".

El valor "UpdateType" se establece en "UpstreamInPipeline", lo que significa que los estados de cada segmento de la tabla y todas las tablas dependientes (ascendentes) se establecen en "En espera". Otro valor posible para este parámetro es "Individual".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Creación de alertas
Azure registra eventos del usuario cuando se crea, actualiza o elimina un recurso de Azure (por ejemplo, una factoría de datos). Puede crear alertas en estos eventos. Puede usar Data Factory para capturar diversas métricas y crear alertas sobre las métricas. Se recomienda que use los eventos con fines de supervisión en tiempo real y las métricas con fines históricos.

### <a name="alerts-on-events"></a>Alertas en eventos
Los eventos de Azure proporcionan información útil sobre lo que sucede en los recursos de Azure. Al usar Azure Data Factory, se generan eventos cuando:

* Se crea, actualiza o elimina una factoría de datos.
* Se inicia o finaliza el procesamiento de datos (su "ejecución").
* Se crea o se elimina un clúster de HDInsight a petición.

Puede crear alertas sobre estos eventos del usuario y configurarlas para enviar notificaciones por correo electrónico al administrador y a los coadministradores de la suscripción. Además, puede especificar direcciones de correo electrónico adicionales de los usuarios que necesiten recibir notificaciones por correo electrónico cuando se cumplan las condiciones. Esta característica resulta muy útil si se quiere recibir una notificación cuando se produzcan errores y no se desea supervisar continuamente la factoría de datos.

> [!NOTE]
> Actualmente, el portal no muestra alertas sobre eventos. Use la [aplicación de supervisión y administración](data-factory-monitor-manage-app.md) para ver todas las alertas.


#### <a name="specify-an-alert-definition"></a>Especificación de una definición de alerta
Para especificar una definición de alerta, cree un archivo JSON que describa las operaciones sobre las que desea recibir alertas. En el ejemplo siguiente, la alerta envía una notificación de correo electrónico para la operación RunFinished. Para ser más específicos, se envía una notificación por correo electrónico cuando se ha completado una ejecución en la Factoría de datos y se ha producido un error (estado = FailedExecution).

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

Puede quitar **subStatus** de la definición de JSON si no desea recibir alertas sobre un error específico.

Este ejemplo configura la alerta para todas las factorías de datos de la suscripción. Si quiere configurar la alerta para una factoría de datos concreta, puede especificar la factoría de datos **resourceUri** en **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

En la tabla siguiente se ofrece una lista de las operaciones y los estados (y subestados) disponibles.

| Nombre de la operación | Status | Subestado |
| --- | --- | --- |
| RunStarted |Started |Iniciando |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |Started | |
| OnDemandClusterCreateSuccessful |Succeeded | |
| OnDemandClusterDeleted |Succeeded | |

Vea [Create Alert Rule](https://msdn.microsoft.com/library/azure/dn510366.aspx) (Creación de una regla de alerta) para más información sobre los elementos JSON usados en el ejemplo.

#### <a name="deploy-the-alert"></a>Implementación de la alerta
Para implementar la alerta, use el cmdlet de Azure PowerShell **New-AzureRmResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Una vez completada correctamente la implementación del grupo de recursos, aparecen los siguientes mensajes:

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> Puede usar la API de REST [Crear regla de alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para crear una regla de alerta. La carga útil de JSON es similar al ejemplo de JSON.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Recuperación de la lista de implementaciones del grupo de recursos de Azure
Para recuperar la lista de implementaciones del grupo de recursos de Azure implementado, use el cmdlet **Get-AzureRmResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Solución de problemas de eventos del usuario
1. Para ver todos los eventos generados, haga clic en el icono **Métricas y operaciones**.

    ![Icono Métricas y operaciones](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Haga clic en el icono **Eventos** para ver los eventos.

    ![Icono de eventos](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. En la hoja **Eventos**, puede ver detalles sobre los eventos o filtrar eventos, entre otras opciones.

    ![Hoja Eventos](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Haga clic en una **operación** en la lista de operaciones que producen un error.

    ![Seleccionar una operación](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Haga clic en un evento de **error** para ver detalles sobre el error.

    ![Evento de error](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Vea el artículo sobre [Azure Insight Cmdlets](https://msdn.microsoft.com/library/mt282452.aspx) para ver los cmdlets de PowerShell que puede usar para agregar, obtener o quitar alertas. Estos son algunos ejemplos del uso del cmdlet **Get-AlertRule** :

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Ejecute los siguientes comandos get-help para ver detalles y ejemplos para el cmdlet Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Si ve los eventos de generación de alertas en la hoja del portal, pero no recibe notificaciones de correo electrónico, compruebe si la dirección de correo electrónico que está especificada se estableció para recibir correos electrónicos de remitentes externos. Puede que la configuración del correo electrónico haya estado bloqueando los mensajes de alertas.

### <a name="alerts-on-metrics"></a>Alertas en métricas
Puede usar Data Factory para capturar diversas métricas y crear alertas sobre las métricas. Puede supervisar y crear alertas en las siguientes métricas para los segmentos de la factoría de datos:

* **Ejecuciones con error**
* **Ejecuciones correctas**

Estas métricas resultan útiles y le permiten obtener información general de todas las ejecuciones correctas y con error en su factoría de datos. Las métricas se emiten cada vez que hay una ejecución del segmento. A la hora en punto, estas métricas se agregan y se insertan en la cuenta de almacenamiento. Para habilitar las métricas, configure una cuenta de almacenamiento.

#### <a name="enable-metrics"></a>Habilitación de métricas
Para habilitar las métricas, haga clic en la secuencia siguiente desde la hoja de **Data Factory**:

**Supervisión** > **Métrica** > **Configuración de diagnóstico** > **Diagnóstico**

![Vínculo a Diagnóstico](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

En la hoja **Diagnóstico**, haga clic en **Activada**, seleccione la cuenta de almacenamiento y haga clic en **Guardar**.

![Hoja Diagnósticos](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Las métricas pueden tardar hasta una hora en estar visibles en la hoja **Supervisión**, porque la agregación de métricas se realiza cada hora.

### <a name="set-up-an-alert-on-metrics"></a>Configuración de alertas en métricas
Haga clic en el icono **Métricas de la factoría de datos**:

![Icono Métricas de la factoría de datos](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

En la hoja **Métrica**, haga clic en **+ Agregar alerta** en la barra de herramientas.
![Hoja Métrica de la factoría de datos &gt; Agregar alerta](./media/data-factory-monitor-manage-pipelines/add-alert.png)

En la página **Agregar una regla de alerta**, realice los pasos siguientes y haga clic en **Aceptar**.

* Escriba un nombre para la alerta (ejemplo: "alerta de error").
* Escriba una descripción de la alerta (ejemplo: "enviar un correo electrónico cuando se produzca un error").
* Seleccione una métrica ("ejecuciones con error" frente a "ejecuciones correctas").
* Especifique una condición y el valor del umbral.   
* Especifique el período de tiempo.
* Especifique si se debe enviar un correo electrónico a los propietarios, colaboradores y lectores.

![Hoja Métrica de la factoría de datos > Agregar regla de alerta](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Una vez que la regla de alerta se agrega correctamente, se cierra la hoja y verá la nueva alerta en la hoja **Métrica**.

![Hoja Métrica de la factoría de datos > Nueva alerta agregada](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

También verá el número de alertas en el icono **Reglas de alerta**. Haga clic en el icono **Reglas de alerta**.

![Hoja Métrica de la factoría de datos: Reglas de alerta](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

En la hoja **Reglas de alerta**, vea las alertas existentes. Para agregar una alerta, haga clic en  **Agregar alerta** en la barra de herramientas.

![Hoja Reglas de alertas](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notificaciones de alerta
Cuando la regla de alerta coincida con la condición, recibirá un correo electrónico en el que se le informa de que la alerta está activada. Cuando se resuelva el problema y la condición de alerta ya no coincida, recibirá un mensaje de correo que le indica que la alerta está resuelta.

Este comportamiento es diferente al de eventos en los que se envía una notificación en todos los errores en los que la regla de alerta se cumple.

### <a name="deploy-alerts-by-using-powershell"></a>Establecimiento de alertas mediante PowerShell
Puede implementar alertas para las métricas de la misma manera que lo hace para los eventos.

**Definición de la alerta**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

Reemplace los elementos *subscriptionId*, *resourceGroupName* y *dataFactoryName* del ejemplo anterior por los valores adecuados.

*metricName* admite actualmente dos valores:

* FailedRuns
* SuccessfulRuns

**Implementación de la alerta**

Para implementar la alerta, use el cmdlet de Azure PowerShell **New-AzureRmResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Debería ver el siguiente mensaje después de una correcta implementación:

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

También puede usar el cmdlet **Add-AlertRule** para implementar una regla de alertas. Consulte el tema [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) para obtener información detallada y ejemplos.  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Desplazamiento de una factoría de datos a una suscripción o un grupo de recursos diferente
Puede mover una factoría de datos a un grupo de recursos o una suscripción diferentes con el botón **Mover** de la barra de comandos que aparece en la página principal de su factoría de datos.

![Mover factoría de datos](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Junto con la factoría de datos, también puede mover todos los recursos relacionados (como las alertas asociadas a la factoría de datos).

![Cuadro de diálogo Mover recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
