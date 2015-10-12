<properties
 pageTitle="Orientación sobre el Centro de IoT de Azure | Microsoft Azure"
 description="Una colección de temas de orientación para soluciones que usan el Centro de IoT de Azure."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Orientación sobre el Centro de IoT de Azure

## Comparación: Centro de IoT y Centros de eventos
Uno de los principales usos del Centro de IoT de Azure es recopilar telemetría de dispositivos. Por este motivo, el Centro de IoT suele compararse con los [Centros de eventos], que son un servicio de procesamiento de eventos que ofrece entrada de telemetría y eventos en la nube a escala masiva, con una latencia baja y una confiabilidad alta.

Los servicios, sin embargo, tienen muchas diferencias que se detallan en las secciones siguientes.

| Ámbito | Centro de IoT | Centros de eventos |
| ---- | ------- | ---------- |
| Patrones de comunicación | Entrada de eventos de dispositivo a nube y mensajería de nube a dispositivo. | Solo ingreso de eventos (normalmente se consideran escenarios dispositivo a nube). |
| Seguridad | Identidad por dispositivo y control de acceso revocable. Consulte la [Guía para desarrolladores del Centro de IoT - Seguridad ]. | [Directivas de acceso compartido][Event Hub - security] para todo el Centro de eventos, con soporte para revocación limitada mediante [directivas de publicador][Event Hub publisher policies]. En el contexto de las soluciones IoT, a menudo es necesario implementar una solución personalizada para admitir las credenciales por dispositivo y las medidas contra la suplantación de la identidad. |
| Escala | Los Centros de IoT están optimizados para admitir millones de dispositivos conectados al mismo tiempo. | Los Centros de eventos pueden admitir un número más reducido de conexiones simultáneas: hasta 5000 conexiones AMQP, según las [cuotas de Bus de servicio]. Por otro lado, los Centros de eventos permiten a los usuarios especificar la partición para cada mensaje enviado. |
| SDK de dispositivo | El Centro de IoT proporciona [SDK de dispositivo][Azure IoT Hub SDKs] para una gran variedad de plataformas y lenguajes | Los Centros de eventos son compatibles con .NET, C y proporcionan interfaces de envío HTTP y AMQP. |

En conclusión, aunque el único uso sea la entrada de telemetría de dispositivo a nube, el Centro de IoT proporciona un servicio que está diseñado específicamente para la conectividad de dispositivos IoT, y continuará ampliando las propuestas de valor para estos escenarios con características específicas de IoT. Los Centros de eventos están diseñados para la entrada de eventos a gran escala, tanto en escenarios de conexión entre centros de datos y dentro del propio centro de datos. No es raro usar el Centro de IoT y los Centros de eventos conjuntamente, y dejar que el primero controle la comunicación de dispositivo a nube y que el segundo incorpore eventos de fase posterior a los motores de procesamiento en tiempo real.

## Aprovisionamiento de dispositivos <a id="provisioning"></a>
Las soluciones IoT mantienen la información de los dispositivo en diversos sistemas y almacenes, el [registro de identidades del Centro de IoT][IoT Hub Developer Guide - identity registry] es uno de ellos, entre otros almacenes que mantienen información de dispositivos específica de la aplicación. Llamamos *aprovisionamiento* al proceso de creación de la información de dispositivo necesaria en todos estos sistemas.

Hay muchos requisitos y estrategias para el aprovisionamiento de dispositivos (consulte la [Guía de administración de dispositivos del Centro de IoT] para obtener más información). El [registro de identidades del Centro de IoT][IoT Hub Developer Guide - identity registry] proporciona las API necesarias para integrar el Centro de IoT en el proceso de aprovisionamiento.

## Puertas de enlace de campo <a id="fieldgateways"></a>
Una puerta de enlace de campo es un dispositivo especializado o un software de uso general que actúa como un activador de comunicación y potencialmente, como un sistema de control de dispositivos local y centro de procesamiento de datos de dispositivo. Una puerta de enlace de campo puede realizar las funciones de procesamiento y control locales hacia los dispositivos; en el otro lado, puede filtrar o agregar la telemetría del dispositivo y, por tanto, reducir la cantidad de datos que se transfieren en el back-end.

