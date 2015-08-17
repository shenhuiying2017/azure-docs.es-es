<properties 
	pageTitle="Creación de alertas sobre eventos de la Factoría de datos de Azure" 
	description="Obtenga información sobre cómo crear alertas sobre eventos generados por Azure para las operaciones de la Factoría de datos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Creación de alertas sobre eventos de Azure
Los eventos de Azure proporcionan información útil sobre lo que sucede en los recursos de Azure. Azure registra eventos del usuario cuando se crea, actualiza o elimina un recurso de Azure (por ejemplo, la Factoría de datos). Al usar la Factoría de datos de Azure, se generan eventos cuando:
 
1.	La Factoría de datos de Azure se crea/actualiza/elimina.
2.	Se inicia o finaliza el procesamiento de datos (denominado ejecución).
3.	Cuando se crea o se elimina un clúster de HDInsight a petición.

Puede crear alertas sobre estos eventos del usuario y configurarlas para enviar notificaciones por correo electrónico al administrador y a los coadministradores de la suscripción. Además, puede especificar direcciones de correo electrónico adicionales de los usuarios que necesiten recibir notificaciones por correo electrónico cuando se cumplan las condiciones.

## Especificación de una definición de alerta
Para especificar una definición de alerta, cree un archivo JSON que describa las operaciones sobre las que desea recibir alertas. En el ejemplo siguiente, la alerta enviará una notificación por correo electrónico para la operación **RunFinished**. Para ser más específicos, se envía una notificación por correo electrónico cuando se ha completado una ejecución en la Factoría de datos y se ha producido un error (estado = FailedExecution).

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

En la definición anterior de JSON, **subStatus** se puede quitar si no desea recibir alertas sobre un error específico.

Consulte [Operaciones y estados disponibles](#AvailableOperationsStatuses) para obtener la lista de operaciones y estados (y subestados).

## Implementación de alertas
Para implementar una alerta, use el cmdlet de PowerShell de Azure: **New-AzureResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

StorageAccountName especifica la cuenta de almacenamiento para almacenar el archivo JSON de la alerta implementada.

Una vez completada correctamente la implementación del grupo de recursos, verá los siguientes mensajes:
	
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

## Recuperación de la lista de implementaciones del grupo de recursos de Azure
Para recuperar la lista de implementaciones del grupo de recursos de Azure implementado, use el cmdlet: **Get-AzureResourceGroupDeployment**, como se muestra en el ejemplo siguiente:
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>Nombres de operaciones y valores de estado disponibles

| Nombre de la operación | Estado | Subestado |
| -------------- | ------ | ---------- |
| RunStarted | Started | Iniciando |
| RunFinished | Failed/Succeeded |	<p>FailedResourceAllocation </p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p>Canceled</p><p>FailedValidation</p><p>Abandoned</p> | 
| SliceOnTime | In Progress | Ontime |
| SliceDelayed | In Progress | Late |
| OnDemandClusterCreateStarted | Started | |
| OnDemandClusterCreateSuccessful | Succeeded | | 
| OnDemandClusterDeleted | Succeeded | |


## Solución de problemas con eventos del usuario
Ejecute el siguiente comando para ver los eventos generados:

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"
 

<!---HONumber=August15_HO6-->