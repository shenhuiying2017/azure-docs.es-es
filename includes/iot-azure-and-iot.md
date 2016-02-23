# Azure y el Internet de las cosas

Bienvenido a Microsoft Azure e Internet de las cosas (IoT). En este artículo se presenta la arquitectura de una solución típica de IoT que describe las características comunes de una solución de IoT que se podría implementar mediante los servicios de Azure. Una solución típica de IoT requiere una comunicación segura y bidireccional entre dispositivos (que pueden ascender a millones) y un back-end de soluciones que, por ejemplo, utilice el análisis predictivo y automatizado para obtener información útil de su secuencia de eventos de dispositivo a nube.

## Arquitectura de solución IoT

El diagrama siguiente muestra la arquitectura típica de una solución de IoT. Tenga en cuenta que dicha arquitectura no incluye los nombres de ningún servicio de Azure específico, pero describe los elementos claves de la arquitectura de una solución de IoT genérica. Las secciones siguientes proporcionan más información sobre los elementos de dicha solución.

![Arquitectura de solución IoT][img-solution-architecture]

### Conectividad de dispositivos

En escenarios típicos de IoT, los dispositivos envían telemetría (como lecturas de temperatura) a un punto de conexión de la nube para su almacenamiento y procesamiento. Los dispositivos también pueden recibir y responder a los comandos de nube a dispositivo mediante la lectura de mensajes desde un extremo de la nube. Por ejemplo, un dispositivo puede recuperar un comando que proporcione instrucciones para cambiar la frecuencia con la que realiza el muestreo de los datos.

Uno de los mayores desafíos relacionados con los proyectos de IoT consiste en conectar dispositivos al back-end de soluciones de manera segura y fiable. Normalmente, los dispositivos de IoT tienen características diferentes de otros clientes, como los exploradores y las aplicaciones móviles. Dispositivos IoT:

- A menudo son sistemas insertados sin operador humano.
- Pueden encontrarse en ubicaciones remotas, donde el acceso físico resulta muy costoso.
- Es posible que solo sean accesibles a través del back-end de soluciones.
- Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
- Es posible que tengan conectividad de red intermitente, lenta o costosa.
- Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.
- Pueden crearse mediante un amplio conjunto de plataformas populares de hardware y software.

Además de los requisitos anteriores, toda solución de IoT debe ser capaz de ofrecer escalabilidad, seguridad y fiabilidad. Esto da lugar a un conjunto de requisitos de conectividad cuya implementación resulta compleja y lenta si se realiza con tecnologías tradicionales como, por ejemplo, los contenedores web y los agentes de mensajería.

Un dispositivo puede comunicarse directamente con un punto de conexión de la puerta de enlace de la nube, o si el dispositivo no puede usar ninguno de los protocolos de comunicaciones que admite la puerta de enlace de nube, es posible establecer conexión a través de una puerta de enlace intermedia que realiza la traducción de protocolos.

### Procesamiento de datos y análisis

En la nube, un back-end de soluciones de IoT:

- Recibe la telemetría a escala de los dispositivos y determina cómo procesar y almacenar esos datos. 
- También puede permitirle enviar comandos desde la nube a dispositivos específicos.
- Proporciona funcionalidades de registro de dispositivos que le permiten aprovisionar dispositivos y controlar qué dispositivos pueden conectarse a la infraestructura.
- Le permite realizar un seguimiento del estado de los dispositivos y supervisar sus actividades.

Las soluciones de IoT pueden incluir bucles de comentarios automáticos. Por ejemplo, un módulo de aprendizaje automático en el back-end puede identificar a partir de los datos de telemetría que la temperatura de un dispositivo específico está por encima de los niveles de funcionamiento normales y enviar un comando al dispositivo, lo que le permite realizar una acción correctora.

### Presentación

Muchas soluciones de IoT permiten a los usuarios ver y analizar los datos recopilados desde sus dispositivos. Estas vistas pueden adoptar la forma de paneles o informes de BI.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=AcomDC_0218_2016-->