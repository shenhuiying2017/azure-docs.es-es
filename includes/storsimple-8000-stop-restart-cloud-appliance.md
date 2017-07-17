#### Detención e inicio de una aplicación en la nube
<a id="to-stop-and-start-a-cloud-appliance" class="xliff"></a>

1. Para detener una aplicación en la nube, vaya a la máquina virtual de su aplicación en la nube.
    ![Máquina virtual de StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. En la barra de comandos, haga clic en **Detener**.

    ![Máquina virtual de StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Cuando se le pida confirmación, haga clic en **Sí**.

    ![Máquina virtual de StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Cuando detiene una máquina virtual, esta se desasigna. Mientras se está deteniendo la aplicación en la nube, su estado es **Desasignando**. Después de que la aplicación en la nube se ha detenido, su estado es **Detenido (desasignado)**.

    ![Máquina virtual de StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Una vez que se detiene una máquina virtual, haga clic en **Iniciar** (el botón pasa a estar disponible) para iniciar la máquina virtual. Después de que se haya iniciado la aplicación en la nube, su estado es **Iniciado**.

    ![Máquina virtual de StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Use los siguientes cmdlets para detener e iniciar una aplicación en la nube.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### Reinicio de una aplicación en la nube
<a id="to-restart-a-cloud-appliance" class="xliff"></a>

Para reiniciar una aplicación en la nube, vaya a la máquina virtual de su aplicación en la nube. En la barra de comandos, haga clic en **Reiniciar**. Cuando se le solicite, confirme el reinicio. Cuando la aplicación en la nube esté lista para su uso, su estado será **En ejecución**.

![Máquina virtual de StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Use el siguiente cmdlet para reiniciar una aplicación en la nube.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

