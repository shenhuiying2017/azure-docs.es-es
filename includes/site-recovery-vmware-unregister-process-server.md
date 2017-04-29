Los pasos necesarios para anular el registro de un servidor de procesos serán diferentes en función del estado de la conexión con el servidor de configuración.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Anulación del registro de un servidor de procesos que se encuentra en estado conectado

1. Conéctese de forma remota al servidor de procesos como administrador.
2. Inicie el **Panel de Control** y abra **Programas > Desinstalar un programa**
3. Desinstale el programa denominado **Servidor de configuración o de procesos de Microsoft Azure Site Recovery**
4. Cuando haya efectuado el paso 3, puede desinstalar las **dependencias del servidor de configuración o de procesos de Microsoft Azure Site Recovery**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Anulación del registro de un servidor de procesos que se encuentra en estado desconectado

> [!WARNING]
> Utilice los pasos que se indican a continuación si no hay ninguna manera de reactivar la máquina virtual en la que se instaló el servidor de procesos.

1. Inicie sesión como administrador en el servidor de configuración.
2. Abra un símbolo del sistema administrativo y vaya al directorio `%ProgramData%\ASR\home\svsystems\bin`.
3. A continuación, ejecute el comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Esto purgará los detalles del servidor de procesos desde el sistema.
