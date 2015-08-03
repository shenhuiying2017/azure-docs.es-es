
#### Para instalar la Actualización 1 desde el Portal de administración

1. En la página de servicio de StorSimple, seleccione el dispositivo. Vaya a **Dispositivos** > **Mantenimiento**.

2. En la parte inferior de la página, haga clic en **Buscar actualizaciones**. Para buscar actualizaciones disponibles, se creará un trabajo. Recibirá una notificación cuando el trabajo esté completado correctamente.

3. En la sección **Actualizaciones de software** en la misma página, verá que hay nuevas actualizaciones de software disponibles. Se recomienda que revise las notas de la versión antes de aplicar la actualización 1.0 en el dispositivo.

    ![Instalación de actualizaciones de software](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. En la parte inferior de la página, haga clic en **Instalar actualizaciones**.

5. Se le pedirá confirmación. Haga clic en **Aceptar**.

6. Aparecerá el cuadro de diálogo **Instalar actualizaciones**. Asegúrese de que el dispositivo cumple las comprobaciones enumeradas en este cuadro de diálogo. Seleccione **Comprendo el requisito anterior y estoy listo para actualizar el dispositivo**. Haga clic en el icono de marca de verificación.

    ![Mensaje de confirmación](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. Se le notificará que las comprobaciones anteriores a la actualización están en curso.
  
    ![Notificación de comprobación previa](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    El siguiente es un ejemplo en el que se produjo un error en la comprobación anterior a la actualización. Deberá comprobar que los controladores del dispositivo están en buen estado y en línea. También necesitará comprobar el estado de los componentes de hardware. En este ejemplo, los componentes del controlador 0 y del controlador 1 necesitan atención. Puede que necesite ponerse en contacto con el soporte técnico de Microsoft si no puede resolver estos problemas por sí mismo.

    ![Error de comprobación previa](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Una vez completadas correctamente las comprobaciones anteriores a la actualización, se creará un trabajo de actualización. Recibirá una notificación cuando el trabajo de actualización esté correctamente creado.
 
    ![Creación del trabajo de actualización](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    La actualización se aplicará en el dispositivo.
 
9. Para supervisar el progreso del trabajo de actualización, haga clic en **Ver trabajo**. En la página Trabajos, puede ver el progreso de la actualización.

    ![Actualización del progreso del trabajo](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. La actualización tardará unas horas en completarse. Puede ver los detalles del trabajo en cualquier momento.

    ![Actualización de detalles del trabajo](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. Una vez completado el trabajo, vaya a la página **Mantenimiento** página y desplácese hacia abajo hasta **Actualizaciones de software**.

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 1.0 (6.3.9600.17491)**. También se debe modificar **Fecha de última actualización:**.

    ![Página de mantenimiento](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=July15_HO4-->