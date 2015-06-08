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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: preguntas más frecuentes

## Preguntas generales

### P: ¿qué es el generador de datos de Azure?

La factoría de datos es un servicio totalmente administrado destinado a los desarrolladores para la composición de servicios de almacenamiento, movimiento y procesamiento de los datos en canalizaciones de datos tolerantes a errores y de gran disponibilidad. La factoría de datos funciona tanto en el almacenamiento de datos en la nube como local. Una canalización es un conjunto de entradas de datos, actividades de procesamiento y salidas de datos y se define con un scripting sencillo JSON y activado mediante comandos de PowerShell. Una vez activada, la factoría de datos organiza y programa las canalizaciones para que se ejecuten en HDInsight (Hadoop) con opciones para la administración automática del clúster en nombre del usuario. La factoría de datos también proporciona una experiencia visual de administración y supervisión a través del Portal de vista previa de Azure para supervisar todas las canalizaciones con información detallada sobre el funcionamiento y el estado del servicio en un panel.
 
### P: ¿qué desafío del cliente factoría de datos solucionar?

La Factoría de datos de Azure equilibra la agilidad de aprovechar diversos servicios de almacenamiento, procesamiento y movimiento de los datos en el almacenamiento relacional tradicional junto con los datos no estructurados, con las capacidades de control y supervisión de un servicio totalmente administrado.

### P: ¿a quién es los destinatarios del generador de datos?


- Desarrolladores de datos: que son responsables de la creación de servicios de integración entre Hadoop y otros sistemas:
	- Debe mantenerse actualizada e integrarse con un panorama de constante cambio y crecimiento de los datos
	- Debe escribir código personalizado para la producción de información, y es costosa, difícil de mantener y no presenta una gran disponibilidad ni es tolerante a errores

- Profesionales de TI: que desean incorporar datos más diversos en su infraestructura de TI:
	- Se requiere para mirar en todos los datos de una organización con el fin de obtener perspectivas empresariales enriquecidas
	- Debe administrar los recursos de proceso y almacenamiento para equilibrar el coste y la escala de manera local y en la nube
	- Debe agregar rápidamente diversos orígenes y procesamiento para abordar las nuevas necesidades del negocio, y mantener al mismo tiempo la visibilidad en todos los recursos de proceso y almacenamiento

###  P: ¿dónde puedo encontrar precios detalles de la factoría de datos de Azure?

Consulte [página de detalles de precios de generador de datos][adf-pricing-details] de los detalles de precios para el generador de datos de Azure.

### P: ¿Cómo puedo comenzar con el generador de datos de Azure?

- Para obtener información general del generador de datos de Azure, consulte [Introducción al generador de datos de Azure][adf-introduction].
- Para obtener un tutorial rápido, consulte [empezar a trabajar con el generador de datos de Azure][adfgetstarted].
- Para obtener documentación completa, vea [documentación del generador de datos de Azure][adf-documentation-landingpage].

  
### P: ¿cómo los clientes acceso factoría de datos?

Los clientes pueden obtener acceso al generador de datos a través de la [Portal de vista previa de Azure][azure-preview-portal].

### P: ¿cuál es la disponibilidad de la región de la fábrica de datos?

En la versión de vista previa pública, la factoría de datos solo estará disponible en el oeste de EE. UU. Los servicios de proceso y almacenamiento utilizados por las factorías de datos pueden estar en otras regiones.
 
### P: ¿Cuáles son los límites de número de generadores de datos/canalizaciones/actividades/datasets? 


- Número de generadores de datos dentro de una suscripción: 50
- Número de canalizaciones dentro de un generador de datos: 100
- Número de actividades dentro de una canalización: 10
- Número de conjuntos de datos con un generador de datos: 100

### P: ¿qué es la experiencia de creación/desarrollador con el servicio de generador de datos de Azure?

Puede crear factorías de datos mediante uno de los sistemas siguientes:

