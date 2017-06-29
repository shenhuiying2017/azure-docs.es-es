> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

En este artículo se ofrece un tutorial detallado sobre el [código de ejemplo Hola mundo][lnk-helloworld-sample] para ilustrar los componentes fundamentales de la arquitectura de [Azure IoT Edge][lnk-iot-edge]. En el ejemplo se usa Azure IoT Edge para crear una puerta de enlace simple que registra un mensaje "hola mundo" en un archivo cada cinco segundos.

En este tutorial, se describen los siguientes procedimientos:

* **Arquitectura de ejemplo de Hola mundo**: describe cómo se aplican los [conceptos de arquitectura de Azure IoT Edge][lnk-edge-concepts] al ejemplo Hola mundo y cómo funcionan conjuntamente los componentes.
* **Compilación del ejemplo**: los pasos necesarios para compilar el ejemplo.
* **Ejecución del ejemplo**: los pasos necesarios para ejecutar el ejemplo. 
* **Salida típica**: un ejemplo de la salida esperada al ejecutar el ejemplo.
* **Fragmentos de código**: una colección de fragmentos de código para mostrar cómo el ejemplo Hola mundo implementa los principales componentes de puerta de enlace de IoT Edge.


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
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md