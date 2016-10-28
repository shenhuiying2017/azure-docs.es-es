<properties
 pageTitle="Información general sobre la administración de dispositivos | Microsoft Azure"
 description="Una descripción general de la administración de dispositivos con Centro de IoT de Azure"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/16/2016"
 ms.author="bzurcher"/>



# Introducción a la administración de dispositivos desde Centro de IoT de Azure (versión preliminar)

## El enfoque de administración de dispositivos de IoT de Azure

La administración de dispositivos de Azure IoT Hub proporciona las características y el modelo de extensibilidad para dispositivos y back-ends a fin de sacar partido de la administración de dispositivos de IoT para los distintos dispositivos y protocolos de IoT. Dispositivos de la gama de IoT desde sensores muy limitados y microcontroladores de finalidad única hasta puertas de enlace más eficaces que habilitan otros dispositivos y protocolos. Las soluciones de IoT también varían considerablemente en dominios verticales y aplicaciones con casos de uso exclusivo para los operadores de cada dominio. Las soluciones de IoT pueden aprovechar las funcionalidades de administración de dispositivos, los patrones y las bibliotecas de código de IoT Hub para habilitar la administración de dispositivos del variado conjunto de dispositivos y usuarios.

## Introducción

Una parte fundamental de la creación de una solución de IoT adecuada es proporcionar una estrategia sobre el modo en que los operadores controlan la administración continua de su flota de dispositivos. Los operadores de IoT requieren herramientas y aplicaciones que sean sencillas y confiables, que les permitan centrarse en los aspectos más estratégicos de sus trabajos. Azure IoT Hub proporciona bloques de creación para crear aplicaciones de IoT que facilitan los patrones más importantes de administración de dispositivos.

Se considera que los dispositivos se administran mediante IoT Hub cuando ejecutan una aplicación sencilla llamada agente de administración de dispositivos que conecta el dispositivo de forma segura a la nube. El código de agente permite que un operador del lado de la aplicación confirme el estado del dispositivo y realice operaciones de administración de forma remota, como aplicar cambios de configuración de red o implementar actualizaciones de firmware.

## Principios de administración de dispositivos de IoT

IoT incorpora un conjunto único de desafíos de administración y una solución debe tener en cuenta los siguientes principios de administración de dispositivos de IoT:

![][img-dm_principles]

- **Escala y automatización**: IoT requiere herramientas sencillas que puedan automatizar tareas rutinarias y permitir que un personal de operaciones relativamente reducido administre millones de dispositivos. Los operadores diarios esperan controlar las operaciones de dispositivos de forma remota y masiva, y que solo se les alerte cuando surjan problemas que requieran su atención directa.

- **Versatilidad y compatibilidad**: el ecosistema de dispositivos de IoT es sumamente variado. Se deben personalizar las herramientas de administración para que se adapten a una gran variedad de clases de dispositivos, plataformas y protocolos. Los operadores deben ser capaces de admitir todos los dispositivos, desde los chips de proceso único incrustados más limitados hasta los equipos potentes y totalmente funcionales.

- **Reconocimiento del contexto**: los entornos de IoT son dinámicos y cambiantes. La confiabilidad del servicio es primordial. Las operaciones de administración de dispositivos deben tener en cuenta las ventanas de mantenimiento del Acuerdo de Nivel de Servicio, los estados de energía y de red, las condiciones de uso y la ubicación geográfica de los dispositivos para asegurarse de que el tiempo de inactividad de mantenimiento no afecte a las operaciones empresariales críticas o cree condiciones peligrosas.

- **Dar servicio a numerosos roles**: la compatibilidad con flujos de trabajo únicos y procesos de roles de operaciones de IoT es fundamental. El personal de operaciones también debe trabajar en armonía con las limitaciones específicas de los departamentos de TI internos y transmitir información importante sobre las operaciones de dispositivos a los supervisores y otros roles de administración.

## Ciclo de vida de dispositivos de IoT 

Aunque los proyectos de IoT difieren en gran medida, existe una serie de patrones comunes a la hora de administrar los dispositivos. En Azure IoT, estos patrones se identifican en el ciclo de vida de los dispositivos de IoT, que consta de cinco fases distintas:

