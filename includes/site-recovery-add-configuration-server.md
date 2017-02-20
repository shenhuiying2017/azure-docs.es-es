1. Ejecute el archivo de instalación unificada.
2. En **Antes de comenzar**, seleccione **Install the configuration server and process server** (Instalar el servidor de configuración y el servidor de procesos).
    ![Antes de comenzar](./media/site-recovery-add-configuration-server/combined-wiz1.png)
3. En **Third-Party Software License** (Licencia de software de terceros), haga clic en **Acepto** para descargar e instalar MySQL.

    ![Software de terceros](./media/site-recovery-add-configuration-server/combined-wiz105.PNG)
4. En **Registro**, seleccione la clave de registro que descargó del almacén.

    ![Registro](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. En **Configuración de Internet**, especifique cómo se conecta el proveedor que se ejecuta en el servidor de configuración a Azure Site Recovery a través de Internet.

   * Si quiere conectarse con el proxy configurado actualmente en la máquina, seleccione **Connect with existing proxy settings**(Conectarse con la configuración de proxy existente).
   * Si quiere que el proveedor se conecte directamente, seleccione **Connect directly without a proxy** (Conectarse directamente sin un proxy).
   * Si el proxy existente requiere autenticación, o quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Connect with custom proxy settings**(Conectarse con una configuración de proxy personalizada).

     * Si utiliza un proxy personalizado, deberá especificar la dirección, el puerto y las credenciales.
     * Si utiliza un servidor proxy, se deberían haber permitido ya las direcciones URL descritas en los [requisitos previos](#configuration-server-prerequisites).

     ![Firewall](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. En **Comprobación de requisitos previos**, el programa de instalación ejecuta una comprobación para asegurarse de que se pueda ejecutar la instalación. Si aparece una advertencia sobre la **comprobación de la sincronización de hora global**, compruebe que la hora del reloj del sistema (configuración de **fecha y hora**) es la misma que la de la zona horaria.

    ![Requisitos previos](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. En **MySQL Configuration** (Configuración de MySQL), cree credenciales para iniciar sesión en la instancia de servidor MySQL que se va a instalar.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. Si realiza la configuración, compruebe si PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz7.png)

9. En **Ubicación de instalación**, seleccione dónde quiere instalar los archivos binarios y almacenar la memoria caché. La unidad que seleccione debe tener al menos 5 GB de espacio disponible en disco, pero se recomienda usar una unidad de memoria caché con 600 GB o más de espacio libre.

    ![Ubicación de instalación](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. En **Selección de red**, especifique el agente de escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviará y recibirá los datos de replicación. El puerto 9443 es el que se usa de forma predeterminada para enviar y recibir el tráfico de replicación, pero puede modificar este número de puerto para adecuarlo a los requisitos de su entorno. Además del puerto 9443, también se abre el puerto 443 que un servidor web utiliza para coordinar las operaciones de replicación. No use el puerto 443 para enviar o recibir tráfico de replicación.

    ![Selección de red](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. En **Resumen**, revise la información y haga clic en **Instalar**. Se genera una frase de contraseña cuando finaliza la instalación. La necesitará al habilitar la replicación, así que cópiela y manténgala en una ubicación segura.

    ![Resumen](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Después de finalizar el registro, el servidor aparecerá en la hoja **Configuración** > **Servidores** del almacén.


<!--HONumber=Feb17_HO2-->


