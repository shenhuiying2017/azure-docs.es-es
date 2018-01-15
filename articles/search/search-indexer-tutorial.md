---
title: "Tutorial para la indexación de bases de datos SQL de Azure en Azure Search | Microsoft Docs"
description: "Rastree Azure SQL Database para extraer los datos que permiten búsqueda y rellenar un índice de Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: bebfdfdf72014019a49a6da0e512e72932b096ac
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="how-to-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Cómo rastrear una base de datos SQL de Azure mediante los indexadores de Azure Search

En este tutorial se muestra cómo configurar un indexador para extraer datos que permiten búsqueda de una base de datos SQL de Azure de ejemplo. [Los indexadores](search-indexer-overview.md) son un componente de Azure Search que rastrean orígenes de datos externos y rellenan un [índice de búsqueda](search-what-is-an-index.md) con contenido. De todos los indexadores, el más usado es el indexador de base de datos SQL de Azure. 

Tener un profundo conocimiento de la configuración del indexador resulta de utilidad porque simplifica la cantidad de código que se tiene que escribir y mantener. En lugar de preparar e insertar un conjunto de datos JSON conforme a un esquema, puede asociar un indexador a un origen de datos, hacer que el indexador extraiga los datos y los inserte en un índice y, luego, de manera opcional, puede ejecutar el indexador según una programación recurrente para seleccionar los cambios en el origen subyacente.

