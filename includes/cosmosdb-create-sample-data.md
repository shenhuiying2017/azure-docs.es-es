Ahora puede agregar datos a la nueva colección mediante el Explorador de datos.

1. En el Explorador de datos, la nueva base de datos aparece en el panel Colecciones. Expanda la base de datos **Elementos**, expanda la colección **ToDoList**, haga clic en **Documentos** y, después, haga clic en **Nuevos documentos**. 

   ![Crear documentos en el Explorador de datos en Azure Portal](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Ahora agregue algunos documentos nuevos a la colección con la estructura siguiente, en la que debe insertar valores únicos para la identificación de cada documento y puede cambiar las demás propiedades como considere oportuno. Los nuevos documentos pueden tener la estructura que quiera, ya que Azure Cosmos DB no impone ningún esquema en los datos.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Ahora puede usar consultas en el Explorador de datos para recuperar los datos. De forma predeterminada, el Explorador de datos usa SELECT * FROM c para recuperar todos los documentos de la colección, pero puede cambiar este valor a SELECT * FROM c ORDER BY c.name ASC para devolver todos los documentos en orden alfabético ascendente en función de la propiedad name. 
 
     También puede usar el Explorador de datos para crear procedimientos almacenados, UDF y desencadenadores para realizar la lógica de negocios del servidor. El Explorador de datos expone todo el acceso a datos mediante programación integrado que está disponible en las API, pero permite un acceso fácil a los datos de Azure Portal.