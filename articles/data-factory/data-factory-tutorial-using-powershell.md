<properties 
	pageTitle="Desplazamiento y procesamiento de archivos de registro mediante la factoría de datos de Azure" 
	description="En este tutorial avanzado se describe un escenario casi real y se implementa el escenario con el servicio de la factoría de datos de Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Tutorial: Mover y procesar los archivos de registro mediante el generador de datos [PowerShell]
En este artículo se ofrece un completo tutorial de un escenario canónico de procesamiento de registros utilizando la factoría de datos de Azure para transformar los datos de archivos de registro en perspectivas.

## Escenario
Contoso es una empresa de juegos que cree juegos para múltiples plataformas: equipos personales (PC), dispositivos de mano y consolas de juegos. Cada uno de estos juegos produce miles de registros. El objetivo de Contoso es recopilar y analizar los registros generados por estos juegos para obtener información de uso, identificar las oportunidades de venta y venta cruzada, desarrollar nuevas características atractivas, etc. para mejorar el negocio y ofrecer la mejor experiencia a los clientes.
 
En este tutorial, recopilamos registros de ejemplo, los procesamos y enriquecemos con datos de referencia, y transformamos los datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente.

## Preparación para el tutorial
1.	Lectura [Introducción al generador de datos de Azure][adfintroduction] para obtener información general del generador de datos de Azure y comprensión de los conceptos de nivel superior.
2.	Debe tener una suscripción de Azure para realizar este tutorial. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].
3.	Debe descargar e instalar [Azure PowerShell][download-azure-powershell] en el equipo. 
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
El flujo de trabajo de extremo a extremo se muestra a continuación: ![Tutorial flujo de extremo a extremo][image-data-factory-tutorial-end-to-end-flow]

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

