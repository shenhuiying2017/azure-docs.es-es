Ahora puede usar la herramienta Explorador de datos en Azure Portal para crear una base de datos de grafos. 

1. En Azure Portal, en el menú de la izquierda, haga clic en **Explorador de datos (versión preliminar)**.

2. En **Explorador de datos (versión preliminar)**, seleccione **Nuevo grafo**. A continuación, rellene la página con la siguiente información:

    ![Explorador de datos en Azure Portal](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Configuración|Valor sugerido|DESCRIPCIÓN
    ---|---|---
    Id. de base de datos|sample-database|Escriba *sample-database* como nombre de la nueva base de datos. Los nombres de bases de datos deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final.
    Graph id (Id. de grafo)|sample-graph|Escriba *sample-graph* como nombre de la nueva colección. Los nombres de grafo tienen los mismos requisitos de caracteres que los identificadores de base de datos.
    Capacidad de almacenamiento| 10 GB|Deje el valor predeterminado. Se trata de la capacidad de almacenamiento de la base de datos.
    Rendimiento|400 RU|Deje el valor predeterminado. Se puede escalar el rendimiento más adelante si quiere reducir la latencia.

3. Una vez que haya rellenado el formulario, seleccione **Aceptar**.
