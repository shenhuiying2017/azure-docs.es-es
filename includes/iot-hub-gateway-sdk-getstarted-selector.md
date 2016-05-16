> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

En este artículo se ofrece un tutorial detallado sobre el [código de ejemplo Hello World][lnk-helloworld-sample] para ilustrar los componentes fundamentales de la arquitectura del [SDK de puerta de enlace de IoT de Azure][lnk-gateway-sdk]. En el ejemplo se usa el SDK de puerta de enlace para crear una puerta de enlace simple que registra un mensaje "hello world" en un archivo cada cinco segundos.

En este tutorial, se describen los siguientes procedimientos:

- **Conceptos**: información general conceptual de los componentes que constituyen cualquier puerta de enlace creada con el SDK de puerta de enlace.  
- **Arquitectura de ejemplo Hello World**: describe cómo se aplican los conceptos al ejemplo Hello World y cómo funcionan conjuntamente los componentes.
- **Compilación del ejemplo**: los pasos necesarios para compilar el ejemplo.
- **Ejecución del ejemplo**: los pasos necesarios para ejecutar el ejemplo. 
- **Salida típica**: un ejemplo de la salida esperada al ejecutar el ejemplo.
- **Fragmentos de código**: una colección de fragmentos de código para mostrar cómo el ejemplo Hello World implementa los principales componentes de puerta de enlace.

## Conceptos del SDK de puerta de enlace

Antes de examinar el código de ejemplo o de crear su propia puerta de enlace de campo mediante el SDK de puerta de enlace, debe comprender los conceptos clave que subyacen a la arquitectura del SDK.

### Módulos

Una puerta de enlace se crea con el SDK de puerta de enlace de Azure mediante la creación y el ensamblado de *módulos*. Los módulos utilizan *mensajes* para intercambiarse datos. Un módulo recibe un mensaje, realiza alguna acción en él, opcionalmente lo transforma en un nuevo mensaje y luego lo publica para que otros módulos lo procesen. Existe la posibilidad de que algunos módulos solo produzcan nuevos mensajes y nunca procesen los mensajes entrantes. Una cadena de módulos crea una canalización de procesamiento de datos donde cada módulo realiza una transformación en los datos en un punto de esa canalización.

![][1]
 
El SDK está formado por los siguientes componentes:

- Módulos ya escritos que realizan funciones comunes de puerta de enlace.
- Las interfaces que un desarrollador puede utilizar para escribir módulos personalizados.
- La infraestructura necesaria para implementar y ejecutar un conjunto de módulos.

El SDK proporciona una capa de abstracción que permite crear puertas de enlace para trabajar en una diversidad de sistemas operativos y plataformas.

![][2]

### error de Hadoop

Aunque una manera práctica de conceptualizar cómo funciona una puerta de enlace es pensar que los módulos se pasan mensajes entre ellos, no refleja realmente lo que sucede. Los módulos utilizan un bus de mensajes para comunicarse entre sí, publican mensajes en el bus y el bus difunde los mensajes a todos los módulos conectados a él.

Un módulo usa la función **MessageBus\_Publish** para publicar un mensaje en el bus de mensajes. El bus de mensajes entrega los mensajes a un módulo mediante la invocación de una función de devolución de llamada. Un mensaje consta de un conjunto de propiedades de clave/valor y del contenido que se pasa como un bloque de memoria.

![][3]

Cada módulo es responsable de filtrar los mensajes, ya que el bus de mensajes utiliza un mecanismo de difusión para entregar cada mensaje a cada módulo conectado a él. Un módulo debe actuar solo en los mensajes que van dirigidos a él. El filtrado de mensajes crea de manera eficaz la canalización de los mensajes. Un módulo normalmente filtra los mensajes que recibe usando las propiedades del mensaje para identificar aquellos que debe procesar.

## Arquitectura de ejemplo Hello World

El ejemplo Hello World ilustra los conceptos descritos en la sección anterior. El ejemplo Hello World implementa una puerta de enlace que tiene una canalización formada por dos módulos:

-	El módulo *hello world* crea un mensaje cada cinco segundos y lo pasa al módulo logger.
-	El módulo *logger* escribe los mensajes que recibe en un archivo.

![][4]

Como se ha descrito en la sección anterior, el módulo Hello World no pasa los mensajes directamente al módulo logger cada cinco segundos. Lo que hace es publicar un mensaje en el bus de mensajes cada cinco segundos.

El módulo logger recibe el mensaje del bus de mensajes y examina sus propiedades en un filtro. Si este módulo determina que debe procesar el mensaje, escribe el contenido en un archivo.

El módulo logger solo consume mensajes del bus de mensajes, no publica nunca nuevos mensajes en el bus.

![][5]

La ilustración anterior muestra la arquitectura del ejemplo Hello World y las rutas de acceso relativas a los archivos de origen que implementan diferentes partes del ejemplo en el [repositorio][lnk-gateway-sdk]. Explore el código por su cuenta, o use los siguientes fragmentos de código como guía.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

