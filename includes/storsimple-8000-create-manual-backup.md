
<!--author=alkohli last changed: 01/20/2017-->

#### <a name="to-create-a-manual-backup"></a>Creación de una copia de seguridad manual

1. Vaya al servicio Administrador de dispositivos de StorSimple y, después, haga clic en **Dispositivos**. En la lista tabular de dispositivos, seleccione el dispositivo. Vaya a **Configuración > Administrar > Directivas de copia de seguridad**.

2. En la pestaña **Directivas de copia de seguridad** se enumeran todas las directivas de copia de seguridad en formato tabular, incluida la directiva para el volumen del que desea realizar copias de seguridad. Seleccione la directiva asociada con el volumen del que desea realizar una copia de seguridad y haga clic con el botón derecho para invocar el menú contextual. Seleccione **Hacer una copia de seguridad ahora** en la lista desplegable.

    ![Crear copia de seguridad manual](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. En la hoja **Hacer una copia de seguridad ahora**, realice los pasos siguientes:

    1. Elija el **tipo de instantánea** adecuado en la lista desplegable: instantánea **local** o en la **nube**. Seleccione la instantánea local para copias de seguridad o restauraciones rápidas y la instantánea en la nube para la resistencia de datos.

        ![Crear copia de seguridad manual](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Haga clic en **Aceptar** para iniciar un trabajo para crear una instantánea. Verá una notificación en la parte superior de la página después de que el trabajo se cree correctamente.

        ![Crear copia de seguridad manual](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Para supervisar el trabajo, haga clic en la notificación. Esto le llevará a la hoja **Trabajos** donde puede ver el progreso del trabajo.


5. Una vez finalizado el trabajo de copia de seguridad, vaya a la pestaña **Catálogo de copia de seguridad** .

6. Establezca las selecciones de filtro en el dispositivo, la directiva de copia de seguridad y el intervalo de tiempo adecuados. La copia de seguridad debe aparecer en la lista de conjuntos de copia de seguridad que se muestra en el catálogo.

