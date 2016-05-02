<properties 
	pageTitle="Factoría de datos de Azure: preguntas más frecuentes" 
	description="Preguntas más frecuentes acerca de la factoría de datos de Azure." 
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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: preguntas más frecuentes

## Preguntas generales

### ¿Qué es la factoría de datos de Azure?

Data Factory es un servicio de integración de datos basado en la nube que **automatiza el movimiento y la transformación de datos**. Del mismo modo que una fábrica que pone equipo en funcionamiento para tomar materias primas y transformarlas en productos terminados, la Factoría de datos organiza los servicios existentes que recopilan datos sin procesar y los transforman en información lista para usar.
 
El servicio Data Factory permite crear flujos de trabajo controlados por datos para mover datos entre almacenes de datos locales y en la nube, así como procesar o transformar datos mediante servicios de proceso como HDInsight de Azure y Análisis de Azure Data Lake. Después de crear una canalización que realice la acción que necesita, puede programar que se ejecute periódicamente (cada hora, diariamente, semanalmente, etc.).

Consulte [Introducción y conceptos clave](data-factory-introduction.md) para obtener más detalles.

### ¿Dónde puedo encontrar detalles de precios de la factoría de datos de Azure?

Vea la [página de detalles de precios de Factoría de datos][adf-pricing-details] para obtener información al respecto.

### ¿Cómo puedo comenzar con la factoría de datos de Azure?

- Para obtener información general sobre la factoría de datos de Azure, vea [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).
- Para ver un tutorial rápido, vea [Introducción a la Factoría de datos de Azure](data-factory-get-started.md).
- Para obtener la documentación completa, vea [Documentación de la Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/).
  
