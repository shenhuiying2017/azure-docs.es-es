<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-download-hotfixes"></a>Descargar revisiones
Realice los pasos siguientes para descargar la actualización de software.

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Si esta es la primera vez que utiliza el Catálogo de Microsoft Update en este equipo, haga clic en **Instalar** cuando se le solicite que instale el complemento del Catálogo de Microsoft Update.
    ![Instalación del catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. En el cuadro de búsqueda del Catálogo de Microsoft Update, escriba el número de Knowledge Base (KB) de la revisión que desee descargar (por ejemplo, **3063418**) y haga clic en **Buscar**.
4. Verá la agrupación de **StorSimple Update 1.2 Appliance Update** . Haga clic en **Agregar**. La actualización se agregará a la cesta.
5. Busque todas las revisiones adicionales enumeradas en la tabla anterior (**3043005** y **3063416**) y agréguelas a la cesta.
6. Haga clic en **Ver cesta**.
   
    ![Ver cesta](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. Haga clic en **Descargar**. Especifique o **busque** una ubicación local en la que quiera que aparezcan las descargas. Las actualizaciones se descargan en la ubicación especificada y se colocan en una subcarpeta con el mismo nombre que la actualización. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.

> [!NOTE]
> Las revisiones deben ser accesibles desde ambos controladores para detectar los posibles mensajes de error desde el controlador del mismo nivel.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Instalar y comprobar las revisiones de modo normal
Realice los pasos siguientes para instalar y comprobar las revisiones normales. Si ya las ha instalado mediante el Portal de Azure, puede ir directamente a [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar la actualización de software, acceda a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Siga las instrucciones detalladas de [Use PuTTy to connect to the serial console](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console) (Uso de PuTTy para conectarse a la consola serie). En el símbolo del sistema, presione **Entrar**.
2. Seleccione **Opción 1** para iniciar sesión en el dispositivo con acceso completo.
3. Para instalar el paquete de actualización, en el símbolo del sistema, escriba:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Use IP en lugar de DNS en la ruta de acceso de recurso compartido en el comando anterior. El parámetro credential se usa únicamente si tiene acceso a un recurso compartido autenticado.
   
    Se recomienda que use el parámetro de credencial para obtener acceso a los recursos compartidos. Incluso los recursos compartidos que están abiertos para "todos" no suelen estar abiertos a los usuarios no autenticados.
   
    A continuación se muestra una salida de ejemplo.
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. Escriba **Y** cuando se le solicite que confirme la instalación de la revisión.
5. Supervise la actualización mediante el cmdlet `Get-HcsUpdateStatus` .
   
    La siguiente salida de ejemplo muestra la actualización en curso. `RunInprogress` será `True` cuando la actualización esté en curso.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 9/02/2015 10:36:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     La siguiente salida de ejemplo indica que ha finalizado la actualización. `RunInProgress` será `False` cuando se haya completado la actualización.

    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 9/02/2015 10:56:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > En ocasiones, el cmdlet notifica `False` cuando la actualización está todavía en curso. Para garantizar que la revisión está completada, espere unos minutos, vuelva a ejecutar este comando y compruebe que `RunInProgress` es `False`. Si es así, se habrá completado la revisión.
   > 
   > 
6. Cuando se complete la actualización del software, compruebe las versiones de software del sistema. Escriba el siguiente comando:
   
    `Get-HcsSystem`
   
    Verá las versiones siguientes:
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     Si los números de versión no cambian después de aplicar la actualización, indica que la revisión no se ha podido aplicar. Si ve esto, póngase en contacto con [Soporte técnico de Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) para obtener más ayuda.
7. Repita los pasos 3 a 5 para instalar las revisiones en modo normal restantes (KB3043005).

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Instalar y comprobar las revisiones del modo de mantenimiento
Use KB3063416 para instalar las actualizaciones de firmware del disco. Se trata de actualizaciones potencialmente perjudiciales y tardan unos 30-45 minutos en completarse. Puede elegir instalarlas en una ventana de mantenimiento planificado mediante la conexión a la consola serie del dispositivo.

Para instalar las actualizaciones de firmware de disco, siga las instrucciones a continuación.

1. Active el modo de mantenimiento del dispositivo. Tenga en cuenta que no debe usar la conexión remota de Windows PowerShell al conectarse a un dispositivo en modo de mantenimiento. Tendrá que ejecutar este cmdlet en el controlador del dispositivo cuando esté conectado a través de la consola serie del dispositivo. Escriba:
   
    `Enter-HcsMaintenanceMode`
   
    A continuación se muestra una salida de ejemplo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    A continuación, se reiniciarán ambos controladores en modo de mantenimiento.
2. Para instalar la actualización de firmware del disco, escriba:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    A continuación se muestra una salida de ejemplo.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Supervise el progreso de la instalación con el comando `Get-HcsUpdateStatus` . La actualización se habrá completado cuando `RunInProgress` cambie a `False`.
4. Una vez completada la instalación, se reiniciará el controlador en el que se instaló la revisión de modo de mantenimiento. Inicie sesión como en la opción 1 con acceso completo y compruebe la versión de firmware del disco. Escriba:
   
   `Get-HcsFirmwareVersion`
   
   Las versiones de firmware del disco esperadas son:
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   Ejecute el comando `Get-HcsFirmwareVersion` en el segundo controlador para comprobar que se actualizó la versión de software. A continuación, puede salir del modo de mantenimiento. Escriba el comando siguiente para cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`
5. Los controladores se reiniciarán al salir del modo de mantenimiento. Cuando las actualizaciones de firmware de disco se apliquen correctamente y el dispositivo haya salido del modo de mantenimiento, regrese al Portal de Azure clásico. Tenga en cuenta que, durante 24 horas, es posible que no aparezca en el portal la instalación de las actualizaciones en modo de mantenimiento.