![][img-device_lifecycle]

1. **Planeación**: habilitar a los operadores para que creen un esquema de propiedad de los dispositivos, que les permita consultar y centrarse, con facilidad y precisión, en un grupo de dispositivos para operaciones de administración masiva.

    *Bloques de creación relacionados*: [Introducción a dispositivos gemelos][lnk-twins-getstarted], [Cómo usar propiedades gemelas][lnk-twin-properties]

2. **Aprovisionamiento**: autenticar de forma segura nuevos dispositivos en IoT Hub y permitir que los operadores puedan detectar de inmediato las funcionalidades de los dispositivos y el estado actual.

    *Bloques de creación relacionados*: [Introducción a IoT Hub][lnk-hub-getstarted], [Cómo usar propiedades gemelas][lnk-twin-properties]

3. **Configuración**: facilitar los cambios de configuración de forma masiva y las actualizaciones de firmware en dispositivos, a la vez que se mantienen el estado y la seguridad.

    *Bloques de creación relacionados*: [Cómo usar propiedades gemelas][lnk-twin-properties], [Métodos D2C][lnk-c2d-methods], [Programación y difusión de trabajos][lnk-jobs]

4. **Supervisión**: supervisar la situación general de la flota de dispositivos y el estado de las implementaciones de actualización en curso para alertar a los operadores sobre los problemas que puedan necesitar su atención.

    *Bloques de creación relacionados*: [Cómo usar propiedades gemelas][lnk-twin-properties]

5. **Retirada**: reemplazar o retirar dispositivos después de un error, ciclo de actualización o al final de la duración del servicio.

    *Bloques de creación relacionados*:
    
## Patrones de administración de dispositivos de IoT Hub

IoT Hub habilita el siguiente conjunto de patrones de administración de dispositivos (iniciales). Tal como se muestra en los [tutoriales][lnk-get-started], puede ampliar estos patrones para que se ajusten al escenario exacto que necesite y diseñar nuevos patrones para otros escenarios basándose en estos patrones principales.

1. **Reinicio**: la aplicación back-end informa al dispositivo mediante un método D2C que se ha lanzado un reinicio. El dispositivo usa las propiedades notificadas de dispositivos gemelos para actualizar el estado de reinicio del dispositivo.

    ![][img-reboot_pattern]

2. **Restablecimiento de fábrica**: la aplicación back-end informa al dispositivo mediante un método D2C que se ha iniciado un restablecimiento de fábrica. El dispositivo usa las propiedades notificadas de dispositivos gemelos para actualizar el estado de restablecimiento de fábrica del dispositivo.

    ![][img-facreset_pattern]

3. **Configuración**: la aplicación back-end utiliza las propiedades deseadas de dispositivos gemelos para configurar el software que se ejecuta en el dispositivo. El dispositivo usa las propiedades notificadas de dispositivos gemelos para actualizar el estado de configuración del dispositivo.

    ![][img-config_pattern]

4. **Actualización de firmware**: la aplicación back-end informa al dispositivo mediante un método D2C que se ha iniciado una Actualización de firmware. El dispositivo inicia un proceso de varios pasos para descargar el paquete de firmware, aplicarlo y volver a conectar por último con el servicio de IoT Hub. A lo largo del proceso de varios pasos, el dispositivo usa las propiedades notificadas de dispositivos gemelos para actualizar el progreso y el estado del dispositivo.

    ![][img-fwupdate_pattern]

5. **Informes de progreso y estado**: la aplicación back-end ejecuta consultas de dispositivos gemelos, entre un conjunto de dispositivos, para informar sobre el estado y el progreso de acciones que se ejecutan en el dispositivo.

    ![][img-report_progress_pattern]

## Pasos siguientes

Con los bloques de creación que ofrece Azure IoT Hub, los desarrolladores pueden crear aplicaciones de IoT que cumplan los requisitos exclusivos de los operadores de IoT dentro en cada etapa del ciclo de vida de los dispositivos.

Para continuar aprendiendo sobre las características de administración de los dispositivos de IoT Hub, consulte el tutorial [Introducción a la administración de dispositivos de Azure IoT Hub][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->