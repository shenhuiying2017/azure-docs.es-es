<properties
 pageTitle="Prácticas recomendadas de seguridad de IoT | Microsoft Azure"
 description="Prácticas recomendadas de seguridad para proteger su infraestructura IoT"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="YuriDio"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/05/2016"
 ms.author="yurid"/>

# Prácticas recomendadas de seguridad de Internet de las cosas (IoT)

La protección de una infraestructura IoT requiere una estrategia de seguridad rigurosa y detallada. A partir de la protección de datos en la nube, pasando por la protección de la integridad de los datos en tránsito a través de la red pública de Internet y el ofrecimiento de la posibilidad de aprovisionar dispositivos de forma segura, cada capa crea una mayor garantía de seguridad en la infraestructura general.

## Protección de una infraestructura IoT
 
Esta estrategia de seguridad detallada puede desarrollarse y ejecutarse con la participación activa de diversas personas implicadas en la fabricación, el desarrollo y la implementación de infraestructura y dispositivos IoT. A continuación verá una descripción de alto nivel de estos participantes.

- **Integrador/fabricante de hardware IoT**: suele tratarse de los fabricantes del hardware IoT implementado, un integrador de hardware que reúne hardware de diversos fabricantes o un proveedor de hardware que proporciona hardware para una implementación de IoT que fabrican o integran otros proveedores.
- **Desarrollador de soluciones de IoT**: del desarrollo de una solución de IoT suele encargarse un desarrollador de soluciones, que puede formar parte de un equipo interno o de un Integrador de sistemas (SI) especializado en esta actividad. El desarrollador de soluciones de IoT puede desarrollar diversos componentes de la solución de IoT desde cero, integrar varios componentes de serie o de código abierto o adoptar soluciones configuradas previamente con una menor adaptación.
- **Implementador de soluciones de IoT**: una vez que se desarrolla una solución de IoT, debe implementarse en el campo. Esto implica la implementación de hardware, la interconexión de dispositivos y la implementación de soluciones en dispositivos de hardware o en la nube.
- **Operador de soluciones de IoT**: una vez implementada la solución de IoT, esta requiere operaciones a largo plazo, supervisión, actualizaciones y mantenimiento. De esto puede encargarse un equipo interno formado por especialistas en tecnología de la información, operaciones de hardware, equipos de mantenimiento y especialistas en dominios que supervisan el comportamiento correcto de la infraestructura IoT general. 

En las secciones que aparecen a continuación se proporcionan las prácticas recomendadas para cada uno de los implicados a fin de desarrollar, implementar y poner en funcionamiento una infraestructura IoT segura.

## Integrador/fabricante de hardware IoT

Siga las prácticas recomendadas que aparecen a continuación si es integrador o fabricante de hardware IoT:

- **Hardware de ámbito de requisitos mínimos**: el diseño del hardware debe incluir características mínimas necesarias para el funcionamiento del hardware y nada más. Un ejemplo es la inclusión de puertos USB únicamente si es necesario para el funcionamiento del dispositivo. Estas características adicionales abren el dispositivo para vectores de ataques no deseados, los cuales deben evitarse. 
- **Crear hardware a prueba de manipulaciones**: mecanismo integrado para detectar la alteración física del hardware, por ejemplo, abrir la cubierta del dispositivo, quitar una parte del dispositivo, etc. Estas señales de alteración pueden formar parte de la transmisión de datos cargada en la nube que habilita la alerta a los operadores de estos eventos. 
- **Basarse en hardware seguro**: si COGS lo permite, cree características de seguridad como el almacenamiento seguro y cifrado y Módulo de plataforma segura (TPM), basadas en la funcionalidad de arranque. Estas características crean dispositivos más seguros protegiendo la infraestructura IoT general.
- **Proteger las actualizaciones**: la actualización de firmware durante el ciclo de vida del dispositivo es inevitable. La creación de dispositivos con rutas de acceso seguras para las actualizaciones y la garantía criptográfica de la versión del firmware permitirá la protección del dispositivo durante y después de las actualizaciones.

## Desarrollador de soluciones de IoT

Siga las prácticas recomendadas que aparecen a continuación si es desarrollador de soluciones de IoT:

- **Seguir metodología de desarrollo de software seguro **: el desarrollo de software seguro requiere un primer concepto sobre la seguridad desde el inicio del proyecto hasta su implementación y prueba. La elección de plataformas, idiomas y herramientas se ve influenciada por esta metodología. El Ciclo de vida de desarrollo de seguridad de Microsoft proporciona un enfoque paso a paso para la creación de software seguro.
- **Elegir software de código abierto con cuidado**: el software de código abierto ofrece una oportunidad para desarrollar soluciones con rapidez. Al elegir software de código abierto, tenga en cuenta el nivel de actividad de la comunidad para cada componente de código abierto. Una comunidad activa garantiza la compatibilidad con el software; se detectarán problemas, a los que se hará frente. Como alternativa, no se admitirá software de código abierto complejo e inactivo y lo más probable es que no se detecten problemas.
- **Integrar con cuidado**: muchos de los errores de seguridad del software existen en el límite de bibliotecas y API. Es posible que la funcionalidad que puede no requerirse para la implementación actual siga estando disponible a través de un nivel de API. Asegurarse de que todas las interfaces de componentes que se integran están protegidas garantiza la seguridad general.      