1.	Tras iniciar sesión en la [Portal de vista previa de Azure][azure-preview-portal], haga clic en **nuevo** desde la esquina inferior izquierda y haga clic en **factoría de datos** en el **nuevo** blade. 

	![Nuevo -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Si no ve **factoría de datos** en el **nuevo** blade, desplácese hacia abajo.
	
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

> [AZURE.NOTE]En este artículo usa Azure PowerShell para crear canalizaciones, las tablas y servicios vinculados. Consulte [Tutorial con Editor de generador de datos][adftutorial-using-editor] si desea realizar este tutorial mediante el Portal de Azure, específicamente el Editor de generador de datos.

En este paso, creará los siguientes servicios vinculados: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService y HDInsightLinkedService.


1.	En el **LogProcessingFactory** blade, haga clic en **servicios vinculados** en mosaico.

	![Icono de servicios vinculados][image-data-factory-tutorial-linkedservice-tile]

2. En el **servicios vinculados** blade, haga clic en **+ el almacén de datos** desde la barra de comandos.

	![Servicios vinculados - Agregar almacén][image-data-factory-tutorial-linkedservices-add-datstore]

3. En el **nuevo almacén de datos** blade, escriba **StorageLinkedService** para el **nombre**, haga clic en **(configuración necesaria) de tipo**, y seleccione **cuenta de almacenamiento de Azure**.

	![Tipo de almacén de datos: almacenamiento de Azure][image-data-factory-tutorial-datastoretype-azurestorage]

4. En el **nuevo almacén de datos** blade, verá dos nuevos campos: **nombre de la cuenta** y **clave de cuenta**. Escriba el nombre de cuenta y clave de cuenta para su **cuenta de almacenamiento de Azure**.

	![Configuración de almacenamiento de Azure][image-data-factory-tutorial-azurestorage-settings]

	Puede obtener el nombre de la cuenta y la clave de la cuenta de su cuenta de almacenamiento de Azure desde el portal, como se muestra a continuación:

	![Clave de almacenamiento][image-data-factory-tutorial-storage-key]
  
5. Tras hacer clic en **Aceptar** en los datos nuevos almacenar blade, debería ver **StorageLinkedService** en la lista de **almacenes de datos** en el **servicios vinculados** blade. Comprobar **notificaciones** concentrador (a la izquierda) para ver los mensajes.

	![Módulo de servicios vinculados con almacenamiento de información][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Repetir **los pasos 2 a 5** para crear otro vinculadas servicio denominado: **HDInsightStorageLinkedService**. Se trata el almacenamiento utilizado por su clúster de HDInsight.
7. Confirme que ve ambos **StorageLinkedService** y **HDInsightStorageLinkedService** en la lista en el módulo de servicios vinculados.
8. En el **servicios vinculados** blade, haga clic en **Añadir (+) en el almacén de datos** desde la barra de comandos.
9. Escriba **AzureSqlLinkedService** para el nombre.
10. Haga clic en **(configuración necesaria) de tipo**, seleccione **base de datos de SQL Azure**.
11. Ahora, debería los siguientes campos en el **nuevo almacén de datos** blade. Escriba el nombre de la base de datos de SQL Azure **server**, **base de datos** nombre, **nombre de usuario**, y **contraseña**, y haga clic en **Aceptar**.
	1. Escriba **MarketingCampaigns** para el **base de datos**. Se trata de la base de datos de SQL Azure creado por las secuencias de comandos que se ejecutó en el paso 1. Debe confirmar que esta base de datos de hecho se creó las secuencias de comandos (en caso de que se han producido errores).
		
 		![Configuración de SQL Azure][image-data-factory-tutorial-azuresql-settings]

		Para obtener estos valores desde el Portal de administración de Azure: haga clic en las cadenas de conexión de base de datos de vista SQL para base de datos MarketingCampaigns

		![Cadena de conexión de base de datos SQL Azure][image-data-factory-tutorial-azuresql-database-connection-string]

12. Confirme que todos los almacenes de datos que ha creado: **StorageLinkedService**, **HDInsightStorageLinkedService**, y **AzureSqlLinkedService**.
13. Tendrá que crear otro servicio vinculado, pero éste es un servicio de proceso, específicamente **clúster de HDInsight de Azure**. El portal no admite la creación de un servicio informático vinculado todavía. Por lo tanto, necesitará usar Azure PowerShell para crear este servicio vinculado. 
14. Cambiar a **Azure PowerShell** si tiene ya está abierto (o) inicie **Azure PowerShell**. Si se hubiera cerrado y se vuelve a abrir Azure PowerShell, deberá ejecutar los siguientes comandos: 
	- Ejecutar **Add-AzureAccount** y escriba el nombre de usuario y la contraseña que utilizas para iniciar sesión en el Portal de vista previa de Azure.  
	- Ejecutar **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecutar **Select-AzureSubscription** para seleccionar la suscripción que desea trabajar. Esta suscripción debe ser la misma que la usada en el Portal de vista previa de Azure. 
15. Cambiar a **AzureResourceManager** están disponibles en este modo de modo que los cmdlets del generador de datos de Azure.

		Switch-AzureMode AzureResourceManager

16. Navegue hasta la **LinkedServices** subcarpeta **C:\ADFWalkthrough** (o) de la carpeta de la ubicación donde extrajo los archivos.
17. Abra **HDInsightLinkedService.json** en su editor favorito y observe que el tipo está establecido en **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición. El HDInsightLinkedService vincula un clúster de HDInsight a petición con el generador de datos. Para utilizar su propio clúster de HDInsight, actualice la sección de propiedades del archivo HDInsightLinkedService.json tal como se muestra a continuación (replace clustername, nombre de usuario y contraseña con los valores adecuados):
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. Utilice el siguiente comando para establecer la variable $df en el nombre de la factoría de datos.

		$df = “LogProcessingFactory”
19. Use el cmdlet **nueva AzureDataFactoryLinkedService** para crear un servicio vinculado como sigue. Comience con la cuenta de almacenamiento:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Si está utilizando un nombre diferente para ResourceGroupName, DataFactoryName o LinkedService, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON del servicio vinculado si no se encuentra el archivo.
20. Debería ver todos los servicios vinculados cuatro de los **servicios vinculados** blade tal como se muestra a continuación. Si el módulo de servicios vinculado no está abierto, haga clic en servicios vinculados en el **factoría de datos** página **LogProcessingFactory**. La hoja Servicios vinculados puede tardar algunos segundos en actualizarse.

	![Vincular servicios todos][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Paso 4: Crear tablas 
En este paso, creará las tablas siguientes:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial flujo de extremo a extremo][image-data-factory-tutorial-end-to-end-flow]
 
La imagen anterior muestra los procesos en la fila central y las tablas de las filas superior e inferior.

El Portal de Azure no admite crear conjuntos de datos y tablas, por lo que deberá usar Azure PowerShell para crear tablas en esta versión.

### Para crear las tablas

1.	En Azure PowerShell, navegue hasta el **tablas** carpeta (** C:\ADFWalkthrough\Tables**) desde la ubicación donde extrajo los ejemplos. 
2.	Use el cmdlet **nueva AzureDataFactoryTable** para crear las tablas de manera para **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Si está utilizando otro nombre para ResourceGroupName y DataFactoryName, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON de la tabla si no se encuentra el archivo mediante el cmdlet.

3. Repita el paso anterior para crear las tablas siguientes:
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. En el **Portal de vista previa de Azure**, haga clic en **conjuntos de datos** en el **factoría de datos** módulo para **LogProcessingFactory** y confirme que todos los conjuntos de datos (tablas son conjuntos de datos rectangular).

	![Establece todos datos][image-data-factory-tutorial-datasets-all]

	También puede utilizar el siguiente comando de Azure PowerShell:
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Paso 5: Crear y programar las canalizaciones
En este paso, creará las siguientes canalizaciones: PartitionGameLogsPipeline, EnrichGameLogsPipeline y AnalyzeMarketingCampaignPipeline.

1. En **Windows Explorer**, navegue hasta el **canalizaciones** subcarpeta en **C:\ADFWalkthrough** carpeta (o desde la ubicación donde extrajo los ejemplos).
2.	Abra **PartitionGameLogsPipeline.json** en su editor favorito, reemplace el resaltado con su cuenta de almacenamiento para la información de cuenta de almacenamiento de datos y guarde el archivo.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Repita el paso para crear los siguientes procesos:
	1. **EnrichGameLogsPipeline**.json (3 repeticiones)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 repeticiones)

	**Importante:** confirme que haya reemplazado todos <storageaccountname> con su nombre de cuenta de almacenamiento.
 
4.  En **Azure PowerShell**, navegue hasta el **canalizaciones** subcarpeta en **C:\ADFWalkthrough** carpeta (o desde la ubicación donde extrajo los ejemplos).
5.  Use el cmdlet **nueva AzureDataFactoryPipeline** para crear las canalizaciones de manera para **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Si está utilizando un nombre diferente para ResourceGroupName, DataFactoryName o Pipeline, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON de proceso.
6. Repita el paso anterior para crear los siguientes procesos:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Use el cmdlet **Get AzureDataFactoryPipeline** para obtener la lista de las canalizaciones.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Una vez creados los procesos, puede especificar la duración en la que se producirá el procesamiento de datos. Al especificar el período activo de un proceso, está definiendo el tiempo en el que se procesarán los segmentos de datos basándose en las propiedades de disponibilidad que se han definido para cada tabla ADF.

Para especificar el período activo para el proceso, puede usar el cmdlet Set-AzureDataFactoryPipelineActivePeriod. En este tutorial, los datos de ejemplo son de 05/01 05/05. Uso de 2014-05-01 como el StartDateTime. EndDateTime es opcional.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Confirme para establecer el período activo del proceso.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Repita los dos pasos anteriores para establecer el período activo para los siguientes procesos.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. En el **Portal de vista previa de Azure**, haga clic en **canalizaciones** disponer en mosaico (no en los nombres de las canalizaciones) en el **factoría de datos** módulo para la **LogProcessingFactory**, debería ver las canalizaciones que creó.

	![Todas las canalizaciones][image-data-factory-tutorial-pipelines-all]

12. En el **factoría de datos** módulo para la **LogProcessingFactory**, haga clic en **diagrama**.

	![Vínculo de diagrama][image-data-factory-tutorial-diagram-link]

13. Puede reorganizar el diagrama que ve; aquí se muestra un diagrama reorganizado en el que se muestran entradas directas en la parte superior y las salidas en la parte inferior. Puede ver que la salida de la **PartitionGameLogsPipeline** se pasa como una entrada a la EnrichGameLogsPipeline y la salida de la **EnrichGameLogsPipeline** se pasa a la **AnalyzeMarketingCampaignPipeline**. Haga doble clic en un título para ver detalles sobre el artefacto que representa la hoja.

	![Vista de diagrama][image-data-factory-tutorial-diagram-view]

	**¡Enhorabuena!** Ha creado la factoría de datos de Azure, servicios vinculados, procesos y tablas, y ha iniciado el flujo de trabajo.


## <a name="MainStep6"></a> Paso 6: Supervisar los sectores de canalizaciones y los datos 

1.	Si no tiene la hoja FACTORÍA DE DATOS para abrir LogProcessingFactory, puede hacer lo siguiente:
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
 
7. Ahora, en la **canalización** módulo para **PartiionGameLogsPipeline**, haga clic en **producido**.
8. Debería ver la lista de conjuntos de datos que genera este proceso: 
9. Haga clic en **PartitionedGameEvents** de la tabla en la **genera conjuntos de datos** blade. 
10.	Confirme que el **estado** de todos los sectores se establece en **Listo**. 
11.	Haga clic en uno de los sectores que es **Listo** para ver el **segmento de datos** blade para ese sector.

	![SEGMENTO de datos RawGameEventsTable blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si se produjo un error, verá una **error **estado aquí. También puede ver cualquier ambos segmentos con estado **Listo**, o ambos con el estado **PendingValidation**, dependiendo de la rapidez se procesan los sectores.
 
	Consulte la [referencia del programador de generador de datos de Azure][developer-reference] para obtener una descripción de todos los Estados posibles del sector.

12.	En el **segmento de datos** blade, haga clic en la ejecución de la **se ejecuta la actividad** lista. Debería ver la hoja Ejecución de actividad para ese segmento. Debería aparecer el siguiente **Detalles de ejecución de la actividad** blade.

	![Cuadro de detalles de actividad][image-data-factory-monitoring-activity-run-details]

13.	Haga clic en **descargar** para descargar los archivos. Esta pantalla es especialmente útil al solucionar errores de procesamiento de HDInsight.
	 
	
Cuando se completa la ejecución de todas la canalización, se puede examinar en el **MarketingCampaignEffectivenessTable** en la **MarketingCampaigns** base de datos de SQL Azure para ver los resultados.

**¡Enhorabuena!** Ahora puede supervisar y solucionar problemas de los flujos de trabajo. Ha aprendido a utilizar la factoría de datos de Azure para procesar los datos y obtener análisis.

## Ampliar el tutorial para usar datos locales
En el último paso del escenario del tutorial en este artículo de procesamiento del registro, la salida de eficacia de campaña marketing se ha copiado en una base de datos de SQL Azure. También puede mover estos datos a un SQL Server local para realizar análisis dentro de su organización.
 
Para copiar los datos de eficacia de campaña marketing de Azure Blob a SQL Server local, tendrá que crear servicios vinculados de locales adicionales, tabla y la canalización se introdujeron en el tutorial en este artículo.

Práctica la [Tutorial: utilizando el origen de datos de la local][tutorial-onpremises-using-powershell] para aprender a crear una canalización para copiar datos de eficacia de campaña de marketing a una base de datos de SQL Server local.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir--> 