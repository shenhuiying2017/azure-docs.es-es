### Determinación del nombre DNS de la máquina virtual

Para conectarse al motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del Sistema de nombres de dominio (DNS) de la máquina virtual. (Este es el nombre que Internet utiliza para identificar la máquina virtual. Puede utilizar la dirección IP, pero esta podría cambiar cuando Azure mueva recursos por redundancia o mantenimiento. El nombre DNS será estable, porque se puede redirigir a una dirección IP nueva).

1. En el Portal de Azure (o desde el paso anterior), seleccione **Máquinas virtuales (clásico)**.

2. Seleccione la máquina virtual de SQL.

2. En la hoja **Máquina virtual**, copie el **nombre DNS** de la máquina virtual.

	![Nombre DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)


### Conexión al motor de base de datos desde otro equipo

1. En un equipo conectado a Internet, abra SQL Server Management Studio.

2. En el cuadro de diálogo **Conectar al servidor ** o **Conectarse al motor de base de datos**, en el cuadro **Nombre del servidor**, escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un número de puerto de punto de conexión público con formato **NombreDNS,nombredepuerto** como, por ejemplo, *mysqlvm.cloudapp.net,57500*.

	![Conectar mediante SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

	Si no recuerda el número de puerto de extremo público que creó anteriormente, puede encontrarlo en el área **Puntos de conexión** de la hoja **Máquina virtual**.

	![Public Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)

3. En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.

5. En el cuadro **Inicio de sesión**, escriba el nombre de un inicio de sesión que haya creado en una tarea anterior.

6. En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión que creó en una tarea anterior.

7. Haga clic en **Conectar**.

<!---HONumber=AcomDC_0629_2016-->