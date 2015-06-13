<properties 
	pageTitle="Desplazamiento y procesamiento de archivos de registro mediante la factoría de datos de Azure" 
	description="Este tutorial avanzada describe un escenario real casi e implementa el escenario con el servicio de generador de datos de Azure y Editor de generador de datos." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Tutorial: Medir la eficacia de una campaña de marketing  
Contoso es una empresa de juegos que cree juegos para múltiples plataformas: equipos personales (PC), dispositivos de mano y consolas de juegos. Estos juegos generan muchos registros y objetivo de Contoso es recopilar y analizar estos registros para obtener información sobre las preferencias del cliente, los datos demográficos, etc. para identificar oportunidades de ventas y ventas cruzadas, desarrollar nuevas características atractivas para impulsar el crecimiento empresarial y ofrecer una mejor experiencia a los clientes de comportamiento de uso.

En este tutorial, creará las canalizaciones de generador de datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente por recopilar registros de ejemplo, procesamiento y enriquecimiento de datos de referencia y transformar los datos. Tiene las siguientes tres canalizaciones:

1.	El **PartitionGameLogsPipeline** lee los eventos de juegos sin procesar del almacenamiento de blobs y crea particiones basadas en el año, mes y día.
2.	El **EnrichGameLogsPipeline** combina eventos de juegos con particiones con datos de referencia de código geográfico y enriquece los datos mediante la asignación de direcciones IP a las ubicaciones geográficas correspondientes.
3.	El **AnalyzeMarketingCampaignPipeline** canalización aprovecha los datos enriquecidos y lo procesa con los datos de publicidad para crear el resultado final que contiene la eficacia de la campaña de marketing.

