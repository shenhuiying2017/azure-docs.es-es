   <properties
   pageTitle="Carga de datos en Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Conozca los escenarios comunes para la carga de datos en Almacenamiento de datos SQL. Estos incluyen el uso de PolyBase, el almacenamiento de blobs de Azure, archivos planos y el envío de discos. También puede usar herramientas de otros fabricantes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/17/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Carga de datos en Almacenamiento de datos SQL de Azure

Resumen de las opciones de escenario y recomendaciones para cargar datos en Almacenamiento de datos SQL.

La parte más difícil de carga de datos suele ser la preparación de los datos para la carga. Azure simplifica la carga mediante el almacenamiento de blobs de Azure como almacén de datos común para muchos de los servicios y usando Data Factory de Azure para organizar la comunicación y el movimiento de datos entre los servicios de Azure. Estos procesos se integran con la tecnología PolyBase, que usa el procesamiento paralelo masivo (MPP) para cargar datos en paralelo desde el almacenamiento de blobs de Azure en Almacenamiento de datos SQL.

Para ver tutoriales que cargan bases de datos de ejemplo, vea [Load sample data into SQL Data Warehouse][] (Carga de datos de ejemplo).

## Carga desde el almacenamiento de blobs de Azure
La manera más rápida de importar datos en Almacenamiento de datos SQL es usar PolyBase para cargar datos desde el almacenamiento de blobs de Azure. PolyBase usa el procesamiento paralelo masivo (MPP) de Almacenamiento de datos SQL para cargar datos en paralelo desde el almacenamiento de blobs de Azure. Para usar PolyBase, puede usar comandos T-SQL o una canalización de Data Factory de Azure.

### 1\. Usar PolyBase y T-SQL

Resumen del proceso de carga:

2. Formatee los datos como UTF-8, ya que actualmente PolyBase no admite UTF-16.
2. Mueva los datos al almacenamiento de blobs de Azure y almacénelos en archivos de texto.
3. Configure objetos externos en Almacenamiento de datos SQL para definir la ubicación y el formato de los datos.
4. Ejecute un comando T-SQL para cargar los datos en paralelo en una nueva tabla de base de datos.

<!-- 5. Schedule and run a loading job. --> 

Para ver un tutorial, consulte [Load data from Azure blob storage to SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]][].

### 2\. Usar Data Factory de Azure

Para usar PolyBase de manera más sencilla, puede crear una canalización de Data Factory de Azure que use PolyBase para cargar datos del almacenamiento de blobs de Azure en Almacenamiento de datos SQL. Esto se configura rápido porque no es necesario definir los objetos T-SQL. Si necesita consultar los datos externos sin importarlos, use T-SQL.

Resumen del proceso de carga:

2. Formatee los datos como UTF-8, ya que actualmente PolyBase no admite UTF-16.
2. Mueva los datos al almacenamiento de blobs de Azure y almacénelos en archivos de texto.
3. Cree una canalización de Data Factory de Azure para introducir los datos. Use la opción PolyBase.
4. Programe y ejecute la canalización.

Para ver un tutorial, consulte [Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (Data Factory de Azure)]][].


## Carga desde SQL Server
Para cargar datos desde SQL Server en Almacenamiento de datos SQL, puede usar Integration Services (SSIS), transferir archivos planos o enviar discos a Microsoft. Siga leyendo para ver un resumen de los distintos procesos de carga y los vínculos a tutoriales.

Para planear una migración de datos completa de SQL Server a Almacenamiento de datos SQL, consulte la [información general sobre migración][].

### Uso de Integration Services (SSIS)
Si ya usa paquetes de Integration Services (SSIS) para cargarlos en SQL Server, puede actualizarlos para usar SQL Server como origen y Almacenamiento de datos SQL como destino. Esto es rápido y fácil de hacer, y es una buena opción si no desea migrar el proceso de carga para usar datos que ya están en la nube. La desventaja es que la carga será más lenta que con PolyBase porque este SSIS no realiza la carga en paralelo.

Resumen del proceso de carga:

1. Revisar el paquete de Integration Services para que apunte a la instancia de SQL Server para el origen y a la base de datos de Almacenamiento de datos SQL para el destino.
2. Migre el esquema a Almacenamiento de datos SQL si aún no está allí.
3. Cambie la asignación en los paquetes para usar solamente los tipos de datos que admite Almacenamiento de datos SQL.
3. Programe y ejecute el paquete.

Para ver un tutorial, consulte [Load data from SQL Server to Azure SQL Data Warehouse (SSIS) [Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (SSIS)]][].

### Uso de AZCopy (recomendado para datos de menos de 10 TB)
Si el tamaño de los datos es menor que 10 TB, puede exportar los datos de SQL Server a archivos planos, copiar los archivos al almacenamiento de blobs de Azure y luego usar PolyBase para cargar los datos en Almacenamiento de datos SQL.

Resumen del proceso de carga:

1. Use la utilidad de línea de comandos bcp para exportar datos de SQL Server a archivos planos.
2. Usar la utilidad de línea de comandos AZCopy para copiar datos de archivos planos en el almacenamiento de blobs de Azure.
3. Use PolyBase para cargar datos en Almacenamiento de datos SQL

Para ver un tutorial, consulte [Load data from Azure blob storage to SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]][].

### Uso de bcp
Si tiene una pequeña cantidad de datos, puede usar bcp para cargar directamente en Almacenamiento de datos SQL de Azure.

Resumen del proceso de carga:
1. Use la utilidad de línea de comandos bcp para exportar datos de SQL Server a archivos planos.
2. Use bcp para cargar datos de archivos planos directamente en Almacenamiento de datos SQL.

Para ver un tutorial, consulte [Load data from SQL Server to Azure SQL Data Warehouse (bcp) [Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (bcp)]][].


### Uso de importación/exportación (recomendado para datos de menos de 10 TB)
Si el tamaño de los datos es menor que 10 TB y quiere moverlos a Azure, se recomienda que use nuestro servicio de envío de discos [Importación y exportación][].

Resumen del proceso de carga
2. Use la utilidad de línea de comandos bcp para exportar datos de SQL Server a archivos planos o discos transferibles.
3. Envíe los discos a Microsoft.
4. Microsoft carga los datos en Almacenamiento de datos SQL


## Recomendaciones

Muchos de nuestros asociados tienen soluciones de carga. Para más información, consulte una lista de nuestros [asociados de soluciones][].


Si los datos provienen de un origen no relacional y quiere cargarlos en Almacenamiento de datos SQL,debe transformarlos en filas y columnas antes de cargarlos. Los datos transformados no necesitan almacenarse en una base de datos; se puede almacenar en archivos de texto.

Cree estadísticas de los datos recién cargados. Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Para obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Para más información, consulte las [estadísticas][].


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]]: sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (Data Factory de Azure)]]: sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS) [Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (SSIS)]]: sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp) [Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (bcp)]]: sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[información general sobre migración]: sql-data-warehouse-overview-migrate.md
[asociados de soluciones]: sql-data-warehouse-integrate-solution-partners.md
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md
[estadísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importación y exportación]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

<!---HONumber=AcomDC_0615_2016-->