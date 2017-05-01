### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Creación de un extremo TCP para la máquina virtual
Para poder acceder a SQL Server desde Internet, la máquina virtual debe tener un extremo para escuchar la comunicación TCP de entrada. Este paso de la configuración de Azure dirige el tráfico del puerto TCP de entrada a un puerto TCP al que puede tener acceso la máquina virtual.

> [!NOTE]
> Si se va a conectar en el mismo servicio en la nube o red virtual, no es necesario crear un extremo accesible públicamente. En ese caso, puede continuar con el paso siguiente. Para obtener más información, consulte: [Escenarios de conexión](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. En el Portal de Azure, seleccione **Máquinas virtuales (clásico)**.
2. Luego seleccione la máquina virtual de SQL Server.
3. Seleccione **Puntos de conexión** y, después, haga clic en el botón **Agregar** de la parte superior de la hoja Puntos de conexión.
   
    ![Pasos que se deben seguir en el portal para la creación del punto de conexión](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. En la hoja **Agregar punto de conexión**, especifique un **nombre** como SQLEndpoint.
5. Seleccione **TCP** en **Protocolo**.
6. En **Puerto público**, especifique un número de puerto como **57500**.
7. En **Puerto privado**, especifique el puerto de escucha de SQL Server, cuyo valor predeterminado es **1433**.
8. Haga clic en **Aceptar** para crear el punto de conexión.

