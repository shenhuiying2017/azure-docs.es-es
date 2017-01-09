#### <a name="to-create-public-endpoints-on-the-virtual-device"></a>Para crear puntos de conexión públicos en el dispositivo virtual

1. Inicie sesión en el portal clásico de Azure.
2. Haga clic en **Máquinas virtuales**y, a continuación, seleccione la máquina virtual que se utiliza como dispositivo virtual.
3. Haga clic en **Extremos**. En la página **Extremos** aparecen todos los extremos para la máquina virtual.
4. Haga clic en **Agregar**. Aparecerá el cuadro de diálogo **Add Endpoint** . Haga clic en la flecha para continuar.
5. Para el **Nombre**, escriba el siguiente nombre para el punto de conexión: **WinRMHttps**.
6. En **Protocolo**, especifique **TCP**.
7. En **Puerto público**, escriba los números de puerto que desea usar para la conexión.
8. Para el **puerto privado**, escriba **5986**.
9. Haga clic en la marca de verificación para crear el extremo.

Una vez creado el punto de conexión, puede ver sus detalles para determinar la dirección IP virtual (VIP) pública. Anote esta dirección.



<!--HONumber=Jan17_HO1-->


