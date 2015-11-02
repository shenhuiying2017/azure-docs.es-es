# Microsoft Azure e Internet de las cosas (IoT)

Las soluciones IoT típicas requieren comunicación bidireccional segura entre dispositivos, posiblemente millones, y una aplicación back-end. Algunos ejemplos de funcionalidad de aplicación back-end pueden incluir el procesamiento de eventos de dispositivo a nube para descubrir información sobre el uso de análisis predictivos y automatizados.

Microsoft proporciona un conjunto de bibliotecas (que admiten varios lenguajes y distintas plataformas de hardware) que puede usar para desarrollar aplicaciones cliente para ejecutarlas en un dispositivo IoT. Para implementar la aplicación back-end IoT, puede combinar varios servicios de Azure o usar una de las soluciones preconfiguradas disponibles en el [conjunto de aplicaciones de IoT de Azure][]. Para comprender cómo permite Azure esta infraestructura IoT, resulta útil considerar una arquitectura de solución IoT típica.

## Arquitectura de solución IoT

El diagrama siguiente muestra la arquitectura típica de una solución de IoT. Tenga en cuenta que dicha arquitectura no incluye los nombres de ningún servicio de Azure específico, pero describe los elementos claves de la arquitectura de una solución de IoT genérica. Las secciones siguientes proporcionan más información sobre los elementos de dicha solución.

![Arquitectura de solución IoT][img-solution-architecture]

### Conectividad de dispositivos

En escenarios típicos de IoT, los dispositivos envían datos de telemetría de dispositivo a nube como, por ejemplo, las lecturas de temperatura, a un extremo de la nube para su almacenamiento y procesamiento. Los dispositivos también pueden recibir y responder a los comandos de nube a dispositivo mediante la lectura de mensajes desde un extremo de la nube. Por ejemplo, un dispositivo puede recuperar un comando que proporcione instrucciones para cambiar la frecuencia con la que realiza el muestreo de los datos.

Un dispositivo u origen de datos de una solución IoT puede ser desde un sensor sencillo conectado a la red a un dispositivo informático independiente y eficaz. Un dispositivo puede tener limitaciones de capacidad de procesamiento, memoria y ancho de banda de comunicación y ofrecer compatibilidad con el protocolo de comunicación.

Un dispositivo puede comunicarse directamente con un extremo de puerta de enlace de nube mediante un protocolo de comunicación como, por ejemplo, AMQP o HTTP, o bien mediante algún intermediario como, por ejemplo, una puerta de enlace de campo que proporciona un servicio de traducción de protocolos.

### Procesamiento de datos y análisis

En la nube, un procesador de eventos de secuencias recibe mensajes de dispositivo a nube a escala de los dispositivos y determina cómo procesar y almacenar dichos mensajes. Una solución para los dispositivos conectados permite enviar datos de nube a dispositivo en forma de comandos para dispositivos específicos. El registro de dispositivos con la solución IoT permite aprovisionar dispositivos y controlar qué dispositivos pueden conectarse a la infraestructura. El back-end permite realizar un seguimiento del estado de los dispositivos y supervisar sus actividades.

Las soluciones de IoT pueden incluir bucles de comentarios automáticos. Por ejemplo, un módulo de aprendizaje automático puede identificar a partir de los datos de telemetría de dispositivo a nube que la temperatura de un dispositivo específico está por encima de los niveles de funcionamiento normales y enviar un comando al dispositivo, lo que le permite realizar una acción correctora.

### Presentación

Muchas soluciones de IoT permiten a los usuarios ver y analizar los datos recopilados desde sus dispositivos. Estas vistas pueden adoptar la forma de paneles o informes de BI.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[conjunto de aplicaciones de IoT de Azure]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Oct15_HO4-->