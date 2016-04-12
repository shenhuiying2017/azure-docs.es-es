<!--author=alkohli last changed: 03/17/16-->

#### Descargar revisiones

Realice los pasos siguientes para descargar la actualización de software desde el catálogo de Microsoft Update.

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si esta es la primera vez que utiliza el Catálogo de Microsoft Update en este equipo, haga clic en **Instalar** cuando se le solicite que instale el complemento del Catálogo de Microsoft Update. ![Instalación del catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. En el cuadro de búsqueda del Catálogo de Microsoft Update, escriba el número de Knowledge Base de la revisión que desea descargar, por ejemplo **3121901** y, a continuación, haga clic en **Buscar**.

    Aparece la lista de revisiones; por ejemplo, la **actualización acumulativa 2.0 para la serie StorSimple 8000**.

    ![Búsqueda de catálogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Haga clic en **Agregar**. La actualización se agrega a la cesta.

5. Busque todas las revisiones adicionales enumeradas en la tabla anterior (**3121900**, **3080728**, **3090322** y **3121899**) y agréguelas a la cesta.

5. Haga clic en **Ver cesta**.

6. Haga clic en **Descargar**. Especifique o **busque** una ubicación local en la que quiera que aparezcan las descargas. Las actualizaciones se descargan en la ubicación especificada y se colocan en una subcarpeta con el mismo nombre que la actualización. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.

>   [AZURE.NOTE]
Las revisiones deben ser accesibles desde ambos controladores para detectar los posibles mensajes de error desde el controlador del mismo nivel.

#### Instalar y comprobar las revisiones de modo normal

Realice los pasos siguientes para instalar y comprobar las revisiones de modo normal. Si ya las ha instalado mediante el Portal de Azure, puede ir directamente a la sección [Instalar y comprobar las revisiones del modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar las revisiones, acceda a la interfaz de Windows PowerShell en la consola serie del dispositivo de StorSimple. Siga las instrucciones detalladas en [Uso de PuTTy para conectarse a la consola serie del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). En el símbolo del sistema, presione **Entrar**.

4. Seleccione **Opción 1** para iniciar sesión en el dispositivo con acceso completo.

5. Para instalar la revisión, en el símbolo del sistema, escriba lo siguiente:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Use IP en lugar de DNS en la ruta de acceso de recurso compartido en el comando anterior. El parámetro credential se usa únicamente si tiene acceso a un recurso compartido autenticado.

	Se recomienda que use el parámetro de credencial para obtener acceso a los recursos compartidos. Incluso los recursos compartidos que están abiertos para "todos" no suelen estar abiertos a los usuarios no autenticados.

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

6. Escriba **Y** cuando se le solicite que confirme la instalación de la revisión.

7. Supervise la actualización mediante el cmdlet `Get-HcsUpdateStatus`.

    La siguiente salida de ejemplo muestra la actualización en curso. `RunInprogress` será `True` cuando la actualización esté en curso.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````

     La siguiente salida de ejemplo indica que ha finalizado la actualización. `RunInProgress` será `False` cuando se haya completado la actualización.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````

	> [AZURE.NOTE] En ocasiones, el cmdlet notifica `False` cuando la actualización está todavía en curso. Para garantizar que la revisión está completada, espere unos minutos, vuelva a ejecutar este comando y compruebe que `RunInProgress` es `False`. Si es así, se habrá completado la revisión.

8. Una vez completada la actualización de software, repita los pasos 3 a 5 para instalar y supervisar el agente de SaaS y el agente MDS. Asegúrese de que `all-hcsmdssoftwareupdate_0b438ddf0d5b686aada2378b754fac8c7f2160e9.exe` se instale antes de `all-cismdsagentupdatebundle_f98e62f4d56c79e2a6644d027af7a2393a93827a.exe`.

9. Compruebe las versiones de software del sistema. Escriba:

    `Get-HcsSystem`

    Verá las versiones siguientes:

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13

	Si los números de versión no cambian después de aplicar la actualización, indica que la revisión no se ha podido aplicar. Si ve esto, póngase en contacto con [Soporte de Microsoft](storsimple-contact-microsoft-support.md) para obtener más ayuda.

9. Repita los pasos 3 a 5 para instalar las revisiones en modo normal restantes.

	- Controlador LSI - KB3121900
	- Actualización de Storport - KB3080728
	- Actualización de Spaceport - KB3090322

#### Instalar y comprobar las revisiones del modo de mantenimiento

Use KB3121899 para instalar las actualizaciones de firmware del disco. Se trata de actualizaciones potencialmente perjudiciales y tardan unos 30 minutos en completarse. Puede elegir instalarlas en una ventana de mantenimiento planificado mediante la conexión a la consola serie del dispositivo.

Tenga en cuenta que, si el firmware del disco ya está actualizado, no será necesario instalar estas actualizaciones. Ejecute el cmdlet `Get-HcsUpdateAvailability` desde la consola serie del dispositivo para comprobar si hay actualizaciones disponibles y si provocan interrupciones (modo de mantenimiento) o no (modo normal).

Para instalar las actualizaciones de firmware de disco, siga las instrucciones a continuación.

1. Active el modo de mantenimiento del dispositivo. Tenga en cuenta que no debe usar la conexión remota de Windows PowerShell al conectarse a un dispositivo en modo de mantenimiento. En su lugar, ejecute este cmdlet en el controlador del dispositivo cuando esté conectado a través de la consola serie del dispositivo. Escriba:

	`Enter-HcsMaintenanceMode`

	A continuación se muestra la salida de ejemplo.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
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

3. Para instalar la actualización de firmware del disco, escriba:

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	A continuación se muestra una salida de ejemplo.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Supervise el progreso de la instalación con el comando `Get-HcsUpdateStatus`. La actualización se habrá completado cuando `RunInProgress` cambie a `False`.

2.  Una vez completada la instalación, se reiniciará el controlador en el que se haya instalado la revisión de modo de mantenimiento. Inicie sesión como en la opción 1 con acceso completo y compruebe la versión de firmware del disco. Escriba:

	`Get-HcsFirmwareVersion`

	Las versiones de firmware del disco esperadas son:

	`XMGG, XGEG, KZ50, F6C2, VR08`

	A continuación se muestra una salida de ejemplo.

        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
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

	 Ejecute el comando `Get-HcsFirmwareVersion` en el segundo controlador para comprobar que se haya actualizado la versión de software. A continuación, puede salir del modo de mantenimiento. Para ello, escriba el comando siguiente para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`

1. Los controladores se reiniciarán al salir del modo de mantenimiento. Cuando las actualizaciones de firmware de disco se apliquen correctamente y el dispositivo haya salido del modo de mantenimiento, regrese al Portal de Azure clásico. Tenga en cuenta que, durante 24 horas, es posible que no aparezca en el portal la instalación de las actualizaciones en modo de mantenimiento.

<!---HONumber=AcomDC_0323_2016-->