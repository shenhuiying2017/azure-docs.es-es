

## Creación de un nuevo usuario de base de datos con SSMS

Utilice los pasos siguientes para crear un nuevo usuario en una base de datos existente con SSMS.

Estos pasos se basan en el supuesto de que está conectado a Base de datos SQL en el Explorador de objetos mediante SSMS y que está conectado al servidor lógico de Base de datos SQL como un administrador de entidad de seguridad de nivel de servidor o con una cuenta de usuario con permisos para crear un nuevo usuario.

1. En el Explorador de objetos, expanda el nodo Bases de datos y seleccione la base de datos en el que quiere crear una nueva cuenta de usuario.

     ![SQL Server Management Studio: conexión a un servidor de Base de datos SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Haga clic con el botón derecho en la base de datos seleccionada y luego haga clic en **Consulta**.

     ![SQL Server Management Studio: conexión a un servidor de Base de datos SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. En la ventana de consulta, edite y utilice la siguiente instrucción Transact-SQL para crear un usuario contenido en la base de datos de usuario.

    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';
    ```

     ![SQL Server Management Studio: conexión a un servidor de Base de datos SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)

<!---HONumber=AcomDC_0629_2016-->