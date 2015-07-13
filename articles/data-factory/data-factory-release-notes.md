<properties 
	pageTitle="Factoría de datos - Notas de la versión | Azure" 
	description="Notas de la versión de la Factoría de datos" 
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

## Notas para la versión de 04/10/2015 de la Factoría de datos
Ahora puede ver las listas **Segmentos actualizados recientemente** y **Segmentos erróneos recientes** en la hoja **TABLA**. Estas listas se ordenan por la hora de actualización del segmento. En las situaciones siguientes, se cambia la hora de actualización de un segmento.

-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).

Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos. Haga clic en **Filtro** en la barra de herramientas para filtrar los segmentos.
 
Todavía puede ver los segmentos ordenados por las horas del segmento clic en el icono **Segmentos de datos (por hora del segmento)**. Los segmentos de estas colecciones se ordenan por hora de segmento. Por ejemplo, si se trata de una programación horaria, los segmentos serían: - 4/4/2015 5 p.m. En curso - 4/4/2015 4 p.m. Correcto - 4/4/2015 3 p.m. Error

Pero, si un segmento anterior se vuelve a ejecutar, no aparecería en la parte superior de esta lista, aunque probablemente sea el que más interese al usuario.

## Notas para la versión 3/31/2015 de la Factoría de datos
- El paquete de instalación actualizado de **Data Management Gateway** se ha publicado en el [Centro de descarga de Microsoft][adf-gateway-download].
- Ahora se admite copiar desde un **sistema de archivos local a un blob de Azure**. Para obtener más información, consulte los temas siguientes.
	-  [Servicio vinculado del sistema de archivos local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propiedades OnPremisesFileSystemLocation en una tabla de JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Orígenes y receptores compatibles](https://msdn.microsoft.com/library/dn894007.aspx). Consulte la matriz de la copia actualizada y las propiedades **FileSystemSource**. 
-  Ahora se admite copiar desde una **base de datos Oracle local a un blob de Azure**. Para obtener más información, consulte los temas siguientes. 
	-  [Servicio vinculado de Oracle local](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propiedades OnPremisesOracleTableLocation en una tabla de JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Orígenes y receptores compatibles](https://msdn.microsoft.com/library/dn894007.aspx). Consulte la matriz de la copia actualizada y las propiedades **OracleSource**.
-  Puede especificar la codificación para los archivos de texto en un Blob de Azure. Vea la nueva propiedad [encodingName property](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Puede invocar un procedimiento almacenado con parámetros adicionales al copiar en un receptor de SQL. Vea [Invocación del procedimiento almacenado para el receptor de SQL][adf-copy-advanced].   

Vea la entrada de blog: [Actualización de la Factoría de datos de Azure: nuevos almacenes de datos](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) para obtener información adicional y ejemplos.

## Notas para la versión de 27/02/2015 de la Factoría de datos

### Nuevas mejoras
- **Editor de la Factoría de datos de Azure**. El Editor de la Factoría de datos, que forma parte del Portal de vista previa Azure, permite crear, editar e implementar archivos JSON que definen servicios vinculados, conjuntos de datos y canalizaciones. El objetivo principal del editor es proporcionar una interfaz de usuario (IU) rápida y ligera para crear artefactos de la Factoría de datos de Azure sin tener que instalar Azure PowerShell y para mejorar el uso de los cmdlets de PowerShell. Consulte la entrada de blog [Editor de la Factoría de datos de Azure: un editor web ligero][adf-editor-blog] para obtener información general rápida y un vídeo sobre el Editor de la Factoría de datos. Consulte el artículo [Editor de la Factoría de datos][adf-editor] para obtener información general del editor.          

### Cambios

## Notas para la versión de 26/01/2015 de la Factoría de datos ##

### Cambios
- El paquete de instalación actualizado de **Data Management Gateway** se ha publicado en el [Centro de descarga de Microsoft][adf-gateway-download]. A partir de esta versión, puede encontrar en esta ubicación de descarga la versión más reciente de Data Management Gateway para usarla con la Factoría de datos. Este paquete de instalación sirve para la Factoría de datos de Azure y Power BI para servicios de Office 365. Si usa los dos servicios, tenga en cuenta que las puertas de enlace para la Factoría de datos y Power BI se deben instalar en distintos equipos y configurar de manera diferente según las instrucciones de la documentación de la Factoría de datos y de Power BI.
- La **actividad de copia** ahora admite la copia de datos entre una base de datos de SQL Server local y una base de datos SQL de Azure. Consulte [Actividad de copia][adf-copy-activity] para obtener información detallada y [GitHub][adf-github-samples] para obtener ejemplos de JSON.
- **SqlSink** admite una nueva propiedad: **WriteBatchTimeout**. Esta propiedad ofrece flexibilidad para configurar el tiempo que tarda en completarse la operación de inserción por lotes antes de que se agote el tiempo de espera de la operación. Para obtener una copia híbrida (operación de copia que implica un origen de datos local y un origen de datos en la nube), debe tener la puerta de enlace de la versión 1.4 o superior para usar esta propiedad. 
- El **servicio vinculado de SQL Server** ahora admite **Autenticación de Windows**. 
	- Al crear un servicio vinculado de SQL Server con el portal, puede optar por usar Autenticación de Windows y establecer las credenciales adecuadas. Para ello, hay que tener la puerta de enlace de la versión 1.4 o superior. 
	- Al crear un servicio vinculado de SQL Server con Azure PowerShell, puede especificar la información de conexión en texto sin formato o cifrar la información de conexión con el cmdlet actualizado [New-AzureDataFactoryEncryptValue cmdlet][adf-encrypt-value-cmdlet] y luego usar la cadena cifrada de la propiedad de cadena de conexión en la carga de JSON del servicio vinculado. Consulte [Servicios vinculados][adf-msdn-linked-services] para obtener información sobre la definición de un servicio vinculado en JSON. La característica de cifrado aún no es compatible con el cmdlet New-AzureDataFactoryEncryptValue. 

## Notas para la versión de 12/11/2014 de la Factoría de datos ##

### Nuevas mejoras

- Integración de aprendizaje automático de Azure
	- Esta versión del servicio Factoría de datos de Azure le permite integrar la Factoría de datos de Azure con el Aprendizaje automático de Azure (ML) mediante **AzureMLLinkedService** y **AzureMLBatchScoringActivity**. Vea [Crear canalizaciones predictivas con la Factoría de datos y el Aprendizaje automático de Azure][adf-azure-ml] para obtener más información. 
- Se proporciona el estado de la versión de puerta de enlace
	- El estado de "NewVersionAvailable" se mostrará en el Portal de vista previa de Azure y en la salida del cmdlet Get-AzureDataFactoryGateway, si hay una versión más reciente de la puerta de enlace disponible que la instalada actualmente. A continuación, puede seguir el viaje del portal para descargar el nuevo archivo de instalación (.msi) y ejecutarlo para instalar la puerta de enlace más reciente. No se necesita ninguna configuración adicional.

### Cambios

- Se quita JobsContainer en HdInsightOnDemandLinkedService.
	- En la definición de JSON para HDInsightOnDemandLinkedService, ya no tiene que especificar la propiedad **jobsContainer**. Si tiene la propiedad especificada para un servicio vinculado a petición, se ignora la propiedad. Puede quitar la propiedad de la definición de JSON para el servicio vinculado y actualizar la definición de servicio vinculado mediante el cmdlet New-AzureDataFactoryLinkedService.
- Parámetros de configuración opcionales para HDInsightOnDemandLinkedService
	- Esta versión presenta compatibilidad con algunos parámetros de configuración opcionales para HDInsightOnDemandLinked (clúster de HDInsight a petición). Vea [Propiedades ClusterCreateParameters][on-demand-hdi-parameters] para obtener más información.
- Se quita la ubicación de la puerta de enlace
	- Al crear una puerta de enlace de la factoría de datos de Azure mediante el portal o PowerShell (New-AzureDataFactoryGateway), ya no necesitará especificar la ubicación para la puerta de enlace. Se heredará la región de la factoría de datos. De forma similar, para configurar un servicio vinculado de SQL Server con JSON, la propiedad "gatewayLocation" ya no es necesaria. El SDK de .NET de la factoria de datos también se actualiza para reflejar estos cambios.
	- Si usa una versión anterior del SDK y PowerShell de Azure, seguirá teniendo que proporcionar la configuración de la ubicación.
 
     

#### Cambios drásticos
	
- CustomActivity a DotNetActivity
	- El nombre de la interfaz de **ICustomActivity** cambia a **IDotNetActivity**. Necesitará actualizar paquetes de NuGet de la factoría de datos y cambiar ICustomActivity a IDotNetActivity en el código fuente para su actividad personalizada.  
	- El tipo de actividad personalizada de la definición de JSON para su actividad personalizada debe cambiarse de **CustomActivity** a **DotNetActivity** 
	- Se ha cambiado el nombre de las clases **CustomActivity** y **CustomActivityProperties** a **DotNetActivity** y **DotNetActivityProperties** con el mismo conjunto de propiedades.

		Si usa la versión anterior del SDK y Azure PowerShell, puede continuar usando CustomActivity en lugar de DotNetActivity.
    
  		Vea el tutorial [Uso de actividades personalizadas en una canalización de la Factoría de datos de Azure][adf-custom-activities] sobre cómo crear una actividad personalizada y usarla en una canalización de la Factoría de datos de Azure.

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



 

<!---HONumber=62-->