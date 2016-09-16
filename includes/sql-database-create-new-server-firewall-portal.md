
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-08-01 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## Creación de un nuevo firewall de nivel de servidor SQL de Azure

Utilice los pasos siguientes en el portal de Azure para crear una regla de firewall de nivel de servidor que permita conexiones desde una dirección IP individual (el equipo cliente) o un intervalo completo de direcciones IP a un servidor lógico de Base de datos SQL.

1. Si no está conectado, conéctese a [Azure Portal](http://portal.azure.com).
2. En la hoja predeterminada, haga clic en **Servidores SQL Server**.

  	![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

3. En la hoja **SQL Server**, haga clic en el servidor en el que va a crear la regla de firewall.

 	![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)

4. Revise las propiedades del servidor.

 	![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)

5. En la hoja **Configuración**, haga clic en **Firewall**.

 	![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)

 	> [AZURE.NOTE] También puede acceder a la hoja **Configuración de firewall** en el nivel de servidor desde la barra de herramientas de la hoja **Base de datos**.

6. Haga clic en **Agregar IP de cliente** para que Azure cree una regla para la dirección IP del cliente.

      ![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

7. Opcionalmente, haga clic en la dirección IP que se agregó para editar la dirección del firewall y permitir el acceso a un intervalo de direcciones IP.

      ![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)

8. Haga clic en **Guardar** para crear la regla de firewall en el nivel de servidor.

     ![Nuevo firewall de servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

	>[AZURE.IMPORTANT] Puede que la dirección IP de su cliente cambie de vez en cuando, y es posible que no pueda acceder al servidor hasta que cree una nueva regla de firewall. Puede comprobar la dirección IP mediante [Bing](http://www.bing.com/search?q=my%20ip%20address). A continuación, agregue una única dirección IP o un intervalo de direcciones IP. Consulte [Administración de la configuración de firewall](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal) para más información.

<!---HONumber=AcomDC_0912_2016-->