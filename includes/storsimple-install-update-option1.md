
#### Para instalar la Actualización 1 desde Windows PowerShell para StorSimple

1. Realice los pasos siguientes para descargar la actualización de software.

    1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Si es la primera vez que es usuario de esta página, se le pedirá que instale un catálogo de Microsoft Update. Haga clic en **Instalar**.
    
        ![Instalación del catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Verá una pantalla de búsqueda de catálogo. Escriba **3063418** en el cuadro de búsqueda y haga clic en **Buscar**.

        ![Búsqueda de catálogo](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Verá **StorSimple Update 1.0 Appliance Update bundle**. Haga clic en **Agregar**. La actualización se agregará a la cesta.

        ![Actualización de agrupación](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Haga clic en **Ver cesta**.
 
        ![Visualización de cesta](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Haga clic en **Descargar**. Especifique o seleccione una ubicación local en la que desea que aparezca la descarga. Se descargará la actualización (all hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) en una carpeta StorSimple Update 1.0 Appliance Update bundle (KB3063418)” en la ubicación elegida. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.
        
2. Para instalar la actualización de software, acceda a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Siga las instrucciones detalladas en [Uso de PuTTy para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-serial-console).

3. En el símbolo del sistema, presione Entrar.

4. Seleccione **Opción 1** para iniciar sesión en el dispositivo con acceso completo.

5. Para instalar el paquete de actualización, en el símbolo del sistema, escriba:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    El parámetro credential se usa únicamente si tiene acceso a un recurso compartido autenticado.

    A continuación se muestra la salida de ejemplo.

        ````
        Controller0>Start-HcsHotfix -Path \10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Escriba **Y** cuando se le solicite que confirme la instalación de la revisión.

7. Supervise la actualización mediante el cmdlet Get-HcsUpdateStatus.

    La siguiente salida de ejemplo muestra la actualización en curso.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     La siguiente salida de ejemplo indica que ha finalizado la actualización.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
 
8. Una vez completada la actualización de software, vaya a la página Mantenimiento en el Portal de administración. Busque las actualizaciones disponibles. Verá que hay disponibles más actualizaciones de software.

9. Haga clic en **Instalar actualizaciones** para solicitar todas las actualizaciones de software disponible desde el portal.

10. Una vez completadas las actualizaciones de software, compruebe las versiones de software, controladores y firmware del sistema. Escriba el siguiente comando:

    `Get-HcsSystem`

    Verá las versiones siguientes:

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. Para comprobar que el firmware se ha actualizado correctamente, escriba:

    `Start-HcsFirmwareCheck`

    El estado de firmware debe ser **UpToDate**.

<!---HONumber=62-->