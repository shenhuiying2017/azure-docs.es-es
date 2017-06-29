## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección, usará [Azure Portal][lnk-azure-portal] para crear una identidad de dispositivo en el registro de identidades de su instancia de IoT Hub. No se puede conectar un dispositivo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección sobre el registro de la identidad de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. En el portal, el **Explorador de dispositivos** ayuda a generar una clave y un identificador de dispositivo únicos con los que el dispositivo puede identificarse cuando envía mensajes de dispositivo a la nube a IoT Hub. Los identificadores de dispositivos distinguen mayúsculas de minúsculas.

1. Asegúrese de iniciar sesión en [Azure Portal][lnk-azure-portal]. 

1. En la barra de salto, haga clic en **Todos los recursos** y busque su recurso de IoT Hub. 
   
    ![Vaya a su instancia de IoT Hub][img-find-iothub]
1. Cuando se abra el recurso de IoT Hub, haga clic en la herramienta **Explorador de dispositivos** y, a continuación, haga clic en **Agregar** en la parte superior. Proporcione el nombre del nuevo dispositivo y haga clic en **Guardar**. 

    ![Creación de la identidad del dispositivo en el portal][img-create-device]

   Se crea una nueva identidad de dispositivo para su instancia de IoT Hub.
1. En la lista de dispositivos del **Explorador de dispositivos**, haga clic en el dispositivo recién creado y tome nota del valor de **Cadena de conexión: clave principal**. 

    ![Cadena de conexión de dispositivo][img-connection-string]
> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro al centro de IoT. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.
> 
> 

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

