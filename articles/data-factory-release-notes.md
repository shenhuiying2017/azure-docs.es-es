<properties 
	pageTitle="Factoría de datos - Notas de la versión | Azure" 
	description="Notas de generador de datos" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Notas de la versión de la factoría de datos Azure

## Notas de versión 10/04/2015 del generador de datos
Verá el **actualizado recientemente sectores** y **error recientemente sectores** se enumeran en la **tabla** blade ahora. Estas listas se ordenan por el tiempo de actualización del sector. En las situaciones siguientes, se cambia la hora de un intervalo de actualización.

-  Actualiza el estado del sector manualmente, por ejemplo, utilizando la **conjunto AzureDataFactorySliceStatus** (o), haga clic en **ejecutar** en el **sector** módulo para el sector.
-  El segmento cambia estado debido a una ejecución (por ejemplo, una ejecución iniciado, una ejecución finalizado y no se pudo, una ejecución finaliza y se realizó correctamente, etc.).

Haga clic en el título de las listas o **... (puntos suspensivos)** Para ver la lista más amplia de sectores. Haga clic en **filtro** en la barra de herramientas para filtrar los sectores.
 
Todavía puede ver intervalos ordenados por las horas de sector haciendo clic en **rebanadas de datos (por hora de sector)** en mosaico. Los sectores en esas colecciones se ordenan por intervalo de tiempo. Por ejemplo, si se trata de una programación por hora, los sectores sería: - 4/4/2015 5 p.m. en curso - 4/4/2015 4 pm Succeeded - 4/4/2015 3 p.m. Error

Pero, si un segmento anterior se vuelve a ejecutar, no aparecerían en la parte superior de esta lista, aunque probablemente es lo que está más interesado en el usuario.

