En este paso, se creará una regla de firewall para abrir el puerto de sondeo para el punto de conexión de carga equilibrada (59999, como se especificó anteriormente) y otra regla para abrir el puerto de escucha del grupo de disponibilidad. Como se creó el punto de conexión de carga equilibrada en las máquinas virtuales que contienen réplicas del grupo de disponibilidad, será necesario abrir el puerto de sondeo y el puerto de escucha en las respectivas máquinas virtuales.

1. Inicie el **Firewall de Windows con seguridad avanzada** en las máquinas virtuales que hospedan réplicas.

2. Haga clic con el botón derecho en **Reglas de entrada** y después en **Nueva regla**.

3. En la página **Tipo de regla**, seleccione **Puerto** y después haga clic en **Siguiente**.

4. En la página **Protocolo y puertos** seleccione **TCP**, escriba **59999** en el cuadro de texto **Puertos locales específicos** y haga clic en **Siguiente**.

5. En la página **Acción**, mantenga seleccionado **Permitir la conexión** y luego haga clic en **Siguiente**.

6. En la página **Perfiles**, acepte la configuración predeterminada y luego haga clic en **Siguiente**.

7. En la página **Nombre**, especifique un nombre de regla, como **Always On Listener Probe Port** (Puerto de sondeo de escucha Always On), en el cuadro de texto **Nombre** y haga clic en **Finalizar**.

8. Repita los pasos anteriores para el puerto de escucha del grupo de disponibilidad (como se especificó anteriormente en el parámetro $EndpointPort del script) y especifique un nombre de regla adecuado, como **Puerto de escucha Always On**.