- **Portal de vista previa de azure**. Las hojas de Factoría de datos en el Portal de vista previa de Azure proporcionan una interfaz de usuario enriquecida para crear factorías de datos y servicios vinculados. El **Editor del generador de datos**, que también forma parte del portal, le permite crear fácilmente servicios vinculados, tablas, conjuntos de datos y las canalizaciones mediante la especificación de las definiciones de JSON para estos artefactos. Consulte [Editor del generador de datos][data-factory-editor] para obtener información general del editor y [comenzar con el generador de datos][datafactory-getstarted] para obtener un ejemplo del uso del editor de portal para crear e implementar un generador de datos.   
- **PowerShell azure**. Si es un usuario de PowerShell y prefiere usar PowerShell en lugar de la interfaz de usuario del Portal, puede usar los cmdlets de Factoría de datos de Azure que forman parte de Azure PowerShell para crear e implementar factorías de datos. Consulte [monitor factoría de datos de Azure con Azure PowerShell y crear][create-data-factory-using-powershell] para obtener un ejemplo simple y [Tutorial: mover y procesar los archivos de registro mediante el generador de datos][adf-tutorial] para obtener un ejemplo del uso de PowerShell avanzado cmdles crear ad implementar un generador de datos. Consulte [referencia de cmdlets de generador de datos][adf-powershell-reference] el contenido de MSDN Library para obtener información completa de cmdlets de generador de datos.  
- **Biblioteca de clases .NET**. Se pueden crear factorías de datos mediante programación con el SDK de .NET de Factoría de datos. Vea [crear, supervisar y administrar los generadores de datos mediante el SDK .NET][create-factory-using-dotnet-sdk] para un tutorial para crear un generador de datos con el SDK. NET. Consulte [referencia de biblioteca de clases de generador de datos][msdn-class-library-reference] para una amplia documentación del SDK de .NET del generador de datos.  
- **API DE REST**. También puede utilizar la API de REST expuesta por el servicio Factoría de datos de Azure para crear e implementar factorías de datos. Consulte [datos de referencia de la API de REST de fábrica][msdn-rest-api-reference] para una documentación exhaustiva de la API de REST de generador de datos. 

## Actividades - preguntas más frecuentes
### P: ¿Cuáles son los orígenes de datos admitidos y actividades?

- **Orígenes de datos admitidos:** el almacenamiento de Azure (blobs y tablas), SQL Server, base de datos de SQL Azure, sistema de archivos, bases de datos Oracle.
- **Admite actividades:**: actividad de copia (local a la nube y la nube a local), actividad de HDInsight (Pig, Hive, MapReduce, Hadoop transformaciones de transmisión por secuencias), Azure máquina aprendizaje por lotes de puntuación actividad, actividad de procedimiento almacenado y actividades personalizadas de C#.

### ¿Cuándo se ejecuta una actividad?
El **disponibilidad** de configuración de la tabla de datos de salida determina cuando se ejecuta la actividad. La actividad comprueba si se cumplen todas las dependencias de datos de entrada (es decir, **Listo** estado) antes de que comience la ejecución.

## Copiar actividad - preguntas más frecuentes
### P: ¿qué regiones son compatibles con la actividad de copia?

La actividad de copia es compatible con copiar los datos en las siguientes regiones: East US, East US 2, oeste de EE., EE. UU., Ee.uu. Central Norte, sur Central de EE, Europa del Norte, Europa occidental y sudeste de Asia.

Copiar los datos en otras regiones también se admite, mediante una de las regiones anteriormente para el enrutamiento de los datos. La operación de copia se mide según la región por la que se enrutan los datos.

Región de destino de copia | Región utilizado para el enrutamiento
-------------------------- | -----------------------
Asia oriental | Sudeste de Asia
Este de Japón | Oeste de EE. UU.
Oeste de Japón | Oeste de EE. UU.
Sur de Brasil | Este de EE. UU. 2

### ¿Cómo puedo copiar a varias tablas de salida?
Puede tener varias tablas de salida en una canalización, tal como se muestra en el ejemplo siguiente:

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### ¿Es mejor tener una canalización con varias actividades o una canalización separada para cada actividad? 
Se supone que las canalizaciones para agrupar actividades relacionadas. Lógicamente, puede mantener las actividades en una canalización, si las tablas que los conectan no son consumidas por cualquier otra actividad fuera de la canalización. De este modo, no necesitará períodos activos de canalización de cadena para que se relacionan entre sí. Además, la integridad de los datos de las tablas internas de la canalización mejor conservarán durante la actualización de la canalización. Actualización de canalización esencialmente detiene todas las actividades dentro de la canalización, los elimina y crea de nuevo. Desde la perspectiva de creación, también podría ser más fácil de ver el flujo de datos dentro de las actividades relacionadas en un archivo JSON para la canalización.

