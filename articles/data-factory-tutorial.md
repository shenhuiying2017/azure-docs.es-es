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
	ms.date="1/28/2015" 
	ms.author="spelluru"/>

# Tutorial: Desplazamiento y procesamiento de archivos de registro mediante la factoría de datos
En este artículo se ofrece un completo tutorial de un escenario canónico de procesamiento de registros utilizando la factoría de datos de Azure para transformar los datos de archivos de registro en perspectivas. 

## Escenario
Contoso es una empresa de juegos que crea juegos para múltiples plataformas: consolas de juegos, dispositivos de mano y ordenadores personales (PC). Cada uno de estos juegos produce miles de registros. El objetivo de Contoso es recopilar y analizar los registros generados por estos juegos para obtener información de uso, identificar las oportunidades de venta y venta cruzada, desarrollar nuevas características atractivas, etc. para mejorar el negocio y ofrecer la mejor experiencia a los clientes.
 
En este tutorial, recopilamos registros de ejemplo, los procesamos y enriquecemos con datos de referencia, y transformamos los datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente.

## Preparación para el tutorial
1.	Lea [Introducción a la factoría de datos de Azure][adfintroduction] para obtener información general sobre la factoría de datos de Azure y conocer los conceptos de nivel superior.
2.	Debe tener una suscripción de Azure para realizar este tutorial. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra] [azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Evaluación gratuita][azure-free-trial].
3.	Debe descargar e instalar [Azure PowerShell][download-azure-powershell] en el equipo. 
2.	**(recomendado)** Revise y practique el tutorial en el artículo [Introducción a la factoría de datos de Azure][adfgetstarted] para obtener un tutorial sencillo para familiarizarse con el portal y cmdlets.
3.	**(recomendado)** Revise y practique el tutorial en el artículo [Uso de Pig y Hive con la factoría de datos de Azure][usepigandhive] para obtener un tutorial sobre cómo crear un proceso para desplazar datos de origen de datos local a un almacenamiento de blobs de Azure.
4.	Descargue [los archivos de ADFWalkthrough][adfwalkthrough-download] en la carpeta **C:\ADFWalkthrough**** conservando la estructura de carpetas**:
	- **Procesos:** Incluye  archivos JSON que contienen la definición de los procesos.
	- **Tablas:** Incluye  archivos JSON que contienen la definición de las tablas.
	- **LinkedServices:** Incluye archivos JSON que contienen la definición de su clúster de almacenamiento y cálculo (HDInsight) 
	- **Scripts:** Incluye los scripts Hive y Pig que se utilizan para procesar los datos y se invoca desde los procesos
	- **SampleData:** Incluye datos de ejemplo para este tutorial
	- **OnPremises:** Incluye archivos de JSON y scripts que se usan para demostrar el acceso a los datos locales
	- **AzureEnvironmentSetup.ps1:** El script de PowerShell para configurar el entorno de Azure
	- **uploadSampleDataAndScripts.ps1:** Este script carga los datos de ejemplo y los scripts en Azure.
5. Asegúrese de haber creado los siguientes recursos de Azure:			
	- Cuenta de almacenamiento de Azure.
	- Base de datos SQL de Azure
	- Clúster de Azure HDInsight versión 3.1 o superior	
7. Una vez creados los recursos de Azure, asegúrese de tener la información necesaria para conectarse a cada uno de estos recursos.
 	- **Cuenta de almacenamiento de Azure** - nombre y clave de la cuenta.  
	- **Base de datos SQL de Azure** - servidor, base de datos, nombre de usuario y contraseña.
	- **Clúster de Azure HDInsight** - nombre del clúster de HDInsight, nombre de usuario, contraseña y nombre de cuenta y clave de cuenta para el almacenamiento de Azure asociado a este clúster.  
 8. Inicie **Azure PowerShell**, vaya a**C:\ADFWalkthrough** y ejecute el script de preparación .**\AzureEnvironmentSetup.ps1**.
 
		Debe tener el nombre de la suscripción y la cuenta de Microsoft asociados a la suscripción antes de ejecutar el script.

		Debe configurar el entorno de Azure mediante el script anterior cada vez que se inicie  Azure PowerShell. La configuración no se conserva entre sesiones y debe ejecutarse por separado, incluso cuando se utilizan varias sesiones simultáneamente.

		Como alternativa, puede usar el cmdlet **Add-AzureAccount** para iniciar sesión en Azure y seleccione una suscripción (si tiene varias suscripciones) utilizando el cmdlet **Select-AzureSubscription**.
	