## Preparación para el tutorial
1.	Lectura [Introducción al generador de datos de Azure][adfintroduction] para obtener información general del generador de datos de Azure y comprensión de los conceptos de nivel superior.
2.	Debe tener una suscripción de Azure para realizar este tutorial. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra](http://azure.microsoft.com/pricing/purchase-options/), [Ofertas para miembros](http://azure.microsoft.com/pricing/member-offers/) o [Prueba gratuita](http://azure.microsoft.com/pricing/free-trial/).
3.	Debe descargar e instalar [Azure PowerShell][download-azure-powershell] en el equipo. Se ejecutarán los cmdlets de generador de datos para cargar datos de ejemplo y pig y hive secuencias de comandos para el almacenamiento de blobs. 
2.	**(recomendado)** revisión y practicar el tutorial en el [empezar a trabajar con el generador de datos de Azure][adfgetstarted] artículo para obtener un tutorial para familiarizarse con los cmdlets y portal simple.
3.	**(recomendado)** revisión y practicar el tutorial en el [usar Pig y Hive con el generador de datos de Azure][usepigandhive] artículo para ver un tutorial sobre la creación de una canalización para mover datos de origen de datos local a un almacenamiento de blobs de Azure.
4.	Descargar [ADFWalkthrough][adfwalkthrough-download] archivos **C:\ADFWalkthrough** carpeta **conservando la estructura de carpetas**:
	- **Canalizaciones:** incluye archivos JSON que contiene la definición de las canalizaciones.
	- **Tablas:** incluye archivos JSON que contiene la definición de las tablas.
	- **LinkedServices:** incluye archivos JSON que contiene la definición de su almacenamiento y cálculo (HDInsight) clúster 
	- **Secuencias de comandos:** incluye Hive y Pig secuencias de comandos que se utilizan para procesar los datos y se invoca desde las canalizaciones
	- **SampleData:** incluye datos de ejemplo para este tutorial
	- **Donde:** incluye archivos de JSON y secuencias de comandos que se usa para demostrar el acceso a los datos locales
	- **uploadSampleDataAndScripts.ps1:** esta secuencia de comandos carga los datos de ejemplo y las secuencias de comandos en Azure.
5. Asegúrese de haber creado los siguientes recursos de Azure:			
	- Cuenta de almacenamiento de Azure.
	- Base de datos SQL de Azure
	- HDInsight de Clúster Azure versión 3.1 o superior (o clúster de HDInsight de uso a petición que va a crear el servicio de generador de datos automáticamente)	
7. Una vez creados los recursos de Azure, asegúrese de tener la información necesaria para conectarse a cada uno de estos recursos.
 	- **Cuenta de almacenamiento de azure** : nombre de cuenta y clave de cuenta.  
	- **Base de datos de SQL azure** -servidor, base de datos, nombre de usuario y contraseña.
	- **Clúster de HDInsight de azure**.-nombre del clúster de HDInsight, nombre de usuario, contraseña y nombre de cuenta y clave de cuenta para el almacenamiento de Azure asociado a este clúster. Si desea utilizar un clúster de HDInsight a petición en lugar de su propio clúster de HDInsight puede omitir este paso.  
8. Iniciar **Azure PowerShell** y ejecute los comandos siguientes. Mantenga abierta la PowerShell de Azure. Si cierra y vuelve a abrir, deberá volver a ejecutar estos comandos.
	- Ejecutar **Add-AzureAccount** y escriba el nombre de usuario y la contraseña que utilizas para iniciar sesión en el Portal de vista previa de Azure.  
	- Ejecutar **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecutar **Select-AzureSubscription** para seleccionar la suscripción que desea trabajar. Esta suscripción debe ser la misma que la usada en el Portal de vista previa de Azure.	

## Información general
El flujo de trabajo completo se muestra a continuación:

![Tutorial flujo de extremo a extremo][image-data-factory-tutorial-end-to-end-flow]

1. El **PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs (RawGameEventsTable) y crea particiones basadas en el año, mes y día (PartitionedGameEventsTable).
2. El **EnrichGameLogsPipeline** combina eventos juegos con particiones (PartitionedGameEvents tabla, que es un resultado de la PartitionGameLogsPipeline) con código geográfica (RefGetoCodeDictionaryTable) y enriquece los datos mediante la asignación de una dirección IP a la correspondiente ubicación geográfica (EnrichedGameEventsTable).
3. El **AnalyzeMarketingCampaignPipeline** canalización aprovecha los datos enriquecidos (EnrichedGameEventTable producido por la EnrichGameLogsPipeline) y lo procesa con los datos de publicidad (RefMarketingCampaignnTable) para crear el resultado final de la eficacia de la campaña de marketing que se copian en la base de datos de SQL Azure (MarketingCampainEffectivensessSQLTable) y un almacenamiento de blobs de Azure (MarketingCampaignEffectivenessBlobTable) para el análisis.
    
## Tutorial: Crear, implementar y supervisar flujos de trabajo
1. [Paso 1: cargar datos de ejemplo y las secuencias de comandos](#MainStep1). En este paso, cargará todos los datos de ejemplo (incluidos todos los registros y datos de referencia) y los flujos de trabajo ejecutarán los scripts Hive/Pig. Los scripts que ejecuta también crean una base de datos SQL de Azure (denominada MarketingCampaigns), tablas, tipos definidos por el usuario y procedimientos almacenados.
2. [Paso 2: crear un generador de datos de Azure](#MainStep2). En este paso, creará una factoría de datos de Azure denominada LogProcessingFactory.
3. [Paso 3: crear servicios vinculados](#MainStep3). En este paso, creará los siguientes servicios vinculados: 
	
	- 	**StorageLinkedService**. Vincula la ubicación de almacenamiento de Azure que contiene eventos de juegos sin procesar, eventos de juegos con particiones, eventos de juegos enriquecidos, información efectiva de campañas de marketing, datos de código geográfico de referencia y datos de campaña de marketing de referencia a LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Vincula una base de datos SQL de Azure que contiene información de eficacia de campaña de marketing. 
	- 	**HDInsightStorageLinkedService**. Vincula un almacenamiento de blobs de Azure asociado al clúster de HDInsight al que hace referencia HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Vincula un clúster de Azure HDInsight a LogProcessingFactory. Este clúster se usa para realizar el procesamiento pig y hive en los datos. 
 		
4. [Paso 4: crear tablas](#MainStep4). En este paso, creará las tablas siguientes:
	
	- **RawGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego sin procesar en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego con particiones en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Esta tabla especifica la ubicación de los datos de código geográfico de referencia en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Esta tabla especifica la ubicación de los datos de campaña de marketing de referencia en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego enriquecidos en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Esta tabla especifica la tabla SQL (MarketingCampaignEffectiveness) en la base de datos de SQL Azure definidos por AzureSqlLinkedService que contiene los datos de eficacia de campaña marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Esta tabla especifica la ubicación de los datos de eficacia de la campaña de marketing en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Paso 5: crear y programar las canalizaciones](#MainStep5). En este paso, creará los siguientes procesos:
	- **PartitionGameLogsPipeline**. Este proceso lee los eventos de juegos sin procesar de un almacenamiento de blobs (RawGameEventsTable) y crea particiones basadas en el año, el mes y el día (PartitionedGameEventsTable). 


		![Canalización de PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Este proceso combina eventos de juegos con particiones (tabla PartitionedGameEvents, que es un resultado de PartitionGameLogsPipeline) con código geográfico (RefGetoCodeDictionaryTable) y enriquece los datos mediante la asignación de una dirección IP a la ubicación geográfica correspondiente (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Este proceso aprovecha los datos de eventos de juego enriquecidos (EnrichedGameEventTable producido por EnrichGameLogsPipeline) y lo procesa con los datos de publicidad (RefMarketingCampaignnTable) para crear el resultado final de la eficacia de la campaña de marketing, que se copian en la base de datos SQL de Azure (MarketingCampainEffectivensessSQLTable) y un almacenamiento de blobs de Azure (MarketingCampaignEffectivenessBlobTable) para el análisis.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Paso 6: supervisar los sectores de las canalizaciones y datos](#MainStep6). En este paso, supervisará los procesos, las tablas y los segmentos de datos mediante el Portal de Azure.

## <a name="MainStep1"></a> Paso 1: Cargar datos de ejemplo y las secuencias de comandos
En este paso, cargará todos los datos de ejemplo (incluidos todos los registros y datos de referencia) y los scripts Hive y Pig, que se invocan mediante los flujos de trabajo. Las secuencias de comandos se ejecuta también crean una base de datos de SQL Azure denominada **MarketingCampaigns**, tablas, tipos definidos por el usuario y procedimientos almacenados.

Las tablas, los tipos definidos por el usuario y procedimientos almacenados se utilizan al mover los resultados de la eficacia de la campaña de marketing desde el almacenamiento de blobs de Azure para la base de datos SQL de Azure.

1. Abra **uploadSampleDataAndScripts.ps1** de **C:\ADFWalkthrough** carpeta (o la carpeta que contiene los archivos extraídos) en su editor favorito, reemplace el resaltado con la información de clúster y guarde el archivo.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][descargar][sqlcmd-install]   
2. Confirme que el equipo local tiene acceso a la base de datos SQL de Azure. Para habilitar el acceso, utilice la **Portal de administración de Azure** o **sp_set_firewall_rule** en la base de datos maestra para crear una regla de firewall para la dirección IP del equipo. Puede tardar hasta cinco minutos que este cambio surta efecto. Consulte [definición de reglas de firewall para SQL Azure][azure-sql-firewall].
4. En PowerShell de Azure, navegue a la ubicación donde extrajo los ejemplos (por ejemplo: **C:\ADFWalkthrough**)
5. Ejecutar **uploadSampleDataAndScripts.ps1** 
6. Una vez que el script se ejecute correctamente, verá lo siguiente:

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> Paso 2: Crear un generador de datos de Azure
En este paso, se crea un generador de datos de Azure denominado **LogProcessingFactory**.

1.	Tras iniciar sesión en la [Portal de vista previa de Azure][azure-preview-portal], haga clic en **nuevo** desde la esquina inferior izquierda, haga clic en **análisis de datos** en el **crear** blade y haga clic en **factoría de datos** en el **análisis de datos** blade. 

	![Nuevo -> DataFactory][image-data-factory-new-datafactory-menu]

5. En el **Nuevo generador de datos** blade, escriba **LogProcessingFactory** para el **nombre**.

	![Módulo de generador de datos][image-data-factory-tutorial-new-datafactory-blade]

6. Si no ha creado un grupo de recursos de Azure denominado **ADF** ya, haga lo siguiente:
	1. Haga clic en **nombre del grupo de recursos**, y haga clic en **crear un nuevo grupo de recursos**.
	
		![Cuadro de grupo de recursos][image-data-factory-tutorial-resourcegroup-blade]
	2. En el **Crear grupo de recursos** blade, escriba **ADF** para el nombre del grupo de recursos y haga clic en **Aceptar**.
	
		![Crear grupo de recursos][image-data-factory-tutorial-create-resourcegroup]
7. Seleccione **ADF** para el **nombre del grupo de recursos de**.  
8.	En el **Nuevo generador de datos** blade, tenga en cuenta que **Agregar al panel de inicio** seleccionado de forma predeterminada. Esto agrega un vínculo a la factoría de datos al panel de inicio (lo que verá cuando inicie sesión en el Portal de vista previa de Azure).

	![Crear el módulo de generador de datos][image-data-factory-tutorial-create-datafactory]

9.	En el **Nuevo generador de datos** blade, haga clic en **crear** para crear el generador de datos.
10.	Después de crea el generador de datos, debería ver el **factoría de datos** blade titulada **LogProcessingFactory**.

	![Página principal del generador de datos][image-data-factory-tutorial-datafactory-homepage]

	
	Si no la ve, realice una de las acciones siguientes:

	- Haga clic en **LogProcessingFactory** en el **panel de inicio** (página principal)
	- Haga clic en **Examinar** a la izquierda, haga clic en **todo**, haga clic en **generadores de datos**, y haga clic en el generador de datos.
 
	> [AZURE.NOTE]El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **nombre del generador de datos "LogProcessingFactory" no está disponible**, cambie el nombre (por ejemplo, yournameLogProcessingFactory). Use este nombre en lugar de LogProcessingFactory mientras realiza los pasos de este tutorial.
 
## <a name="MainStep3"></a> Paso 3: Crear servicios vinculados

> [AZURE.NOTE]Este artículo usa el Portal de Azure, específicamente el Editor de generador de datos, para crear canalizaciones, las tablas y servicios vinculados. Consulte [Tutorial con Azure PowerShell][adftutorial-using-powershell] si desea realizar este tutorial con Azure PowerShell.

En este paso, creará los siguientes servicios vinculados:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Crear StorageLinkedService y HDInsightStorageLinkedService

1.	En el **factoría de datos** blade, haga clic en **autor e implementar** mosaico para iniciar la **Editor** para el generador de datos.

	![Mosaico Crear e implementar][image-author-deploy-tile]

	> [AZURE.NOTE]Consulte [Editor del generador de datos][data-factory-editor] tema de información general detallada del editor de generador de datos.

2.  En el **Editor**, haga clic en **nuevo almacén de datos** botón en la barra de herramientas y seleccione **almacenamiento de Azure** desde el menú desplegable. Debería ver la plantilla JSON para crear un servicio de almacenamiento de Azure vinculado en el panel derecho.
	
	![Botón de almacén de datos nuevo editor][image-editor-newdatastore-button]

3. Reemplace **accountname** y **accountkey** con el nombre de cuenta y valores de clave de cuenta para la cuenta de almacenamiento de Azure.

	![Almacenamiento de blobs de editor JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Consulte [referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obtener más información acerca de las propiedades JSON.

4. Haga clic en **implementar** en la barra de herramientas para implementar el StorageLinkedService. Confirme que aparece el mensaje **vinculado el servicio creado correctamente** en la barra de título.

	![Implementar el almacenamiento de blobs de editor][image-editor-blob-storage-deploy]

5. Repita los pasos para crear otro almacenamiento de Azure vinculan servicio denominado: **HDInsightStorageLinkedService** para el almacenamiento asociado con el clúster de HDInsight. En el script JSON para el servicio vinculado, cambie el valor de la **nombre** propiedad **HDInsightStorageLinkedService**.

### Crear AzureSqlLinkedService
1. En el **Editor del generador de datos** , haga clic en **nuevo almacén de datos** botón en la barra de herramientas y seleccione **base de datos de SQL Azure** desde el menú desplegable. Debería ver la plantilla JSON para crear el servicio SQL Azure vinculado en el panel derecho.
2. Reemplace **nombreDeServidor**, **username@servername**, y **contraseña** con los nombres de su servidor SQL Azure, la cuenta de usuario y la contraseña. 3. Reemplace **databasename** con **MarketingCampaigns**. Se trata de la base de datos de SQL Azure creado por las secuencias de comandos que se ejecutó en el paso 1. Debe confirmar que esta base de datos de hecho se creó las secuencias de comandos (en caso de que se han producido errores). 
3. Haga clic en **implementar** en la barra de herramientas para crear e implementar el AzureSqlLinkedService.

### Crear HDInsightLinkedService
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición.

#### Para utilizar un clúster de HDInsight a petición
1. Haga clic en **nuevo cálculo** en la barra de comandos y seleccione **clúster de HDInsight a petición** en el menú.
2. Haga lo siguiente en la secuencia de comandos JSON: 
	1. Para el **clusterSize** propiedad, especifique el tamaño del clúster de HDInsight.
	2. Para el **jobsContainer** propiedad, especifique el nombre del contenedor predeterminado donde se almacenarán los registros de clúster. Para este tutorial, especifique **adfjobscontainer**.
	3. Para el **timeToLive** propiedad, especifique cuánto tiempo el cliente puede estar inactivo antes de eliminarla. 
	4. Para el **versión** propiedad, especificar la versión de HDInsight que desea utilizar. Si excluye esta propiedad, se utiliza la versión más reciente.  
	5. Para el **linkedServiceName**, especifique **HDInsightStorageLinkedService** tutorial de introducción que se han creado en Get. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		Tenga en cuenta que el **tipo** de servicios vinculados se establece en **HDInsightOnDemandLinkedService**.

2. Haga clic en **implementar** en la barra de comandos para implementar el servicio vinculado.
   
   
#### Para utilizar su propio clúster de HDInsight: 

1. Haga clic en **nuevo cálculo** en la barra de comandos y seleccione **clúster de HDInsight** en el menú.
2. Haga lo siguiente en la secuencia de comandos JSON: 
	1. Para el **clusterUri** propiedad, escriba la dirección URL para su HDInsight. Por ejemplo: https://<clustername>.azurehdinsight.net/     
	2. Para el **nombre de usuario** propiedad, escriba el nombre de usuario que tenga acceso al clúster de HDInsight.
	3. Para el **contraseña** propiedad, escriba la contraseña del usuario. 
	4. Para el **LinkedServiceName** propiedad, escriba **StorageLinkedService**. Éste es el servicio vinculado que creó en el tutorial de introducción. 

	Mitir que la **tipo** de servicios vinculados se establece en **HDInsightBYOCLinkedService** (BYOC significa poner su propio clúster).

2. Haga clic en **implementar** en la barra de comandos para implementar el servicio vinculado.


## <a name="MainStep4"></a> Paso 4: Crear tablas
 
En este paso, creará las tablas de la factoría de datos siguientes:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial flujo de extremo a extremo][image-data-factory-tutorial-end-to-end-flow]
 
La imagen anterior muestra los procesos en la fila central y las tablas de las filas superior e inferior.

### Para crear las tablas
	
1. En el **Editor** para el generador de datos, haga clic en **nuevo conjunto de datos** en la barra de herramientas y haga clic en botón **almacenamiento Azure Blob** desde el menú desplegable. 
2. Reemplazar JSON en el panel derecho con el script JSON de la **RawGameEventsTable.json** desde el **C:\ADFWalkthrough\Tables** carpeta.
3. Haga clic en **implementar** en la barra de herramientas para crear e implementar la tabla. Confirme que aparece la **tabla creado correctamente** mensaje en la barra de título del Editor.
4. Repita los pasos 1 a 3 con el contenido de los archivos siguientes: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Repita los pasos 1-3 con el contenido del archivo siguiente. PERO selecciona **Sql Azure** tras hacer clic en **nuevo conjunto de datos**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Paso 5: Crear y programar las canalizaciones
En este paso, creará los siguientes procesos:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Para crear canalizaciones

1. En el **Editor de la Factoría de datos**, haga clic en **Nueva canalización** en la barra de herramientas. Haga clic en **... (puntos suspensivos)** en la barra de herramientas si no ve el botón. O bien, haga clic con el botón secundario **Canalizaciones** en la vista de árbol y elija **Nueva canalización**.
2. Reemplazar JSON en el panel derecho con el script JSON de la **PartitionGameLogsPipeline.json** desde el **C:\ADFWalkthrough\Pipelines** carpeta.
3. Agregue una **coma (',')** al final del **corchete de cierre ('] ')** en el archivo JSON y, a continuación, agregue las tres líneas siguientes después del corchete de cierre. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]Tenga en cuenta que las horas de inicio y finalización están establecidas en 01/05/2014 y 05/05/2014, dado que los datos de ejemplo de este tutorial van del 01/05/2014 al 05/05/2014.
 
3. Haga clic en **Implementar** en la barra de herramientas para implementar la canalización. Confirme que aparece el mensaje **LA CANALIZACIÓN SE HA CREADO CORRECTAMENTE** en la barra de título del Editor.
4. Repita los pasos 1 a 3 con el contenido de los archivos siguientes: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Cierre los módulos del generador de datos presionando **X** (esquina superior derecha) para ver la página principal (** factoría de datos **blade) para el generador de datos. 
### Vista de diagrama

1. En el **factoría de datos** módulo para la **LogProcessingFactory**, haga clic en **diagrama**. 

	![Vínculo de diagrama][image-data-factory-tutorial-diagram-link]

2. Puede reorganizar el diagrama que ve; aquí se muestra un diagrama reorganizado en el que se muestran entradas directas en la parte superior y las salidas en la parte inferior. Puede ver que la salida de la **PartitionGameLogsPipeline** se pasa como una entrada a la EnrichGameLogsPipeline y la salida de la **EnrichGameLogsPipeline** se pasa a la **AnalyzeMarketingCampaignPipeline**. Haga doble clic en un título para ver detalles sobre el artefacto que representa la hoja.

	![Vista de diagrama][image-data-factory-tutorial-diagram-view]

3. Haga clic en **AnalyzeMarketingCampaignPipeline**, y haga clic en **abrir canalización**. Debe ver todas las actividades de la canalización junto con conjuntos de datos de entrada y salida para las actividades.
 
	![Canalización abierto](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Haga clic en **factoría de datos** en la ruta de exploración en la esquina superior izquierda para volver a la vista de diagrama con todas las canalizaciones.


**¡Enhorabuena!** Ha creado la factoría de datos de Azure, servicios vinculados, procesos y tablas, y ha iniciado el flujo de trabajo.


## <a name="MainStep6"></a> Paso 6: Supervisar los sectores de canalizaciones y los datos 

1.	Si no tiene la **factoría de datos** módulo para la **LogProcessingFactory** abierto, puede realizar una de las acciones siguientes:
	1.	Haga clic en **LogProcessingFactory** en el **panel de inicio**. Al crear el generador de datos, el **Agregar al panel de inicio** opción estaba activada automáticamente.

		![Panel de inicio de supervisión][image-data-factory-monitoring-startboard]

	2. Haga clic en **Examinar** concentrador y haga clic en **todo**.
	 	
		![Supervisión de concentrador todo][image-data-factory-monitoring-hub-everything]

		En el **Examinar** blade, seleccione **generadores de datos** y seleccione **LogProcessingFactory** en el **generadores de datos** blade.

		![Supervisión de exploración Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Puede supervisar su factoría de datos de varias maneras. Puede iniciar con procesos o conjuntos de datos. Vamos a comenzar con los procesos e iremos profundizando. 
3.	Haga clic en **canalizaciones** en el **factoría de datos** blade. 
4.	Haga clic en **PartitionGameLogsPipeline** en el cuadro de canalizaciones. 
5.	En el **canalización** módulo para **PartitionGameLogsPipeline**, verá que la canalización consume **RawGameEventsTable** conjunto de datos. Haga clic en **RawGameEventsTable**.

	![Canalización consumido y generado][image-data-factory-monitoring-pipeline-consumed-produced]

6. En el cuadro de tabla para **RawGameEventsTable**, verá que todos los sectores. En la siguiente captura de pantalla, todos los sectores están en **Listo** estado y no hay sectores de ningún problema. Significa que los datos están listos para ser procesados.

	![Módulo de tabla RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Ambos **actualizado recientemente sectores** y **error recientemente sectores** listas se ordenan por el **hora de última actualización**. En las situaciones siguientes, se cambia la hora de un intervalo de actualización.

	-  Actualiza el estado del sector manualmente, por ejemplo, utilizando la **conjunto AzureDataFactorySliceStatus** (o), haga clic en **ejecutar** en el **sector** módulo para el sector.
	-  El segmento cambia estado debido a una ejecución (por ejemplo, una ejecución iniciado, una ejecución finalizado y no se pudo, una ejecución finaliza y se realizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** Para ver la lista más amplia de sectores. Haga clic en **filtro** en la barra de herramientas para filtrar los sectores.
	
	Para ver los intervalos de datos ordenados por los tiempos de inicio y fin del sector en su lugar, haga clic en **rebanadas de datos (por hora de sector)** en mosaico.

	![Segmentos de datos por intervalo de tiempo][DataSlicesBySliceTime]
 
7. Ahora, en la **canalización** módulo para **PartiionGameLogsPipeline**, haga clic en **producido**.
8. Debería ver la lista de conjuntos de datos que genera este proceso: 
9. Haga clic en **PartitionedGameEvents** de la tabla en la **genera conjuntos de datos** blade. 
10.	Confirme que el **estado** de todos los sectores se establece en **Listo**. 
11.	Haga clic en uno de los sectores que es **Listo** para ver el **segmento de datos** blade para ese sector.

	![SEGMENTO de datos RawGameEventsTable blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si se produjo un error, verá una **error **estado aquí. También puede ver cualquier ambos segmentos con estado **Listo**, o ambos con el estado **PendingValidation**, dependiendo de la rapidez se procesan los sectores.

	Si no se encuentra en el sector del **Listo** estado, puede ver los intervalos de nivel superior que no están listos y están bloqueando el intervalo actual de la ejecución en el **intervalos de nivel superior que no están listos** lista.
 
	Consulte la [referencia del programador de generador de datos de Azure][developer-reference] para obtener una descripción de todos los Estados posibles del sector.

12.	En el **segmento de datos** blade, haga clic en la ejecución de la **se ejecuta la actividad** lista. Debería ver la hoja Ejecución de actividad para ese segmento. Debería aparecer el siguiente **Detalles de ejecución de la actividad** blade.

	![Cuadro de detalles de actividad][image-data-factory-monitoring-activity-run-details]

13.	Haga clic en **descargar** para descargar los archivos. Esta pantalla es especialmente útil al solucionar errores de procesamiento de HDInsight.
	 
	
Cuando se completa la ejecución de todas la canalización, se puede examinar en el **MarketingCampaignEffectivenessTable** en la **MarketingCampaigns** base de datos de SQL Azure para ver los resultados.

**¡Enhorabuena!** Ahora puede supervisar y solucionar problemas de los flujos de trabajo. Ha aprendido a utilizar la factoría de datos de Azure para procesar los datos y obtener análisis.

## Ampliar el tutorial para usar datos locales
En el último paso del escenario del tutorial en este artículo de procesamiento del registro, la salida de eficacia de campaña marketing se ha copiado en una base de datos de SQL Azure. También puede mover estos datos a un SQL Server local para realizar análisis dentro de su organización.
 
Para copiar los datos de eficacia de campaña marketing de Azure Blob a SQL Server local, tendrá que crear servicios vinculados de locales adicionales, tabla y la canalización se introdujeron en el tutorial en este artículo.

Práctica la [Tutorial: utilizando el origen de datos de la local][tutorial-onpremises] para aprender a crear una canalización para copiar datos de eficacia de campaña de marketing a una base de datos de SQL Server local.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir--> 