El ámbito de una puerta de enlace de campo incluye la puerta de enlace de campo en sí y todos los dispositivos que están conectados a ella. Como su nombre indica, las puertas de enlace de campo actúan fuera de las instalaciones de procesamiento de datos dedicadas y suelen estar enlazadas a la ubicación. Una puerta de enlace de campo es diferente de un enrutador de tráfico simple, que tiene un rol activo en la administración del flujo de información y el acceso. Esto significa que es una entidad dirigida a la aplicación y un terminal de sesión o conexión de red (por ejemplo, en este contexto, las puertas de enlace pueden ayudar en el aprovisionamiento de dispositivos, la transformación de datos, la traducción de protocolos y el procesamiento de reglas de evento). Por el contrario, los dispositivos NAT o los firewalls no se consideran puertas de enlace de campo porque no son terminales de sesión o de conexión explícitos, sino que enrutan o deniegan las conexiones o sesiones que se realizan a través de ellos.

### Puertas de enlace de campo opacas frente a transparentes
Con respecto a las identidades de los dispositivos, la puerta de enlace de campo se denomina *transparente* si otros dispositivos en su ámbito tienen entradas de identidad de dispositivo en el registro de identidades del Centro de IoT. Se denominan *opacos* si la única identidad en el registro de identidades del Centro de IoT es la identidad de la puerta de enlace de campo.

Es importante tener en cuenta que el uso de puertas de enlace *opacas* impide que el Centro de IoT proporcione [protección con la suplantación de identidad de dispositivo][IoT Hub Developer Guide - Anti-spoofing]. Además, como todos los dispositivos en el ámbito de la puerta de enlace de campo se representan como un único dispositivo en el Centro de IoT, estarán sujetos a las cuotas y limitaciones de un solo dispositivo.

### Otras consideraciones

Al implementar una puerta de enlace de campo puede usar los SDK de dispositivo IoT de Azure. Algunos SDK proporcionan funcionalidad específica de la puerta de enlace de campo, como la capacidad de multiplexar la comunicación de varios dispositivos en la misma conexión al Centro de IoT. Tal y como se explica en la [Guía para el desarrollador del Centro de IoT - Elegir el protocolo de comunicación], debe evitar usar HTTP/1 como protocolo de transporte para puertas de enlace de campo.

## Uso de esquemas y servicios de autenticación de dispositivos personalizados. <a id="customauth"></a>
El Centro de IoT de Azure permite configurar las credenciales de seguridad de cada dispositivo y el control de acceso mediante el uso del [registro de identidades de dispositivo][IoT Hub Developer Guide - identity registry]. Si una solución IoT ya realizó una inversión importante en un esquema de autenticación o de registro de identidades de dispositivo personalizado, puede seguir aprovechando las demás características del Centro de IoT creando un *servicio de token* para el Centro de IoT.

El servicio de token es un servicio en la nube que se implementa automáticamente, que usa una directiva de acceso compartido del Centro de IoT con permisos **DeviceConnect** para crear tokens *orientados al dispositivo*.

  ![][img-tokenservice]

Estos son los pasos principales del modelo de servicio de tokens.

1. Cree una [directiva de acceso compartido del Centro de IoT][IoT Hub Developer Guide - Security] con permisos **DeviceConnect** para el Centro de IoT. El servicio de tokens usará esta directiva para firmar los tokens.
2. Cuando un dispositivo desea tener acceso al Centro de IoT, solicita un token firmado al servicio de tokens. El dispositivo puede usar el esquema de autenticación o registro de identidades de dispositivo personalizado.
3. El servicio de tokens devuelve un token, creado tal y como se describe en la [Guía para desarrolladores del Centro de IoT - Seguridad], usando `/devices/{deviceId}` como `resourceURI`, con `deviceId` como dispositivo que se va a autenticar.
4. El dispositivo usa el token directamente con el Centro de IoT.

El servicio de token puede establecer la caducidad de los tokens como desee. Al caducar, el Centro de IoT servirá la conexión y el dispositivo tendrá que solicitar un nuevo token al servicio de token. Claramente, un tiempo de expiración corto aumentará la carga tanto en el dispositivo como en el servicio de token.

Merece la pena especificar que aún hay que crear la identidad del dispositivo en el Centro de IoT para que el dispositivo pueda conectarse. Esto significa también que el control de acceso por dispositivo (deshabilitando las identidades de dispositivo según se indica en la [Guía del desarrollador del Centro de IoT - Registro de identidades]) sigue siendo funcional, aunque el dispositivo se autentique con un token. Esto mitiga la existencia de tokens de larga duración.