## Actividad de HDInsight - preguntas más frecuentes

### P: ¿qué regiones son compatibles con HDInsight?

Vea la sección disponibilidad geográfica en el siguiente artículo: o [Detalles de precios de HDInsight][hdinsight-supported-regions].

### P: ¿qué región utiliza un clúster de HDInsight a petición?

El clúster de HDInsight a petición se crea en la misma región que existe para el almacenamiento que se va a usar con el clúster.

### P: ¿cómo asociar cuentas de almacenamiento adicionales para el clúster de HDInsight?

Si está usando su propio HDInsight clúster (BYOC - Traer su propio clúster), vea los temas siguientes:

- [Usar un clúster de HDInsight con las tiendas de metadatos y las cuentas de almacenamiento alternativo][hdinsight-alternate-storage]
- [Utilice cuentas de almacenamiento adicional con HDInsight Hive][hdinsight-alternate-storage-2]

Si utiliza un clúster a petición que se crea el servicio del generador de datos, deberá especificar las cuentas de almacenamiento adicional para el HDInsight vinculan servicio para que el servicio de generador de datos puede registrarlos en su nombre. En la definición de JSON para el servicio vinculado a petición, utilice **additionalLinkedServiceNames** propiedad para especificar las cuentas de almacenamiento alternativo, como se muestra en el siguiente fragmento JSON:
 
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

En el ejemplo anterior, otherLinkedServiceName1 y otherLinkedServiceName2 representan servicios vinculados cuyas definiciones contienen las credenciales que el clúster de HDInsight se necesita tener acceso a las cuentas de almacenamiento alternativo.

## Actividad de procedimiento almacenado - preguntas más frecuentes
### ¿Qué orígenes de datos admite la actividad de procedimiento almacenado?
La actividad de procedimiento almacenado admite solo Azure SQL Database en este momento.

## Sectores: preguntas más frecuentes

### ¿Cómo puedo volver a ejecutar un sector?
Puede volver a ejecutar un segmento de una de las maneras siguientes:

- Haga clic en **ejecutar** en la barra de comandos en el **segmento de datos** módulo para el segmento en el portal. 
- Ejecutar **AzureDataFactorySliceStatus Set** cmdlet con el estado se establece en **PendingExecution** del sector.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Consulte [AzureDataFactorySliceStatus Set][set-azure-datafactory-slice-status] para obtener más información acerca del cmdlet.

### ¿Cuánto tiempo tardó en procesar un sector?
1. Haga clic en **conjuntos de datos** de mosaico en el **factoría de datos** módulo para su generador de datos.
2. Haga clic en el conjunto de datos específico en el **conjuntos de datos** blade.
3. Seleccione el sector que le interesa de la **recientes sectores** lista el **tabla** blade.
4. Haga clic en la actividad se ejecute desde el **se ejecuta la actividad** lista el **segmento de datos** blade. 
5. Haga clic en **propiedades** en mosaico en el **Detalles de ejecución de la actividad** blade. 
6. Debería ver el **duración** campo con un valor. Esto es el tiempo necesario para procesar el sector.   

### ¿Cómo detener un segmento de ejecución?
Si necesita detener la canalización de ejecución, puede usar [Suspender AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) cmdlet. Actualmente, la suspensión de la canalización no detiene las ejecuciones del sector que están en curso. Una vez que termine de las ejecuciones en curso, no se toma ningún segmento adicional.

Si desea detener todas las ejecuciones inmediatamente, la única manera sería eliminar la canalización y vuelva a crearlo. Si decide eliminar la canalización, no es necesario eliminar tablas y vinculado los servicios utilizados por la canalización.



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

<!---HONumber=GIT-SubDir-->