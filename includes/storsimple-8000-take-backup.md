<!--author=alkohli last changed: 01/12/17-->

### <a name="to-take-a-backup"></a>Para realizar una copia de seguridad

1. Vaya al servicio StorSimple Device Manager. En la lista tabular de dispositivos, seleccione y haga clic en su dispositivo y después en **Toda la configuración**. En la hoja **Configuración**, vaya a **Configuración > Administrar > Directiva de copia de seguridad**.

    ![Agregar directiva de copia de seguridad](./media/storsimple-8000-take-backup/step8takebu1.png)

2. En la hoja **Directiva de copia de seguridad**, haga clic en **+ Agregar directiva**.

    ![Agregar directiva de copia de seguridad](./media/storsimple-8000-take-backup/step8takebu2.png)

3. En la hoja **Crear directiva de copia de seguridad** proporcione un nombre que tenga entre 3 y 150 caracteres para la directiva de copia de seguridad.

4. Seleccione los volúmenes de los que se va a hacer la copia de seguridad. Si selecciona más de un volumen, estos volúmenes se agruparán para crear una copia de seguridad preparada para bloqueos.

    ![Agregar directiva de copia de seguridad](./media/storsimple-8000-take-backup/step8takebu4.png)

5. En la hoja **Agregar la primera programación**:

    1. Seleccione el tipo de copia de seguridad. Para restauraciones más rápidas, seleccione instantánea **local**. Para lograr resistencia de datos, seleccione instantánea **en la nube**.
    2. Especifique la frecuencia de copia de seguridad en minutos, horas, días o semanas.
    3. Seleccione un tiempo de retención. Las opciones de retención dependen de la frecuencia de copia de seguridad. Por ejemplo, para una directiva diaria, la retención puede especificarse en semanas, mientras que la retención de una directiva mensual es en meses.
    4. Seleccione la fecha y hora de inicio para la directiva de copia de seguridad.
    5. Haga clic en **Aceptar** para crear la directiva de copia de seguridad.

        ![Agregar directiva de copia de seguridad](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Haga clic en **Crear** para iniciar la creación de directivas de copia de seguridad. Recibirá una notificación cuando la directiva de copia de seguridad se haya creado correctamente. También se actualiza la lista de directivas de copia de seguridad.
      
      ![Agregar directiva de copia de seguridad](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Ahora dispone de una directiva de copia de seguridad que creará copias de seguridad programadas de los datos del volumen.




