<properties
   pageTitle="Conectar un dispositivo mediante C en Windows | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en Windows."
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
   ms.date="05/09/2016"
   ms.author="dobett"/>


# Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Creación de una solución de ejemplo de C en Windows

En los siguientes pasos se explica cómo crear una aplicación cliente simple que se comunique con la solución preconfigurada de supervisión remota mediante un programa de C en Visual Studio.

Cree un proyecto inicial en Visual Studio 2015 y agregue los paquetes NuGet del cliente de dispositivo del Centro de IoT:

1. En Visual Studio 2015, cree una nueva aplicación de consola de C mediante la plantilla **Aplicación de consola Win32** de Visual C++. Asigne al proyecto el nombre **RMDevice**.

2. En la página **Configuración de la aplicación** del **Asistente para aplicaciones Win32**, asegúrese de que se ha seleccionado **Aplicación de consola**, y desmarque **Encabezado precompilado** y **Habilitar comprobaciones adicionales del ciclo de vida de desarrollo de seguridad (SDL)**.

3. En el **Explorador de soluciones**, elimine los archivos stdafx.h, targetver.h y stdafx.cpp.

4. En el **Explorador de soluciones**, cambie el nombre del archivo RMDevice.cpp a RMDevice.c.

5. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **RMDevice** y, después, haga clic en **Administrar paquetes NuGet**. Haga clic en **Examinar** y, después, busque e instale los siguientes paquetes NuGet en el proyecto:

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

## Adición de código para especificar el comportamiento del dispositivo simple del Centro de IoT

Las bibliotecas de cliente del Centro de IoT utilizan un modelo para especificar el formato de los mensajes que el dispositivo envía al Centro de IoT y los comandos de este último a los que responde dicho dispositivo.

1. En Visual Studio, abra el archivo RMDevice.c. Sustituya las instrucciones `#include` existentes por las siguientes:

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    ```

2. Agregue las siguientes declaraciones de variable después de las instrucciones `#include`. Sustituya los valores de marcador de posición [Device Id] y [Device Key] por valores para el dispositivo en el panel de la solución de supervisión remota. Utilice el nombre de host del Centro de IoT en el panel para sustituir [IoTHub Name]. Por ejemplo, si el nombre de host del Centro de IoT es **contoso.azure-devices.net**, sustituya [IoTHub Name] por "contoso":

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Agregue el código siguiente para definir el modelo que permite que el dispositivo se comunique con el Centro de IoT. Este modelo especifica que el dispositivo debe enviar datos de temperatura, temperatura externa, humedad y un id. de dispositivo como telemetría. El dispositivo también envía metadatos sobre sí mismo al Centro de IoT, incluida la lista de comandos que admite el dispositivo. Este dispositivo responde a los comandos **SetTemperature** y **SetHumidity**:

    ```
    // Define the Model
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

## Adición de código para implementar el comportamiento del dispositivo

Ahora tiene que agregar código que implemente el comportamiento definido en el modelo. Agregará las funciones que se deberán ejecutar cuando el dispositivo reciba el comando del Centro y un código para enviar telemetría simulada a dicho Centro.

1. Agregue las siguientes funciones que se ejecutan cuando el dispositivo recibe los comandos **SetTemperature** y **SetHumidity** definidos en el modelo:

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

2. Agregue la siguiente función que envía un mensaje al Centro de IoT:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Agregue la siguiente función que enlaza la biblioteca de serialización del SDK:

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Agregue la siguiente función para conectarse al Centro de IoT, enviar y recibir mensajes, y desconectarse del Centro. Observe cómo el dispositivo envía metadatos sobre sí mismo, incluidos los comandos que admite, al Centro de IoT en cuanto que se conecta. Esto permite a la solución actualizar el estado del dispositivo a **En ejecución** en el panel:

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
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

5. Sustituya la función **main** por el siguiente código para invocar la función **remote\_monitoring\_run**:

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. Haga clic en **Compilar** y, después, en **Compilar solución** para compilar la aplicación del dispositivo.

7. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **RMDevice**, haga clic en **Depurar** y, después, en **Iniciar nueva instancia** para compilar y ejecutar el ejemplo. En la consola se muestran mensajes a medida que la aplicación envía telemetría de ejemplo al Centro de IoT.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0622_2016-->