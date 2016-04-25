# Azure y el Internet de las cosas

Bienvenido a Microsoft Azure e Internet de las cosas (IoT). En este artículo se presenta la arquitectura de una solución de IoT que describe las características comunes de una solución de IoT que se podría implementar mediante los servicios de Azure. Una solución de IoT requiere una comunicación segura y bidireccional entre dispositivos (que pueden ascender a millones) y un back-end de soluciones que, por ejemplo, utilice el análisis predictivo y automatizado para obtener información útil de la secuencia de eventos de su dispositivo a la nube.

El Centro de IoT de Azure es un componente fundamental cuando se implementa esta arquitectura de solución de IoT mediante los servicios de Azure. El Conjunto de aplicaciones de IoT proporciona implementaciones completas, de un extremo a otro, de esta arquitectura para escenarios específicos de IoT. Por ejemplo, la solución *supervisión remota* le permite supervisar el estado de dispositivos como las máquinas expendedoras y *mantenimiento predictivo* le ayuda a anticiparse a las necesidades de mantenimiento de dispositivos como las bombas de centros de bombeo remotos y a evitar el tiempo de inactividad no programado.

## Arquitectura de solución IoT

El diagrama siguiente muestra la arquitectura típica de una solución de IoT. Tenga en cuenta que dicha arquitectura no incluye los nombres de ningún servicio de Azure específico, pero describe los elementos claves de la arquitectura de una solución de IoT genérica. En esta arquitectura, los dispositivos de IoT recopilan datos que luego envían a una puerta de enlace en la nube. La puerta de enlace en la nube hace que los datos estén disponibles para su procesamiento por otros servicios de back-end desde donde se entregarán a otras aplicaciones de línea de negocio o a operadores humanos mediante un panel u otro dispositivo de presentación.

![Arquitectura de solución IoT][img-solution-architecture]

> [AZURE.NOTE] Para ver un análisis detallado de la arquitectura de IoT, consulte [Microsoft Azure IoT Reference Architecture (Arquitectura de referencia de IoT de Microsoft Azure)][lnk-refarch].

### Conectividad de dispositivos

En esta arquitectura de solución de IoT, los dispositivos envían telemetría (como lecturas de sensor de un centro de bombeo) a un punto de conexión de la nube para su almacenamiento y procesamiento. En un escenario de mantenimiento predictivo, el back-end podría usar la transmisión de datos del sensor para determinar el momento en que una bomba determinada necesita mantenimiento. Los dispositivos también pueden recibir y responder a los comandos de nube a dispositivo mediante la lectura de mensajes desde un extremo de la nube. Por ejemplo, en el escenario de mantenimiento predictivo, el back-end de soluciones podría enviar comandos a otras bombas del centro de bombeo para empezar a redirigir flujos justo antes del inicio del mantenimiento a fin de asegurarse de que el ingeniero de mantenimiento pudiera empezar tan pronto como llegara.

Uno de los mayores desafíos de los proyectos de IoT consiste en conectar dispositivos al back-end de soluciones de manera segura y fiable para permitir que el dispositivo envíe telemetría y reciba comandos. Los dispositivos de IoT tienen características diferentes de otros clientes, como los exploradores y las aplicaciones móviles. Dispositivos IoT:

- A menudo son sistemas insertados sin operador humano.
- Se pueden implementar en ubicaciones remotas, donde el acceso físico resulta muy costoso.
- Es posible que solo sean accesibles a través del back-end de soluciones. No hay ninguna otra manera de interactuar con el dispositivo.
- Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
- Es posible que tengan conectividad de red intermitente, lenta o costosa.
- Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.
- Pueden crearse mediante un amplio conjunto de plataformas populares de hardware y software.

Además de los requisitos anteriores, toda solución de IoT debe ser capaz de ofrecer escalabilidad, seguridad y fiabilidad. Esto da lugar a un conjunto de requisitos de conectividad cuya implementación resulta compleja y lenta si se realiza con tecnologías tradicionales como, por ejemplo, los contenedores web y los agentes de mensajería. El Centro de IoT de Azure y los SDK de dispositivos IoT facilitan la implementación de soluciones que cumplen estos requisitos.

Un dispositivo puede comunicarse directamente con un punto de conexión de puerta de enlace de nube, o si no puede usar ninguno de los protocolos de comunicaciones que admite la puerta de enlace de nube, puede establecer conexión a través de una puerta de enlace intermedia, como la [puerta de enlace de protocolo del Centro de IoT][lnk-protocol-gateway], que realiza la traducción de protocolos. Por ejemplo, desde el Protocolo industrial común (CIP, Common Industrial Protocol) a AMQPS.

### Procesamiento de datos y análisis

En la nube, un back-end de soluciones de IoT es el lugar en que se produce la mayor parte del procesamiento de datos en la solución, concretamente, el filtrado y la incorporación de telemetría y su enrutamiento a otros servicios. El back-end de soluciones de IoT:

- Recibe la telemetría a escala de los dispositivos y determina cómo procesar y almacenar esos datos. 
- Puede permitirle enviar comandos desde la nube a dispositivos específicos.
- Proporciona funcionalidades de registro de dispositivos que le permiten aprovisionar dispositivos y controlar qué dispositivos pueden conectarse a la infraestructura.
- Le permite realizar un seguimiento del estado de los dispositivos y supervisar sus actividades.

En el escenario de mantenimiento predictivo, el back-end de soluciones almacena los datos de telemetría históricos para identificar patrones y analiza la telemetría a medida que llega para detectar los patrones que indican la necesidad de mantenimiento en una bomba determinada.

Las soluciones de IoT pueden incluir bucles de comentarios automáticos. Por ejemplo, un módulo de análisis en el back-end puede identificar a partir de la telemetría que la temperatura de un dispositivo específico está por encima de los niveles de funcionamiento normales y enviar un comando al dispositivo indicándole que tome una medida correctora.

### Presentación y conectividad empresarial

El nivel de presentación y conectividad empresarial permite a los usuarios finales interactuar con la solución de IoT y con los dispositivos. Permite a los usuarios ver y analizar los datos recopilados desde sus dispositivos. Estas vistas pueden adoptar la forma de paneles o informes de BI que pueden mostrar tanto datos históricos como datos prácticamente en tiempo real. Por ejemplo, un operador puede comprobar el estado de un centro de bombeo determinado y ver las alertas generadas por el sistema. Este nivel también permite la integración del back-end de soluciones de IoT con las aplicaciones de línea de negocio existentes para conectarse con los procesos empresariales o flujos de trabajo. Por ejemplo, la solución de mantenimiento predictivo puede integrarse con un sistema de programación que reserve a un ingeniero para visitar un centro de bombeo siempre que la solución identifique una bomba que necesita mantenimiento.

![Panel de soluciones de IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

<!---HONumber=AcomDC_0413_2016-->