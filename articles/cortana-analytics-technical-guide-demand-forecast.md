<properties
	pageTitle="Guía técnica para la previsión de demanda de energía | Microsoft Azure"
	description="Guía técnica de la plantilla de solución con Microsoft Cortana Intelligence para la previsión de demanda de energía."
	services="cortana-analytics"
	documentationCenter=""
	authors="yijichen"
	manager="ilanr9"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="inqiu;yijichen;ilanr9"/>

# Guía técnica de la plantilla de solución de Cortana Intelligence para la previsión de demanda de energía

## **Información general**

Las plantillas de solución están diseñadas para acelerar el proceso de creación de una demostración E2E basada en Cortana Intelligence Suite. Una plantilla implementada aprovisionará la suscripción con los componentes necesarios de Cortana Intelligence y creará las relaciones entre ellos. También proporciona a la canalización de datos de ejemplo que se generan desde una aplicación de simulación de datos. Descargue el simulador de datos desde el vínculo que se proporciona e instálelo en su máquina local. Consulte el archivo léame.txt para obtener instrucciones sobre cómo utilizar el simulador. Los datos generados con el simulador hidratarán la canalización de datos y comenzarán a generar predicciones de aprendizaje automático que luego se pueden visualizar en el panel de Power BI.

El proceso de implementación le guiará a través de varios pasos para configurar las credenciales de la solución. Asegúrese de que registra estas credenciales: nombre de la solución, nombre de usuario y contraseña que proporcionó durante la implementación.

El objetivo de este documento es explicar la arquitectura de referencia y los diferentes componentes aprovisionados en su suscripción como parte de esta plantilla de solución. El documento trata también sobre cómo reemplazar los datos de ejemplo con datos reales para poder ver detalles y predicciones a partir de sus propios datos. Además, el documento explica las partes de la plantilla de solución que deberán modificarse si desea personalizar la solución con sus propios datos. Se proporcionan instrucciones sobre cómo crear el panel de Power BI para esta plantilla de solución al final.

## **Idea general**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### Arquitectura explicada
Cuando se implementa la solución, se activan varios servicios de Azure dentro de Cortana Analytics Suite (*por ejemplo,* Centro de eventos, Análisis de transmisiones, HDInsight, Data Factory, Aprendizaje automático, *etc.*). El diagrama de arquitectura anterior muestra, a nivel general, cómo se construye la plantilla de solución Previsión de demanda de energía de un extremo a otro. Podrá investigar estos servicios haciendo clic en ellos en el diagrama de la plantilla de solución creado con la implementación de la solución. Las secciones siguientes describen cada parte.

## **Origen e ingesta de datos**

### Origen de datos sintéticos

