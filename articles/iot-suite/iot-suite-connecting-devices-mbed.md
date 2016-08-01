<properties
   pageTitle="Conectar un dispositivo mediante C en mbed | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en un dispositivo de mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="dobett"/>


# Conectar el dispositivo a la solución preconfigurada de supervisión remota (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilación y ejecución de la solución de ejemplo de C

Las instrucciones siguientes describen los pasos para conectar un dispositivo [Freescale FRDM-K64F habilitado para mbed][lnk-mbed-home] a la solución de supervisión remota.

### Conexión del dispositivo mbed a la máquina de escritorio y a la red

1. Conecte el dispositivo mbed a la red mediante un cable Ethernet. Este paso es necesario porque la aplicación de ejemplo requiere acceso a Internet.

2. Vea [Introducción a mbed][lnk-mbed-getstarted] para conectar el dispositivo de mbed al equipo de escritorio.

3. Si el equipo de escritorio está ejecutando Windows, vea [Configuración del equipo][lnk-mbed-pcconnect] para configurar el acceso al puerto serie para el dispositivo mbed.

### Crear un proyecto de mbed e importar el código de ejemplo

1. En el explorador web, vaya al [sitio para desarrolladores](https://developer.mbed.org/) mbed.org. Si no se registró, verá una opción para crear una nueva cuenta (es gratis). De lo contrario, inicie sesión con las credenciales de su cuenta. Luego haga clic en **Compilador** en la esquina superior derecha de la página. Accederá a la interfaz *Área de trabajo*.

2. Asegúrese de que la plataforma de hardware que está usando aparezca en la esquina superior derecha de la ventana, o bien haga clic en el icono de la esquina derecha para seleccionar la plataforma de hardware.

3. Haga clic en **Importar** en el menú principal. Luego haga clic en **Haga clic aquí** para importar desde el vínculo de dirección URL situado junto al logotipo de globo terráqueo de mbed.

    ![][6]

4. En la ventana emergente, especifique el vínculo del código de ejemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ y haga clic en **Importar**.

    ![][7]

5. Puede ver en la ventana del compilador de mbed que se importaron varias bibliotecas durante la importación de este proyecto. El equipo de IoT de Azure proporciona y mantiene algunas ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure\_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), mientras que otras son bibliotecas de terceros que están disponibles en el catálogo de bibliotecas de mbed.

    ![][8]

6. Abra remote\_monitoring\\remote\_monitoring.c y busque el código siguiente en el archivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Reemplace [Device Id] y [Device Key] con los datos del dispositivo para habilitar el programa de ejemplo para conectarse a su centro de IoT. Use el Nombre de host del Centro de IoT para reemplazar los marcadores de posición [IoTHub Name] y [IoTHub Suffix, i.e. azure-devices.net]. Por ejemplo, si el nombre de host del Centro de IoT es **contoso.azure-devices.net**, **Contoso** será **hubName** y todo lo que aparece detrás será **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### Recorrido del código

Si está interesado en cómo funciona el programa, esta sección describe algunas partes clave del código de ejemplo. Si desea ejecutar el código, vaya a [Compilar y ejecutar el programa](#buildandrun).

#### Definición del modelo

Este ejemplo utiliza la biblioteca [serializer][lnk-serializer] para definir un modelo que especifica los mensajes. El dispositivo puede enviar al Centro de IoT y recibir de dicho centro. En este ejemplo, el espacio de nombres **Contoso** define un modelo **Thermostat** que especifica los datos de telemetría de **Temperature**, **ExternalTemperature** y **Humidity** junto los metadatos, como el identificador de dispositivo, las propiedades del dispositivo y los a los que el dispositivo responde:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Relacionadas con la definición del modelo están las definiciones de los comandos **SetTemperature** y **SetHumidity** a los que el dispositivo responde:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### Conexión del modelo a la biblioteca

Las funciones **sendMessage** y **IoTHubMessage** son código reutilizable para enviar telemetría desde el dispositivo y mensajes de conexión desde el Centro de IoT a los controladores de comandos.

#### La función remote\_monitoring\_run

La función **principal** del programa invoca la función **remote\_monitoring\_run** cuando se inicia la aplicación para ejecutar el comportamiento del dispositivo como un cliente de dispositivo del Centro de IoT. Esta función **remote\_monitoring\_run** consta principalmente de pares anidados de funciones:

- **platform\_init** y **platform\_deinit** realizan las operaciones de inicialización y cierre específicas de plataforma.
- **serializer\_init** y **serializer\_deinit** inicializan y desinicializan la biblioteca serializer.
- **IoTHubClient\_Create** y **IoTHubClient\_Destroy** crean un controlador de cliente, **iotHubClientHandle** utilizando las credenciales del dispositivo para conectarse al Centro de IoT.

En la sección principal de la función **remote\_monitoring\_run**, el programa realiza las siguientes operaciones mediante el controlador **iotHubClientHandle**:

- Crea una instancia del modelo de termostato Contoso y configura las devoluciones de llamada de mensaje para los dos comandos.
- Envía información sobre el propio dispositivo, incluidos los comandos que admite, a su centro de IoT mediante la biblioteca serializer. Cuando el centro recibe este mensaje, cambia el estado del dispositivo en el panel de **Pendiente** a **En ejecución**.
- Inicia un bucle **while** que envía los valores de temperatura, temperatura exterior y humedad al Centro de IoT cada segundo.

Como referencia, este es un mensaje de **información del dispositivo** de ejemplo enviado al Centro de IoT durante el inicio:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Como referencia, este es un mensaje de **telemetría** de ejemplo enviado al Centro de IoT:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Como referencia, este es un **comando** de ejemplo recibido del Centro de IoT:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### Compilar y ejecutar el programa

1. Haga clic en **Compilar** para compilar el programa. Puede omitir con seguridad las advertencias, pero si la compilación genera errores, corríjalos antes de continuar.

2. Si la compilación es correcta, el sitio web del compilador de mbed genera un archivo .bin con el nombre del proyecto y lo descarga en el equipo local. Copie el archivo .bin en el dispositivo. Si guarda el archivo .bin en el dispositivo, este último se reiniciará y ejecutará el programa incluido en el archivo .bin. Puede reiniciar manualmente el programa en cualquier momento presionando el botón Restablecer en el dispositivo mbed.

3. Conecte con el dispositivo mediante una aplicación cliente de SSH, como PuTTY. Puede determinar el puerto serie que usa el dispositivo comprobando el Administrador de dispositivos de Windows.

    ![][11]

4. En PuTTY, haga clic en el tipo de conexión **serie**. Como el dispositivo normalmente se conecta a 9600 baudios, especifique 9600 en el cuadro **Velocidad**. A continuación, haga clic en **Abrir**.

5. El programa comienza a ejecutarse. Puede que tenga que restablecer la placa (presione CTRL+pausa o el botón de reinicio de la placa) si el programa no se inicia automáticamente cuando se conecta.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer

<!---HONumber=AcomDC_0720_2016-->