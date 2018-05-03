<!--author=alkohli last changed: 09/01/16-->

#### <a name="to-download-hotfixes"></a>Descargar revisiones
Realice los pasos siguientes para descargar la actualización de software desde el catálogo de Microsoft Update.

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Si esta es la primera vez que utiliza el Catálogo de Microsoft Update en este equipo, haga clic en **Instalar** cuando se le solicite que instale el complemento del Catálogo de Microsoft Update.
    ![Instalación del catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. En el cuadro de búsqueda del Catálogo de Microsoft Update, escriba el número de Knowledge Base (KB) de la revisión que desee descargar (por ejemplo, **3186843**) y haga clic en **Buscar**.
   
    Aparecerá la lista de revisiones; por ejemplo, la **actualización acumulativa 3.0 para la serie StorSimple 8000**.
   
    ![Búsqueda de catálogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. Haga clic en **Agregar**. La actualización se agrega a la cesta.
5. Busque cualquier otra revisión que figure en la tabla anterior (**3186859**), y agréguela a la cesta.
6. Haga clic en **Ver cesta**.
7. Haga clic en **Descargar**. Especifique o **busque** una ubicación local en la que quiera que aparezcan las descargas. Las actualizaciones se descargan en la ubicación especificada y se colocan en una subcarpeta con el mismo nombre que la actualización. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.

> [!NOTE]
> Las revisiones deben ser accesibles desde ambos controladores para detectar los posibles mensajes de error desde el controlador del mismo nivel.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Instalar y comprobar las revisiones de modo normal
Realice los pasos siguientes para instalar y comprobar las revisiones de modo normal. Si ya las ha instalado a través del Portal de Azure, puede ir directamente a la sección [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar las revisiones, acceda a la interfaz de Windows PowerShell en la consola serie del dispositivo de StorSimple. Siga las instrucciones detalladas de [Use PuTTy to connect to the serial console](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console) (Uso de PuTTy para conectarse a la consola serie). En el símbolo del sistema, presione **Entrar**.
2. Seleccione **Opción 1** para iniciar sesión en el dispositivo con acceso completo. Se recomienda instalar primero la revisión en el controlador pasivo.
3. Para instalar la revisión, en el símbolo del sistema, escriba lo siguiente:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Use IP en lugar de DNS en la ruta de acceso de recurso compartido en el comando anterior. El parámetro credential se usa únicamente si tiene acceso a un recurso compartido autenticado.
   
    Se recomienda que use el parámetro de credencial para obtener acceso a los recursos compartidos. Incluso los recursos compartidos que están abiertos para "todos" no suelen estar abiertos a los usuarios no autenticados.
   
    Indique la contraseña cuando se le solicite.
   
    A continuación se muestra una salida de ejemplo.
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. Escriba **Y** cuando se le solicite que confirme la instalación de la revisión.
5. Supervise la actualización mediante el cmdlet `Get-HcsUpdateStatus` . La actualización se completará en primer lugar en el controlador pasivo. Una vez actualizado el controlador pasivo, se producirá una conmutación por error, tras lo cual la actualización se aplicará en el otro controlador. La actualización estará completa cuando ambos controladores se hayan actualizado.
   
    La siguiente salida de ejemplo muestra la actualización en curso. `RunInprogress` será `True` cuando la actualización esté en curso.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 8/29/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     La siguiente salida de ejemplo indica que ha finalizado la actualización. `RunInProgress` será `False` cuando se haya completado la actualización.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 8/30/2016 9:15:55 AM
    LastUpdateTimestamp : 8/30/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE] 
    > En ocasiones, el cmdlet notifica `False` cuando la actualización está todavía en curso. Para garantizar que la revisión está completada, espere unos minutos, vuelva a ejecutar este comando y compruebe que `RunInProgress` es `False`. Si es así, se habrá completado la revisión.

1. Cuando se complete la actualización del software, compruebe las versiones de software del sistema. Escriba:
   
    `Get-HcsSystem`
   
    Verá las versiones siguientes:
   
   * `HcsSoftwareVersion: 6.3.9600.17759`
   * `CisAgentVersion:  1.0.9343.0`
   * `MdsAgentVersion: 30.0.4698.16`
     
     Si los números de versión no cambian después de aplicar la actualización, indica que la revisión no se ha podido aplicar. Si ve esto, póngase en contacto con [Soporte técnico de Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) para obtener más ayuda.
     
     > [!IMPORTANT]
     > Antes de aplicar el resto de actualizaciones, debe reiniciar el controlador activo a través del cmdlet `Restart-HcsController`. 
     > 
     > 
2. Repita los pasos del 3 a 5 para instalar el controlador LSI y la revisión de firmware **KB3186859**. Después de instala la revisión, use el cmdlet `Get-HcsSystem` . La versión de LSI debe ser:
   
   * `Lsisas2Version: 2.0.78.00`
3. Repita los pasos del 3 a 5 para instalar la actualización de Storport y Spaceport **KB3121261**.
4. Si va a actualizar desde la versión 2 o una anterior, tendrá que descargar también lo siguiente:
   
   * Corrección de iSCSI mediante KB3146621
   * Corrección de WMI mediante KB3103616

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Instalar y comprobar las revisiones del modo de mantenimiento
Use KB3121899 para instalar las actualizaciones de firmware del disco. Se trata de actualizaciones potencialmente perjudiciales y tardan unos 30 minutos en completarse. Puede elegir instalarlas en una ventana de mantenimiento planificado mediante la conexión a la consola serie del dispositivo.

Tenga en cuenta que, si el firmware del disco ya está actualizado, no será necesario instalar estas actualizaciones. Ejecute el cmdlet `Get-HcsUpdateAvailability` desde la consola serie del dispositivo para ver si hay actualizaciones disponibles y si provocan interrupciones (modo de mantenimiento) o no (modo normal).

Para instalar las actualizaciones de firmware de disco, siga las instrucciones a continuación.

1. Active el modo de mantenimiento del dispositivo. Tenga en cuenta que no debe usar la conexión remota de Windows PowerShell al conectarse a un dispositivo en modo de mantenimiento. En su lugar, ejecute este cmdlet en el controlador del dispositivo cuando esté conectado a través de la consola serie del dispositivo. Escriba:
   
    `Enter-HcsMaintenanceMode`
   
    A continuación se muestra una salida de ejemplo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
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
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Supervise el progreso de la instalación con el comando `Get-HcsUpdateStatus` . La actualización se habrá completado cuando `RunInProgress` cambie a `False`.
4. Una vez completada la instalación, se reiniciará el controlador en el que se haya instalado la revisión de modo de mantenimiento. Inicie sesión como en la opción 1 con acceso completo y compruebe la versión de firmware del disco. Escriba:
   
   `Get-HcsFirmwareVersion`
   
   Las versiones de firmware del disco esperadas son:
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   A continuación se muestra una salida de ejemplo.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    Ejecute el comando `Get-HcsFirmwareVersion` en el segundo controlador para comprobar que se actualizó la versión de software. A continuación, puede salir del modo de mantenimiento. Para ello, escriba el comando siguiente para cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`
5. Los controladores se reiniciarán al salir del modo de mantenimiento. Cuando las actualizaciones de firmware de disco se apliquen correctamente y el dispositivo haya salido del modo de mantenimiento, regrese al Portal de Azure clásico. Tenga en cuenta que, durante 24 horas, es posible que no aparezca en el portal la instalación de las actualizaciones en modo de mantenimiento.