### Comparación con una puerta de enlace personalizada
El modelo de servicio de token es el método recomendado para implementar un esquema de registro y autenticación de identidades personalizado en el Centro de IoT, porque permite al Centro de IoT controlar la mayoría del tráfico de la solución. Hay casos, sin embargo, en los que el esquema de autenticación personalizado está tan imbricado con el protocolo (por ejemplo, [TLS-PSK]) que se necesita un servicio que procese todo el tráfico (*puerta de enlace personalizada*). Consulte el artículo [Puerta de enlace de protocolo] para obtener más información.

## Escalado del Centro de IoT
El Centro de IoT puede admitir hasta un millón de dispositivos conectados al mismo tiempo; para ello, se aumenta el número de S1 o S2 del Centro de IoT a 2.000. Consulte [Precios del Centro de IoT][lnk-pricing] para obtener más información.

Cada unidad del Centro de IoT permite un número determinado de identidades de dispositivo en el Registro, todos los cuales se pueden conectar simultáneamente, y un número diario de mensajes.

Para escalar correctamente su solución, debe tener en cuenta el uso particular que haga del Centro de IoT. En concreto, tenga en cuenta la capacidad de procesamiento máxima requerida para las siguientes categorías de operaciones:

* Mensajes de dispositivo a nube
* Mensajes de nube a dispositivo
* Operaciones de registro de identidad

Además de esta información sobre la capacidad de procesamiento, recuerde consultar el tema sobre [Cuotas y limitaciones del Centro de IoT] y diseñar su solución en consecuencia.

### Capacidad de procesamiento para los mensajes de dispositivo a nube y de nube a dispositivo
La mejor forma de dimensionar una solución de Centro de IoT es evaluar el tráfico en cada dispositivo.

Los mensajes de dispositivo a nube siguen estas directrices de capacidad de procesamiento sostenida.

| Nivel: | Capacidad de procesamiento sostenida | Velocidad de envío sostenida |
| ---- | -------------------- | ------------------- |
| S1 | hasta 8 kb/m por dispositivo | promedio de 4 mensajes por hora por dispositivo |
| S2 | hasta 4 kb/m por dispositivo | promedio de 2 mensajes por minuto por dispositivo |

Al recibir los mensajes de dispositivo a nube, la aplicación back-end puede esperar el rendimiento máximo siguiente (a través de todos los lectores).

| Nivel: | Capacidad de procesamiento sostenida |
| ---- | -------------------- |
| S1 | hasta 120 Kb/m por unidad, con un mínimo de 2 Mb/s |
| S2 | hasta 4 Mb/m por unidad, con un mínimo de 2 Mb/s |

El rendimiento de los mensajes de nube a dispositivo escala en cada dispositivo, y cada dispositivo recibe hasta 5 mensajes por minuto.

### Capacidad de procesamiento para las operaciones de registro de identidad
Las operaciones de registro de identidad del Centro de IoT no deberían ser operaciones en tiempo de ejecución porque tienen que ver principalmente con el aprovisionamiento de dispositivos. Consulte las cifras de rendimiento específicas en [Cuotas y limitaciones del Centro de IoT].

### Clave de particionamiento
Aunque un único Centro de IoT puede escalar a millones de dispositivos, a veces la solución requiere características de rendimiento específicas que un único Centro de IoT no puede garantizar. En ese caso, se recomienda particionar los dispositivos en varios Centros de IoT, con el fin de suavizar los picos de tráfico y obtener la capacidad de procesamiento y las velocidades de funcionamiento necesarias.

## Alta disponibilidad y recuperación ante desastres
Como un servicio de Azure, el Centro de IoT proporciona alta disponibilidad (HA) usando redundancias en la región de Azure, sin que la solución tenga que realizar ningún trabajo adicional. Además, Azure ofrece una serie de características que ayudan a crear soluciones con funcionalidad de recuperación ante desastres o disponibilidad entre regiones, si es necesario. Las soluciones deben diseñarse y prepararse para aprovechar las ventajas de esas características y poder proporcionar alta disponibilidad global entre regiones a los dispositivos o usuarios. El artículo [Orientación técnica de la continuidad del negocio de Azure] describe las características de Azure integradas para la continuidad del negocio y la recuperación ante desastres. El documento [Consideraciones sobre la alta disponibilidad y la recuperación ante desastres para las aplicaciones de Azure] proporciona orientación arquitectónica sobre las estrategias para las aplicaciones de Azure lograr alta disponibilidad y recuperación ante desastres.

## Conmutación por error regional con el Centro de IoT
La descripción completa de las topologías de implementación de soluciones IoT queda fuera del ámbito de esta sección pero, para fines de alta disponibilidad y recuperación ante desastres, tendremos en cuenta el modelo de implementación de *conmutación por error*.

