

## <a name="grant-new-database-user-dbowner-permissions"></a>Concesión de permisos de db_owner de usuario de base de datos nueva
Siga estos pasos para conceder a usuario de una base de datos existente permisos de db_owner

Estos pasos se basan en el supuesto de que está conectado a Base de datos SQL en el Explorador de objetos en SSMS y que está conectado al servidor lógico de Base de datos SQL como un administrador de entidad de seguridad de nivel de servidor o con una cuenta de usuario con permisos para conceder permisos de usuario. 

1. En el Explorador de objetos, expanda el nodo Bases de datos y seleccione la base de datos con el usuario al que desea conceder permisos de dbo.
   
     ![SQL Server Management Studio: conexión a un servidor de Base de datos SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)
2. Haga clic con el botón derecho en la base de datos seleccionada y luego haga clic en **Consulta**.
   
     ![SQL Server Management Studio: conexión a un servidor de Base de datos SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)
3. En la ventana de consulta, edite y utilice la siguiente instrucción Transact-SQL para conceder permisos de dbo a un usuario específico. 
   
    ```ALTER ROLE db_owner ADD MEMBER user1;
    ```
   
     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

