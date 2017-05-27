> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

En este artículo se ofrece un tutorial detallado sobre el [código de ejemplo Hola mundo][lnk-helloworld-sample] para ilustrar los componentes fundamentales de la arquitectura de [Azure IoT Edge][lnk-iot-edge]. En el ejemplo se usa Azure IoT Edge para crear una puerta de enlace simple que registra un mensaje "hola mundo" en un archivo cada cinco segundos.

En este tutorial, se describen los siguientes procedimientos:

* **Conceptos**: información general conceptual de los componentes que constituyen cualquier puerta de enlace creada con IoT Edge.
* **Arquitectura de ejemplo Hello World**: describe cómo se aplican los conceptos al ejemplo Hello World y cómo funcionan conjuntamente los componentes.
* **Compilación del ejemplo**: los pasos necesarios para compilar el ejemplo.
* **Ejecución del ejemplo**: los pasos necesarios para ejecutar el ejemplo. 
* **Salida típica**: un ejemplo de la salida esperada al ejecutar el ejemplo.
* **Fragmentos de código**: una colección de fragmentos de código para mostrar cómo el ejemplo Hola mundo implementa los principales componentes de puerta de enlace de IoT Edge.

## <a name="azure-iot-edge-concepts"></a>Conceptos de Azure IoT Edge
Antes de examinar el código de ejemplo o de crear su propia puerta de enlace de campo mediante IoT Edge, debe comprender los conceptos clave que subyacen a la arquitectura de IoT Edge.

### <a name="iot-edge-modules"></a>Módulos de IoT Edge
Una puerta de enlace se crea con Azure IoT Edge mediante la creación y el ensamblado de *módulos de IoT Edge*. Los módulos utilizan *mensajes* para intercambiarse datos. Un módulo recibe un mensaje, realiza alguna acción en él, opcionalmente lo transforma en un nuevo mensaje y luego lo publica para que otros módulos lo procesen. Existe la posibilidad de que algunos módulos solo produzcan nuevos mensajes y nunca procesen los mensajes entrantes. Una cadena de módulos crea una canalización de procesamiento de datos donde cada módulo realiza una transformación en los datos en un punto de esa canalización.

![Cadena de módulos de puerta de enlace creada con Azure IoT Edge][1]

IoT Edge contiene lo siguiente:

* Módulos ya escritos que realizan funciones comunes de puerta de enlace.
* Las interfaces que un desarrollador puede utilizar para escribir módulos personalizados.
* La infraestructura necesaria para implementar y ejecutar un conjunto de módulos.

El SDK proporciona una capa de abstracción que permite crear puertas de enlace para trabajar en una diversidad de sistemas operativos y plataformas.

![Capa de abstracción de Azure IoT Edge][2]

### <a name="messages"></a>error de Hadoop
Aunque una manera práctica de conceptualizar cómo funciona una puerta de enlace es pensar que los módulos se pasan mensajes entre ellos, no refleja realmente lo que sucede. Los módulos de IoT Edge usan un agente para comunicarse entre sí, publican mensajes en dicho agente (bus, pubsub o cualquier otro patrón de mensajes) y, después, dejan que sea este el que enrute los mensajes a los módulos conectados a él.

Un módulo usa la función **Broker_Publish** para publicar un mensaje en el agente. El agente entrega los mensajes a un módulo mediante la invocación de una función de devolución de llamada. Un mensaje consta de un conjunto de propiedades de clave/valor y del contenido que se pasa como un bloque de memoria.

![Rol del agente en Azure IoT Edge][3]

### <a name="message-routing-and-filtering"></a>Enrutamiento y filtro de mensajes
Hay dos maneras de dirigir los mensajes a los módulos de IoT Edge correctos. Se puede pasar un conjunto de vínculos al agente para que este conozca tanto el origen como el receptor de cada módulo, o bien para que el módulo pueda filtrar por las propiedades del mensaje. Un módulo solo debe actuar en los mensajes destinados a ello. Los vínculos y el filtro de mensajes es lo que realmente crea una canalización de mensajes.

## <a name="hello-world-sample-architecture"></a>Arquitectura de ejemplo Hello World
El ejemplo Hello World ilustra los conceptos descritos en la sección anterior. El ejemplo Hola mundo implementa una puerta de enlace de IoT Edge que tiene una canalización formada por dos módulos de IoT Edge:

* El módulo *hello world* crea un mensaje cada cinco segundos y lo pasa al módulo logger.
* El módulo *logger* escribe los mensajes que recibe en un archivo.

![Arquitectura del ejemplo Hola mundo creada con Azure IoT Edge][4]

Como se ha descrito en la sección anterior, el módulo Hello World no pasa los mensajes directamente al módulo logger cada cinco segundos. En su lugar, lo que hace es publicar un mensaje en el agente cada cinco segundos.

El módulo logger recibe el mensaje del agente y actúa en él, escribe el contenido del mensaje en un archivo.

El módulo logger solo consume mensajes del agente, nunca publica mensajes nuevos en el agente.

![Cómo transmite el agente los mensajes entre los módulos en Azure IoT Edge][5]

La ilustración anterior muestra la arquitectura del ejemplo Hello World y las rutas de acceso relativas a los archivos de origen que implementan diferentes partes del ejemplo en el [repositorio][lnk-iot-edge]. Explore el código por su cuenta, o use los siguientes fragmentos de código como guía.

<!-- Images -->
[1]: media/iot-hub-iot-edge-getstarted-selector/modules.png
[2]: media/iot-hub-iot-edge-getstarted-selector/modules_2.png
[3]: media/iot-hub-iot-edge-getstarted-selector/messages_1.png
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge