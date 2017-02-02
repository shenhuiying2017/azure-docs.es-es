
1. En la máquina local, inicie sesión en el [Portal de administración de Azure](http://manager.windowsazure.com) (el portal antiguo).
2. En la parte inferior del panel de navegación, seleccione **+NUEVO** > **App Services** > **Servicio de BizTalk** > **Creación personalizada**.
3. Especifique un **nombre del servicio de BizTalk** y seleccione una **edición**. 
   
    Este tutorial usa **móvil1**. Deberá especificar un nombre exclusivo para su nuevo servicio de BizTalk.
4. Una vez que se haya creado el servicio de BizTalk, seleccione la pestaña **Conexiones híbridas** y haga clic en **Agregar**.
   
    ![Add Hybrid Connection](./media/hybrid-connections-create-new/3.png)
   
    De esta forma, se creará una nueva conexión híbrida.
5. Especifique el **nombre** y el **nombre de host** de la conexión híbrida y en **Puerto** seleccione `1433`. 
   
    ![Configure Hybrid Connection](./media/hybrid-connections-create-new/4.png)
   
    El nombre de host es el nombre del servidor local. De esta manera se configura la conexión híbrida para el acceso a SQL Server que se ejecuta en el puerto 1433. Si utiliza una instancia con nombre de SQL Server, en su lugar, utilice el puerto estático que se definió anteriormente.
6. Una vez creada la nueva conexión, El estado de la nueva conexión muestra **Instalación local incompleta**.
7. Vuelva a su servicio móvil, haga clic en **Configurar**, desplácese hacia abajo hasta **Conexiones híbridas** y haga clic en **Agregar conexión híbrida**; después, seleccione la conexión híbrida que acaba de crear y haga clic en **Aceptar**.
   
    Esto permite al servicio móvil usar la nueva conexión híbrida.

Ahora, será necesario instalar el Administrador de conexiones híbridas en el equipo local.



<!--HONumber=Jan17_HO3-->