### ¿Cuál es la disponibilidad de regiones de la factoría de datos?
Data Factory está disponible en el **Oeste de EE. UU.** y en **Europa del Norte**. Los servicios de proceso y almacenamiento utilizados por las factorías de datos pueden estar en otras regiones. Consulte las [regiones admitidas](data-factory-introduction.md#supported-regions).
 
### ¿Cuáles son los límites en el número de factorías, canalizaciones, actividades y conjuntos de datos?
 
Consulte la sección **Límites de la Factoría de datos de Azure** del artículo [Límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md#data-factory-limits).


### ¿Cuál es la experiencia de desarrollador/creación con el servicio de Factoría de datos de Azure?

Puede crear factorías de datos mediante uno de los sistemas siguientes:

- **Portal de Azure** Las hojas de Data Factory en el Portal de Azure proporcionan una interfaz de usuario enriquecida para crear factorías de datos y servicios vinculados. El **Editor de Factoría de datos**, que también forma parte del portal, le permite crear fácilmente servicios vinculados, tablas, conjuntos de datos y canalizaciones mediante la especificación de definiciones de JSON para estos artefactos. Vea [Información general de Data Factory](data-factory-get-started.md) para obtener un ejemplo de uso del portal o el editor para crear e implementar una factoría de datos.   

- **Azure PowerShell** Si es un usuario de PowerShell y prefiere usar PowerShell en lugar de la interfaz de usuario del Portal, puede usar los cmdlets de Azure Data Factory que forman parte de Azure PowerShell para crear e implementar factorías de datos. Consulte [Crear y supervisar la Factoría de datos de Azure con Azure PowerShell](data-factory-monitor-manage-using-powershell.md) para obtener un ejemplo sencillo y [Tutorial: desplazamiento y procesamiento de archivos de registro mediante la Factoría de datos][adf-tutorial] para un ejemplo avanzado de uso de cmdlets de PowerShell para crear e implementar una factoría de datos. Consulte el contenido de [Referencia de cmdlets de Factoría de datos][adf-powershell-reference] en MSDN Library para obtener documentación completa de los cmdlets de Factoría de datos.
  
- **Visual Studio** También puede usar Visual Studio para crear, supervisar y administrar mediante programación las factorías de datos. Consulte el artículo [Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de .NET de la factoría de datos](data-factory-create-data-factories-programmatically.md) para obtener detalles.
  
- **Biblioteca de clases .NET** Se pueden crear factorías de datos mediante programación con el SDK de .NET de Data Factory. Consulte [Creación, supervisión y administración de las factorías de datos mediante el SDK de .NET][create-factory-using-dotnet-sdk] para un ver tutorial sobre la creación de una factoría de datos con el SDK de .NET. Consulte [Referencia de biblioteca de clases de Factoría de datos][msdn-class-library-reference] para una amplia documentación sobre el SDK de .NET de Factoría de datos.

- **API de REST** También puede usar la API de REST expuesta por el servicio Azure Data Factory para crear e implementar factorías de datos. Consulte [Referencia de la API de REST de la Factoría de datos][msdn-rest-api-reference] para ver una amplia documentación sobre la API de REST de la Factoría de datos.
 
- **Plantilla de Azure Resource Manager** Consulte [Tutorial: Compilación de la primera Data Factory de Azure con la plantilla de Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) para obtener más información.

### ¿Se puede cambiar el nombre de una Factoría de datos?
No. Al igual que otros recursos de Azure, el nombre de una Factorías de datos de Azure no se puede cambiar.

## Actividades: preguntas más frecuentes
### ¿Qué tipos de actividades diferentes se pueden usar en una canalización de Data Factory? 

- [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para mover los datos.
- [Actividades de transformación de datos](data-factory-data-transformation-activities.md) para procesar o transformar los datos. 

### ¿Cuándo se ejecuta una actividad?
La configuración de **disponibilidad** en la tabla de datos de salida determina cuándo se ejecuta la actividad. Si se especifican conjuntos de datos de entrada, la actividad comprueba si se cumplen todas las dependencias de datos de entrada (es decir, estado **Listo**) antes de ejecutarse.

## Actividad de copia: preguntas más frecuentes
### ¿Es mejor tener una canalización con varias actividades o una canalización independiente para cada actividad? 
Se supone que las canalizaciones incluyen actividades relacionadas. Lógicamente, puede mantener las actividades en una canalización si las tablas que las conectan no se consumen por otra actividad fuera de la canalización. De este modo, no necesitará períodos activos de canalizaciones de cadena puesto que se alinean con las demás. Además, la integridad de los datos de las tablas internas de la canalización se conservarán mejor cuando se actualice la canalización. La actualización de la canalización detiene fundamentalmente todas las actividades en la canalización, las elimina y las vuelve a crear. Desde la perspectiva de la creación, puede ser más fácil ver el flujo de datos dentro de las actividades relacionadas en un archivo JSON para la canalización.

## Actividad de HDInsight: preguntas más frecuentes

### ¿Qué regiones admiten HDInsight?

Vea la sección Disponibilidad geográfica en el siguiente artículo: o [Detalles de precios de HDInsight][hdinsight-supported-regions].

### ¿Qué región se usa con un clúster de HDInsight a petición?

El clúster de HDInsight a petición se crea en la misma región que existe para el almacenamiento que se va a usar con el clúster.

### ¿Cómo se asocian cuentas de almacenamiento adicionales al clúster de HDInsight?

Si está usando su propio clúster de HDInsight (BYOC, Bring Your Own Cluster, traiga su propio clúster), vea los temas siguientes:

- [Uso de un clúster de HDInsight con cuentas de almacenamiento y tiendas de metadatos alternativas][hdinsight-alternate-storage]
- [Uso de cuentas de almacenamiento adicionales con HDInsight Hive][hdinsight-alternate-storage-2]

Si usa un clúster a petición que se crea con el servicio de la Factoría de datos, deberá especificar las cuentas de almacenamiento adicionales para el servicio vinculado de HDInsight de manera que el servicio de la factoría de datos pueda registrarlas en su nombre. En la definición de JSON para el servicio vinculado a petición, use la propiedad **additionalLinkedServiceNames** para especificar las cuentas de almacenamiento alternativas como se muestra en el siguiente fragmento JSON:
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

En el ejemplo anterior, otherLinkedServiceName1 y otherLinkedServiceName2 representan servicios vinculados cuyas definiciones contienen las credenciales que el clúster de HDInsight necesita para acceder a las cuentas de almacenamiento alternativas.

## Segmentos: preguntas más frecuentes

### ¿Cómo puedo volver a ejecutar un segmento?
Puede volver a ejecutar un segmento de una de las siguientes maneras:

- Haga clic en **Ejecutar** en la barra de comandos de la hoja **SEGMENTO DE DATOS** para el segmento del portal. 
- Ejecute el cmdlet **Set-AzureRmDataFactorySliceStatus** con el estado establecido en **En espera** para el segmento.   
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Consulte [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] para más información sobre el cmdlet.

### ¿Cuánto tiempo se tardó en procesar un segmento?
1. Haga clic en la ventana **Conjuntos de datos** de la hoja **FACTORÍA DE DATOS** para su factoría de datos.
2. Haga clic en el conjunto de datos específico de la hoja **Conjuntos de datos**.
3. Seleccione el segmento en el que está interesado en la lista **Segmentos recientes** de la hoja **TABLA**.
4. Haga clic en la actividad ejecutada en la lista **Ejecuciones de actividad** de la hoja **SEGMENTO DE DATOS**. 
5. Haga clic en la ventana **Propiedades** de la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**. 
6. Debería ver el campo **DURACIÓN** con un valor. Este es el tiempo necesario para procesar el segmento.   

### ¿Cómo detener un segmento en ejecución?
Si necesita detener la ejecución de la canalización, puede usar el cmdlet [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx). Actualmente, la suspensión de la canalización no detiene las ejecuciones de segmentos en curso. Cuando terminan las ejecuciones en curso, no se selecciona ningún segmento adicional.

Si desea realmente detener todas las ejecuciones inmediatamente, la única manera sería eliminar la canalización y crearla de nuevo. Si decide eliminar la canalización, NO es necesario eliminar tablas y servicios vinculados usados por la canalización.


[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0420_2016-->