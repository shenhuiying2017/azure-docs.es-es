### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una instancia de [Azure SQL Database](../articles/sql-database/sql-database-get-started.md) con su información de conexión, como el nombre del servidor, el nombre de la base de datos y el nombre de usuario y la contraseña. Esta información se incluye en la cadena de conexión de Base de datos SQL:
  
    Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  
    Más información sobre [Azure SQL Database](https://azure.microsoft.com/services/sql-database).

> [!NOTE]
> Cuando se crea una Base de datos SQL de Azure, también puede crear las bases de datos de ejemplo incluidas con SQL. 
> 
> 

Antes de utilizar su instancia de Base de datos SQL de Azure en una aplicación lógica, conéctese a su base de datos SQL. Esto lo puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.  

Para conectarse a su instancia de Base de datos SQL de Azure, siga estos pasos:  

1. Cree una aplicación lógica. En el diseñador de Logic Apps, agregue un desencadenador y luego agregue una acción. Seleccione **Mostrar las API administradas por Microsoft** en la lista desplegable y, luego, escriba "sql" en el cuadro de búsqueda. Seleccione una de las acciones:  
   
    ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)
2. Si no ha creado anteriormente ninguna conexión a Base de datos SQL, se le pedirán los detalles de conexión:  
   
    ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 
3. Escriba los detalles de la base de datos SQL. Aquellas propiedades con un asterisco son obligatorias.
   
   | Propiedad | Detalles |
   | --- | --- |
   | Connect via Gateway (Conectar a través de puerta de enlace) |Deje esta opción desactivada. Se utiliza al conectarse a un servidor SQL Server local. |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Nombre de SQL Server * |Escriba el nombre del servidor, que será similar a *servername.database.windows.net*. El nombre del servidor aparece en las propiedades de la base de datos SQL en el Portal de Azure y también se muestra en la cadena de conexión. |
   | Nombre de la base de datos SQL * |Escriba el nombre que asignó a la base de datos SQL. Este nombre se muestra en las propiedades de SQL Database en la cadena de conexión: Initial Catalog=*nombreDeBaseDeDatosSQL*. |
   | Nombre de usuario * |Escriba el nombre de usuario que creó al crear la base de datos SQL. Este nombre se muestra en las propiedades de la base de datos SQL en el Portal de Azure. |
   | Contraseña * |Escriba la contraseña que creó al crear la base de datos SQL. |
   
    Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y acceder a sus datos SQL. Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:  
   
    ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. Seleccione **Crear**. 
5. Observe que la conexión se ha creado en el portal. Ahora, continúe con el resto de los pasos en la aplicación lógica: 
   
    ![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/table.png)

