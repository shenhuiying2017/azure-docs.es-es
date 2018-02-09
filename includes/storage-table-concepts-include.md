## <a name="what-is-table-storage"></a>¿Qué es Table Storage?
Azure Table Storage permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales. Table Storage suele usarse para realizar las siguientes tareas:

* Almacenamiento de TB de datos estructurados capaces de ofrecer servicio a aplicaciones de escalado web
* Almacenamiento de conjuntos de datos que no requieren uniones complejas, claves externas o procedimientos almacenados y que pueden desnormalizarse para obtener un acceso rápido
* Consulta rápida de datos mediante un índice agrupado
* Acceso a datos mediante el protocolo OData y las consultas LINQ con la bibliotecas .NET del servicio de datos de WCF

Puede usar Table Storage para almacenar grandes conjuntos de datos estructurados no relacionales y realizar consultas sobre ellos, y las tablas se escalarán a medida que aumente la demanda.

## <a name="table-storage-concepts"></a>Descripción de Table Storage
Table Storage contiene los siguientes componentes:

![Diagrama de componentes de Table Storage][Table1]

* **Formato de la dirección URL:** las cuentas de Azure Table Storage utilizan este formato: `http://<storage account>.table.core.windows.net/<table>`

  Las cuentas de Table API de Azure Cosmos DB utilizan este formato:`http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Puede desviar las tablas de Azure directamente mediante esta dirección con el protocolo OData. Para más información, consulte [OData.org][OData.org].
* **Cuentas**: todo el acceso a Azure Storage se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../articles/storage/common/storage-scalability-targets.md) para obtener información sobre la capacidad de la cuenta de almacenamiento. 

    Todo el acceso a Azure Cosmos DB se realiza a través de una cuenta de Table API. Consulte [Creación de una cuenta de Table API](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) para obtener información sobre cómo crear una cuenta de Table API.
* **Tabla**: una tabla es una colección de entidades. Las tablas no exigen un esquema sobre entidades, lo que significa que una única tabla puede contener entidades que dispongan de diferentes conjuntos de propiedades.  
* **Entidad**: una entidad es un conjunto de propiedades, similar a una fila de base de datos. Una entidad en Azure Storage puede tener hasta 1 MB. Una entidad en Azure Cosmos DB puede tener hasta 2 MB.
* **Propiedades**: una propiedad es un par nombre-valor. Cada entidad puede incluir hasta 252 propiedades para almacenar datos. Cada entidad dispone también de tres propiedades del sistema que especifican una clave de partición, una clave de fila y una marca de tiempo. Pueden realizarse consultas en las entidades con la misma partición de manera más rápida e insertarse o actualizarse en operaciones atómicas. Una clave de fila de la entidad es el identificador exclusivo en una partición.

Para más información sobre la nomenclatura de propiedades y tablas, consulte [Introducción al modelo de datos del servicio Tabla](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
