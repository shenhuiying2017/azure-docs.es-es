<properties 	
	pageTitle="Factoría de datos de Azure: ejemplos" 
	description="Proporciona detalles sobre ejemplos que se distribuyen con el servicio Factoría de datos de Azure." 
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
	ms.date="05/23/2016" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: ejemplos

## Ejemplos en GitHub
El [repositorio de GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contiene varios ejemplos que le ayudarán a arrancar rápidamente el servicio Factoría de datos de Azure (o) modificar los scripts y usarlos en su propia aplicación. La carpeta Samples\\JSON contiene fragmentos de JSON para escenarios comunes.

| Muestra | Descripción |
| :----- | :---------- | 
| [Tutorial de ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | Este ejemplo ofrece un tutorial exhaustivo para el procesamiento de archivos de registro mediante Data Factory de Azure a fin de convertir los datos de los archivos de registro en información útil. <br/><br/>En este tutorial, la canalización de Data Factory recopila registros de muestra, procesa y enriquece los datos de los registros con datos de referencia, y transforma los datos para evaluar la eficacia de una campaña de marketing lanzada recientemente. |
| [Muestras de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | Esta muestra ofrece ejemplos de JSON para escenarios comunes. | 
| [Muestra de descargador de datos HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Muestra la descarga de datos de un punto de conexión HTTP al Almacenamiento de blobs de Azure mediante una actividad .NET personalizada. |
| [Muestra de CrossAppDomainDotNetActivity](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Esta muestra le permite crear una actividad .NET personalizada que no esté restringida a las versiones de ensamblado utilizadas por el iniciador de ADF (p. ej., WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.). |
| [Ejecutar script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) | Esta muestra incluye la actividad personalizada de Data Factory que puede usarse para invocar RScript.exe. Esta muestra solo funciona con su propio clúster de HDInsight (no a petición) que ya tiene R instalado. |
| [Invocar trabajos de Spark en clústeres de Hadoop de HDInsight](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | Este ejemplo muestra cómo utilizar la actividad MapReduce para invocar un programa Spark. El programa Spark se limita a copiar datos de un contenedor de blobs de Azure a otro. |
| [Análisis de Twitter mediante actividad de puntuación de lotes de Aprendizaje automático de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | Este ejemplo muestra cómo utilizar AzureMLBatchScoringActivity para invocar un modelo de Aprendizaje automático de Azure que realiza análisis de opinión de Twitter, puntuaciones, predicciones, etc. |
| [Análisis de Twitter mediante actividad personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) | Este ejemplo muestra cómo usar una actividad de .NET personalizada para invocar un modelo de Aprendizaje automático de Azure que realiza análisis de opinión de Twitter, puntuaciones, predicciones, etc. |
| [Canalizaciones con parámetros para el Aprendizaje automático de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | Esta muestra ofrece un código C# de un extremo a otro a fin de implementar canalizaciones N para la puntuación y el reciclaje de cada una con un parámetro de una región distinta, donde la lista regiones procede de un archivo .txt de parámetros que se incluye con esta muestra. | 
| [Actualización de datos de referencia para los trabajos de Análisis de transmisiones de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) | Este ejemplo muestra cómo aprovechar Data Factory de Azure y Análisis de transmisiones de Azure de forma conjunta para ejecutar las consultas con datos de referencia y configurar la actualización de dichos datos en un programa. |
| [Canalización híbrida con Hadoop Hortonworks local](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | El ejemplo utiliza un clúster de Hadoop local como un destino de proceso para ejecutar trabajos en Data Factory del mismo modo en que agregaría otros destinos de proceso, como un clúster de Hadoop basado en HDInsight en la nube. |
| [Herramienta de conversión de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | Esta herramienta le permite convertir los archivos JSON de una versión anterior a la versión preliminar del 01-07-2015 a la más reciente o a la preliminar del 01-07-2015 (predeterminada). |  
| [Archivo de entrada de ejemplo de U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) | Se trata de un archivo de ejemplo utilizado por una actividad U-SQL. | 



## Ejemplos en el Portal de Azure
Puede usar el icono **Canales de muestras** de la página principal de la factoría de datos para implementar canalizaciones de ejemplo y sus entidades asociadas (conjuntos de datos y servicios vinculados) en la factoría de datos.

1. Cree una factoría de datos nueva o abra una ya existente. Consulte [Introducción a Factoría de datos de Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory) para ver los pasos crear una factoría de datos.
2. En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en el icono **Canalizaciones de ejemplo**.

	![Icono Canalizaciones de ejemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. En la hoja **Canalizaciones de ejemplo**, haga clic en el **ejemplo** que quiere implementar.
	
	![Hoja Canalizaciones de ejemplo](./media/data-factory-samples/SampleTile.png)

3. Especifique las opciones de configuración para el ejemplo. Por ejemplo, el nombre de la cuenta de almacenamiento de Azure y la clave de la cuenta, el nombre del servidor de SQL Azure, la base de datos, el id. de usuario y la contraseña, etc.

	![Hoja de ejemplo](./media/data-factory-samples/SampleBlade.png)

4. Cuando haya terminado de especificar las opciones de configuración, haga clic en **Crear** para crear o implementar las canalizaciones de ejemplo y los servicios o las tablas vinculados que usan las canalizaciones.
5. Verá el estado de implementación en el icono de ejemplo en el que hizo clic anteriormente en la hoja **Canalizaciones de ejemplo canalizaciones**.

	![Estado de la implementación](./media/data-factory-samples/DeploymentStatus.png)

6. Cuando vea el mensaje **La implementación se realizó correctamente** en el icono del ejemplo, cierre la hoja **Canalizaciones de ejemplo**.
5. En la hoja **FACTORÍA DE DATOS**, verá que los servicios vinculados, los conjuntos de datos y las canalizaciones se han agregado a la factoría de datos.

	![Hoja de la Factoría de datos](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## Ejemplos en Visual Studio

### Requisitos previos

Debe tener lo siguiente instalado en el equipo:

- Visual Studio 2013 o Visual Studio 2015.
- Descargue el SDK de Azure para Visual Studio 2013 o Visual Studio 2015. Vaya a la [página Descargas de Azure](https://azure.microsoft.com/downloads/) y haga clic en **VS 2013** o **VS 2015** en la sección **.NET**.
- Descargue el último complemento de Factoría de datos de Azure para Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si usa Visual Studio 2013, también puede actualizar el complemento mediante el proceso siguiente: haga clic en el menú **Herramientas** -> **Extensiones y actualizaciones** -> **En línea** -> **Galería de Visual Studio** -> **Herramientas de Factoría de datos de Microsoft Azure para Visual Studio** -> **Actualizar**.

### Uso de plantillas de Data Factory

1. Haga clic en **Archivo** en el menú, seleccione **Nuevo** y haga clic en **Proyecto**.
2. En el cuadro de diálogo **Nuevo proyecto**, haga lo siguiente:
	1. Seleccione **DataFactory** en **Plantillas**.
	2. Seleccione **Data Factory Templates** (Plantillas de Data Factory) en el panel derecho.
	3. Escriba un **nombre** para el proyecto.
	4. Seleccione una **ubicación** para el proyecto.
	5. Haga clic en **Aceptar**.

	![Cuadro de diálogo Nuevo proyecto](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. En el cuadro de diálogo **Data Factory Templates** (Plantillas de Data Factory), seleccione la plantilla de ejemplo desde la sección **Use-Case Templates** (Plantillas de caso de uso) y haga clic en **Siguiente**. Los siguientes pasos le guiarán en la utilización de la plantilla **Generación de perfiles de cliente**. Los pasos son similares para los otros ejemplos.

	![Cuadro de diálogo Plantillas de Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
7. En el cuadro de diálogo **Data Factory Configuration** (Configuración de Data Factory), haga clic en **Siguiente** en la página **Data Factory Basics** (Aspectos básicos de Data Factory).
8. En la página **Configure data factory** (Configurar factoría de datos), haga lo siguiente:
	1. Seleccione **Create New Data Factory** (Crear nueva factoría de datos) para el objetivo de este tutorial. También puede seleccionar **Use existing data factory** (Usar factoría de datos existente).
	2. Escriba un **nombre** para la factoría de datos.
	3. Seleccione la **suscripción de Azure** donde desea crear la factoría de datos.
	4. Seleccione el **grupo de recursos** para la factoría de datos.
	5. Seleccione **oeste de EE. UU.**, **este de EE. UU.** o **Europa del Norte** para la **región**.
	6. Haga clic en **Siguiente**.
9. En la página **Configure data stores** (Configurar almacenes de datos), especifique una **base de datos SQL de Azure** y una **cuenta de almacenamiento de Azure** ya existentes o cree otras nuevas y haga clic en Siguiente.
10. En la página **Configurar proceso**, seleccione los valores predeterminados y haga clic en **Siguiente**.
11. En la página **Resumen** revise toda la configuración y haga clic en **Siguiente**.
12. En la página **Estado de la implementación**, espere hasta que finalice la implementación y haga clic en **Finalizar**.
13. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y haga clic en **Publicar**.
19. Si ve el cuadro de diálogo **Iniciar sesión en tu cuenta Microsoft**, escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **Iniciar sesión**.
20. Debería ver el siguiente cuadro de diálogo:

	![Cuadro de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. En la página **Configure data factory** (Configurar factoría de datos), haga lo siguiente:
	1. Confirme la opción **Use existing data factory** (Usar factoría de datos existente).
	2. Seleccione la **factoría de datos** que había seleccionado al utilizar la plantilla anterior.
	6. Haga clic en **Siguiente** para cambiar a la página **Publicar elementos**. (Presione la tecla **TAB** para salir del campo Nombre si el botón **Siguiente** está deshabilitado).
23. En la página **Publicar elementos**, asegúrese de que todas las entidades de Factorías de datos están seleccionadas y haga clic en **Siguiente** para cambiar a la página **Resumen**.
24. Revise el resumen y haga clic en **Siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página **Estado de implementación**, debería ver el estado del proceso de implementación. Cuando se haya completado la implementación, haga clic en Finalizar.

Consulte [Compilación de la primera Data Factory de Azure mediante Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) para obtener más información sobre cómo usar Visual Studio para crear entidades de Data Factory y publicarlas en Azure.

<!---HONumber=AcomDC_0629_2016-->