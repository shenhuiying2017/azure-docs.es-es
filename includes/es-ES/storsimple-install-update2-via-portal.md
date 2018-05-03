<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Instalar una actualización desde el Portal de Azure

1. En la página de servicio de StorSimple, seleccione el dispositivo. Navegue a **Dispositivos** > **Mantenimiento**.
2. En la parte inferior de la página, haga clic en **Buscar actualizaciones**. Se crea un trabajo para buscar las actualizaciones disponibles. Se le notificará cuando el trabajo se complete correctamente.
3. En la sección **Actualizaciones de software** de la misma página, verá las nuevas actualizaciones disponibles. Se recomienda revisar las notas de la versión antes de aplicar una actualización en el dispositivo.
4. En la parte inferior de la página, haga clic en **Instalar actualizaciones** y, luego, en **Aceptar**.
5. En el cuadro de diálogo **Instalar actualizaciones**, asegúrese de que ha seguido las recomendaciones y, después, seleccione **Entiendo el requisito anterior y estoy listo para actualizar el dispositivo** y haga clic en el botón de comprobación.
   
    ![Mensaje de confirmación](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Ahora se inicia un conjunto de comprobaciones previas. Estas comprobaciones incluyen:
   
   * **Comprobaciones del estado del controlador** para comprobar que los controladores del dispositivo están en buen estado y en línea.
   * **Comprobaciones de mantenimiento de componentes de hardware** para comprobar que todos los componentes de hardware del dispositivo StorSimple están en buen estado.
   * **Comprobaciones de DATA 0** para comprobar DATA 0 está habilitado en el dispositivo. Si la interfaz no está habilitada, tendrá que habilitarla y, después, volver a intentarlo.
   * **Comprobaciones de DATA 2 y DATA 3** para comprobar que las interfaces de red de DATA 2 y DATA 3 no están habilitadas. Si estas interfaces están habilitadas, debe deshabilitarlas e intentar actualizar el dispositivo. Esta comprobación solo se realiza se va a realizar una actualización de un dispositivo que ejecuta software de disponibilidad general. Los dispositivos que ejecuten las versiones 0.1, 0.2, o 0.3 no necesitarán esta comprobación.
   * **Comprobación de la puerta de enlace** de todos los dispositivos que ejecuten una versión anterior a la actualización 1. Esta comprobación se realiza en todos los dispositivos que ejecutan el software de la versión previa a la actualización 1 pero genera error en los dispositivos que tienen una puerta de enlace configurada para una interfaz de red distinta de DATA 0.
     
     Si se han realizado correctamente todas las comprobaciones, se aplicará la actualización. Recibirá una notificación cuando las comprobaciones están en curso.
     
     ![Notificación de comprobación previa](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     El siguiente es un ejemplo en el que se produjo un error en las comprobaciones. Debe comprobar que los controladores del dispositivo están en buen estado y en línea. También necesita comprobar el estado de los componentes de hardware. En este ejemplo, los componentes del controlador 0 y del controlador 1 necesitan atención. Puede que necesite ponerse en contacto con el soporte técnico de Microsoft si no puede resolver estos problemas por sí mismo.
     
       ![Error en las comprobaciones](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Una vez realizadas correctamente las comprobaciones, se crea un trabajo de actualización. Recibirá una notificación cuando el trabajo de actualización esté correctamente creado.
   
    ![Creación del trabajo de actualización](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    La actualización se aplica en el dispositivo.
    
8. Para supervisar el progreso del trabajo de actualización, haga clic en **Ver trabajo**. En la página **Trabajos** , puede ver el progreso de la actualización.
9. La actualización tarda unas horas en completarse. Seleccione el trabajo de actualización y haga clic en **Detalles** para ver los detalles del trabajo en cualquier momento.
10. Una vez que el trabajo se haya completado, navegue a la página **Mantenimiento** página y desplácese hacia abajo hasta **Actualizaciones de software**.