## Notas de la versión de 31/3/2015 del generador de datos
- Actualizar **Data Management Gateway** paquete de instalación se ha registrado en [Microsoft Download Center][adf-gateway-download].
- Copiar desde **local sistema de archivos a Azure blob** ahora se admite. Vea los temas siguientes para obtener más información.
	-  [Sistema de archivos vinculados de servicio de forma local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propiedades de OnPremisesFileSystemLocation en una tabla de JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Admite orígenes y receptores](https://msdn.microsoft.com/library/dn894007.aspx). Consulte la matriz de la copia actualizada y **FileSystemSource** propiedades. 
-  Copiar desde **base de datos de Oracle de local a Azure blob** ahora se admite. Vea los temas siguientes para obtener más información. 
	-  [Oracle local vinculado servicio](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propiedades de OnPremisesOracleTableLocation en una tabla de JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Admite orígenes y receptores](https://msdn.microsoft.com/library/dn894007.aspx). Consulte la matriz de la copia actualizada y **OracleSource** propiedades.
-  Puede especificar la codificación para los archivos de texto en un Blob de Azure. Vea el nuevo [propiedad encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Puede invocar un procedimiento almacenado con parámetros adicionales cuando se copian en el receptor de SQL. Consulte [invocar el procedimiento almacenado para el receptor de SQL][adf-copy-advanced] para obtener más información.   

Vea el blob de entrada: [actualizar de generador de datos de Azure - nuevos almacenes de datos](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) para obtener información adicional, incluidos ejemplos.

## Notas de la versión de 27/2/2015 del generador de datos

### Nuevas mejoras
- **Editor de generador de datos azure**. El Editor de generador de datos, que forma parte de la vista previa del Portal de Azure, permite crear, editar e implementar archivos JSON que definen servicios vinculados, conjuntos de datos y canalizaciones. El objetivo principal del editor es proporcionar una rápido y ligero interfaz de usuario (UI) para crear artefactos de la factoría de datos de Azure sin necesidad de instalar Azure PowerShell y rampa de seguridad sobre el uso de cmdlets de PowerShell. Consulte la [Editor de generador de datos de Azure - una luz peso Web Editor][adf-editor-blog] entrada de blog para una rápida visión general y un vídeo en el Editor del generador de datos. Para obtener información detallada del editor, vea la [Editor del generador de datos][adf-editor] artículo.          

### Cambios

## Notas de la versión de 26/1/2015 del generador de datos ##

### Cambios
- Actualizar **Data Management Gateway** paquete de instalación se ha registrado en [Microsoft Download Center][adf-gateway-download]. A partir de esta versión, encontrará Data Management Gateway más reciente para usar con el generador de datos de Azure en esta ubicación de descarga. Este paquete de instalación sirve factoría de datos de Azure y Power BI para servicios de Office 365. Si utiliza ambos servicios, tenga en cuenta que deben ser instalados en equipos diferentes puertas de enlace para el generador de datos y Power BI y configurar de forma diferente según la orientación de la documentación del generador de datos o Power BI.
- El **copia actividad** ahora admite la copia de datos entre la base de datos de SQL Server local y una base de datos de SQL Azure. Consulte [copia actividad][adf-copy-activity] para obtener información detallada y [GitHub][adf-github-samples] para obtener ejemplos JSON.
- **SqlSink** admite una nueva propiedad: **WriteBatchTimeout**. Esta propiedad proporciona la flexibilidad para configurar tiempo de espera para que la operación de inserción por lotes completar antes agote el tiempo de espera de la operación. Para obtener una copia de híbrido (operación de copia que implica un origen de datos local y un origen de datos de nube), debe tener la puerta de enlace de versión 1.4 o superior para poder utilizar esta propiedad. 
- **SQL Server vinculado servicio** ahora admite **la autenticación de Windows**. 
	- Al crear un servidor SQL Server vinculado mediante el portal de servicio, ahora puede usar la autenticación de Windows y establecer las credenciales adecuadas. Esto requiere que la puerta de enlace de versión 1.4 o superior. 
	- Al crear un servidor SQL Server vinculado servicio con Azure PowerShell, puede especificar información de conexión en texto sin formato o cifrar la información de conexión mediante actualizado [cmdlet New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] y, a continuación, utilizar la cadena cifrada de la propiedad de cadena de conexión en la carga de JSON de servicios vinculados. Consulte [servicios vinculados][adf-msdn-linked-services] para obtener más información acerca de cómo definir un servicio vinculado en JSON. La característica de cifrado no es compatible con el cmdlet New-AzureDataFactoryEncryptValue todavía. 

## Notas de versión 11/12/2014 del generador de datos ##

### Nuevas mejoras

- Integración de aprendizaje automático de Azure
	- Esta versión del servicio de generador de datos de Azure le permite integrar factoría de datos de Azure con el aprendizaje de máquina de Azure (ML) mediante el uso de **AzureMLLinkedService** y **AzureMLBatchScoringActivity**. Consulte [crear canalizaciones predictivas mediante el generador de datos y el aprendizaje automático de Azure][adf-azure-ml] para obtener más información. 
- Se proporciona el estado de la versión de puerta de enlace
	- El estado de "NewVersionAvailable" se mostrará en el Portal de vista previa de Azure y en la salida del cmdlet Get-AzureDataFactoryGateway, si hay una versión más reciente de la puerta de enlace disponible que la instalada actualmente. A continuación, puede seguir el viaje del portal para descargar el nuevo archivo de instalación (.msi) y ejecutarlo para instalar la puerta de enlace más reciente. No se necesita ninguna configuración adicional.

### Cambios

- Se quita JobsContainer en HdInsightOnDemandLinkedService.
	- En la definición de JSON para un HDInsightOnDemandLinkedService, no es necesario especificar **jobsContainer** propiedad ya. Si tiene la propiedad especificada para un servicio vinculado a petición, se ignora la propiedad. Puede quitar la propiedad de la definición de JSON para el servicio vinculado y actualizar la definición de servicio vinculado mediante el cmdlet New-AzureDataFactoryLinkedService.
- Parámetros de configuración opcionales para HDInsightOnDemandLinkedService
	- Esta versión presenta compatibilidad con algunos parámetros de configuración opcionales para HDInsightOnDemandLinked (clúster de HDInsight a petición). Consulte [Propiedades ClusterCreateParameters][on-demand-hdi-parameters] para obtener más información.
- Se quita la ubicación de la puerta de enlace
	- Al crear una puerta de enlace de la factoría de datos de Azure mediante el portal o PowerShell (New-AzureDataFactoryGateway), ya no necesitará especificar la ubicación para la puerta de enlace. Se heredará la región de la factoría de datos. De forma similar, para configurar un servicio vinculado de SQL Server con JSON, la propiedad "gatewayLocation" ya no es necesaria. El SDK de .NET de la factoria de datos también se actualiza para reflejar estos cambios.
	- Si usa una versión anterior del SDK y PowerShell de Azure, seguirá teniendo que proporcionar la configuración de la ubicación.
 
     

#### Cambios drásticos
	
- CustomActivity a DotNetActivity
	- **ICustomActivity** interfaz se cambia a **IDotNetActivity**. Necesitará actualizar paquetes de NuGet de la factoría de datos y cambiar ICustomActivity a IDotNetActivity en el código fuente para su actividad personalizada.  
	- El tipo de actividad personalizada en la definición de JSON para la actividad personalizada debe cambiarse de **CustomActivity** a **DotNetActivity**. 
	- El **CustomActivity** y **CustomActivityProperties** clases se ha cambiado a **DotNetActivity** y **DotNetActivityProperties** con el mismo conjunto de propiedades.

		Si usa la versión anterior del SDK y Azure PowerShell, puede continuar usando CustomActivity en lugar de DotNetActivity.
    
  		Consulte [utilizar actividades personalizadas en una canalización del generador de datos de Azure][adf-custom-activities] para obtener un tutorial sobre cómo crear una actividad personalizada y utilizarla en una canalización del generador de datos de Azure.

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx

<!---HONumber=GIT-SubDir-->