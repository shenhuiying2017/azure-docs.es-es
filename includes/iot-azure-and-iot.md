
# <a name="azure-and-the-internet-of-things"></a>Azure y e Internet de las cosas

Bienvenido a Microsoft Azure e Internet de las cosas (IoT). En este artículo se describen las características comunes de una solución de IoT en la nube. Las soluciones de IoT típicas requieren comunicación bidireccional segura entre dispositivos, posiblemente millones, y un back-end de soluciones. Por ejemplo, una solución puede usar un análisis predictivo y automatizado para obtener información del flujo de eventos del dispositivo a la nube.

## <a name="iot-solution-architecture"></a>Arquitectura de solución IoT

El diagrama siguiente muestra los elementos clave de una arquitectura típica de solución de IoT. El diagrama es independiente de los detalles de implementación específicos, como los servicios de Azure utilizados y los sistemas operativos de los dispositivos. En esta arquitectura, los dispositivos de IoT recopilan datos que luego envían a una puerta de enlace en la nube. La puerta de enlace de nube hace que los datos estén disponibles para el procesamiento por otros servicios back-end. Estos servicios de back-end proporcionan datos para:

* Otras aplicaciones de línea de negocio.
* Operadores humanos a través de un panel u otro dispositivo de presentación.

![Arquitectura de solución IoT][img-solution-architecture]

> [!NOTE]
> Para ver un análisis detallado de la arquitectura de IoT, consulte el PDF [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Arquitectura de referencia de IoT de Microsoft Azure).

### <a name="device-connectivity"></a>Conectividad de dispositivos

En una arquitectura de solución de IoT, los dispositivos suelen enviar telemetría a la nube para el almacenamiento y el procesamiento. Por ejemplo, en un escenario de mantenimiento predictivo, el back-end de la solución podría usar el flujo de datos del sensor para determinar el momento en el que una bomba determinada necesita mantenimiento. Los dispositivos también pueden recibir y responder a los mensajes de nube a dispositivo mediante la lectura de mensajes desde un punto de conexión en la nube. En el mismo ejemplo, el back-end de la solución podría enviar mensajes a otras bombas del centro de bombeo para empezar a redirigir flujos justo antes del inicio del mantenimiento. Con este procedimiento se garantiza que el ingeniero de mantenimiento puede empezar en cuanto llegue.

La conexión segura de dispositivos y la confiabilidad suele ser el mayor reto de las soluciones de IoT. Esto se debe a que los dispositivos de IoT tienen características diferentes de otros clientes, como los exploradores y las aplicaciones para dispositivos móviles. En concreto, los dispositivos de IoT:

* A menudo son sistemas insertados sin operador humano (a diferencia de un teléfono).
* Se pueden implementar en ubicaciones remotas, donde el acceso físico resulta costoso.
* Es posible que solo sean accesibles a través del back-end de soluciones. No hay ninguna otra manera de interactuar con el dispositivo.
* Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
* Es posible que tengan conectividad de red intermitente, lenta o costosa.
* Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.
* Pueden crearse mediante un amplio conjunto de plataformas populares de hardware y software.

Además de las restricciones anteriores, cualquier solución de IoT también debe ser escalable, segura y confiable.

En función del protocolo de comunicación y la disponibilidad de la red, los dispositivos se pueden comunicar con la nube tanto directamente como a través de una puerta de enlace intermedia. Las arquitecturas de IoT suelen tener una combinación de estos dos patrones de comunicación.

### <a name="data-processing-and-analytics"></a>Procesamiento de datos y análisis

En las soluciones de IoT modernas, el procesamiento de datos se produce en la nube o en el dispositivo. El procesamiento en el dispositivo se conoce como *informática perimetral*. La elección de dónde procesar los datos depende de factores como:

* Las restricciones de red. Un ancho de banda entre los dispositivos y la nube limitado motiva el procesamiento perimetral.
* El tiempo de respuesta. Si hay un requisito para actuar en un dispositivo casi en tiempo real, puede ser mejor procesar la respuesta en el propio dispositivo. Por ejemplo, un brazo robotizado que deba detenerse en caso de emergencia.
* Un entorno reglamentario. Algunos datos no se pueden enviar a la nube.

En general, el procesamiento de datos tanto en el perímetro como en la nube es una combinación de las siguientes funcionalidades:

* Recepción de la telemetría a escala de los dispositivos y determinación de cómo procesar y almacenar esos datos.
* Análisis de la telemetría para proporcionar información detallada, ya sea en tiempo real o después de que se produzcan los hechos.
* Envío de comandos de la nube o un dispositivo de puerta de enlace a un dispositivo específico.

Además, un back-end de IoT en la nube proporciona:

* Funcionalidades de registro de dispositivos que le permiten:
    * Aprovisionar dispositivos.
    * Controlar los dispositivos que tienen permiso para conectarse a la infraestructura.
* Administración de los dispositivos para controlar el estado de los dispositivos y supervisar sus actividades.

Por ejemplo, en el escenario de mantenimiento predictivo, el back-end en la nube almacena datos de telemetría históricos. La solución utiliza estos datos para identificar posibles comportamientos anómalos en bombas concretas antes de que causen un problema real. Mediante el análisis de los datos, identifica que la solución preventiva va a devolver un comando al dispositivo para emprender una acción correctiva. Este proceso genera un bucle de realimentación automatizado entre el dispositivo y la nube que aumenta en gran medida la eficacia de la solución.

### <a name="presentation-and-business-connectivity"></a>Presentación y conectividad empresarial

El nivel de presentación y conectividad empresarial permite a los usuarios finales interactuar con la solución de IoT y con los dispositivos. Permite a los usuarios ver y analizar los datos recopilados desde sus dispositivos. Estas vistas pueden adoptar la forma de paneles o informes de BI que pueden mostrar tanto datos históricos como datos prácticamente en tiempo real. Por ejemplo, un operador puede comprobar el estado de un centro de bombeo determinado y ver las alertas generadas por el sistema. Esta capa también permite la integración del back-end de la solución de IoT con las aplicaciones de línea de negocio existentes para conectarse con los procesos o flujos de trabajo empresariales. Por ejemplo, una solución de mantenimiento predictivo puede integrarse con un sistema de programación que reserve la visita a un centro de bombeo para un ingeniero cuando identifique una bomba que necesite mantenimiento.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
