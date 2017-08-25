<!--author=alkohli last changed: 08/04/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Instalar una actualización desde el Portal de Azure

1. En la página de servicio de StorSimple, seleccione el dispositivo.

    ![Selección del dispositivo](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Vaya a **Configuración del dispositivo** > **Actualizaciones del dispositivo**.

    ![Haga clic en Actualizaciones del dispositivo.](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Aparecerá una notificación si hay nuevas actualizaciones disponibles. O bien, en la hoja **Actualizaciones del dispositivo**, haga clic en **Buscar actualizaciones**. Se crea un trabajo para buscar las actualizaciones disponibles. Se le notificará cuando el trabajo se complete correctamente.

    ![Haga clic en Actualizaciones del dispositivo.](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Se recomienda revisar las notas de la versión antes de aplicar una actualización en el dispositivo. Para aplicar las actualizaciones, haga clic en **Instalar actualizaciones**. En la hoja **Confirmar actualizaciones normales**, revise los requisitos previos que debe completar antes de aplicar las actualizaciones. Seleccione la casilla para indicar que está listo para actualizar el dispositivo y, a continuación, haga clic en **Instalar**.

    ![Haga clic en Actualizaciones del dispositivo.](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Ahora se inicia un conjunto de comprobaciones previas. Estas comprobaciones incluyen:
   
   * **Comprobaciones del estado del controlador** para comprobar que los controladores del dispositivo están en buen estado y en línea.
   * **Comprobaciones de mantenimiento de componentes de hardware** para comprobar que todos los componentes de hardware del dispositivo StorSimple están en buen estado.
   * **Comprobaciones de DATA 0** para comprobar DATA 0 está habilitado en el dispositivo. Si la interfaz no está habilitada, tendrá que habilitarla y, después, volver a intentarlo.

    La actualización se descargará e instalará solo si todas las comprobaciones se completan correctamente. Recibirá una notificación cuando las comprobaciones están en curso. Si se produce un error en la comprobación previa, se le indicarán las causas del error. Resuelva estos problemas e intente realizar de nuevo la operación. Puede que necesite ponerse en contacto con el soporte técnico de Microsoft si no puede resolver estos problemas por sí mismo.

7. Una vez realizadas correctamente las comprobaciones previas, se crea un trabajo de actualización. Recibirá una notificación cuando el trabajo de actualización esté correctamente creado.
   
    ![Creación del trabajo de actualización](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    La actualización se aplica en el dispositivo.

9. La actualización tarda unas horas en completarse. Seleccione el trabajo de actualización y haga clic en **Detalles** para ver los detalles del trabajo en cualquier momento.

    ![Creación del trabajo de actualización](./media/storsimple-8000-install-update5-via-portal/update8.png)

     También puede supervisar el progreso del trabajo de actualización desde **Configuración del dispositivo > Trabajos**. En la hoja **Trabajos**, puede ver el progreso de la actualización.

     ![Creación del trabajo de actualización](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Una vez completado el trabajo, vaya a **Configuración del dispositivo > Actualizaciones del dispositivo**. La versión del software ya debería estar actualizada.

