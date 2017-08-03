<!--author=alkohli last changed: 01/18/2017-->


#### <a name="to-configure-and-register-the-device"></a>Para configurar y registrar el dispositivo

1. Acceder a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obtener instrucciones. **Siga el procedimiento exactamente como se indica o no podrá acceder a la consola.**

2. En la sesión que se abre, presione **Entrar** una vez para obtener un símbolo del sistema.

3. Se le pedirá que elija el idioma que desee establecer para el dispositivo. Especifique el idioma y, a continuación, presione **Entrar**.

4. En el menú de la consola serie que se muestra, seleccione la opción 1 para **iniciar sesión con acceso completo**.
     Complete los pasos 5-12 para configurar las opciones de red necesarias mínimas para el dispositivo. **Estos pasos de configuración deben realizarse en el controlador activo del dispositivo.** El menú de la consola serie indica el estado del controlador en el mensaje del banner. Si no está conectado al controlador activo, desconéctese y, a continuación, conéctese al controlador activo.

5. En el símbolo del sistema, escriba su contraseña. La contraseña predeterminada del dispositivo es **Password1**.

6. Escriba el siguiente comando: `Invoke-HcsSetupWizard`.

7. Aparecerá un Asistente para instalación que le ayudará a configurar las opciones de red para el dispositivo. Proporcione la siguiente información:
   
   * Dirección IP para la interfaz de red DATA 0
   * Máscara de subred
   * Puerta de enlace
   * Dirección IP para el servidor DNS principal

   A continuación se muestra una salida de ejemplo.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    En la salida de ejemplo anterior, puede ver que el sistema valida la configuración de red después de cada paso del proceso.

     > [!NOTE]
     > Tendrá que esperar unos minutos para que se apliquen la máscara de subred y la configuración de DNS. Si recibe el mensaje de error «Compruebe la conectividad de red para Data 0», compruebe la conexión de red física en la interfaz de red DATA 0 de su controlador activo.

8. (Opcional) Configure el servidor proxy web. Aunque la configuración del proxy web es opcional, **tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí**. Para obtener más información, vaya a [Configurar el proxy web para el dispositivo](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
9. Configure un servidor NTP principal para el dispositivo. Se requieren servidores NTP, dado que el dispositivo debe sincronizar la hora para que pueda autenticarse con los proveedores de servicios en la nube. Asegúrese de que su red permite que el tráfico NTP pase del centro de datos a Internet. Si esto no es posible, especifique un servidor NTP interno.

    A continuación se muestra una salida de ejemplo.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Por motivos de seguridad, la contraseña del administrador del dispositivo expira después de la primera sesión y deberá cambiarla ahora. Cuando se le solicite, proporcione una contraseña de administrador del dispositivo. Una contraseña de administrador del dispositivo válida debe tener entre 8 y 15 caracteres. La contraseña debe contener tres de los siguientes elementos: minúsculas, mayúsculas, números y caracteres especiales.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. El último paso del Asistente para instalación registra el dispositivo en el servicio StorSimple Device Manager. Para ello, necesitará la clave de registro del servicio que obtuvo en el paso 2. Después de proporcionar la clave de registro, puede que tenga que esperar entre 2 y 3 minutos hasta que el dispositivo se registre.
    
    > [!NOTE]
    > También puede presionar Ctrl+C en cualquier momento para salir del Asistente para instalación. Si ha especificado toda la configuración de red (dirección IP para Data 0, máscara de subred y puerta de enlace), se conservarán las entradas.
    
    A continuación se muestra una salida de ejemplo.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Una vez registrado el dispositivo, aparecerá una clave de cifrado de datos de servicio. Copie esta clave y guárdela en un lugar seguro, **Esta clave se solicita junto con la clave de registro del servicio para registrar dispositivos adicionales en el servicio StorSimple Device Manager.** Consulte [Seguridad de StorSimple](../articles/storsimple/storsimple-security.md) para obtener más información sobre esta clave.
    
    ![Registrar el dispositivo 7 de StorSimple](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Para copiar el texto de la ventana de la consola serie, simplemente seleccione el texto. A continuación, podrá pegarlo en el Portapapeles o en cualquier editor de texto. No use Ctrl+C para copiar la clave de cifrado de datos de servicio. Si usa Ctrl+C, saldrá del Asistente para instalación. Como resultado, no se cambiará la contraseña del administrador del dispositivo y el dispositivo volverá a usar la contraseña predeterminada.
    
13. Salga de la consola serie.
14. Vuelva al portal de Azure y realice los siguientes pasos:
    
    1. Vaya al servicio StorSimple Device Manager.
    2. Haga clic en **Dispositivos**.
    3. En la lista tabular de dispositivos, consulte el estado del dispositivo para comprobar que se conectó correctamente al servicio. El estado del dispositivo debe ser **Listo para configurar**.
       
        ![Página de dispositivos de StorSimple](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Puede que tenga que esperar unos minutos a que el estado del dispositivo cambie a **Listo para configurar**.
       
        Si el dispositivo no se muestra en esta lista, debe asegurarse de que la red de su firewall se haya configurado como se describe en los [requisitos de red para su dispositivo de StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md). Compruebe que el puerto 9354 está abierto para la comunicación saliente, ya que lo usa el bus de servicio para la comunicación entre el dispositivo y el servicio StorSimple Device Manager.

