#### <a name="to-install-mpio-on-the-host"></a>Para instalar MPIO en el host
1. Abra el Administrador del servidor en el host de Windows Server. El Administrador del servidor se inicia de forma predeterminada cuando un miembro del grupo Administradores inicia sesión en un equipo que ejecuta Windows Server 2012 R2 o Windows Server 2012. Si el Administrador del servidor no está abierto, haga clic en **Inicio > Administrador del servidor**.
   
    ![Administrador del servidor](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Haga clic en **Administrador del servidor > Panel > Agregar roles y características**. De esta forma, se abre el **Asistente para agregar roles y características** .
   
    ![Asistente para agregar roles y características 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. En el **Asistente para agregar roles y características** , haga lo siguiente:
   
   * En la página **Antes de comenzar**, haga clic en **Siguiente**.
   * En la página **Seleccionar tipo de instalación**, acepte la configuración predeterminada de **Instalación basada en características o en roles**. Haga clic en **Siguiente**.
     
       ![Asistente para agregar roles y características 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * En la página **Seleccionar servidor de destino** y elija **Seleccionar un servidor del grupo de servidores**. El servidor host debería detectarse automáticamente. Haga clic en **Siguiente**.
   * En la página **Seleccionar roles de servidor**, haga clic en **Siguiente**.
   * En la página **Seleccionar características**, seleccione **E/S de múltiples rutas** y haga clic en **Siguiente**.
     
       ![Asistente para agregar roles y características 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * En la página **Confirmar selecciones de instalación**, confirme la selección y, después, seleccione **Reiniciar automáticamente el servidor de destino en caso necesario**, tal como se muestra a continuación. Haga clic en **Instalar**.
     
       ![Asistente para agregar roles y características 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Se le notificará cuando la instalación finalice. Haga clic en **Cerrar** para cerrar el asistente.
     
       ![Asistente para agregar roles y características 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

