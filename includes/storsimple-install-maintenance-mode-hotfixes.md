<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar revisiones en el modo de mantenimiento a través de Windows PowerShell para StorSimple
> [!IMPORTANT]
> En el modo de mantenimiento, deberá aplicar la revisión primero en un controlador y, a continuación, en el otro controlador.
> 
> 

1. Active el modo de mantenimiento del dispositivo. Consulte [Paso 2: Acceso al modo de mantenimiento](../articles/storsimple/storsimple-update-device.md#step2) para obtener instrucciones sobre cómo acceder al modo de mantenimiento.
2. Para aplicar la revisión, escriba:
   
     `Start-HcsHotfix` 
3. Cuando se le solicite, proporcione la ruta de acceso a la carpeta compartida de red que contiene los archivos de la revisión.
4. Se le pedirá confirmación. Escriba **Y** para continuar con la instalación de la revisión.
5. Después de aplicar la revisión en un controlador, inicie sesión en el otro controlador. Aplique la revisión tal y como lo hizo con el controlador anterior.
6. Una vez aplicadas las revisiones, salga del modo de mantenimiento. Consulte [Paso 4: Salida del modo de mantenimiento](../articles/storsimple/storsimple-update-device.md#step4) para obtener instrucciones.

