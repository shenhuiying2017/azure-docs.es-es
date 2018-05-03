1. Inicie Azure Site Recovery UnifiedSetup.exe
2. En **Antes de comenzar**, seleccione **Add additional process servers to scale out deployment** (Agregar servidores de procesos adicionales para el escalado horizontal de la implementación).

  ![Agregar servidores de procesos](./media/site-recovery-add-process-server/ps-page-1.png)

3. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de configuración y la frase de contraseña.

  ![Agregar servidores de procesos 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. En **Configuración de Internet**, especifique cómo se conecta el proveedor que se ejecuta en el servidor de configuración a Azure Site Recovery a través de Internet.

  ![Agregar servidores de procesos 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Si quiere conectarse con el proxy configurado actualmente en la máquina, seleccione **Connect with existing proxy settings**(Conectarse con la configuración de proxy existente).
   * Si quiere que el proveedor se conecte directamente, seleccione **Connect directly without a proxy** (Conectarse directamente sin un proxy).
   * Si el proxy existente requiere autenticación, o quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Connect with custom proxy settings**(Conectarse con una configuración de proxy personalizada).

     * Si utiliza un proxy personalizado, deberá especificar la dirección, el puerto y las credenciales.
     * Si usa un proxy, ya debe haber permitido el acceso a las direcciones URL del servicio.

5. En **Comprobación de requisitos previos**, el programa de instalación ejecuta una comprobación para asegurarse de que se pueda ejecutar la instalación. Si aparece una advertencia sobre la **comprobación de la sincronización de hora global**, compruebe que la hora del reloj del sistema (configuración de **fecha y hora**) es la misma que la de la zona horaria.

     ![Agregar servidores de procesos 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. Si realiza la configuración, compruebe si PowerCLI 6.0 está instalado.

     ![Agregar servidores de procesos 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. En **Ubicación de instalación**, seleccione dónde quiere instalar los archivos binarios y almacenar la memoria caché. La unidad que seleccione debe tener al menos 5 GB de espacio disponible en disco, pero se recomienda usar una unidad de memoria caché con 600 GB o más de espacio libre.
     ![Agregar servidores de procesos 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. En **Selección de red**, especifique el agente de escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviará y recibirá los datos de replicación. El puerto 9443 es el que se usa de forma predeterminada para enviar y recibir el tráfico de replicación, pero puede modificar este número de puerto para adecuarlo a los requisitos de su entorno. Además del puerto 9443, también se abre el puerto 443 que un servidor web utiliza para coordinar las operaciones de replicación. No use el puerto 443 para enviar o recibir tráfico de replicación.

     ![Agregar servidores de procesos 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. En **Resumen**, revise la información y haga clic en **Instalar**. Se genera una frase de contraseña cuando finaliza la instalación. La necesitará al habilitar la replicación, así que cópiela y manténgala en una ubicación segura.

     ![Agregar servidores de procesos 7](./media/site-recovery-add-process-server/ps-page-8.png)