## Información general
El flujo de trabajo completo se muestra a continuación:
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs (RawGameEventsTable) y crea particiones basadas en el año, el mes y el día (PartitionedGameEventsTable).
 2. **EnrichGameLogsPipeline** combina eventos de juegos con particiones (tabla PartitionedGameEvents, que es un resultado de PartitionGameLogsPipeline) con código geográfico (RefGetoCodeDictionaryTable) y enriquece los datos mediante la asignación de una dirección IP a la ubicación geográfica correspondiente (EnrichedGameEventsTable).
3. El proceso **AnalyzeMarketingCampaignPipeline** aprovecha los datos enriquecidos (EnrichedGameEventTable producido por EnrichGameLogsPipeline) y lo procesa con los datos de publicidad (RefMarketingCampaignnTable) para crear el resultado final de la eficacia de la campaña de marketing, que se copian en la base de datos SQL de Azure (MarketingCampainEffectivensessSQLTable) y un almacenamiento de blobs de Azure (MarketingCampaignEffectivenessBlobTable) para el análisis.
    
## Tutorial: Creación, implementación y supervisión de flujos de trabajo
1. [Paso 1: Cargue los scripts y datos de ejemplo](#MainStep1). En este paso, cargará todos los datos de ejemplo (incluidos todos los registros y datos de referencia) y los flujos de trabajo ejecutarán los scripts Hive/Pig. Los scripts que ejecuta también crean una base de datos SQL de Azure (denominada MarketingCampaigns), tablas, tipos definidos por el usuario y procedimientos almacenados.
2. [Paso 2: Cree una factoría de datos de Azure](#MainStep2). En este paso, creará una factoría de datos de Azure denominada LogProcessingFactory.
3. [Paso 3: Cree servicios vinculados](#MainStep3). En este paso, creará los siguientes servicios vinculados: 
	
	- 	**StorageLinkedService**. Vincula la ubicación de almacenamiento de Azure que contiene eventos de juegos sin procesar, eventos de juegos con particiones, eventos de juegos enriquecidos, información efectiva de campañas de marketing, datos de código geográfico de referencia y datos de campaña de marketing de referencia a LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Vincula una base de datos SQL de Azure que contiene información de eficacia de campaña de marketing. 
	- 	**HDInsightStorageLinkedService**. Vincula un almacenamiento de blobs de Azure asociado al clúster de HDInsight al que hace referencia HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Vincula un clúster de Azure HDInsight a LogProcessingFactory. Este clúster se usa para realizar el procesamiento pig y hive en los datos. 
 		
4. [Paso 4: Cree tablas](#MainStep4). En este paso, creará las tablas siguientes:  	
	
	- **RawGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego sin procesar en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego con particiones en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Esta tabla especifica la ubicación de los datos de código geográfico de referencia en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Esta tabla especifica la ubicación de los datos de campaña de marketing de referencia en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego enriquecidos en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**.Esta tabla especifica la tabla SQL (MarketingCampaignEffectiveness) en la base de datos SQL de Azure definidos por AzureSqlLinkedService que contiene los datos de eficacia de la campaña de marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Esta tabla especifica la ubicación de los datos de eficacia de la campaña de marketing en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Paso 5: Cree y programe procesos](#MainStep5). En este paso, creará los siguientes procesos:
	- **PartitionGameLogsPipeline**. Este proceso lee los eventos de juegos sin procesar de un almacenamiento de blobs (RawGameEventsTable) y crea particiones basadas en el año, el mes y el día (PartitionedGameEventsTable). 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Este proceso combina eventos de juegos con particiones (tabla PartitionedGameEvents, que es un resultado de PartitionGameLogsPipeline) con código geográfico (RefGetoCodeDictionaryTable) y enriquece los datos mediante la asignación de una dirección IP a la ubicación geográfica correspondiente (EnrichedGameEventsTable) 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Este proceso aprovecha los datos de eventos de juego enriquecidos (EnrichedGameEventTable producido por EnrichGameLogsPipeline) y lo procesa con los datos de publicidad (RefMarketingCampaignnTable) para crear el resultado final de la eficacia de la campaña de marketing, que se copian en la base de datos SQL de Azure (MarketingCampainEffectivensessSQLTable) y un almacenamiento de blobs de Azure (MarketingCampaignEffectivenessBlobTable) para el análisis.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Paso 6: Supervise los procesos y los segmentos de datos](#MainStep6). En este paso, supervisará los procesos, las tablas y los segmentos de datos mediante el Portal de Azure.

### <a name="MainStep1"></a>Paso 1: Cargue los scripts y datos de ejemplo.
En este paso, cargará todos los datos de ejemplo (incluidos todos los registros y datos de referencia) y los scripts Hive y Pig, que se invocan mediante los flujos de trabajo. Los scripts que ejecuta también crean una base de datos SQL de Azure denominada **MarketingCampaigns**, tablas, tipos definidos por el usuario y procedimientos almacenados. 

Las tablas, los tipos definidos por el usuario y procedimientos almacenados se utilizan al mover los resultados de la eficacia de la campaña de marketing desde el almacenamiento de blobs de Azure para la base de datos SQL de Azure.

1. Abra **uploadSampleDataAndScripts.ps1** desde la carpeta **C:\ADFWalkthrough** (o la carpeta que contenga los archivos extraídos) en su editor favorito, reemplace el texto resaltado por la información de su clúster y guarde el archivo.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] Este script requiere tener instalada en el equipo la utilidad sqlcmd. Si tiene SQL Server instalado, ya la tiene. De lo contrario, [descargue][sqlcmd-install] e instale la utilidad. 
	> Si lo prefiere, puede utilizar los archivos de la carpeta: C:\ADFWalkthrough\Scripts cargar los scripts pig y hive y los archivos de ejemplo en el contenedor adfwalkthrough, en el almacenamiento de blobs, y crear la tabla MarketingCampaignEffectiveness en la base de datos SQL de Azure MarketingCamapaigns.   
2. Confirme que el equipo local tiene acceso a la base de datos SQL de Azure. Para permitir el acceso, utilice el **Portal de administración de Azure** o **sp_set_firewall_rule** en la base de datos maestra para crear una regla de firewall para la dirección IP del equipo. Puede tardar hasta cinco minutos que este cambio surta efecto. Consulte [Definición de reglas de firewall para SQL Azure][azure-sql-firewall].
3. Inicie **Azure PowerShell**. 
4. Desplácese a la ubicación donde extrajo los ejemplos (por ejemplo: **C:\ADFWalkthrough**)
5. Ejecute **uploadSampleDataAndScripts.ps1** 
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
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>Paso 2: Crear una factoría de datos de Azure
En este paso, creará una factoría de datos de Azure denominada **LogProcessingFactory**.

1.	Tras iniciar sesión en el [Portal de vista previa de Azure][azure-preview-portal], haga clic en **NUEVO** en la esquina inferior izquierda y haga clic en **Factoría de datos** en la hoja **Nuevo**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Si no ve la **factoría de datos** en la hoja **Nuevo**, desplácese hacia abajo. 
	
5. En la hoja **Nueva factoría de datos**, escriba **LogProcessingFactory** como **Nombre**.

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Si aún no ha creado un grupo de recursos de Azure denominado **ADF**, haga lo siguiente:
	1. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y haga clic en **Crea un nuevo grupo de recursos**.
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. En la hoja **Crear grupo de recursos**, escriba **ADF** como nombre del grupo de recursos y haga clic en **Aceptar**.
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. Seleccione **ADF** como **NOMBRE DEL GRUPO DE RECURSOS**.  
8.	En la hoja **Nueva factoría de datos** observe que **Agregar al Panel de inicio** está seleccionado de forma predeterminada. Esto agrega un vínculo a la factoría de datos al panel de inicio (lo que verá cuando inicie sesión en el Portal de vista previa de Azure).

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	En la hoja **Nueva factoría de datos**, haga clic en **Crear** para crear la factoría de datos.
10.	Después de crear la factoría de datos, debería ver la hoja **FACTORÍA DE DATOS** denominada **LogProcessingFactory**.

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	Si no la ve, realice una de las acciones siguientes:

	- Haga clic en **LogProcessingFactory** en el **panel de inicio** (página principal)
	- Haga clic en **EXAMINAR** en el lado izquierdo, haga clic en **Todo**, haga clic en **Factorías de datos** y haga clic en la factoría de datos.
 

 
### <a name="MainStep3"></a>Paso 3: Crear servicios vinculados

En este paso, creará los siguientes servicios vinculados: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService y HDInsightLinkedService.

1.	En la hoja **LogProcessingFactory**, haga clic en el icono **Servicios vinculados**.

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. En la hoja **Servicios vinculados**, haga clic en **+ almacén de datos** en la barra de comandos.	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. En la hoja **Nuevo almacén de datos**, escriba **StorageLinkedService** como **Nombre**, haga clic en **TIPO (configuración necesaria)** y seleccione **Cuenta de almacenamiento de Azure**.

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. En la hoja **Nuevo almacén de datos**, verá dos nuevos campos: **Nombre de cuenta** y **Clave de cuenta**. Escriba el nombre de la cuenta y la clave de la cuenta para la **Cuenta de almacenamiento de Azure**.

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	Puede obtener el nombre de la cuenta y la clave de la cuenta de su cuenta de almacenamiento de Azure desde el portal, como se muestra a continuación:

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. Después de hacer clic en **Aceptar** en la hoja Nuevo almacén de datos, debería ver **StorageLinkedService** en la lista de **ALMACENES DE DATOS** en la hoja **Servicios vinculados**. Seleccione el centro **NOTIFICACIONES** (situado a la izquierda) para ver los mensajes.

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Repita los **pasos del 2 al 5** para crear otro servicio vinculado denominado: **HDInsightStorageLinkedService**. Se trata el almacenamiento utilizado por su clúster de HDInsight.
7. Confirme que aparecen **StorageLinkedService** y **HDInsightStorageLinkedService** en la lista de la hoja Servicios vinculados.
8. En la hoja **Servicios vinculados**, haga clic en **Agregar (+) almacén de datos** en la barra de comandos.
9. Escriba **AzureSqlLinkedService** como nombre.
10. Haga clic en **TIPO (configuración necesaria)**, seleccione **Base de datos SQL de Azure**.
11. Ahora, debería ver los siguientes campos en la hoja **Nuevo almacén de datos**. Escriba el nombre de la base de datos SQL de Azure **servidor**, **nombre de la** base de datos, **nombre de usuario** y **contraseña ** y haga clic en **Aceptar**.
	1. Escriba **MarketingCampaigns** para la **base de datos**. Se trata de la base de datos SQL de Azure creada por los scripts ejecutados en el paso 1. Debe confirmar que esta base de datos realmente fue creada por los scripts (en caso de que se han producido errores).
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		Para obtener estos valores desde el Portal de administración de Azure: Haga clic en Consultar las cadenas de conexión de las base de datos SQL para la base de datos MarketingCampaigns

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. Confirme que ve los tres almacenes de datos que ha creado: **StorageLinkedService**, **HDInsightStorageLinkedService** y **AzureSqlLinkedService**.
13. Tendrá que crear otro servicio vinculado, pero éste es para un servicio informático, específicamente **Clúster de Azure HDInsight**. El portal no admite la creación de un servicio informático vinculado todavía. Por lo tanto, necesitará usar Azure PowerShell para crear este servicio vinculado. 
14. Cambie a **Azure PowerShell** si ya lo tiene abierto (o) inicie **Azure PowerShell**.
15. Cambie al modo **AzureResourceManager** a medida que los cmdlets de la factoría de datos de Azure están disponibles.

		Switch-AzureMode AzureResourceManager

16. Vaya a la subcarpeta **LinkedServices** en **C:\ADFWalkthrough** (o) desde la carpeta de la ubicación donde extrajo los archivos.
17. Abra **HDInsightLinkedService.json** en su editor favorito, reemplace el texto resaltado por la información de su clúster y guarde el archivo.

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. Utilice el siguiente comando para establecer la variable $df en el nombre de la factoría de datos.

		$df = "LogProcessingFactory"
19. Use el cmdlet **New-AzureDataFactoryLinkedService** para crear un servicio vinculado como sigue. Comience con la cuenta de almacenamiento:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Si está utilizando un nombre diferente para ResourceGroupName, DataFactoryName o LinkedService, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON del servicio vinculado si no se encuentra el archivo.
20. Debería ver los cuatro servicios vinculados en la hoja **Servicios vinculados** como se muestra a continuación. Si la hoja Servicios vinculados no está abierta, haga clic en Servicios vinculados en la página **FACTORÍA DE DATOS** para **LogProcessingFactory**. La hoja Servicios vinculados puede tardar algunos segundos en actualizarse.

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>Paso 4: Cree las tablas .
En este paso, creará las tablas siguientes: 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
La imagen anterior muestra los procesos en la fila central y las tablas de las filas superior e inferior. 

El Portal de Azure no admite crear conjuntos de datos y tablas, por lo que deberá usar Azure PowerShell para crear tablas en esta versión.

#### Para crear las tablas

1.	En Azure PowerShell, vaya a la carpeta **Tablas** (**C:\ADFWalkthrough\Tables\**) desde la ubicación donde extrajo los ejemplos. 
2.	Use el cmdlet **New-AzureDataFactoryTable** para crear las tablas como sigue para **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	Si está utilizando otro nombre para ResourceGroupName y DataFactoryName, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON de la tabla si no se encuentra el archivo mediante el cmdlet.

3. Repita el paso anterior para crear las tablas siguientes:	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. En el **Portal de vista previa de Azure**, haga clic en **Conjuntos de datos** en la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory** y confirme que ve todos los conjuntos de datos (la tablas son conjuntos de datos rectangulares). 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	También puede utilizar el siguiente comando de Azure PowerShell:
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>Paso 5: Cree y programe procesos
En este paso, creará los siguientes procesos: PartitionGameLogsPipeline, EnrichGameLogsPipeline y AnalyzeMarketingCampaignPipeline.

1. En el **Explorador de Windows**, vaya a la subcarpeta **Pipelines** en la carpeta **C:\ADFWalkthrough** (o desde la ubicación donde extrajo los ejemplos).
2.	Abra **PartitionGameLogsPipeline.json** en su editor favorito, reemplace el texto resaltado por su cuenta de almacenamiento para la información de la cuenta de almacenamiento de datos y guarde el archivo.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Repita el paso para crear los siguientes procesos:
	1. **EnrichGameLogsPipeline**.json (3 apariciones)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 apariciones)

	**IMPORTANTE:** Confirme que ha reemplazado todas las apariciones de <storageaccountname> por el nombre de la cuenta de almacenamiento. 
 
4.  En **Azure PowerShell**, vaya a la subcarpeta **Pipelines** en la carpeta **C:\ADFWalkthrough** (o desde la ubicación donde extrajo los ejemplos).
5.  Use el cmdlet **New-AzureDataFactoryPipeline** para crear los procesos como sigue para **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	Si está utilizando un nombre diferente para ResourceGroupName, DataFactoryName o Pipeline, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON de proceso.
6. Repita el paso anterior para crear los siguientes procesos:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. Use el cmdlet **Get-AzureDataFactoryPipeline** para obtener la lista de los procesos.
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. Una vez creados los procesos, puede especificar la duración en la que se producirá el procesamiento de datos. Al especificar el período activo de un proceso, está definiendo el tiempo en el que se procesarán los segmentos de datos basándose en las propiedades de disponibilidad que se han definido para cada tabla ADF.

Para especificar el período activo para el proceso, puede usar el cmdlet Set-AzureDataFactoryPipelineActivePeriod. En este tutorial, los datos de ejemplo son del 05/01 al 05/05. Utilice 2014-05-01 como StartDateTime. EndDateTime es opcional.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. Confirme para establecer el período activo del proceso.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Repita los dos pasos anteriores para establecer el período activo para los siguientes procesos.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. En el **Portal de vista previa de Azure**, haga clic en el mosaico **Procesos** (no en los nombres de los procesos) en la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**, debería ver los procesos que ha creado.

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. En la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**, haga clic en **Diagrama**.

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. Puede reorganizar el diagrama que ve; aquí se muestra un diagrama reorganizado en el que se muestran entradas directas en la parte superior y las salidas en la parte inferior. Puede ver que la salida de **PartitionGameLogsPipeline** se pasa como entrada a EnrichGameLogsPipeline y la salida de **EnrichGameLogsPipeline** se pasa a **AnalyzeMarketingCampaignPipeline**. Haga doble clic en un título para ver detalles sobre el artefacto que representa la hoja.

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**¡Enhorabuena!** Ha creado la factoría de datos de Azure, servicios vinculados, procesos y tablas, y ha iniciado el flujo de trabajo. 


### <a name="MainStep6"></a>Paso 6: Supervise los procesos y los segmentos de datos .

1.	Si no tiene la hoja FACTORÍA DE DATOS para abrir LogProcessingFactory, puede hacer lo siguiente:
	1.	Haga clic en **LogProcessingFactory** en el **panel de inicio.**. Al crear la factoría de datos, se selecciona automáticamente la opción **Agregar al Panel de inicio**.

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. Haga clic en el centro **EXAMINAR** y haga clic en **Todo**.
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. En la hoja **Examinar**, seleccione **Factorías de datos** y seleccione **LogProcessingFactory** en la hoja **Factorías de datos**.

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Puede supervisar su factoría de datos de varias maneras. Puede iniciar con procesos o conjuntos de datos. Vamos a comenzar con los procesos e iremos profundizando. 
3.	Haga clic en **Procesos** en la hoja **FACTORÍA DE DATOS**. 
4.	Haga clic en **PartitionGameLogsPipeline** en la hoja Procesos. 
5.	En la hoja **PROCESO** para **PartitionGameLogsPipeline**, verá que el proceso consume el conjunto de datos **RawGameEventsTable**.  Haga clic en **RawGameEventsTable**.

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. En la hoja TABLA de **RawGameEventsTable**, verá todos los segmentos. En la siguiente captura de pantalla, todos los segmentos están en estado **Preparado**y no hay segmentos con problemas. Significa que los datos están listos para ser procesados.	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. Ahora, en la hoja **PROCESO** para **PartiionGameLogsPipeline**, haga clic en **Producido**. 
8. Debería ver la lista de conjuntos de datos que genera este proceso: 
9. Haga clic en la tabla **PartitionedGameEvents** en la hoja **Conjuntos de datos producidos**. 
10.	Confirme que el **estado** de todos los segmentos se establece en **Listo**. 
11.	Haga clic en uno de los segmentos que está **Listo** para ver la hoja **SEGMENTO DE DATOS**para ese segmento.

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si se produjo un error, verá un estado **Error** aquí.  También puede ver ambos segmentos con el estado **Listo** o ambos con estado **PendingValidation**, dependiendo de la rapidez con que se procesan los segmentos.
 
	Consulte [Referencia del programador de la factoría de datos de Azure][developer-reference] para obtener una descripción de todos los estados posibles de los segmentos.

12.	En la hoja **SEGMENTO DE DATOS**, haga clic en la ejecución de la lista **Ejecuciones de actividades**. Debería ver la hoja Ejecución de actividad para ese segmento. Debería ver la siguiente hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**.

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	Haga clic en **Descargar** para descargar los archivos. Esta pantalla es especialmente útil al solucionar errores de procesamiento de HDInsight. 
	 
	
Cuando se completa la ejecución de todos los procesos, puede buscar en **MarketingCampaignEffectivenessTable** en la base de datos SQL de Azure **MarketingCampaigns** para ver los resultados. 

**¡Enhorabuena!** Ahora puede supervisar y solucionar problemas de los flujos de trabajo. Ha aprendido a utilizar la factoría de datos de Azure para procesar los datos y obtener análisis.

	 

## Tutorial: Uso de datos locales

**(recomendado)** Revise y practique el tutorial en el artículo [Habilitar el proceso para trabajar con datos locales][useonpremisesdatasources] para obtener un tutorial sobre cómo crear un proceso para mover datos de un SQL Server local a un almacén de blobs de Azure.


En este tutorial, aprenderá a configurar el entorno para habilitar el proceso y trabajar con sus datos locales.
 
En el último paso del escenario de procesamiento de registro desde el primer tutorial con Partición -> Enriquecer -> Analizar flujo de trabajo, la salida de la eficacia de la campaña de marketing se ha copiado en una base de datos SQL de Azure. También puede mover estos datos a un SQL Server local para realizar análisis dentro de su organización.
 
Para copiar los datos de eficacia de la campaña de marketing de blobs de Azure a un SQL Server local, deberá crear servicios vinculados locales adicionales, una tabla y un proceso con el mismo conjunto de cmdlets que se introdujo en el primer tutorial.

En este tutorial, realizará los siguientes pasos: 

1. [Paso 1: Cree una puerta de enlace de administración de datos](#OnPremStep1). La puerta de enlace de administración de datos  es un agente cliente que proporciona acceso a orígenes de datos locales de la organización desde la nube. La puerta de enlace permite transferir datos entre un SQL Server local y almacenes de datos de Azure.	

	Debe tener al menos una puerta de enlace instalada en su entorno corporativo, así como registrarla con la factoría de datos de Azure antes de agregar la base de datos de SQL Server local como un servicio vinculado a una factoría de datos de Azure.

2. [Paso 2: Cree un servicio vinculado para el SQL Server local](#OnPremStep2). En este paso, cree primero una base de datos y una tabla en el equipo de SQL Server local y, a continuación, cree el servicio vinculado: **OnPremSqlLinkedService**.  
3. [Paso 3: Cree la tabla y el proceso](#OnPremStep3). En este paso, creará una tabla **MarketingCampaignEffectivenessOnPremSQLTable** y el proceso **EgressDataToOnPremPipeline**. 

4. [Paso 4: Supervise el proceso y vea el resultado](#OnPremStep4). En este paso, supervisará los procesos, las tablas y los segmentos de datos mediante el Portal de Azure.


### <a name="OnPremStep1"></a>Paso 1: Cree una puerta de enlace de administración de datos

La puerta de enlace de administración de datos es un agente cliente que proporciona acceso a orígenes de datos locales de la organización desde la nube. La puerta de enlace permite transferir datos entre un SQL Server local y almacenes de datos de Azure.
  
Debe tener al menos una puerta de enlace instalada en su entorno corporativo, así como registrarla con la factoría de datos de Azure antes de agregar la base de datos de SQL Server local como un servicio vinculado a una factoría de datos de Azure.

Si tiene una puerta de enlace de datos existente que puede utilizar, omita este paso.

1.	Cree una puerta de enlace de datos lógica. En el **Portal de vista previa de Azure**, haga clic en **Servicios vinculados** en la hoja **FACTORÍA DE DATOS**.
2.	Haga clic en **Agregar (+) puerta de enlace de datos** en la barra de comandos.  
3.	En la hoja **Nueva puerta de enlace de datos**, haga clic en **CREAR**.
4.	En la hoja **Crear**, escriba **MyGateway** como **nombre** de la puerta de enlace de datos.
5.	Haga clic en **ELEGIR UNA REGIÓN** y cámbiela si es necesario. 
6.	Haga clic en **Aceptar** en la hoja **Crear**. 
7.	Aparecerá la hoja**Configurar**. 
8.	En la hoja **Configurar**, haga clic en **Instalar directamente en este equipo**. Esto se descarga, instala y configura la puerta de enlace en el equipo y registra la puerta de enlace con el servicio. Si tiene una puerta de enlace existente instalada en el equipo que desea vincular a esta puerta de enlace lógica en el portal, utilice la clave de esta hoja para volver a registrar la puerta de enlace mediante la herramienta Administrador de configuración de puerta de enlace de administración de datos (vista previa).

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Haga clic en **Aceptar** para cerrar la hoja **Configurar** y **Aceptar** para cerrar la hoja **Crear**. Espere hasta que el estado de **MyGateway** en la hoja **Servicios vinculados** cambie a **BIEN**. También puede iniciar la herramienta **Administrador de configuración de puerta de enlace de administración de datos (vista previa)** para confirmar que el nombre de la puerta de enlace coincide con el nombre en el portal y el **estado** es **Registrado**. Puede ser necesario cerrar y volver a abrir la hoja Servicios vinculados para ver el estado más reciente. La pantalla puede tardar unos minutos antes de actualizarse con el estado más reciente.	

### <a name="OnPremStep2"></a>Paso 2: Cree un servicio vinculado para el SQL Server local

En este paso, cree primero la base de datos necesaria y una tabla en el equipo de SQL Server local y, a continuación, cree el servicio vinculado.

#### Preparación de la tabla y la base de datos local

Para empezar, deberá crear la base de datos de SQL Server, tabla, tipos definidos por el usuario y procedimientos almacenados. Se utilizarán para pasar los resultados de **MarketingCampaignEffectiveness** del blob de Azure a la base de datos de SQL Server.

1.	En el **Explorador de Windows**, vaya a la subcarpeta **OnPremises** en **C:\ADFWalkthrough** (o la ubicación donde extrajo los ejemplos).
2.	Abra **prepareOnPremDatabase&Table.ps1** en su editor favorito, reemplace el texto resaltado con su información de SQL Server y guarde el archivo (proporcione los detalles de la **autenticación de SQL**). Para el tutorial, habilite la autenticación de SQL para la base de datos. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. En **Azure PowerShell**, vaya la carpeta **C:\ADFWalkthrough\OnPremises**.
4.	Ejecute **prepareOnPremDatabase&Table.ps1** **(& entre dobles comillas o como se muestra a continuación)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### Creación del servicio vinculado

1.	En el **portal de vista previa de Azure**, haga clic en el mosaico **Servicios vinculados** en la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**.
2.	En la hoja **Servicios vinculados**, haga clic en **Agregar (+) almacén de datos**.
3.	En la hoja **Nuevo almacén de datos**, escriba **OnPremSqlLinkedService** como **Nombre**. 
4.	Haga clic en **Tipo (configuración necesaria)** y seleccione **SQL Server**. Debería ver las opciones **PUERTA DE ENLACE DE DATOS**, **Servidor**, **Base de datos** y **CREDENCIALES** en la hoja **Nuevo almacén de datos**. 
5.	Haga clic en **PUERTA DE ENLACE DE DATOS (configure las opciones necesarias)** y seleccione el **MyGateway** que ha creado antes. 
6.	Escriba el **nombre** del servidor de base de datos que aloja la base de datos **MarketingCampaigns**. 
7.	Escriba **MarketingCampaigns** para la base de datos. 
8.	Haga clic en **CREDENCIALES**. 
9.	En la hoja **Credenciales**, haga clic en **Haga clic aquí para establecer las credenciales de forma segura**.
10.	Instala una aplicación de un clic por primera vez e inicia el cuadro de diálogo **Definición de credenciales**. 
11.	En el cuadro de diálogo **Definición de credenciales**, escriba **Nombre de usuario** y **Contraseña**, y haga clic en **Aceptar**. Espere hasta que se cierre el cuadro de diálogo. 
12.	Haga clic en **Aceptar** en la hoja **Nuevo almacén de datos**. 
13.	En la hoja **Servicios vinculados**, confirme que **OnPremSqlLinkedService** aparece en la lista y el **estado** del servicio vinculado es **Bien**.

### <a name="OnPremStep3"></a>Paso 3: Cree la tabla y el proceso

#### Creación de la tabla lógica local

1.	En **Azure PowerShell**, cambie a la carpeta **C:\ADFWalkthrough\OnPremises**. 
2.	Use el cmdlet **New-AzureDataFactoryTable** para crear las tablas como sigue para **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Creación del proceso para copiar los datos del blob de Azure a SQL Server

1.	Use el cmdlet **New-AzureDataFactoryPipeline** para crear el proceso como sigue para **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. Use el cmdlet **Set-AzureDataFactoryPipelineActivePeriod** para especificar el período activo para **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	Presione **'Y'** para continuar.
	
### <a name="OnPremStep4"></a>Paso 4: Supervise el proceso y vea el resultado

Ahora puede usar los mismos pasos realizados en el [paso 6: Supervisión de tablas y procesos](#MainStep6)  para supervisar el nuevo proceso y los segmentos de datos para la nueva tabla ADF local.
 
Cuando vea que el estado de un segmento de la tabla **MarketingCampaignEffectivenessOnPremSQLTable** pasa a Listo, significa que el proceso ha completado la ejecución del segmento. Para ver los resultados, consulte la tabla **MarketingCampaignEffectiveness** en la base de datos **MarketingCampaigns** en su SQL Server.
 
¡Enhorabuena! Ha realizado correctamente el tutorial para usar los datos locales. 
 
## Otras referencias

Artículo | descripción
------ | ---------------
[Habilitación del proceso para trabajar con datos locales][useonpremisesdatasources] |  Este artículo tiene un tutorial que muestra cómo copiar datos desde una base de datos de SQL Server local a un blob de Azure.
[Uso de Pig y Hive con la factoría de datos][usepigandhive] | Este artículo tiene un tutorial que muestra cómo utilizar HDInsight Activity para ejecutar un script de hive/pig para procesar los datos de entrada para generar datos de salida. 
[Uso de actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones paso a paso para crear una actividad personalizada y utilizarla en un proceso. 
[Supervisión y administración de la factoría de datos de Azure con PowerShell][monitor-manage-using-powershell] | Este artículo describe cómo supervisar una factoría de datos de Azure mediante cmdlets de Azure PowerShell. Puede probar los ejemplos del artículo en ADFTutorialDataFactory.
[Solución de problemas de la factoría de datos][troubleshoot] | Este artículo describe cómo solucionar problemas de la factoría de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminación de la tabla en la Base de datos SQL de Azure). 
[Referencia del programador de la factoría de datos de Azure][developer-reference] | La Referencia del programador tiene un completo contenido de referencia para cmdlets, scripts JSON, funciones, etc...
[Referencia de Cmdlet de la factoría de datos de Azure][cmdlet-reference] | Esta referencia tiene información detallada acerca de todos los cmdlets de la factoría de datos. 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/es-es/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


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

<!--HONumber=46--> 
