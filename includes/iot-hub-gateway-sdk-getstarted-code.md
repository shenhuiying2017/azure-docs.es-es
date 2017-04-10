## <a name="typical-output"></a>Salida típica

A continuación se muestra un ejemplo de la salida escrita en el archivo de registro mediante el ejemplo Hola mundo. Se le otorga un formato a la salida para facilitar la lectura:

```json
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Fragmentos de código

En esta sección se describen algunas secciones principales del código del ejemplo Hola\_mundo.

### <a name="gateway-creation"></a>Creación de puerta de enlace

El desarrollador debe escribir el *proceso de puerta de enlace*. Este programa crea la infraestructura interna (el agente), carga los módulos y prepara todo para que funcione correctamente. El SDK proporciona la función **Gateway\_Create\_From\_JSON** que le permite arrancar una puerta de enlace desde un archivo JSON. Para usar la función **Gateway\_Create\_From\_JSON**, debe pasar la ruta de acceso a un archivo JSON que especifique los módulos que se van a cargar.

Puede encontrar el código para el proceso de puerta de enlace en el ejemplo Hello World del archivo [main.c][lnk-main-c]. Para facilitar la lectura, el siguiente fragmento muestra una versión abreviada del código de proceso de puerta de enlace. Este programa de ejemplo crea una puerta de enlace y luego espera a que el usuario presione la tecla **ENTRAR** antes de anularla.

```c
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

El archivo de configuración JSON contiene una lista de los módulos que se van a cargar y los vínculos entre los módulos. Cada módulo debe especificar:

* **name**: un nombre único para el módulo.
* **loader**: un cargador que sabe cómo cargar el módulo deseado. Los cargadores son un punto de extensión para cargar diferentes tipos de módulos. Se proporcionan cargadores para usarlos con módulos escritos en C nativo, Node.js, Java y .Net. El ejemplo Hola mundo solo usa el cargador nativo porque todos los módulos de este ejemplo son bibliotecas dinámicas escritas en C. Consulte los ejemplos para [Node.js](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/java_sample) o [.NET](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/dotnet_binding_sample) para más información sobre cómo usar módulos escritos en lenguajes diferentes.
    * **name**: nombre del cargador usado para cargar el módulo.
    * **entrypoint**: la ruta de acceso a la biblioteca que contiene el módulo. En Linux esta biblioteca es un archivo .so y en Windows un archivo .dll. El punto de entrada es específico del tipo de cargador que se esté usando. El punto de entrada del cargador de Node.js es un archivo .js. El punto de entrada del cargador de Java es una ruta de clase junto con un nombre de clase. El punto de entrada del cargador de .NET es un nombre de ensamblado junto con un nombre de clase.

* **args**: cualquier información de configuración que necesita el módulo.

El código siguiente muestra el JSON que se usa para declarar todos los módulos para el ejemplo Hola mundo en Linux. El que un módulo necesite argumentos depende del diseño del módulo. En este ejemplo, el módulo logger toma un argumento que es la ruta de acceso al archivo de salida y el módulo hola\_mundo no tiene ningún argumento.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

El archivo JSON también contiene los vínculos entre los módulos que se pasarán al agente. Los vínculos tienen dos propiedades:

* **source**: un nombre de módulo de la sección `modules`, o "\*".
* **sink**: un nombre de módulo de la sección `modules`.

Cada vínculo define la ruta y dirección de un mensaje. Los mensajes del módulo `source` se deben entregar al módulo `sink`. `source` se puede establecer en "\*", lo que indica que `sink` recibirá mensajes de cualquier módulo.

El código siguiente muestra el JSON que se usa para configurar vínculos entre los módulos usados en el ejemplo hola\_mundo en Linux. Todos los mensajes que genere el módulo `hello_world` los consumirá el módulo `logger`.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Publicación de mensajes del módulo hola\_mundo

Puede buscar el código que utiliza el módulo "hola\_mundo" para publicar mensajes en el archivo ['hello_world.c'][lnk-helloworld-c]. El siguiente fragmento muestra una versión modificada del código con comentarios adicionales y algún código de control de errores quitado para facilitar la lectura:

```c
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="helloworld-module-message-processing"></a>Procesamiento de mensajes del módulo Hola\_mundo

El módulo hola\_mundo no necesita nunca procesar los mensajes que otros módulos publican en el agente. Por ello, la implementación de la devolución de llamada de mensajes en el módulo hola\_mundo es una función no operativa.

```c
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Publicación y procesamiento de mensajes del módulo logger

El módulo logger recibe mensajes del agente y los escribe en un archivo. Nunca publica ningún mensaje. Por consiguiente, el código del módulo logger nunca llama a la función **Broker_Publish**.

La función **Logger_Recieve** del archivo [logger.c][lnk-logger-c] es la devolución de llamada que invoca el agente para entregar mensajes al módulo logger. El siguiente fragmento muestra una versión modificada con comentarios adicionales y algún código de control de errores quitado para facilitar la lectura:

```c
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo usar el SDK de puerta de enlace de IoT, consulte los siguientes artículos:

* [SDK de puerta de enlace de IoT: envío de mensajes del dispositivo a la nube con un dispositivo simulado mediante Linux][lnk-gateway-simulated].
* [Azure IoT Gateway SDK][lnk-gateway-sdk] (SDK de puerta de enlace de Azure IoT) en GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md