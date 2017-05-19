Ahora puede usar el Explorador de datos para crear una colección y agregar datos a la base de datos. 

1. En Azure Portal, en el menú de navegación, haga clic en **Explorador de datos**. 
2. En la hoja Explorador de datos, haga clic en **Nueva colección** y rellene la página con la información siguiente.

    ![Explorador de datos en Azure Portal](./media/cosmosdb-create-collection/azure-cosmosdb-data-explorer.png)

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Id. de base de datos|Elementos|Identificador de la nueva base de datos. Los nombres de bases de datos deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final.
    Id. de colección|ToDoList|Identificador de la nueva colección. Los nombres de colección tienen los mismos requisitos de caracteres que los identificadores de base de datos.
    Capacidad de almacenamiento| Fija (10 GB)|Deje el valor predeterminado. Se trata de la capacidad de almacenamiento de la base de datos.
    Rendimiento|400 RU|Deje el valor predeterminado. Se puede escalar el rendimiento más adelante si quiere reducir la latencia.
    Clave de partición|/userid|Clave de partición que distribuirá los datos uniformemente a cada partición. La selección de la clave de partición correcta es importante al crear una colección de rendimiento. Obtenga más información al respecto en [Designing for partitioning](../articles/cosmos-db/partition-data.md#designing-for-partitioning) (Diseño de la creación de particiones).    



3. Una vez que haya rellenado el formulario, haga clic en **Aceptar**.