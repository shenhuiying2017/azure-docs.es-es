### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Creación un nuevo servidor SQL lógico en Azure Portal

1. Haga clic en **Nuevo**, busque **servidor lógico**y, a continuación, pulse **ENTRAR**.

    ![buscar servidor lógico](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. Seleccione **SQL Server (servidor lógico)** 

    ![seleccionar servidor lógico](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. Haga clic en **Crear** para abrir la nueva hoja SQL Server (servidor lógico).

   <kbd>![abrir la hoja de servidor lógico](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd> ![hoja de servidor lógico](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png)</kbd>
  
3. En la hoja SQL Server (servidor lógico), en el cuadro de texto Nombre de servidor, proporcione un nombre válido para el nuevo servidor lógico. Una marca de verificación verde indica que ha proporcionado un nombre válido.
    
    ![nuevo nombre de servidor](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > El nombre completo del nuevo servidor será <nombreDeSuServidor >.database.windows.net.
    >
    
4. En el cuadro de texto de inicio de sesión del administrador del servidor, proporcione un nombre de usuario para el inicio de sesión de autenticación de SQL para este servidor. Este inicio de sesión se conoce como el inicio de sesión de la entidad de seguridad del servidor. Una marca de verificación verde indica que ha proporcionado un nombre válido.
    
    ![inicio de sesión de administrador de SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. En los cuadros de texto **Contraseña** y **Confirmar contraseña**, escriba una contraseña para la cuenta de inicio de sesión de la entidad de seguridad del servidor. Una marca de verificación verde indica que ha proporcionado una contraseña válida.
    
    ![contraseña del administrador de SQL](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. Seleccione una suscripción en la que tenga permiso para crear objetos.

    ![suscripción](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. En el cuadro de texto Grupo de recursos, seleccione **Crear nuevo** y, en el cuadro de texto del grupo de recursos, proporcione un nombre válido para el nuevo grupo de recursos (también puede usar un grupo de recursos existente si ya ha creado uno para sí mismo). Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![nuevo grupo de recursos](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. En el cuadro de texto **Ubicación**, seleccione un centro de datos adecuado para su ubicación; por ejemplo, "Este de Australia".
    
    ![ubicación del servidor](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > La casilla de verificación **Permitir que los servicios de Azure accedan al servidor** no se puede cambiar en esta hoja. Puede cambiar esta configuración en la hoja del firewall del servidor. Para más información, consulte [Get started with security](../articles/sql-database/sql-database-manage-servers-portal.md) (Introducción a la seguridad).
    >
    
9. Haga clic en **Crear**.

    ![botón Crear](./media/sql-data-warehouse-create-logical-server/create.png)

