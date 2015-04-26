<properties title="Azure Data Factory - Release Notes" pageTitle="Factoría de datos - Notas de la versión | Azure" description="Notas de la versión de Factoría de datos" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Notas de la versión de la factoría de datos Azure

## Notas para la versión 12/11/2014 de la factoría de datos ##

### Nuevas mejoras

- Integración de aprendizaje automático de Azure
	- Esta versión del servicio de factoría de datos de Azure le permite integrar la factoría de datos de Azure con el aprendizaje de máquina de Azure (ML) mediante el uso de **AzureMLLinkedService** y **AzureMLBatchScoringActivity**. Vea [Crear canalizaciones predictivas mediante la factoría de datos y el aprendizaje automático de Azure][adf-azure-ml] para obtener más información. 
- Se proporciona el estado de la versión de puerta de enlace
	- El estado de "NewVersionAvailable" se mostrará en el Portal de vista previa de Azure y en la salida del cmdlet Get-AzureDataFactoryGateway, si hay una versión más reciente de la puerta de enlace disponible que la instalada actualmente. A continuación, puede seguir el viaje del portal para descargar el nuevo archivo de instalación (.msi) y ejecutarlo para instalar la puerta de enlace más reciente. No se necesita ninguna configuración adicional.

### Cambios

- Se quita JobsContainer en HdInsightOnDemandLinkedService.
	- En la definición de JSON para un HDInsightOnDemandLinkedService, ya no es necesario especificar **jobsContainer**. Si tiene la propiedad especificada para un servicio vinculado a petición, se ignora la propiedad. Puede quitar la propiedad de la definición de JSON para el servicio vinculado y actualizar la definición de servicio vinculado mediante el cmdlet New-AzureDataFactoryLinkedService.
- Parámetros de configuración opcionales para HDInsightOnDemandLinkedService
	- Esta versión presenta compatibilidad con algunos parámetros de configuración opcionales para HDInsightOnDemandLinked (clúster de HDInsight a petición). Vea [Propiedades de ClusterCreateParameters][on-demand-hdi-parameters] para obtener más información.
- Se quita la ubicación de la puerta de enlace
	- Al crear una puerta de enlace de la factoría de datos de Azure mediante el portal o PowerShell (New-AzureDataFactoryGateway), ya no necesitará especificar la ubicación para la puerta de enlace. Se heredará la región de la factoría de datos. De forma similar, para configurar un servicio vinculado de SQL Server con JSON, la propiedad "gatewayLocation" ya no es necesaria. El SDK de .NET de la factoria de datos también se actualiza para reflejar estos cambios.
	- Si usa una versión anterior del SDK y PowerShell de Azure, seguirá teniendo que proporcionar la configuración de la ubicación.
 
     

#### Cambios drásticos
	
- CustomActivity a DotNetActivity
	- El nombre de la interfaz de **ICustomActivity** cambia a **IDotNetActivity**. Necesitará actualizar paquetes de NuGet de la factoría de datos y cambiar ICustomActivity a IDotNetActivity en el código fuente para su actividad personalizada.  
	- El tipo de actividad personalizada de la definición de JSON para su actividad personalizada debe cambiarse de **CustomActivity** a **DotNetActivity**. 
	- Se ha cambiado el nombre de las clases**CustomActivity** y **CustomActivityProperties** a **DotNetActivity** y **DotNetActivityProperties** con el mismo conjunto de propiedades.

		Si usa la versión anterior del SDK y Azure PowerShell, puede continuar usando CustomActivity en lugar de DotNetActivity.
    
  		Vea el tutorial que se presenta en [Usar actividades personalizadas en una canalización de la factoría de datos de Azure][adf-custom-activities] acerca de cómo crear una actividad personalizada y usarla en una canalización de la factoría de datos de Azure.  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->
