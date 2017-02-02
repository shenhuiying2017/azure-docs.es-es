
El Administrador de conexiones híbridas permite que su máquina local se conecte a Azure y retransmita el tráfico TCP. El administrador se debe instalar en un equipo local que se pueda conectar a una instancia de SQL Server.

1. La conexión que acaba de crear debería tener el **estado** **On-premesis setup incomplete** (Instalación local incompleta). Haga clic en esta conexión y haga clic en **Instalación local**.
   
    ![Instalación local](./media/hybrid-connections-install-connection-manager/5-1.png)
2. Haga clic en **Instalar y configurar**.
   
    De esta forma, se instala una instancia personalizada del Administrador de conexiones, que ya está configurado para funcionar con la conexión híbrida que acaba de crear.
3. Realice el resto de los pasos de configuración del Administrador de conexiones.
   
    Cuando finalice la instalación, el estado de la conexión híbrida cambia a **1 instancia conectada**. Puede que tenga que actualizar el explorador y esperar unos minutos. 

La instalación de la conexión híbrida ha terminado.



<!--HONumber=Jan17_HO3-->


