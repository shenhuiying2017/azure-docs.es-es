## <a name="typical-output"></a>Salida típica
A continuación se muestra un ejemplo de la salida escrita en el archivo de registro mediante el ejemplo Hello World. Se han agregado los caracteres Newline y Tab para facilitar su lectura:

```
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
En esta sección se describen algunas partes principales del código del ejemplo Hello World.

### <a name="gateway-creation"></a>Creación de puerta de enlace
El desarrollador debe escribir el *proceso de puerta de enlace*. Este programa crea la infraestructura interna (el agente), carga los módulos y prepara todo para que funcione correctamente. El SDK proporciona la función **Gateway_Create_From_JSON** que le permite arrancar una puerta de enlace desde un archivo JSON. Para usar la función **Gateway_Create_From_JSON**, debe pasar la ruta de acceso a un archivo JSON que especifique los módulos que se van a cargar. 

Puede encontrar el código para el proceso de puerta de enlace en el ejemplo Hello World del archivo [main.c][lnk-main-c]. Para facilitar la lectura, el siguiente fragmento muestra una versión abreviada del código de proceso de puerta de enlace. Este programa crea una puerta de enlace y luego espera a que el usuario presione la tecla **ENTRAR** antes de anularla. 

```
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

El archivo de configuración JSON contiene una lista de los módulos que se van a cargar. Cada módulo debe especificar:

* **module_name**: un nombre único para el módulo.
* **module_path**: la ruta de acceso a la biblioteca que contiene el módulo. En Linux es un archivo .so y en Windows un archivo .dll.
* **args**: cualquier información de configuración que necesita el módulo.

El archivo JSON también contiene los vínculos entre los módulos que se pasarán al agente. Los vínculos tienen dos propiedades:

* **source**: un nombre de módulo de la sección `modules`, o "\*".
* **sink**: un nombre de módulo de la sección `modules`.

Cada vínculo define la ruta y dirección de un mensaje. Los mensajes del módulo `source` se deben entregar al módulo `sink`. `sink` se puede establecer en "`source`", lo que indica que \* recibirá mensajes de cualquier módulo.

El ejemplo siguiente muestra el archivo de configuración JSON que se usa para configurar el ejemplo Hello World en Linux. Todos los mensajes que genere el módulo `hello_world` los consumirá el módulo `logger`. Que un módulo necesite un argumento depende del diseño del módulo. En este ejemplo, el módulo logger toma un argumento que es la ruta de acceso al archivo de salida y el módulo Hello World no toma ningún argumento:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "loading args": {
              "module path" : "./modules/logger/liblogger_hl.so"
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "loading args": {
              "module path" : "./modules/hello_world/libhello_world_hl.so"
            },
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Publicación de mensajes del módulo Hello World
Puede buscar el código que utiliza el módulo "hello world" para publicar mensajes en el archivo ['hello_world.c'][lnk-helloworld-c]. El siguiente fragmento muestra una versión modificada con comentarios adicionales y algún código de control de errores quitado para facilitar la lectura:

```
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

### <a name="hello-world-module-message-processing"></a>Procesamiento de mensajes del módulo Hello World
El módulo Hello World nunca necesita procesar mensajes que otros módulos publican en el agente. Esto hace que la implementación de la devolución de llamada de mensajes en el módulo Hello World sea una función no operativa.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Publicación y procesamiento de mensajes del módulo logger
El módulo logger recibe mensajes del agente y los escribe en un archivo. Nunca publica ningún mensaje. Por consiguiente, el código del módulo logger nunca llama a la función **Broker_Publish**.

La función **Logger_Recieve** del archivo [logger.c][lnk-logger-c] es la devolución de llamada que invoca el agente para entregar mensajes al módulo logger. El siguiente fragmento muestra una versión modificada con comentarios adicionales y algún código de control de errores quitado para facilitar la lectura:

```
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
Para más información sobre cómo usar el SDK de puerta de enlace de IoT, consulte lo siguiente:

* [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway-simulated].
* [SDK de puerta de enlace de Azure IoT][lnk-gateway-sdk] en GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!--HONumber=Nov16_HO2-->


