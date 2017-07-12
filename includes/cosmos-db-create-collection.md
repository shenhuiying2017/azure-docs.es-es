Ahora puede usar el Explorador de datos para crear una colección y agregar datos a la base de datos. 

1. En Azure Portal, en el menú de navegación izquierdo, haga clic en **Explorador de datos (versión preliminar)**. 

2. En la hoja **Explorador de datos (versión preliminar)**, haga clic en **Nueva colección** y luego proporcione la siguiente información:

    ![Hoja Explorador de datos en Azure Portal](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Id. de base de datos|Tareas|Identificador de la nueva base de datos. Los nombres de base de datos tiene que tener entre 1 y 255 caracteres y no pueden contener /, \\; #, ?, o un espacio al final.
    Id. de colección|Elementos|Identificador de la nueva colección. Los nombres de colección tienen los mismos requisitos de caracteres que los identificadores de base de datos.
    Capacidad de almacenamiento| Fija (10 GB)|Use el valor predeterminado. Se trata de la capacidad de almacenamiento de la base de datos.
    Rendimiento|400 RU|Use el valor predeterminado. Si quiere reducir la latencia, puede escalar verticalmente el rendimiento más adelante.
    RU/m|Off|Deje el valor predeterminado. Si tiene que controlar grandes cargas de trabajo más adelante, puede activar la característica [RU/m](../articles/cosmos-db/request-units-per-minute.md) en ese momento.
    Clave de partición|/categoría|Una clave de partición que distribuye los datos uniformemente a cada partición. La selección de la clave de partición correcta es importante al crear una colección de rendimiento. Para más información, consulte [Diseño de la creación de particiones](../articles/cosmos-db/partition-data.md#designing-for-partitioning).    
3. Cuando haya completado el formulario, haga clic en **Aceptar**.