En un modelo regional de conmutación por error, el back-end de la solución se ejecutará principalmente en una ubicación de centro de datos, pero se implementará un Centro de IoT y un back-end adicional en una región del centro de datos con fines de conmutación por error, en caso de que el Centro de IoT en el centro de datos principal sufra una interrupción o que la conectividad de red desde el dispositivo al centro de datos principal deba interrumpirse de algún modo. Los dispositivos usan un extremo de servicio secundario siempre que no se puede alcanzar la puerta de enlace principal. Con la capacidad de conmutación por error entre regiones, la disponibilidad de la solución puede mejorar más allá de la alta disponibilidad de una única región.

A grandes rasgos, para implementar un modelo de conmutación por error regional con un Centro de IoT, necesitará la siguiente.

* **Un Centro de IoT secundario y lógica de enrutamiento de dispositivo**: si se produce una interrupción del servicio en la región primaria, los dispositivos necesitan empezar a conectarse a la región secundaria. Dado el estado de la mayoría de los servicios implicados, es habitual que los administradores de la solución desencadenen el proceso de conmutación por error entre regiones. La mejor forma de comunicar el nuevo extremo a los dispositivos, sin perder el control del proceso, es hacer que comprueben periódicamente un servicio de *conserje* para el extremo activo actual. El servicio de conserje puede ser una aplicación web simple que se replica y se mantiene accesible mediante técnicas de redirección de DNS (por ejemplo, con [Administrador de tráfico de Azure]).
* **Replicación del registro de identidades**: para que se pueda usar, el Centro de IoT secundario deberá contener todas las identidades de dispositivo que tienen que poder conectarse a la solución. La solución debe conservar copias de seguridad de replicación geográfica de las identidades de dispositivo y cargarlas en el Centro de IoT secundario antes de cambiar el extremo activo para los dispositivos. La funcionalidad de exportación de identidades de dispositivo del Centro de IoT es muy útil en este contexto, [Guía del desarrollador de Centros de IoT - Registro de identidades].
* **Lógica de combinación**: cuando la región primaria vuelve a estar disponible, todos los estados y datos que se crearon en el sitio secundario deben volver a migrarse a la región principal. Esto tiene que ver principalmente con las identidades de dispositivo y los metadatos de aplicación, que deberán combinarse con el Centro de IoT principal y, probablemente, con otros almacenes específicos de la aplicación en la región primaria. Para simplificar este paso, generalmente se recomienda usar operaciones idempotentes. Esto minimiza los efectos secundarios, no solo una posible distribución uniforme de eventos, sino también duplicados o entrega desordenada de eventos. Además, la lógica de aplicación debe diseñarse para tolerar posibles incoherencias o estados ligeramente obsoletos, debido al tiempo adicional que el sistema tarda en "limpiarse" o en función de los objetivos de punto de recuperación (RPO). El siguiente artículo proporciona más información sobre este tema: [Failsafe: instrucciones para crear arquitecturas de nube resistentes].




[img-tokenservice]: media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guía del desarrollador de Centros de IoT - Registro de identidades]: iot-hub-devguide.md#identityregistry
[Guía del desarrollador del Centro de IoT - Registro de identidades]: iot-hub-devguide.md#identityregistry
[Guía para el desarrollador del Centro de IoT - Elegir el protocolo de comunicación]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guía para desarrolladores del Centro de IoT - Seguridad]: iot-hub-devguide.md#security
[Guía para desarrolladores del Centro de IoT - Seguridad ]: iot-hub-devguide.md#security
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Puerta de enlace de protocolo]: iot-hub-protocol-gateway.md
[Cuotas y limitaciones del Centro de IoT]: iot-hub-devguide.md#throttling

[Guía de administración de dispositivos del Centro de IoT]: iot-hub-device-management.md
[Centros de eventos]: ../event-hubs/event-hubs-what-is-event-hubs/
[Administrador de tráfico de Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview/
[Event Hub publisher policies]: ../event-hubs-overview/#common-publisher-tasks
[cuotas de Bus de servicio]: ../service-bus/service-bus-quotas/
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md

[TLS-PSK]: https://tools.ietf.org/html/rfc4279

[Orientación técnica de la continuidad del negocio de Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Consideraciones sobre la alta disponibilidad y la recuperación ante desastres para las aplicaciones de Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Failsafe: instrucciones para crear arquitecturas de nube resistentes]: https://msdn.microsoft.com/library/azure/jj853352.aspx

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub

<!---HONumber=Oct15_HO1-->