Para esta plantilla, el origen de datos utilizado se genera desde una aplicación de escritorio que se descarga y ejecuta localmente tras una implementación correcta. Encontrará las instrucciones para descargar e instalar esta aplicación en la barra de propiedades al seleccionar el primer nodo denominado Energy Forecasting Data Simulator en el diagrama de la plantilla de solución. Esta aplicación proporciona al servicio [Centro de eventos de Azure](#azure-event-hub) puntos de datos o eventos, que se utilizarán en el flujo de solución restante.

La aplicación de generación de eventos rellenará el Centro de eventos de Azure solo mientras se esté ejecutando en el equipo.

### Centro de eventos de Azure

El servicio [Centro de eventos de Azure](https://azure.microsoft.com/services/event-hubs/) es el destinatario de la entrada proporcionada por el origen de datos sintéticos descrito anteriormente.

## **Preparación y análisis de datos**


### Análisis de transmisiones de Azure

El servicio [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) se utiliza para proporcionar análisis casi en tiempo real sobre el flujo de entrada del servicio [Centro de eventos de Azure](#azure-event-hub) y para publicar los resultados en un panel de [Power BI](https://powerbi.microsoft.com) y, al mismo tiempo, archivar todos los eventos de entrada sin procesar en el servicio [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) para su posterior procesamiento mediante el servicio [Data Factory de Azure](https://azure.microsoft.com/documentation/services/data-factory/).

### Agregación personalizada de HD Insights

El servicio HD Insight de Azure se utiliza para ejecutar scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (organizados mediante Data Factory de Azure) para proporcionar agregaciones en los eventos sin procesar que se almacenaron mediante el servicio Análisis de transmisiones de Azure.

### Aprendizaje automático de Azure

El servicio [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) se utiliza (coordinado por Data Factory de Azure) para realizar previsiones sobre el consumo futuro de energía de una región determinada, en función de los datos recibidos.

## **Publicación de datos**


### Servicio Base de datos SQL de Azure.

El servicio [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) se emplea (administrado por Data Factory de Azure) para almacenar las predicciones recibidas por el servicio de Aprendizaje automático de Azure que se utilizarán en el panel de [Power BI](https://powerbi.microsoft.com).

## **Consumo de datos**

### Power BI

El servicio [Power BI](https://powerbi.microsoft.com) se utiliza para mostrar un panel que contiene las agregaciones proporcionadas por el servicio [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/), así como los resultados de previsión de demanda almacenados en [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) que se produjeron al usar el servicio [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). Para instrucciones sobre cómo crear el panel de Power BI para esta plantilla de solución, consulte la sección siguiente.

## **Incorporación de sus propios datos**

Esta sección describe cómo traer sus propios datos a Azure y qué áreas necesitarían cambios para los datos que ha traído a esta arquitectura.

Es improbable que cualquier conjunto de datos que proporcione coincida con el conjunto de datos utilizado en esta plantilla de solución. Entender bien los datos y los requisitos resultará vital a la hora de modificar esta plantilla para que funcione con sus propios datos. Si se trata del primer contacto con el servicio Aprendizaje automático de Azure, puede obtener una introducción mediante el ejemplo que puede encontrar en [Tutorial de Aprendizaje automático: Creación del primer experimento en Estudio de aprendizaje automático de Azure](machine-learning\machine-learning-create-experiment.md).

Los apartados siguientes describen las secciones de la plantilla que requieren modificaciones cuando se introduce un nuevo conjunto de datos.

### Centro de eventos de Azure

El servicio [Centro de eventos de Azure](https://azure.microsoft.com/services/event-hubs/) es muy genérico, hasta tal punto que se pueden publicar datos en el centro en formato CSV o JSON. No se produce ningún procesamiento especial en el Centro de eventos de Azure, pero es importante que comprenda los datos que se introducen en él.

En este documento no se describe cómo introducir los datos, pero puede enviar fácilmente eventos o datos a un Centro de eventos de Azure mediante la [API del Centro de eventos](event-hubs\event-hubs-programming-guide.md).

### Análisis de transmisiones de Azure

El servicio [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) se utiliza para proporcionar análisis casi en tiempo real mediante la lectura de flujos de datos y el envío de datos a cualquier número de orígenes.

Para la plantilla de solución Previsión de demanda de energía, la consulta de Análisis de transmisiones de Azure consta de dos subconsultas, cada una de las cuales consume eventos del servicio Centro de eventos de Azure como entradas y tiene salidas a dos ubicaciones distintas. Estas salidas constan de un conjunto de datos de Power BI y una ubicación de Almacenamiento de Azure.

La consulta de [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) puede encontrarse de dos maneras:

-   Mediante el inicio de sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).

-   Mediante la búsqueda de los trabajos de Análisis de transmisiones ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) que se hayan generado al implementar la solución. En el primer caso se insertan datos en el almacenamiento de blobs (por ejemplo, mytest1streaming432822asablob) y en el segundo caso se insertan datos en Power BI (por ejemplo, mytest1streaming432822asapbi).


-   Seleccionar

    -   ***INPUTS*** para ver la entrada de consulta

    -   ***QUERY*** para ver la consulta en sí

    -   ***OUTPUTS*** para ver las diferentes salidas

Puede encontrar información acerca de la construcción de consultas de Análisis de transmisiones de Azure en [Stream Analytics Query Reference (Referencia de consultas en Análisis de transmisiones)](https://msdn.microsoft.com/library/azure/dn834998.aspx) en MSDN.

En esta solución, el trabajo de Análisis de transmisiones de Azure que envía un conjunto de datos con información de análisis casi en tiempo real sobre el flujo de datos de entrada a un panel de Power BI se proporciona como parte de esta plantilla de solución. Dado que hay un conocimiento implícito acerca del formato de datos entrantes, estas consultas deben modificarse según su formato de datos.

El segundo trabajo de Análisis de transmisiones de Azure envía todos los eventos del [Centro de eventos](https://azure.microsoft.com/services/event-hubs/) al [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) y, por tanto, no necesita modificación, con independencia del formato de los datos, ya que toda la información de los eventos se transmitió al almacenamiento.

### Factoría de datos de Azure

El servicio [Data Factory de Azure](https://azure.microsoft.com/documentation/services/data-factory/) organiza el movimiento y el procesamiento de los datos. En la plantilla de solución Previsión de demanda de energía, la factoría de datos se compone de doce [canalizaciones](data-factory\data-factory-create-pipelines.md) que mueven y procesan los datos mediante varias tecnologías.

  Puede tener acceso a la factoría de datos si abre el nodo Data Factory en la parte inferior del diagrama de la plantilla de solución que se creó con la implementación de la solución. Esto le llevará a la factoría de datos del Portal de administración de Azure. Si ve errores en los conjuntos de datos, puede ignorarlos ya que se deben a que la Factoría de datos se implementó antes de iniciarse el generador de datos. Estos errores no impiden que la Factoría de datos funcione.

En esta sección se describen las [canalizaciones](data-factory\data-factory-create-pipelines.md) y las [actividades](data-factory\data-factory-create-pipelines.md) necesarias contenidas en [Data Factory de Azure](https://azure.microsoft.com/documentation/services/data-factory/). A continuación se muestra la vista de diagrama de la solución.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinco de las canalizaciones de esta factoría contienen scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) que se utilizan para particionar y agregar los datos. Tenga en cuenta que los scripts se encontrarán en la cuenta de [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) creada durante la instalación. Su ubicación será: demandforecasting\\\script\\\hive\\\ (o https://[El nombre de la solución].blob.core.windows.net/demandforecasting).

Al igual que las consultas de [Análisis de transmisiones de Azure](#azure-stream-analytics-1), los scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) tienen un conocimiento implícito acerca del formato de los datos entrantes, por lo que estas consultas tendrían que modificarse en función del formato de los datos y de los requisitos de la [ingeniería de características](machine-learning\machine-learning-feature-selection-and-engineering.md).

#### *AggregateDemandDataTo1HrPipeline*

Esta [canalización](data-factory\data-factory-create-pipelines.md) contiene una única actividad, una actividad de [HDInsightHive](data-factory\data-factory-hive-activity.md) que usa un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para agregar cada 10 segundos datos transmitidos a petición en el nivel de subestación al nivel de región horaria y colocarlos en el [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) mediante el trabajo de Análisis de transmisiones de Azure.

El script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarea de creación de particiones es ***AggregateDemandRegion1Hr.hql***.


#### *LoadHistoryDemandDataPipeline*

Esta [canalización](data-factory\data-factory-create-pipelines.md) contiene dos actividades:
- La actividad [HDInsightHive](data-factory\data-factory-hive-activity.md) que usa un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de Hive para agregar los datos de demanda del historial de horas en el nivel de subestación al nivel de región horaria y colocarlos en el Almacenamiento de Azure durante el trabajo de Análisis de transmisiones de Azure.

- La actividad [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los datos agregados desde el blob de Almacenamiento de Azure hasta la base de datos SQL de Azure que se aprovisionó como parte de la instalación de la plantilla de solución.

El script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) de esta tarea es ***AggregateDemandHistoryRegion.hql***.


#### *MLScoringRegionXPipeline*

Esta [canalización](data-factory\data-factory-create-pipelines.md) contiene varias actividades cuyo resultado final son las predicciones puntuadas a partir del experimento de Aprendizaje automático de Azure asociado a esta plantilla de solución. Son casi idénticas, excepto que cada una de ellas solo administra la región diferente, lo cual se realiza pasando un valor de RegionID diferente en la canalización ADF y el script de Hive para cada región. Las actividades contenidas son:
-	Una actividad de [HDInsightHive](data-factory\data-factory-hive-activity.md) que utiliza un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de Hive para realizar agregaciones y la ingeniería de características necesarias para el experimento de Aprendizaje automático de Azure. Los scripts de Hive de esta tarea son los archivos ***PrepareMLInputRegionX.hql*** respectivos.

-	Una actividad [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los resultados de la actividad [HDInsightHive](data-factory\data-factory-hive-activity.md) a un solo blob de Almacenamiento de Azure al que se puede acceder mediante la actividad [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-	Una actividad [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que llama al experimento de Aprendizaje automático de Azure que hace que los resultados se coloquen en un único blob del Almacenamiento de Azure.

#### *CopyScoredResultRegionXPipeline*
Esta [canalización](data-factory\data-factory-create-pipelines.md) contienen una única actividad: una actividad [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los resultados del experimento de Aprendizaje automático de Azure desde la canalización ***MLScoringRegionXPipeline*** respectiva a la base de datos SQL de Azure que se haya aprovisionado como parte de la instalación de la plantilla de solución.

#### *CopyAggDemandPipeline*
Esta [canalización](data-factory\data-factory-create-pipelines.md) contienen una única actividad: una actividad [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los datos de demanda continua agregados desde ***LoadHistoryDemandDataPipeline*** hasta la base de datos SQL de Azure que se haya aprovisionado como parte de la instalación de la plantilla de solución.

#### *CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Estas [canalizaciones](data-factory\data-factory-create-pipelines.md) contienen una única actividad: una actividad [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los datos de referencia de una región, subestación o topología, que se cargan en un blob de Almacenamiento de Azure como parte de la instalación de la plantilla de solución, a la base de datos SQL de Azure que se haya aprovisionado como parte de dicha instalación.

### Aprendizaje automático de Azure
El experimento de [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) usado en esta solución proporciona la predicción de demanda de la región. El experimento es específico del conjunto de datos consumido y, por tanto, requiere la modificación o el reemplazo específico de los datos que se han aportado.

## **Supervisión de progreso**
Una vez iniciado el Generador de datos, la canalización comienza a hidratarse y los distintos componentes de la solución empiezan a entrar en acción siguiendo los comandos emitidos por la Factoría de datos. Hay dos maneras de supervisar la canalización.

1. Comprobar los datos del Almacenamiento de blobs de Azure.

	Uno de los trabajos de Análisis de transmisiones escribe los datos entrantes sin procesar en Almacenamiento de blobs. Si hace clic en el componente **Almacenamiento de blobs de Azure** de su solución desde la pantalla en la que haya implementado correctamente la solución y después hace clic en **Abrir** en el panel derecho, irá al [Portal de administración de Azure](https://portal.azure.com). Una vez allí, haga clic en **Blobs**. En el siguiente panel, verá una lista de contenedores. Haga clic en **"energysadata"**. En el siguiente panel, verá la carpeta **"demandongoing"**. Dentro de la carpeta rawdata, verá carpetas con nombres como date=2016-01-28, etc. Si ve estas carpetas, significa que los datos sin procesar se están generando en el equipo y almacenando en Almacenamiento de blobs correctamente. Verá archivos que deben tener tamaños finitos en MB en esas carpetas.

2. Comprobar los datos de Base de datos SQL de Azure.

	El último paso de la canalización es escribir los datos (por ejemplo, predicciones del aprendizaje automático) en Base de datos SQL. Es posible que deba esperar hasta dos horas para que los datos aparezcan en Base de datos SQL. Una manera de supervisar la cantidad de datos disponibles en la base de datos SQL es mediante el [Portal de administración de Azure](https://manage.windowsazure.com/). En el panel izquierdo, busque y haga clic en BASES DE DATOS SQL ![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png). A continuación, busque su base de datos (es decir, demo123456db) y haga clic en ella. En la siguiente página, en la sección **"Conectar con la base de datos"**, haga clic en **"Ejecutar consultas Transact-SQL en la base de datos SQL"**.

	Aquí, puede hacer clic en Nueva consulta y consultar el número de filas (por ejemplo, "select count(*) from DemandRealHourly). Conforme crece la base de datos, el número de filas de la tabla debería aumentar.

3. Compruebe los datos desde el panel de Power BI.

	Puede configurar el panel de análisis en caliente de Power BI para supervisar los datos de entrada sin formato. Siga las instrucciones de la sección "Panel de Power BI".



## **Panel de Power BI**

### Información general

En esta sección se describe cómo configurar el panel de Power BI para visualizar los datos en tiempo real del Análisis de transmisiones de Azure (análisis en caliente), así como los resultados de previsión de Aprendizaje automático de Azure (análisis en frío).


### Configuración del panel de análisis en caliente

Los siguientes pasos le guiarán por los pasos para visualizar la salida de datos en tiempo real de los trabajos de Análisis de transmisiones que se generaron en el momento de la implementación de la solución. Se necesita una cuenta de [Power BI en línea](http://www.powerbi.com/) para realizar los pasos siguientes. Si no tiene una cuenta, puede [crearla](https://powerbi.microsoft.com/pricing).

1.  Agregue una salida de Power BI en el Análisis de transmisiones de Azure (ASA).

    -  Debe seguir las instrucciones descritas en [Análisis de transmisiones y Power BI: panel de análisis en tiempo real de flujo de datos](stream-analytics\stream-analytics-power-bi-dashboard.md) para configurar la salida del trabajo de Análisis de transmisiones de Azure como panel de Power BI.

	- Busque el trabajo de análisis de transmisiones en el [Portal de administración de Azure](https://manage.windowsazure.com). El nombre del trabajo debe ser: SuNombreDeSolución + "streamingjob" + número aleatorio + "asapbi" (es decir, demostreamingjob123456asapbi).

	- Configure la salida de la consulta ASA que es **PBIoutput**. Asegúrese de que el valor de **Alias de salida** sea igual que el de la consulta. Puede asignar a los campos **Nombre de conjunto de datos** y **Nombre de tabla** el nombre **"EnergyStreamData"**. Cuando haya agregado la salida, haga clic en **"Iniciar"** en la parte inferior de la página para iniciar el trabajo de Análisis de transmisiones. Recibirá un mensaje de confirmación (*por ejemplo*, "Se ha iniciado correctamente el trabajo de análisis de transmisiones myteststreamingjob12345asablob").

2. Inicie sesión en [Power BI en línea](http://www.powerbi.com).

    -   En la sección Conjuntos de datos del panel izquierdo, en Mi área de trabajo, debería poder ver un nuevo conjunto de datos en el panel izquierdo de Power BI. Son los datos de transmisión que insertó desde Análisis de transmisiones de Azure en el paso anterior.

    -   Asegúrese de que el panel ***Visualizaciones*** está abierto y se muestra en el lado derecho de la pantalla.

3. Cree el icono "Demanda por marca de hora":
	-	Haga clic en el conjunto de datos **"EnergyStreamData"** en la sección Conjuntos de datos del panel izquierdo.

	-	Haga clic en el icono **"Gráfico de líneas"**
		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

	-	Haga clic en "EnergyStreamData" en el panel **Campos**.

	-	Haga clic en **"Marca de hora"** y asegúrese de que se muestra en "Eje". Haga clic en **"Carga"** y asegúrese de que se muestra en "Valores".

	-	Haga clic en **GUARDAR** en la parte superior y asigne al informe el nombre "EnergyStreamDataReport". El informe denominado "EnergyStreamDataReport" se mostrará en la sección Informes en el panel de navegación de la izquierda.

	-	Haga clic en el icono **"Anclar visualización"**![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) en la esquina superior derecha de este gráfico de líneas y puede que se muestre una ventana "Anclar en el panel" para que elija un panel. Seleccione "EnergyStreamDataReport" y luego haga clic en "Anclar".

	-	Mantenga el mouse sobre este icono en el panel y haga clic en el icono "Editar" en la esquina superior derecha para cambiar el título a "Demanda por marca de hora".

4.	Cree otros iconos de panel basados en conjuntos de datos adecuados. La vista final del panel se muestra a continuación. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic5.png)


### Configuración del panel de análisis en frío
En la canalización de datos de análisis en frío, el objetivo principal es obtener la previsión de la demanda de cada región. Power BI se conecta a una Base de datos SQL de Azure como su origen de datos, donde se almacenan los resultados de predicción.

> [AZURE.NOTE] \(1) Tarda unas horas en recopilar los resultados de previsión suficientes para el panel. Recomendamos iniciar este proceso entre dos y tres horas después de iniciar el generador de datos. 2) En este paso, el requisito previo es descargar e instalar el software gratuito [Power BI Desktop](https://powerbi.microsoft.com/desktop).



1.  Obtenga las credenciales de la base de datos.

    Antes de continuar con los pasos siguientes, necesitará el **nombre del servidor de base de datos, el nombre de la base de datos, el nombre de usuario y la contraseña**. Estos son los pasos que le ayudarán a encontrarlos.

    -   Cuando **"Base de datos SQL de Azure"** se vuelva verde en el diagrama de la plantilla de solución, haga clic sobre este icono y después haga clic en **"Abrir"**. Se le guiará al Portal de administración de Azure y se abrirá también la página de información de la base de datos.

    -   En la página, puede encontrar una sección "Base de datos". En ella se muestra la base de datos que ha creado. El nombre de la base de datos debe ser **"Su nombre de solución + Número aleatorio + 'db'"** (por ejemplo, "mytest12345db").

	-	Haga clic en la base de datos; en el nuevo panel emergente puede encontrar el nombre del servidor de base de datos en la parte superior. El nombre del servidor de base de datos debería ser **"Su nombre de solución + Número aleatorio + 'database.windows.net,1433'"** (por ejemplo, "mytest12345.database.windows.net,1433").

	-   El **nombre de usuario** y la **contraseña** de la base de datos son los mismos que los registrados anteriormente durante la implementación de la solución.

2.	Actualización del origen de datos del archivo de Power BI de análisis en frío
	-  Asegúrese de que ha instalado la versión más reciente de [Power BI Desktop](https://powerbi.microsoft.com/desktop).

	-	En la carpeta **"DemandForecastingDataGeneratorv1.0"** que ha descargado, haga doble clic en el archivo **‘Power BI Template\\DemandForecastPowerBI.pbix’**. Las visualizaciones iniciales se basan en datos ficticios. **Nota:** Si ve un mensaje de error, asegúrese de que ha instalado la versión más reciente de Power BI Desktop.

		Cuando abra el archivo, en la parte superior, haga clic en **"Editar consultas"**. En la ventana emergente, haga doble clic en **"Origen"** en el panel derecho. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

	-   En la ventana emergente, reemplace **"Servidor"** y **"Base de datos"** por los nombres de su servidor y su base de datos y después haga clic en **"Aceptar"**. Para el nombre del servidor, asegúrese de especificar el puerto 1433 (**SuNombreDeSolución.database.windows.net, 1433**). Ignore los mensajes de advertencia que aparezcan en la pantalla.

	-   En la siguiente ventana emergente, verá dos opciones en el panel izquierdo (**Windows** y **Base de datos**). Haga clic en **"Base de datos"**, escriba su **"Nombre de usuario"** y **"Contraseña"** (que es el nombre de usuario y la contraseña que escribió la primera vez que implementó la solución y creó una base de datos SQL de Azure). En ***Seleccionar en qué nivel hay que aplicar estos valores***, active la opción de nivel de base de datos. Haga clic en **"Conectar"**.

	-   Una vez que se le dirija a la página anterior, cierre la ventana. Aparecerá un mensaje. Haga clic en **Aplicar**. Finalmente, haga clic en el botón **Guardar** para guardar los cambios. El archivo de Power BI ha establecido ya la conexión con el servidor. Si las visualizaciones están vacías, asegúrese de borrar todas las selecciones para poder visualizar todos los datos haciendo clic en el icono de borrador de la esquina superior derecha de las leyendas. Utilice el botón Actualizar para reflejar los nuevos datos en las visualizaciones. Inicialmente, solo verá los datos de inicialización en las visualizaciones ya que la Factoría de datos está programada para actualizarse cada 3 horas. Después de 3 horas, verá nuevas predicciones reflejadas en las visualizaciones al actualizar los datos.

3. (Opcional) Publique el panel de análisis en frío en [Power BI en línea](http://www.powerbi.com/). Tenga en cuenta que este paso necesita una cuenta de Power BI (o la cuenta de Office 365).

	-   Haga clic en **"Publicar"**; algunos segundos más tarde aparecerá una ventana con una marca de verificación verde y un mensaje para indicar que la publicación en Power BI se ha realizado correctamente. Haga clic en el vínculo "Abrir demoprediction.pbix en Power BI". Para ver instrucciones detalladas, consulte [Publicar desde Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

	-   Para crear un nuevo panel, haga clic en el signo **+** junto a la sección **Paneles** del panel izquierdo. Escriba el nombre "Demostración de previsión de demanda" para este nuevo panel.

	-   Cuando abra el informe, haga clic en ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) para anclar todas las visualizaciones al panel. Para ver instrucciones detalladas, consulte [Anclaje de un icono a un panel de Power BI desde un informe](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Vaya a la página del panel y ajuste el tamaño y la ubicación de las visualizaciones y edite los títulos. Para ver instrucciones detalladas sobre cómo editar los iconos, consulte [Edición de un icono: cambiar el tamaño, mover, cambiar el nombre, anclar, eliminar, agregar un hipervínculo](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Este es un panel de ejemplo con algunas visualizaciones de la ruta de acceso inactiva ancladas.

		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Opcional) Programe la actualización del origen de datos.
	-	  Para programar la actualización de los datos, mantenga el mouse sobre el conjunto de datos **EnergyBPI-Final**, haga clic en ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) y después elija **Programar actualización**. **Nota:** Si ve un mensaje de advertencia, haga clic en **Editar credenciales** y asegúrese de que las credenciales de base de datos sean las mismas que las descritas en el paso 1.

	![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

	-   Expanda la sección **Programar actualización**. Active "Mantener los datos actualizados".

	-   Programe la actualización según sus necesidades. Para obtener más información, consulte [Actualizar datos en Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).


## **Herramientas de estimación de costos**

Las dos herramientas siguientes están disponibles para ayudarle a comprender mejor los costos totales implicados en la ejecución de la plantilla de solución Previsión de demanda de energía en su suscripción:

-   [Herramienta de estimación de costos de Microsoft Azure (en línea)](https://azure.microsoft.com/pricing/calculator/)

-   [Herramienta de estimación de costos de Microsoft Azure (escritorio)](http://www.microsoft.com/download/details.aspx?id=43376)

<!----HONumber=AcomDC_0406_2016-->