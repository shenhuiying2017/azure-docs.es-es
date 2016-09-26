<properties
	pageTitle="Todos los temas del servicio Data Factory | Microsoft Azure"
	description="Tabla de todos los temas el servicio de Azure denominado Data Factory disponibles en http://azure.microsoft.com/documentation/articles/, título y descripción."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>


# Todos los temas del servicio Data Factory de Azure

En este tema se muestran todos los temas que se aplican directamente al servicio **Data Factory** de Azure. Puede buscar en esta página web las palabras clave mediante **Ctrl+F**, para encontrar los temas de interés.




## Nuevo

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 1 | [Compilación de la primera instancia de Data Factory de Azure mediante la API de REST de Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | En este tutorial se crea un ejemplo de canalización de Data Factory de Azure con la API de REST de Data Factory. |
| 2 | [Tutorial: Crear una canalización con la actividad de copia mediante la API de REST](data-factory-copy-activity-tutorial-using-rest-api.md) | En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante Visual Studio. |
| 3 | [Asistente para copia de Data Factory](data-factory-copy-wizard.md) | Obtenga información sobre cómo utilizar el Asistente para copia de Data Factory para copiar datos de orígenes de datos admitidos en receptores. |
| 4 | [Data Management Gateway](data-factory-data-management-gateway.md) | Configuración de una puerta de enlace para mover datos entre una infraestructura local y la nube. Uso de Data Management Gateway en Data Factory de Azure para mover los datos. |
| 5 | [Movimiento de datos desde una base de datos de Cassandra local con Data Factory de Azure](data-factory-onprem-cassandra-connector.md) | Aprenda a mover los datos de una base de datos de Cassandra local con Data Factory de Azure. |
| 6 | [Movimiento de datos de MongoDB mediante Data Factory de Azure](data-factory-on-premises-mongodb-connector.md) | Obtenga información acerca de cómo mover los datos de la base de datos de MongoDB mediante Data Factory de Azure. |
| 7 | [Movimiento de datos de Salesforce mediante el uso de Data Factory de Azure](data-factory-salesforce-connector.md) | Aprenda a mover datos de Salesforce usando Data Factory de Azure. |


## Artículos actualizados

En esta sección se enumeran los artículos que se han actualizado recientemente. Concretamente, aquellos en los que la actualización fuera grande o considerable. Para cada artículo actualizado, se muestra un fragmento del texto agregado. Los artículos se actualizaron dentro de este intervalo de fechas: del **26-07-2016** al **21-08-2016**.

| &nbsp; | Artículo | Texto actualizado, fragmento |
| --: | :-- | :-- |
| 8 | [Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de .NET de la factoría de datos](data-factory-create-data-factories-programmatically.md) | **Iniciar sesión sin el cuadro de diálogo emergente** El código de ejemplo anterior inicia un cuadro de diálogo para que escriba las credenciales de Azure. Si necesita iniciar sesión mediante programación sin utilizar un cuadro de diálogo, consulte Autenticación de una entidad de servicio con Azure Resource Manager (resource-group-authenticate-service-principal.md authenticate-service-principal-with-certificate---powershell). **Ejemplo** Create GetAuthorizationHeaderNoPopup method as shown below: public static string GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings "ActiveDirectoryTenantId" ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings "AdfClientId" , ConfigurationManager.AppSettings "AdfClientSecret" ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings "WindowsManagementUri" , credential).Result; |
| 9 | [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md) | **Formatos de archivo compatibles** La actividad de copia puede copiar los archivos tal y como están entre dos almacenes de datos basados en archivos como Blob de Azure, Sistema de archivos y Sistema de archivos distribuido de Hadoop (HDFS). Para ello, puede omitir la sección de formato (data-factory-create-datasets.md) en las definiciones del conjunto de datos de entrada y salida y los datos se copian eficazmente sin ninguna serialización/deserialización. La actividad de copia también lee y escribe en archivos de formatos especificados: texto, Avro, ORC y JSON. Estos son algunos ejemplos de actividades de actividades de copia que puede llevar a cabo: / Copiar datos en formato de texto (CSV) desde Azure Blob y escribir en Azure SQL / Copiar archivos en formato de texto (CSV) desde el Sistema de archivos local y escribir en Azure Blob en formato de Avro / Copiar datos en Azure SQL Database y escribir en HDFS local en formato ORC ** a. nombre= "global"../a. Movimiento de datos disponible globalmente** El servicio que potencia la Actividad de copia está disponible globalmente en las siguientes regiones y ámbitos geográficos, incluso si Azure Data Factory solo está disponible en las regiones oeste de EE. UU., este de EE. UU. y Europa del Norte.|
| 10 | [Movimiento de datos de un origen de OData mediante Factoría de datos de Azure](data-factory-odata-connector.md) | **Uso de la autenticación de Windows para acceder al origen de OData local** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## Tutoriales

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 11 | [Tutorial: Compilación de la primera canalización para procesar datos mediante el clúster de Hadoop](data-factory-build-your-first-pipeline.md) | En este tutorial de Data Factory de Azure se muestra cómo crear y programar una factoría de datos que procese los datos mediante el script de Hive en un clúster de Hadoop. |
| 12 | [Tutorial: Compilación de la primera Data Factory de Azure con la plantilla de Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) | En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo con la plantilla del Administrador de recursos de Azure. |
| 13 | [Compilación de la primera instancia de Data Factory de Azure mediante el Portal de Azure o el Editor de Data Factory](data-factory-build-your-first-pipeline-using-editor.md) | En este tutorial, se crea una canalización de Data Factory de Azure de ejemplo con el Editor de Data Factory en el Portal de Azure. |
| 14 | [Compilación de la primera Data Factory de Azure mediante Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | En este tutorial, creará un de ejemplo de canalización de Data Factory de Azure mediante Azure PowerShell. |
| 15 | [Compilación de la primera Data Factory de Azure mediante Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | En este tutorial, creará un de ejemplo de canalización de Data Factory de Azure mediante Visual Studio. |
| 16 | [Tutorial: Crear una canalización con la actividad de copia mediante el editor de la factoría de datos](data-factory-copy-activity-tutorial-using-azure-portal.md) | En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante el Editor de Data Factory en el Portal de Azure. |
| 17 | [Tutorial: Crear una canalización con la actividad de copia con Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante Azure PowerShell. |
| 18 | [Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante Visual Studio. |
| 19 | [Copia de datos de Almacenamiento de blobs en Base de datos SQL mediante Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | En este tutorial se muestra cómo usar la actividad de copia en una canalización de Data Factory de Azure para copiar datos de Almacenamiento de blobs en Base de datos SQL de Azure. |
| 20 | | [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) | En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante el Asistente para copia compatible con Data Factory. |



## Movimiento de datos

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 21 | [Movimiento de datos hacia y desde Blob de Azure mediante Factoría de datos de Azure](data-factory-azure-blob-connector.md) | Aprenda a copiar datos de blob en Data Factory de Azure. Use nuestro ejemplo: Copia de datos entre Almacenamiento de blobs de Azure y Base de datos SQL de Azure. |
| 22 | [Movimiento de datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure](data-factory-azure-datalake-connector.md) | Obtenga información sobre cómo mover datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure. |
| 23 | [Movimiento de datos hacia y desde DocumentDB mediante Factoría de datos de Azure](data-factory-azure-documentdb-connector.md) | Obtenga información acerca de cómo mover los datos hacia y desde DocumentDB de Azure mediante Factoría de datos de Azure |
| 24 | [Movimiento de datos hacia y desde Base de datos SQL de Azure mediante Factoría de datos de Azure](data-factory-azure-sql-connector.md) | Obtenga información acerca de cómo mover los datos hacia y desde la base de datos SQL de Azure mediante Factoría de datos de Azure. |
| 25 | [Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure](data-factory-azure-sql-data-warehouse-connector.md) | Obtenga información acerca de cómo mover los datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure |
| 26 | [Movimiento de datos hacia y desde Tabla de Azure mediante Factoría de datos de Azure](data-factory-azure-table-connector.md) | Obtenga información acerca de cómo mover los datos hacia y desde Almacenamiento de tablas de Azure mediante Factoría de datos de Azure. |
| 27 | [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) | Conozca los factores más importantes que afectan al rendimiento del movimiento de datos en Factoría de datos de Azure mediante la actividad de copia. |
| 28 | [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md) | Aprenda sobre el movimiento de datos en las canalizaciones de Data Factory: migración de datos entre almacenes en la nube, entre el entorno local y la nube. Utilice la actividad de copia. |
| 29 | [Notas de la versión de Data Management Gateway](data-factory-gateway-release-notes.md) | Notas de la versión de Data Management Gateway |
| 30 | [Movimiento de datos desde HDFS local mediante Factoría de datos de Azure](data-factory-hdfs-connector.md) | Obtenga información acerca de cómo mover datos desde HDFS local mediante Factoría de datos de Azure |
| 31 | [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) | Obtenga información sobre cómo usar la Aplicación de supervisión y administración para supervisar y administrar factorías de datos y canalizaciones de Azure |
| 32 | [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) | Configuración de una puerta de enlace para mover datos entre una infraestructura local y la nube. Uso de Data Management Gateway en Data Factory de Azure para mover los datos. |
| 33 | [Movimiento de datos de un origen de OData mediante Factoría de datos de Azure](data-factory-odata-connector.md) | Obtenga información sobre cómo mover datos desde orígenes de OData mediante Factoría de datos de Azure. |
| 34 | [Movimiento de datos desde almacenes de datos ODBC mediante Factoría de datos de Azure](data-factory-odbc-connector.md) | Obtenga información sobre cómo mover datos desde almacenes de datos ODBC mediante Factoría de datos de Azure. |
| 35 | [Movimiento de datos de DB2 mediante Factoría de datos de Azure](data-factory-onprem-db2-connector.md) | Obtenga información acerca de cómo mover los datos de la base de datos de DB2 mediante Factoría de datos de Azure |
| 36 | [Movimiento de datos hacia el sistema de archivos local y desde él con Factoría de datos de Azure](data-factory-onprem-file-system-connector.md) | Aprenda a mover datos hacia y desde el sistema de archivos local con Factoría de datos de Azure |
| 37 | [Movimiento de datos de MySQL mediante Factoría de datos de Azure](data-factory-onprem-mysql-connector.md) | Obtenga información acerca de cómo mover los datos de la base de datos de MySQL mediante Factoría de datos de Azure. |
| 38 | [Transferencia de datos de/a Oracle local mediante Data Factory de Azure](data-factory-onprem-oracle-connector.md) | Obtenga información acerca de cómo mover los datos hacia y desde la base de datos de Oracle local mediante Factoría de datos de Azure. |
| 39 | [Movimiento de datos de PostgreSQL mediante Factoría de datos de Azure](data-factory-onprem-postgresql-connector.md) | Obtenga información acerca de cómo mover los datos de la base de datos de PostgreSQL mediante Factoría de datos de Azure. |
| 40 | [Movimiento de datos de Sybase mediante Factoría de datos de Azure](data-factory-onprem-sybase-connector.md) | Obtenga información acerca de cómo mover los datos de la base de datos de Sybase mediante Factoría de datos de Azure. |
| 41 | [Movimiento de datos de Teradata mediante Factoría de datos de Azure](data-factory-onprem-teradata-connector.md) | Obtenga información acerca del conector Teradata para el servicio Factoría de datos que le permite mover datos desde Base de datos Teradata. |
| 42 | [Movimiento de los datos entre entornos locales de SQL Server o en IaaS (máquina virtual de Azure) mediante Factoría de datos de Azure](data-factory-sqlserver-connector.md) | Aprenda a mover los datos hacia y desde una base de datos SQL Server en un entorno local o en una máquina virtual de Azure mediante Factoría de datos de Azure. |
| 43 | [Movimiento de datos de un origen de tabla web mediante Factoría de datos de Azure](data-factory-web-table-connector.md) | Obtenga información sobre cómo mover datos desde una tabla local en una página web mediante Factoría de datos de Azure |



## Transformación de datos

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 44 | [Creación de canalizaciones predictivas con las actividades de Aprendizaje automático de Azure](data-factory-azure-ml-batch-execution-activity.md) | Describe cómo crear canalizaciones predictivas con Factoría de datos de Azure y Aprendizaje automático de Azure |
| 45 | [Servicios vinculados de procesos](data-factory-compute-linked-services.md) | Obtenga información sobre los entornos de procesos que puede usar en las canalizaciones de la Factoría de datos de Azure para transformar y procesar datos. |
| 46 | [Procesamiento de datos a gran escala mediante Data Factory y Lote](data-factory-data-processing-using-batch.md) | Describe cómo procesar grandes cantidades de datos en una canalización de Factoría de datos de Azure mediante la funcionalidad de procesamiento paralelo de Lote de Azure. |
| 47 | [Más información sobre la transformación y el análisis de datos en Data Factory de Azure](data-factory-data-transformation-activities.md) | Obtenga más información sobre la transformación de datos en Data Factory de Azure. Transforme y procese los datos en el clúster de HDInsight de Azure o en un Lote de Azure. |
| 48 | [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md) | Aprenda cómo puede usar la actividad de streaming de Hadoop en una factoría de datos de Azure para ejecutar programas de streaming de Hadoop en un clúster de HDInsight suyo propio o a petición. |
| 49 | [Actividad de Hive](data-factory-hive-activity.md) | Aprenda cómo puede usar la actividad de Hive en Factoría de datos de Azure para ejecutar consultas de Hive en un clúster de HDInsight suyo propio o a petición. |
| 50 | [Invocar programas MapReduce desde la factoría de datos de Azure](data-factory-map-reduce.md) | Obtenga información sobre cómo procesar datos mediante la ejecución de programas MapReduce en un clúster de HDInsight de Azure desde una factoría de datos de Azure. |
| 51 | [Actividad de Pig](data-factory-pig-activity.md) | Aprenda cómo puede usar la actividad de Pig en Factoría de datos de Azure para ejecutar scripts de Pig en un clúster de HDInsight suyo propio o a petición. |
| 52 | [Invocar programas Spark desde Data Factory](data-factory-spark.md) | Obtenga información sobre cómo invocar programas Spark desde Data Factory de Azure mediante la actividad MapReduce. |
| 53 | [Actividad de procedimiento almacenado de SQL Server](data-factory-stored-proc-activity.md) | Sepa cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una Base de datos SQL de Azure o en un Almacenamiento de datos SQL de Azure desde una canalización de Factoría de datos. |
| 54 | [Uso de actividades personalizadas en una canalización de Factoría de datos de Azure](data-factory-use-custom-activities.md) | Obtenga información acerca de cómo crear actividades personalizadas y usarlas en una canalización de la factoría de datos de Azure. |
| 55 | [Ejecución del script de U-SQL en Análisis de Azure Data Lake desde Factoría de datos de Azure](data-factory-usql-activity.md) | Aprenda a procesar datos ejecutando scripts U-SQL en el servicio de proceso de Análisis de Azure Data Lake. |



## Muestras

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 56 | [Factoría de datos de Azure: ejemplos](data-factory-samples.md) | Proporciona detalles sobre ejemplos que se distribuyen con el servicio Factoría de datos de Azure. |



## Casos de uso

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 57 | [Casos prácticos de clientes](data-factory-customer-case-studies.md) | Obtenga información sobre la manera en que algunos de nuestros clientes han venido utilizando la Factoría de datos de Azure. |
| 58 | [Caso de uso - Generación de perfiles de clientes](data-factory-customer-profiling-usecase.md) | Obtenga información sobre cómo se usa Factoría de datos de Azure para crear un flujo de trabajo orientado a datos (canalización) para generar perfiles de clientes de juegos. |
| 59 | [Caso de uso: recomendaciones de productos](data-factory-product-reco-usecase.md) | Obtenga información acerca de un caso de uso que se implementan mediante Factoría de datos de Azure junto con otros servicios. |



## Supervisar y administrar

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 60 | [Supervisión y administración de canalizaciones de la Factoría de datos de Azure](data-factory-monitor-manage-pipelines.md) | Obtenga información sobre el uso del Portal de Azure y Azure PowerShell para supervisar y administrar las factorías de datos y las canalizaciones de Azure que haya creado. |



## SDK

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 61 | [Factoría de datos de Azure: registro de cambios de la API de .NET](data-factory-api-change-log.md) | Describe los cambios importantes, las adiciones de características y las correcciones de errores, entre otros, en una versión específica de la API de .NET para Factoría de datos de Azure. |
| 62 | [Crear, supervisar y administrar factorías de datos de Azure mediante el SDK de .NET de la factoría de datos](data-factory-create-data-factories-programmatically.md) | Conozca cómo puede crear, supervisar y administrar factorías de datos de Azure mediante programación con el SDK de Factoría de datos. |
| 63 | [Referencia para desarrolladores de Factoría de datos de Azure](data-factory-sdks.md) | Obtenga información sobre las distintas formas de crear, supervisar y administrar factorías de datos de Azure. |



## Varios

| &nbsp; | Título | Description |
| --: | :-- | :-- |
| 64 | [Factoría de datos de Azure: preguntas más frecuentes](data-factory-faq.md) | Preguntas más frecuentes acerca de la factoría de datos de Azure. |
| 65 | [Data Factory de Azure: funciones y variables del sistema](data-factory-functions-variables.md) | Proporciona una lista de funciones y variables del sistema de Data Factory de Azure |
| 66 | [Factoría de datos de Azure: reglas de nomenclatura](data-factory-naming-rules.md) | Describe las reglas de nomenclatura para las entidades de Factoría de datos. |
| 67 | [Solución de problemas de la factoría de datos](data-factory-troubleshoot.md) | Obtenga información acerca de la solución de problemas relacionados con la factoría de datos de Azure. |

<!---HONumber=AcomDC_0914_2016-->