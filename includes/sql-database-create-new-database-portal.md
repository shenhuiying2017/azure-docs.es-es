
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## Creación de una nueva Base de datos SQL de Azure.

Utilice los pasos siguientes en el Portal de Azure para crear una nueva Base de datos SQL de Azure en un servidor lógico de Base de datos SQL de Azure nuevo o en uno ya existente.

1. Si no está conectado, conéctese al [Portal de Azure](http://portal.azure.com).
2. Haga clic en **Nuevo**, escriba **Base de datos SQL** y haga clic en **Base de datos SQL (nueva base de datos)**.

     ![Nueva base de datos](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Haga clic en Base de datos SQL (nueva base de datos).

     ![Nueva base de datos](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
   
4. Haga clic en **Crear** para crear una nueva base de datos en el servicio de Base de datos SQL.

     ![Nueva base de datos](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Proporcione los valores para las propiedades de servidor siguientes:

 - Nombre de la base de datos
 - Suscripción (solo si tiene varias suscripciones)
 - Grupo de recursos (si está empezando, utilice el grupo de recursos del servidor lógico)
 - Seleccione el origen (puede elegir una base de datos vacía, los datos de ejemplo o una copia de seguridad de una base de datos de Azure. Para migrar una base de datos de SQL Server local o cargar datos mediante BCP, consulte los vínculos al final de este artículo)
 - Servidor (un servidor lógico nuevo o uno ya existente)
 - Contraseña del administrador del servidor.
 - Password
 - Plan de tarifa (si está empezando, utilice el valor predeterminado S0)
 - Intercalación (solo si se ha elegido una base de datos en blanco)

        ![new database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Haga clic en **Crear** y podrá ver en el área de notificación que la implementación se ha iniciado.

     ![Nueva base de datos](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Espere a que la implementación finalice antes de continuar con el paso siguiente.

     ![Nueva base de datos](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)

<!---HONumber=AcomDC_0803_2016-->