## Implementador de soluciones de IoT

Siga las prácticas recomendadas que aparecen a continuación si es implementador de soluciones de IoT:

- **Implementar hardware de forma segura**: las implementaciones de IoT pueden requerir la implementación de hardware en ubicaciones no seguras, por ejemplo, espacios públicos o configuraciones regionales no supervisadas. En este tipo de situaciones, asegúrese de que la implementación de hardware es a prueba de manipulaciones en su mayor extensión. Si USB u otros puertos están disponibles en el hardware, asegúrese de que estos se tratan de forma segura. Muchos vectores de ataques pueden usarlos como punto de entrada para los ataques.
- **Mantener seguras las claves de autenticación**: durante la implementación, cada dispositivo requiere identificadores de dispositivo y claves de autenticación asociadas generadas por el servicio en la nube. Mantenga seguras estas claves físicamente incluso después de la implementación. Un dispositivo malintencionado puede usar cualquier clave en peligro para su enmascaramiento como dispositivo existente.

## Operador de soluciones de IoT

Siga las prácticas recomendadas que aparecen a continuación si es operador de soluciones de IoT:

- **Mantener actualizado el sistema**: asegúrese de que los sistemas operativos del dispositivo y todos los controladores de dispositivo se actualizan a las versiones más recientes. Windows 10 (IoT o el resto de SKU), con actualizaciones automáticas activadas, se mantiene actualizado gracias a Microsoft, ofreciendo a los dispositivos IoT un sistema operativo seguro. Para otros sistemas operativos, como Linux, mantenerlos actualizados garantiza también su protección contra ataques malintencionados. 
- **Proteger contra la actividad malintencionada**: si el sistema operativo lo permite, incluya las últimas funcionalidades antivirus y antimalware en cada sistema operativo del dispositivo. Esto puede ayudar a mitigar las amenazas más externas. Los sistemas operativos más modernos, como Windows 10 IoT y Linux, pueden protegerse contra esta amenaza realizando los pasos adecuados. 
- **Auditar con frecuencia**: auditar infraestructura IoT para los problemas relacionados con la seguridad es clave al responder a incidentes de seguridad. La mayoría de los sistemas operativos, como Windows 10 (IoT y el resto de SKU), proporcionan el registro de eventos integrados que se debe revisar con frecuencia para asegurarse de que no se ha producido ninguna infracción de seguridad. Se puede enviar información de auditoría como transmisión independiente de la telemetría al servicio en la nube, así como analizarla.
- **Proteger físicamente la infraestructura IoT**: los peores ataques de seguridad contra la infraestructura IoT se inician mediante el acceso físico a los dispositivos. La protección contra el uso malintencionado de puertos USB y otro acceso físico es una importante práctica de seguridad. El registro de acceso físico, como el uso del puerto USB, es clave para descubrir cualquier brecha que pueda haberse producido. Una vez más, Windows 10 (IoT y el resto de SKU) habilita el registro detallado de estos eventos.
- **Proteger credenciales en la nube**: es posible que las credenciales de autenticación en la nube usadas para configurar y poner en funcionamiento una implementación de IoT sean la forma más fácil de obtener acceso y poner en peligro a un sistema IoT. Proteja las credenciales cambiando la contraseña con frecuencia y no usándolas en equipos públicos. 

Las funcionalidades de los distintos dispositivos IoT varían. Por una parte, algunos dispositivos pueden ser equipos de pleno desarrollo que ejecutan sistemas operativos de escritorio comunes, mientras que, por otra parte, algunos dispositivos pueden ejecutar sistemas operativos muy ligeros. Las prácticas recomendadas de seguridad descritas anteriormente pueden ser aplicables a estos dispositivos en diverso grado. En caso de proporcionarse, deben seguirse las prácticas recomendadas de seguridad e implementación facilitadas por el fabricante de estos dispositivos.

Puede que algunos dispositivos heredados y limitados no se hayan diseñado de forma específica para la implementación de IoT. Estos dispositivos pueden carecer de la funcionalidad para cifrar datos, conectarse a Internet, proporcionar auditoría avanzada, etc. En estos casos, gracias al uso de una puerta de enlace de campo moderna y segura para agregar datos desde dispositivos heredados, puede proporcionarse la seguridad necesaria para la conexión a Internet de estos dispositivos. En este caso, las puertas de enlace de campo proporcionan una autenticación segura, una negociación de sesiones cifradas, la recepción de comandos desde la nube y muchas otras características de seguridad.

<!---HONumber=AcomDC_0406_2016-->