En este tutorial, se usarán las [bibliotecas de cliente de Azure Search .NET](https://aka.ms/search-sdk) y una aplicación de consola de .NET Core para realizar estas tareas:

> [!div class="checklist"]
> * Descargar y configurar la solución
> * Agregar información del servicio de búsqueda a la configuración de la aplicación
> * Preparar un conjunto de datos externo en la base de datos SQL de Azure 
> * Revisar las definiciones de índice e indexador en el código de ejemplo
> * Ejecutar el código de indexador para importar los datos
> * Buscar en el índice
> * Ver la configuración del indexador en el portal

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para una [evaluación gratuita](https://azure.microsoft.com/free/). 

* Un servicio Azure Search. Para configurar uno, consulte [Creación de un servicio de búsqueda](search-create-service-portal.md).

* Una base de datos SQL de Azure que proporcione el origen de datos externo usado por un indexador. La solución de ejemplo proporciona un archivo de datos SQL para crear la tabla.

* Visual Studio 2017. Puede usar la [edición gratuita de Visual Studio 2017 Community](https://www.visualstudio.com/downloads/). 

> [!Note]
> Si va a usar el servicio gratuito de Azure Search, está limitado a tres índices, tres indexadores y tres orígenes de datos. En este tutorial se crea uno de cada uno. Asegúrese de que haya espacio en el servicio para aceptar los nuevos recursos.

## <a name="download-the-solution"></a>Descarga de la solución

La solución de indexador usada en este tutorial es de una colección de ejemplos de Azure Search proporcionados en una única descarga maestra. La solución que se usa en este tutorial es *DotNetHowToIndexers*.

1. Vaya a [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) en el repositorio de GitHub de ejemplos de Azure.

2. Haga clic en **Clone or Download** (Clonar o descargar)  > **Download ZIP** (Descargar ZIP). De forma predeterminada, el archivo se descarga en la carpeta Descargas.

3. En **Explorador de archivos** > **Descargas**, haga clic en el archivo y elija **Extraer todo**.

4. Desactive los permisos de solo lectura. Haga clic con el botón derecho en el nombre de la carpeta > **Propiedades** > **General** y desactive el atributo **Solo lectura** de la carpeta, las subcarpetas y los archivos actuales.

5. En **Visual Studio 2017**, abra la solución *DotNetHowToIndexers.sln*.

6. En el **Explorador de soluciones**, haga clic con el botón derecho en la solución primaria del nodo superior > **Restaurar paquetes Nuget**.

## <a name="set-up-connections"></a>Configuración de las conexiones
La información de conexión para los servicios requeridos se especifica en el archivo **appsettings.json** de la solución. 

En el Explorador de soluciones, abra **appsettings.json** para que pueda rellenar cada valor de configuración, y siga las instrucciones de este tutorial.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Obtención del nombre y la clave de API de administración del servicio de búsqueda

Puede encontrar el punto de conexión y la clave del servicio de búsqueda en el portal. Una clave proporciona acceso a las operaciones de servicio. Las claves de administración permiten acceso de escritura, necesario para crear y eliminar objetos, como índices e indexadores, en el servicio.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y [busque los servicios de búsqueda de la suscripción](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Abra la página de servicio.

3. En la parte superior, busque el nombre del servicio en la página principal. En la siguiente captura de pantalla, es *azs-tutorial*.

   ![Nombre del servicio](./media/search-indexer-tutorial/service-name.png)

4. Cópielo y péguelo como primera entrada en **appsettings.json** en Visual Studio.

  > [!Note]
  > Un nombre de servicio es parte del punto de conexión que incluye search.windows.net. Si tiene curiosidad, puede ver la dirección URL completa en **Información esencial** en la página de información general. La dirección URL es similar a la de este ejemplo: https://your-service-name.search.windows.net

5. En el lado izquierdo, en **Configuración** > **Claves**, copie una de las claves de administración y péguela como segunda entrada en **appsettings.json**. Las claves son cadenas alfanuméricas generadas para el servicio durante el aprovisionamiento y son necesarias para el acceso autorizado a las operaciones de servicio. 

  Después de agregar ambos valores de configuración, el archivo debe tener un aspecto parecido a este ejemplo:

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>Preparación de un origen de datos externo

En este paso, creará un origen de datos externo que se pueda rastrear con un indexador. El archivo de datos en este tutorial es *hotels.sql*, proporcionado en la carpeta de solución \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Azure SQL Database

Puede usar Azure Portal y el archivo *hotels.sql* del ejemplo para crear el conjunto de datos en Azure SQL Database. Azure Search usa conjuntos de filas planas, como los que se generan a partir de una vista o una consulta. El archivo SQL de la solución de ejemplo crea y rellena una sola tabla.

En el ejercicio siguiente se da por supuesto que no hay ningún servidor ni base de datos, y se le indica que cree ambos en el paso 2. Opcionalmente, si tiene un recurso existente, puede agregar en él la tabla de hoteles, a partir del paso 4.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/). 

2. Haga clic en **Nuevo** > **SQL Database** para crear una base de datos, un servidor y un grupo de recursos. Puede usar los valores predeterminados y el plan de tarifa más bajo. Una ventaja de la creación de un servidor es que puede especificar un nombre de usuario y una contraseña de administrador, que son necesarios para crear y cargar las tablas en un paso posterior.

   ![Página Nueva base de datos](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Haga clic en **Crear** para implementar el nuevo servidor y la nueva base de datos. Espere a que se implementen el servidor y la base de datos.

4. Abra la página SQL Database de la nueva base de datos, si aún no está abierta. En el nombre del recurso debe poner *SQL Database* y no *SQL Server*.

  ![Página SQL Database](./media/search-indexer-tutorial/hotels-db.png)

4. En la barra de comandos, haga clic en **Herramientas** > **Editor de consultas**.

5. Haga clic en **Iniciar sesión** y escriba el nombre de usuario y la contraseña del administrador del servidor.

6. Haga clic en **Abrir consulta** y navegue a la ubicación de *hotels.sql*. 

7. Seleccione el archivo y haga clic en **Abrir**. El script debe tener un aspecto similar a la siguiente captura de pantalla:

  ![Script de SQL](./media/search-indexer-tutorial/sql-script.png)

8. Haga clic en **Ejecutar** para ejecutar la consulta. En el panel de resultados, verá un mensaje de consulta correcta, correspondiente a 3 filas.

9. Para devolver un conjunto de filas de esta tabla, puede ejecutar la siguiente consulta como paso de comprobación:

   ```sql
   SELECT HotelId, HotelName, Tags FROM Hotels
   ```
   La consulta prototipo, `SELECT * FROM Hotels`, no funciona en el Editor de consultas. Los datos de ejemplo incluyen coordenadas geográficas en el campo Ubicación, que no se gestionan en el editor por el momento. Para obtener una lista de otras columnas para consultar, puede ejecutar esta instrucción:`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Ahora que tiene un conjunto de datos externo, copie la cadena de conexión de ADO.NET correspondiente a la base de datos. En la página SQL Database de la base de datos, vaya a **Configuración** > **Cadenas de conexión** y copie la cadena de conexión de ADO.NET.
 
  Una cadena de conexión de ADO.NET se parece al ejemplo siguiente, modificada para usar un nombre de base de datos, un nombre de usuario y una contraseña válidos.

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. Pegue la cadena de conexión en "AzureSqlConnectionString" como tercera entrada en el archivo **appsettings.json** en Visual Studio.

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Descripción del código de índice e indexador

Ahora ya se puede compilar y ejecutar el código. Antes de hacerlo, dedique un minuto a estudiar las definiciones de índice e indexador de este ejemplo. El código pertinente aparece en dos archivos:

  + **hotel.cs**, que contiene el esquema que define el índice.
  + **Program.cs**, que contiene las funciones para crear y administrar las estructuras del servicio.

### <a name="in-hotelcs"></a>En hotel.cs

El esquema de índice define la colección de campos, incluidos los atributos que especifican las operaciones permitidas; por ejemplo, si un campo de texto completo permite búsquedas, filtros o clasificación, como se muestra en la siguiente definición de campo para HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Un esquema también puede incluir otros elementos, como los perfiles de puntuación para aumentar una puntuación de búsqueda, analizadores personalizados y otras construcciones. Sin embargo, para nuestros fines, el esquema se define de forma dispersa, estando compuesto solo de los campos encontrados en los conjuntos de datos de ejemplo.

En este tutorial, el indexador extrae los datos de un origen de datos. En la práctica, puede asociar varios indexadores al mismo índice y crear un índice de búsqueda consolidado a partir de varios orígenes de datos e indexadores. Puede usar el mismo par de indexador-índice y variar solo los orígenes de datos, o un índice con varias combinaciones de indexador y origen de datos, según dónde necesite la flexibilidad.

### <a name="in-programcs"></a>En Program.cs

El programa principal incluye funciones para los tres orígenes de datos representativos. Si nos centramos solo en Azure SQL Database, destacan los siguientes objetos:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

En Azure Search, los objetos que puede ver, configurar o eliminar de forma independiente incluyen índices, indexadores y orígenes de datos (*hotels*, *azure-sql-indexer*, *azure-sql*, respectivamente). 

La columna *AzureSqlHighWaterMarkColumnName* merece una mención especial porque proporciona información de detección de cambios, que usa el indexador para determinar si una fila ha cambiado desde la última carga de trabajo de indexación. Solo se admiten [directivas de detección de cambios](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) en los indexadores y varían según el origen de datos. En Azure SQL Database, puede elegir entre dos directivas, según los requisitos de la base de datos.

El código siguiente muestra los métodos de Program.cs usados para crear un origen de datos y un indexador. El código busca y elimina los recursos existentes del mismo nombre, bajo el supuesto de que este programa se podría ejecutar varias veces.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Tenga en cuenta que las llamadas API del indexador son independientes de la plataforma, excepto en el caso de [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), que especifica el tipo de rastreador que se invoca.

## <a name="run-the-indexer"></a>Ejecución del indexador

En este paso, compile y ejecute el programa. 

1. En el Explorador de soluciones, haga clic con el botón derecho en **DotNetHowToIndexers** y seleccione **Compilar**.
2. De nuevo, haga clic con el botón derecho en **DotNetHowToIndexers**, seguido de **Depurar** > **Iniciar nueva instancia**.

El programa se ejecuta en modo de depuración. Una ventana de consola informa del estado de cada operación.

  ![Script de SQL](./media/search-indexer-tutorial/console-output.png)

El código se ejecuta localmente en Visual Studio y se conecta al servicio de búsqueda en Azure, que, a su vez, usa la cadena de conexión para conectarse a Azure SQL Database y recuperar el conjunto de datos. Con estas muchas operaciones, existen varios posibles puntos de error, pero si recibe un error, compruebe primero las siguientes condiciones:

+ La información de conexión del servicio de búsqueda que proporciona está limitada al nombre del servicio en este tutorial. Si escribió la dirección URL completa, las operaciones se detienen durante la creación del índice, con un error de conexión.

+ La información de conexión de la base de datos **appsettings.json**. Debe ser la cadena de conexión de ADO.NET obtenida del portal, modificada para incluir un nombre de usuario y una contraseña que sean válidos para la base de datos. La cuenta de usuario debe tener permiso para recuperar datos.

+ Los límites de recursos. Recuerde que el servicio compartido (gratuito) tiene límites de tres índices, tres indexadores y tres orígenes de datos. Un servicio en el límite máximo no puede crear nuevos objetos.

## <a name="search-the-index"></a>Búsqueda en el índice 

En Azure Portal, en la página Información general del servicio de búsqueda, haga clic en **Explorador de búsqueda** en la parte superior para enviar algunas consultas sobre el nuevo índice.

1. Haga clic en **Cambiar índice** en la parte superior para seleccionar el índice *hotels*.

2. Haga clic en el botón **Buscar** para emitir una búsqueda vacía. 

  Las tres entradas del índice se devuelven como documentos JSON. El Explorador de búsqueda devuelve documentos en JSON para que pueda ver la estructura completa.

3. A continuación, escriba una cadena de búsqueda: `search=river&$count=true`. 

  Esta consulta invoca la búsqueda de texto completo en el término `river`, y el resultado incluye un recuento de los documentos coincidentes. Devolver el número de documentos coincidencias es útil en escenarios de pruebas cuando se tiene un índice grande con miles o millones de documentos. En este caso, solo un documento coincide con la consulta.

4. Por último, escriba una cadena de búsqueda que limite la salida JSON a los campos de interés: `search=river&$count=true&$select=hotelId, baseRate, description`. 

  La respuesta de la consulta se reduce a los campos seleccionados, lo que da lugar a una salida más concisa.

## <a name="view-indexer-configuration"></a>Visualización de la configuración del indexador

Todos los indexadores, incluido el que acaba de crear mediante programación, se muestran en el portal. Puede abrir una definición de indexador y ver su origen de datos, o configurar una programación de actualización para seleccionar las filas nuevas y modificadas.

1. Abra la página Información general del servicio de su servicio de Azure Search.
2. Desplácese hacia abajo hasta encontrar los iconos de **Indexadores** y **Orígenes de datos**.
3. Haga clic en un icono para abrir una lista de cada recurso. Puede seleccionar indexadores u orígenes de datos individuales para ver o modificar la configuración.

  ![Iconos de indexador y origen de datos](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando estos servicios, siga estos pasos para borrar todos los recursos que se crearon en este tutorial en Azure Portal. 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar grupo de recursos**, escriba el nombre del recurso que quiere eliminar en el cuadro de texto y haga clic en **Eliminar**.

## <a name="next-steps"></a>pasos siguientes

Para más información y conocer las tareas que son específicas de otros orígenes de datos admitidos, consulte los siguientes artículos:

* [Azure SQL Database o SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexación de blobs CSV con el indexador de blobs de Azure Search](search-howto-index-csv-blobs.md)
* [Indexación de blobs JSON con el indexador de blobs de Azure Search](search-howto-index-json-blobs.md)

