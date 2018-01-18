---
title: "Guía técnica para la previsión de demanda de energía | Microsoft Docs"
description: "Guía técnica de la plantilla de solución con Microsoft Cortana Intelligence para la previsión de demanda de energía."
services: cortana-analytics
documentationcenter: 
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: bb3520d36e4c34c752fe388f3126da285e2161cd
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guía técnica de la plantilla de solución de Cortana Intelligence para la previsión de demanda de energía
## <a name="overview"></a>**Información general**
Las plantillas de solución están diseñadas para acelerar el proceso de creación de una demostración E2E basada en Cortana Intelligence Suite. Una plantilla implementada aprovisiona la suscripción con los componentes necesarios de Cortana Intelligence y crea las relaciones entre ellos. También proporciona a la canalización de datos de ejemplo que se generan desde una aplicación de simulación de datos. Descargue el simulador de datos desde el vínculo que se proporciona e instálelo en su máquina local. Consulte el archivo léame.txt para obtener instrucciones sobre cómo utilizar el simulador. Los datos generados con el simulador hidratan la canalización de datos y comienzan a generar predicciones de aprendizaje automático que luego se pueden visualizar en el panel de Power BI.

Puede encontrar la plantilla de solución [aquí](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

El proceso de implementación le guía a través de varios pasos para configurar las credenciales de la solución. Asegúrese de que registra estas credenciales: nombre de la solución, nombre de usuario y contraseña que proporcionó durante la implementación.

El objetivo de este documento es explicar la arquitectura de referencia y los diferentes componentes aprovisionados en su suscripción como parte de esta plantilla de solución. El documento trata también sobre cómo reemplazar los datos de ejemplo con datos reales para poder ver detalles y predicciones a partir de sus propios datos. Además, el documento explica las partes de la plantilla de solución que deberán modificarse si desea personalizar la solución con sus propios datos. Se proporcionan instrucciones sobre cómo crear el panel de Power BI para esta plantilla de solución al final.

## <a name="details"></a>**Detalles**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arquitectura explicada
Cuando se implementa la solución, se activan varios servicios de Azure en Cortana Analytics Suite (es decir, Event Hubs, Stream Analytics, HDInsight, Data Factory, Machine Learning, *etc.*). El diagrama de arquitectura muestra, a nivel general, cómo se construye la plantilla de solución Previsión de demanda de energía de un extremo a otro. Puede investigar estos servicios haciendo clic en ellos en el diagrama de la plantilla de solución creado con la implementación de la solución. Las secciones siguientes describen cada parte.

## <a name="data-source-and-ingestion"></a>**Origen e ingesta de datos**
### <a name="synthetic-data-source"></a>Origen de datos sintéticos
Para esta plantilla, el origen de datos utilizado se genera desde una aplicación de escritorio que se descarga y ejecuta localmente tras una implementación correcta. Puede encontrar las instrucciones para descargar e instalar esta aplicación en la barra de propiedades al seleccionar el primer nodo denominado Energy Forecasting Data Simulator en el diagrama de la plantilla de solución. Esta aplicación alimenta el servicio [Azure Event Hubs](#azure-event-hub) con puntos de datos o eventos, que se usan en el flujo de solución restante.

La aplicación de generación de eventos rellena los recursos de Azure Event Hubs solo mientras se esté ejecutando en el equipo.

### <a name="azure-event-hub"></a>Centro de eventos de Azure
El servicio [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) es el destinatario de la entrada proporcionada por el origen de datos sintéticos descrito.

## <a name="data-preparation-and-analysis"></a>**Preparación y análisis de datos**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
El servicio [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) se usa para proporcionar análisis casi en tiempo real en el flujo de entrada del servicio [Azure Event Hubs](#azure-event-hub) y para publicar los resultados en un panel de [Power BI](https://powerbi.microsoft.com) y, al mismo tiempo, archivar todos los eventos de entrada sin procesar en el servicio [Azure Storage](https://azure.microsoft.com/services/storage/) para su posterior procesamiento mediante [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### <a name="hdinsight-custom-aggregation"></a>Agregación personalizada de HDInsight
El servicio Azure HDInsight se utiliza para ejecutar scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (organizados mediante Azure Data Factory) para proporcionar las agregaciones en los eventos sin procesar que se almacenaron con el servicio Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Coordinado por Data Factory de Azure, el servicio [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) se usa para realizar previsiones sobre el consumo futuro de energía de una región determinada, en función de los datos recibidos.

## <a name="data-publishing"></a>**Publicación de datos**
### <a name="azure-sql-database-service"></a>Servicio Azure SQL Database.
Administrado por Azure Data Factory, el servicio [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) se usa para almacenar las predicciones recibidas por el servicio Azure Machine Learning que se usan en el panel de [Power BI](https://powerbi.microsoft.com).

## <a name="data-consumption"></a>**Consumo de datos**
### <a name="power-bi"></a>Power BI
El servicio [Power BI](https://powerbi.microsoft.com) se usa para mostrar un panel que contiene las agregaciones proporcionadas por el servicio [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), así como los resultados de previsión de demanda almacenados en [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) que se produjeron al usar el servicio [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Para obtener instrucciones sobre cómo crear el panel de Power BI para esta plantilla de solución, vea la sección siguiente.

## <a name="how-to-bring-in-your-own-data"></a>**Incorporación de sus propios datos**
Esta sección describe cómo traer sus propios datos a Azure y qué áreas necesitarían cambios para los datos que ha traído a esta arquitectura.

Es improbable que cualquier conjunto de datos que proporcione coincida con el conjunto de datos utilizado en esta plantilla de solución. Entender bien los datos y los requisitos es fundamental a la hora de modificar esta plantilla para que funcione con sus propios datos. Si es nuevo en el servicio Azure Machine Learning, puede obtener una introducción al mismo mediante el ejemplo que puede encontrar en [Creación del primer experimento](machine-learning/studio/create-experiment.md).

En los apartados siguientes se describen las secciones de la plantilla que requieren modificaciones cuando se introduce un nuevo conjunto de datos.

### <a name="azure-event-hub"></a>Centro de eventos de Azure
El servicio [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) es genérico, hasta tal punto que se pueden publicar datos en el centro en formato CSV o JSON. No se produce ningún procesamiento especial en el Centro de eventos de Azure, pero es importante que comprenda los datos que se introducen en él.

En este documento no se describe cómo ingerir los datos, pero puede enviar fácilmente eventos o datos a un Centro de eventos de Azure mediante la [API del Centro de eventos](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
El servicio [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) se usa para proporcionar análisis casi en tiempo real mediante la lectura de flujos de datos y el envío de datos a cualquier número de orígenes.

Para la plantilla de solución Previsión de demanda de energía, la consulta de Azure Stream Analytics consta de dos subconsultas, cada una de las cuales consume eventos del servicio Azure Event Hubs como entradas y tiene salidas a dos ubicaciones distintas. Estas salidas constan de un conjunto de datos de Power BI y una ubicación de Azure Storage.

La consulta de [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) puede encontrarse:

* Mediante el inicio de sesión en [Azure Portal](https://portal.azure.com/).
* Mediante la búsqueda de los trabajos de Análisis de transmisiones ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) que se hayan generado al implementar la solución. En el primer caso se insertan datos en Blob Storage (por ejemplo, mytest1streaming432822asablob) y en el segundo caso se insertan datos en Power BI (por ejemplo, mytest1streaming432822asapbi).
* Seleccionar

  * ***INPUTS*** para ver la entrada de consulta
  * ***QUERY*** para ver la consulta en sí
  * ***OUTPUTS*** para ver las diferentes salidas

Puede encontrar información acerca de la construcción de consultas de Análisis de transmisiones de Azure en [Stream Analytics Query Reference (Referencia de consultas en Análisis de transmisiones)](https://msdn.microsoft.com/library/azure/dn834998.aspx) en MSDN.

En esta solución, el trabajo de Azure Stream Analytics que envía un conjunto de datos con información de análisis casi en tiempo real sobre el flujo de datos de entrada a un panel de Power BI se proporciona como parte de esta plantilla de solución. Dado que hay un conocimiento implícito acerca del formato de datos entrantes, estas consultas deben modificarse según su formato de datos.

El segundo trabajo de Azure Stream Analytics envía todos los eventos del [Centro de eventos](https://azure.microsoft.com/services/event-hubs/) a [Azure Storage](https://azure.microsoft.com/services/storage/) y, con independencia del formato de los datos, no necesita modificación ya que toda la información de los eventos se transmitió al almacenamiento.

### <a name="azure-data-factory"></a>Azure Data Factory
El servicio [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) organiza el traslado y procesamiento de los datos. En la plantilla de solución Previsión de demanda de energía, la factoría de datos se compone de doce [canalizaciones](data-factory/concepts-pipelines-activities.md) que mueven y procesan los datos mediante varias tecnologías.

  Puede tener acceso a la factoría de datos si abre el nodo Data Factory en la parte inferior del diagrama de la plantilla de solución que se creó con la implementación de la solución. Puede ver la factoría de datos en Azure Portal. Si ve errores en los conjuntos de datos, puede ignorarlos ya que se deben a que la Factoría de datos se implementó antes de iniciarse el generador de datos. Estos errores no impiden que la Factoría de datos funcione.

Esta sección describe las [canalizaciones](data-factory/concepts-pipelines-activities.md) y [actividades](data-factory/concepts-pipelines-activities.md) necesarias contenidas en [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). La imagen siguiente se corresponde con la vista de diagrama de la solución:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Cinco de las canalizaciones de esta factoría contienen scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) que se usan para particionar y agregar los datos. Cuando los anote, los scripts se encontrarán en la cuenta de [Azure Storage](https://azure.microsoft.com/services/storage/) creada durante la instalación. Su ubicación es: demandforecasting\\\\script\\\\hive\\\\ o https://[nombre de su solución].blob.core.windows.net/demandforecasting.

Al igual que las consultas de [Azure Stream Analytics](#azure-stream-analytics-1), los scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) tienen un conocimiento implícito sobre el formato de los datos entrantes, por lo que estas consultas tendrían que modificarse en función del formato de los datos y de los requisitos de la [ingeniería de características](machine-learning/team-data-science-process/create-features.md).

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
Esta [canalización](data-factory/concepts-pipelines-activities.md) contiene una única actividad, una actividad de [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) que usa un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para agregar cada diez segundos datos transmitidos a petición en el nivel de subestación al nivel de región horaria y colocarlos en [Azure Storage](https://azure.microsoft.com/services/storage/) mediante el trabajo de Azure Stream Analytics.

El script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarea de creación de particiones es ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
Esta [canalización](data-factory/concepts-pipelines-activities.md) contiene dos actividades:

* La actividad [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) que usa un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de Hive para agregar los datos de demanda del historial de horas en el nivel de subestación al nivel de región horaria y colocarlos en Azure Storage durante el trabajo de Azure Stream Analytics.
* [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los datos agregados desde el blob de Azure Storage hasta la instancia de Azure SQL Database que se aprovisionó como parte de la instalación de la plantilla de solución.

El script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) de esta tarea es ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Esta [canalización](data-factory/concepts-pipelines-activities.md) contiene varias actividades cuyo resultado final son las predicciones puntuadas a partir del experimento de Azure Machine Learning asociado a esta plantilla de solución. Son casi idénticas, excepto que cada una de ellas solo administra la región diferente, lo cual se realiza pasando un valor de RegionID diferente en la canalización ADF y el script de Hive para cada región.  
Las actividades contenidas en esta canalización son:

* Una actividad de [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) que usa un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de Hive para realizar agregaciones y la ingeniería de características necesarias para el experimento de Azure Machine Learning. Los scripts de Hive de esta tarea son los archivos ***PrepareMLInputRegionX.hql*** respectivos.
* Una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los resultados de la actividad [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) a un solo blob de Azure Storage al que se puede acceder mediante la actividad [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que llama al experimento de Azure Machine Learning que hace que los resultados se coloquen en un único recurso de Azure Storage Blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Esta [canalización](data-factory/concepts-pipelines-activities.md) contiene una única actividad: una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los resultados del experimento de Azure Machine Learning desde la canalización ***MLScoringRegionXPipeline*** correspondiente a la instancia de Azure SQL Database que se haya aprovisionado como parte de la instalación de la plantilla de solución.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Esta [canalización](data-factory/concepts-pipelines-activities.md) contiene una única actividad: una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los datos de demanda continua agregados desde ***LoadHistoryDemandDataPipeline*** hasta la instancia de Azure SQL Database que se haya aprovisionado como parte de la instalación de la plantilla de solución.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Esta [canalización](data-factory/concepts-pipelines-activities.md) contiene una única actividad: una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los datos de referencia de una región, subestación o topología, que se cargan en un recurso de Azure Storage Blob como parte de la instalación de la plantilla de solución, en la instancia de Azure SQL Database que se haya aprovisionado como parte de dicha instalación.

### <a name="azure-machine-learning"></a>Azure Machine Learning
El experimento de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usado en esta solución proporciona la predicción de demanda de la región. El experimento es específico del conjunto de datos consumido y, por tanto, requiere la modificación o el reemplazo específico de los datos que se han aportado.

## <a name="monitor-progress"></a>**Supervisión de progreso**
Una vez iniciado el Generador de datos, la canalización comienza a hidratarse y los distintos componentes de la solución empiezan a entrar en acción siguiendo los comandos emitidos por la Factoría de datos. Hay dos maneras de supervisar la canalización.

1. Comprobar los datos de Azure Blob Storage.

    Uno de los trabajos de Stream Analytics escribe los datos entrantes sin procesar en Blob Storage. Si hace clic en el componente **Azure Blob Storage** de su solución desde la pantalla donde implementó correctamente la solución y después hace clic en **Abrir** en el panel derecho, irá a [Azure Portal](https://portal.azure.com). Una vez allí, haga clic en **Blobs**. En el siguiente panel, verá una lista de Containers. Haga clic en **"energysadata"**. En el siguiente panel, verá la carpeta **"demandongoing"**. Dentro de la carpeta rawdata, verá carpetas con nombres como date=2016-01-28, etc. Si ve estas carpetas, significa que los datos sin procesar se están generando en el equipo y almacenando en Almacenamiento de blobs correctamente. Verá archivos que deben tener tamaños finitos en MB en esas carpetas.
2. Comprobar los datos de Azure SQL Database.

    El último paso de la canalización consiste en escribir los datos (por ejemplo, predicciones del aprendizaje automático) en SQL Database. Es posible que deba esperar hasta dos horas para que los datos aparezcan en SQL Database. Puede supervisar la cantidad de datos disponibles en SQL Database a través de [Azure Portal](https://portal.azure.com/). En el panel izquierdo, ubique SQL DATABASES![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) y haga clic aquí. A continuación, busque su base de datos (es decir, demo123456db) y haga clic en ella. En la siguiente página, en la sección **"Conectarse a la base de datos"**, haga clic en **"Ejecutar consultas Transact-SQL en la base de datos SQL"**.

    Aquí, puede hacer clic en Nueva consulta y consultar el número de filas (por ejemplo, "select count(*) from DemandRealHourly"). Conforme crece la base de datos, el número de filas de la tabla debería aumentar.
3. Compruebe los datos desde el panel de Power BI.

    Puede configurar el panel de análisis en caliente de Power BI para supervisar los datos de entrada sin formato. Siga las instrucciones de la sección "Panel de Power BI".

## <a name="power-bi-dashboard"></a>**Panel de Power BI**
### <a name="overview"></a>Información general
En esta sección se describe cómo configurar el panel de Power BI para visualizar los datos en tiempo real de Azure Stream Analytics (análisis en caliente), así como los resultados de previsión de Azure Machine Learning (análisis en frío).

### <a name="setup-hot-path-dashboard"></a>Configuración del panel de análisis en caliente
Los siguientes pasos lo guiarán en el proceso para visualizar la salida de datos en tiempo real de los trabajos de Stream Analytics que se generaron en el momento de la implementación de la solución. Se necesita una cuenta de [Power BI en línea](http://www.powerbi.com/) para realizar los pasos siguientes. Si no tiene una cuenta, puede [crear una](https://powerbi.microsoft.com/pricing).

1. Agregue una salida de Power BI en Azure Stream Analytics (ASA).

   * Debe seguir las instrucciones descritas en [Azure Stream Analytics y Power BI: panel de análisis en tiempo real de flujo de datos](stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar la salida del trabajo de Azure Stream Analytics como panel de Power BI.
   * Busque el trabajo de Stream Analytics en [Azure Portal](https://portal.azure.com). El nombre del trabajo debe ser: SuNombreDeSolución+"streamingjob"+número aleatorio+"asapbi" (es decir, demostreamingjob123456asapbi).
   * Agregue una salida de Power BI para el trabajo ASA. Establezca el **Alias de salida** como **'PBIoutput'**. Configure **Nombre de conjunto de datos** y **Nombre de tabla** como **"EnergyStreamData"**. Una vez agregada la salida, haga clic en **"Iniciar"** en la parte inferior de la página para iniciar el trabajo de Stream Analytics. Recibirá un mensaje de confirmación (por ejemplo, "Se ha iniciado correctamente el trabajo de análisis de transmisiones myteststreamingjob12345asablob").
2. Inicie sesión en [Power BI en línea](http://www.powerbi.com)

   * En la sección Conjuntos de datos del panel izquierdo, en Mi área de trabajo, debería poder ver un nuevo conjunto de datos en el panel izquierdo de Power BI. Son los datos de transmisión que insertó desde Azure Stream Analytics en el paso anterior.
   * Asegúrese de que el panel ***Visualizaciones*** está abierto y se muestra en el lado derecho de la pantalla.
3. Cree el icono "Demanda por marca de hora":

   * Haga clic en el conjunto de datos **"EnergyStreamData"** en la sección Conjuntos de datos del panel izquierdo.
   * Haga clic en el icono **"Gráfico de líneas"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Haga clic en "EnergyStreamData" en el panel **Campos** .
   * Haga clic en **"Marca de hora"** y asegúrese de que se muestra en "Eje". Haga clic en **"Carga"** y asegúrese de que se muestra en "Valores".
   * Haga clic en **GUARDAR** en la parte superior y asigne al informe el nombre "EnergyStreamDataReport". El informe denominado "EnergyStreamDataReport" se muestra en la sección Informes en el panel del navegador de la izquierda.
   * Haga clic en el icono **"Anclar visualización"**![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) en la esquina superior derecha de este gráfico de líneas y puede que se muestre una ventana "Anclar en el panel" para que elija un panel. Seleccione "EnergyStreamDataReport" y luego haga clic en "Anclar".
   * Mantenga el mouse sobre este icono en el panel y haga clic en el icono "Editar" en la esquina superior derecha para cambiar el título a "Demanda por marca de hora".
4. Cree otros iconos de panel basados en conjuntos de datos adecuados. Vista final del panel: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Configuración del panel de análisis en frío
En la canalización de datos de análisis en frío, el objetivo principal es obtener la previsión de la demanda de cada región. Power BI se conecta a una Base de datos SQL de Azure como su origen de datos, donde se almacenan los resultados de predicción.

> [!NOTE]
> 1) Tarda unas horas en recopilar los resultados de previsión suficientes para el panel. Recomendamos iniciar este proceso entre dos y tres horas después de iniciar el generador de datos. 2) En este paso, el requisito previo es descargar e instalar gratuitamente el software [Power BI Desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Obtenga las credenciales de la base de datos.

   Antes de continuar con los pasos siguientes, necesita el **nombre del servidor de la base de datos, el nombre de la base de datos, el nombre de usuario y la contraseña**. Estos son los pasos que le ayudarán a encontrarlos.

   * Una vez que **"Azure SQL Database"** se muestre en color verde en el diagrama de la plantilla de solución, haga clic en este icono y, después, haga clic en **"Abrir"**. Se le remitirá a Azure Portal y se abrirá también la página de información de la base de datos.
   * En la página, puede encontrar una sección "Base de datos". En ella se muestra la base de datos que ha creado. El nombre de la base de datos debe ser **"Su nombre de solución + Número aleatorio + 'db'"** (por ejemplo, "mytest12345db").
   * Haga clic en la base de datos; en el nuevo panel emergente puede encontrar el nombre del servidor de base de datos en la parte superior. El nombre del servidor de la base de datos debe ser `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (por ejemplo, "mytest12345.database.windows.net,1433").
   * El **nombre de usuario** y la **contraseña** de la base de datos son los mismos que los registrados anteriormente durante la implementación de la solución.
2. Actualización del origen de datos del archivo de Power BI de análisis en frío

   * Asegúrese de que ha instalado la versión más reciente de [Power BI Desktop](https://powerbi.microsoft.com/desktop).
   * En la carpeta **"DemandForecastingDataGeneratorv1.0"** que ha descargado, haga doble clic en el archivo **‘Power BI Template\DemandForecastPowerBI.pbix’**. Las visualizaciones iniciales se basan en datos ficticios. **Nota:** Si ve un mensaje de error, asegúrese de que ha instalado la última versión de Power BI Desktop.

     Cuando abra el archivo, en la parte superior, haga clic en **"Editar consultas"**. En la ventana emergente, haga doble clic en **"Origen"** en el panel derecho.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * En la ventana emergente, reemplace **"Servidor"** y **"Base de datos"** por sus propios nombres de servidor y base de datos y, después, haga clic en **"Aceptar"**. Para el nombre del servidor, asegúrese de especificar el puerto 1433 (**SuNombreDeSolución.database.windows.net, 1433**). Ignore los mensajes de advertencia que aparezcan en la pantalla.
   * En la siguiente ventana emergente, verá dos opciones en el panel izquierdo (**Windows** y **Base de datos**). Haga clic en **"Base de datos"**, escriba su **"Nombre de usuario"** y **"Contraseña"** (que es el nombre de usuario y la contraseña que escribió la primera vez que implementó la solución y creó una instancia de Azure SQL Database). En ***Seleccionar en qué nivel hay que aplicar estos valores***, active la opción de nivel de base de datos. Después, haga clic en **"Conectar"**.
   * Una vez que se le dirija a la página anterior, cierre la ventana. Aparece un mensaje. Haga clic en **Aplicar**. Finalmente, haga clic en el botón **Guardar** para guardar los cambios. El archivo de Power BI ha establecido ya la conexión con el servidor. Si las visualizaciones están vacías, asegúrese de borrar todas las selecciones para poder visualizar todos los datos haciendo clic en el icono de borrador de la esquina superior derecha de las leyendas. Utilice el botón Actualizar para reflejar los nuevos datos en las visualizaciones. Inicialmente, solo verá los datos de inicialización en las visualizaciones ya que la factoría de datos está programada para actualizarse cada tres horas. Después de tres horas, verá nuevas predicciones reflejadas en las visualizaciones al actualizar los datos.
3. (Opcional) Publique el panel de la ruta de acceso en frío en [Power BI en línea](http://www.powerbi.com/). Tenga en cuenta que este paso necesita una cuenta de Power BI (o la cuenta de Office 365).

   * Haga clic en **"Publicar"** y unos segundos más tarde aparecerá una ventana con un mensaje que indicará que la publicación en Power BI se ha realizado correctamente. y con una marca de verificación verde. Haga clic en el vínculo siguiente "Abrir demoprediction.pbix en Power BI". Para obtener instrucciones detalladas, consulte [Publicar desde Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para crear un nuevo panel: haga clic en el signo **+** junto a la sección **Paneles** del panel izquierdo. Escriba el nombre "Demostración de previsión de demanda" para este nuevo panel.
   * Una vez abierto el informe, haga clic en ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) para anclar todas las visualizaciones al panel. Para obtener instrucciones detalladas, consulte [Anclaje de un icono a un panel de Power BI desde un informe](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vaya a la página del panel y ajuste el tamaño y la ubicación de las visualizaciones y edite los títulos. Para obtener instrucciones detalladas sobre cómo editar los iconos, consulte [Edición de un icono: cambiar el tamaño, mover, cambiar el nombre, anclar, eliminar, agregar un hipervínculo](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Este es un panel de ejemplo con algunas visualizaciones de la ruta de acceso inactiva ancladas.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Opcional) Programe la actualización del origen de datos.

   * Para programar la actualización de los datos, mantenga el mouse sobre el conjunto de datos **EnergyBPI-Final**, haga clic en ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) y después elija **Programar actualización**.
     **Nota:** Si ve un mensaje de advertencia, haga clic en **Editar credenciales** y asegúrese de que las credenciales de la base de datos son las mismas que las descritas en el paso 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Expanda la sección **Programar actualización** . Active "Mantener los datos actualizados".
   * Programe la actualización según sus necesidades. Para más información, consulte [Actualizar datos en Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Eliminación de la solución**
Asegúrese de detener el generador de datos cuando no se use activamente la solución, ya que de lo contrario incurrirá en costos elevados. Elimine la solución si no la está usando. Al eliminar la solución se eliminan todos los componentes aprovisionados en su suscripción cuando se implementó la solución. Para eliminar la solución, haga clic en el nombre de la solución en el panel izquierdo de la plantilla de soluciones y haga clic en Eliminar.

## <a name="cost-estimation-tools"></a>**Herramientas de estimación de costos**
Las dos herramientas siguientes están disponibles para ayudarle a comprender mejor los costos totales implicados en la ejecución de la plantilla de solución Previsión de demanda de energía en su suscripción:

* [Herramienta de estimación de costos de Microsoft Azure (en línea)](https://azure.microsoft.com/pricing/calculator/)
* [Herramienta de estimación de costos de Microsoft Azure (escritorio)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Agradecimientos**
Este artículo fue creado por el científico de datos Yijing Chen y el ingeniero de software Qiu Min de Microsoft.
