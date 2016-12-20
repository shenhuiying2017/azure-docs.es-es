<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>Para configurar y registrar el dispositivo
1. Acceder a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obtener instrucciones. **Siga el procedimiento exactamente como se indica o no podrá acceder a la consola.**
2. En la sesión que se abre, presione ENTRAR una vez para obtener un símbolo del sistema. 
3. Se le pedirá que elija el idioma que desee establecer para el dispositivo. Especifique el idioma y, a continuación, presione ENTRAR. 
   
    ![Configurar y registrar el dispositivo 1 de StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. En el menú de la consola serie que se muestra, seleccione la opción 1 para iniciar sesión con acceso completo. 
   
    ![Registrar el dispositivo 2 de StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. Realice los siguientes pasos para configurar los valores de red mínimos necesarios para su dispositivo.
   
   > [!IMPORTANT]
   > Estos pasos de configuración deben realizarse en el controlador activo del dispositivo. El menú de la consola serie indica el estado del controlador en el mensaje del banner. Si no está conectado al controlador activo, desconéctese y, a continuación, conéctese al controlador activo.
   > 
   > 
   
   1. En el símbolo del sistema, escriba su contraseña. La contraseña predeterminada del dispositivo es **Password1**.
   2. Escriba el siguiente comando:
      
        `Invoke-HcsSetupWizard`
   3. Aparecerá un Asistente para instalación que le ayudará a configurar las opciones de red para el dispositivo. Proporcione la siguiente información: 
      
      * Dirección IP para la interfaz de red DATA 0
      * Máscara de subred
      * Puerta de enlace
      * Dirección IP para el servidor DNS principal
      * Dirección IP para el servidor NTP principal
      
      > [!NOTE]
      > Tendrá que esperar unos minutos a que se aplique la configuración de máscara de subred y DNS. 
      > 
      > 
   4. De manera opcional, configure el servidor proxy web.
      
      > [!IMPORTANT]
      > Aunque la configuración del proxy web es opcional, tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí. Para obtener más información, vaya a [Configurar el proxy web para el dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md). 
      > 
      > 
6. Presione Ctrl + C para salir del asistente de configuración.
7. Instale las actualizaciones de la siguiente manera:
   
   1. Use el siguiente cmdlet para establecer direcciones IP en ambos controladores:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. En el símbolo del sistema, ejecute `Get-HcsUpdateAvailability`. Debe recibir una notificación para avisarle de que hay actualizaciones disponibles.
   3. Ejecute `Start-HcsUpdate`. Puede ejecutar este comando en cualquier nodo. Las actualizaciones se aplicarán en el primer controlador, el controlador se conmutará por error y luego las actualizaciones se aplicarán en el otro controlador.
      
      Para supervisar el progreso de la actualización, ejecute `Get-HcsUpdateStatus`.    
      
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
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


