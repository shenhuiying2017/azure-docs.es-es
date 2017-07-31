#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Creación de puntos de conexión públicos en la aplicación en la nube

1. Inicie sesión en el Portal de Azure.
2. Vaya a **Máquinas virtuales**y, a continuación, haga clic en la máquina virtual que se utiliza como aplicación en la nube.
    
3. Debe crear una regla de grupo de seguridad de red (NSG) para controlar el flujo de tráfico de entrada y salida de la máquina virtual. Realice los siguientes pasos para crear una regla de NSG.
    1. Seleccione **Grupo de seguridad de red**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Haga clic en el grupo de seguridad de red predeterminado que se presenta.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Seleccione **Reglas de seguridad de entrada**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Haga clic en **+ Agregar** para crear una regla de seguridad de entrada.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        En la hoja Agregar regla de seguridad de entrada:

        1. En **Nombre**, escriba el siguiente nombre para el punto de conexión: WinRMHttps.
        
        2. En **Prioridad**, seleccione un número menor que 1000 (que es la prioridad para la regla predeterminada). Cuanto mayor sea el valor, menor es la prioridad.

        3. Establezca **Origen** en **Cualquiera**.

        4. En **Servicio**, seleccione **WinRM**. El **protocolo** se establece automáticamente en **TCP** y el **intervalo de puertos** se establece en **5986**.

        5. Haga clic en **Aceptar** para crear la regla.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. El paso final es asociar el grupo de seguridad de red a una subred o una interfaz de red específica. Realice los pasos siguientes para asociar el grupo de seguridad de red a una subred.
    1. Vaya a **Subredes**.
    2. Haga clic en **+ Asociar**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Seleccione la red virtual y luego seleccione la subred adecuada.
    4. Haga clic en **Aceptar** para crear la regla.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Una vez creada la regla, puede ver sus detalles para determinar la dirección IP virtual (VIP) pública. Anote esta dirección.


