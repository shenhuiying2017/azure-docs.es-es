---
title: "Introducción a Azure IoT Hub Device Provisioning Service | Microsoft Docs"
description: Describe el aprovisionamiento de dispositivos con el servicio Device Provisioning e IoT Hub
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 12/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 108ae4a66d2c13906cd0892b6c8ee5b20c3ca605
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Aprovisionamiento de dispositivos con el servicio Azure IoT Hub Device Provisioning
Microsoft Azure proporciona un amplio conjunto de servicios públicos en la nube integrados para todas las necesidades de su solución de IoT. El servicio IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que ofrece un aprovisionamiento just-in-time, sin intervención del usuario, de la instancia apropiada de IoT Hub, que permite a los clientes aprovisionar millones de dispositivos de forma segura y escalable.

## <a name="when-to-use-device-provisioning-service"></a>Cuándo se debe usar el servicio Device Provisioning
Hay muchos escenarios de aprovisionamiento en los que el servicio Device Provisioning es una excelente opción para conectar dispositivos y configurarlos para IoT Hub como, por ejemplo:

* Aprovisionamiento sin intervención del usuario en una única solución de IoT sin codificar la información de conexión de IoT Hub instalada de fábrica (configuración inicial)
* Equilibrado de carga de dispositivos en varios centros
* Conexión de dispositivos a la solución de IoT de su propietario según los datos de transacción de ventas (multiinquilino)
* Conexión de dispositivos a una solución de IoT concreta en función del caso de uso (aislamiento de la solución)
* Conexión de un dispositivo a la instancia de IoT Hub con la latencia más baja (particionamiento geográfico)
* Reaprovisionamiento basado en un cambio del dispositivo
* Reversión de las claves utilizadas por el dispositivo para conectarse a IoT Hub (si no utiliza certificados X.509 para conectarse)

## <a name="behind-the-scenes"></a>Entre bambalinas
Todos los escenarios mencionados en la sección anterior se pueden realizar mediante el servicio de aprovisionamiento para el aprovisionamiento sin intervención del usuario con el mismo flujo. Muchos de los pasos manuales normalmente incluidos en el aprovisionamiento se automatizan con el servicio Device Provisioning para reducir el tiempo de implementación de los dispositivos de IoT y reducir el riesgo de error manual. La siguiente es una descripción de lo que ocurre en segundo plano a la hora de aprovisionar un dispositivo. El primer paso es manual pero todos los demás pasos se han automatizado.

![Flujo básico de aprovisionamiento](./media/about-iot-dps/dps-provisioning-flow.png)

1. El fabricante del dispositivo agrega la información de registro del dispositivo a la lista de inscripción en Azure Portal.
2. El dispositivo contacta con el punto de conexión del servicio de aprovisionamiento configurado de fábrica. El dispositivo pasa al servicio de aprovisionamiento su información de identificación para demostrar su identidad.
3. El servicio de aprovisionamiento valida la identidad del dispositivo mediante la validación del identificador del registro y la clave en la entrada de la lista de inscripción mediante el uso de un desafío nonce ([Módulo de plataforma segura](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) o una comprobación estándar X.509 (X.509).
4. El servicio de aprovisionamiento registra el dispositivo con un IoT Hub y rellena el [estado deseado de los dispositivos gemelos](../iot-hub/iot-hub-devguide-device-twins.md).
5. La instancia de IoT Hub devuelve la información del identificador de dispositivo al servicio de aprovisionamiento.
6. El servicio de aprovisionamiento devuelve la información de conexión de la instancia de IoT Hub al dispositivo. El dispositivo ya puede empezar a enviar datos directamente a la instancia de IoT Hub.
7. El dispositivo se conecta a IoT Hub.
8. El dispositivo obtiene el estado deseado del dispositivo gemelo en la instancia de IoT Hub.

## <a name="provisioning-process"></a>Proceso de aprovisionamiento
Hay dos pasos diferentes en el proceso de implementación de un dispositivo, en los que Device Provisioning Service toma parte, que se pueden llevar a cabo de forma independiente:

* El **paso de fabricación** en el que se crea el dispositivo y se prepara la configuración de fábrica, y
* el **paso de configuración en la nube** en el que el servicio Device Provisioning se configura para el aprovisionamiento automatizado.

Ambos pasos se adaptan perfectamente con los procesos de fabricación e implementación existentes. El servicio Device Provisioning simplifica incluso algunos procesos de implementación que implican una gran cantidad de trabajo manual para obtener la información de conexión del dispositivo.

### <a name="manufacturing-step"></a>Paso de fabricación
Este paso es todo lo que sucede en la línea de fabricación. Los roles implicados en este paso incluyen diseñador de Silicon, fabricante de Silicon, integrador y/o el fabricante final del dispositivo. En este paso se crea el propio hardware.

El servicio Device Provisioning no introduce un nuevo paso en el proceso de fabricación; más bien, se vincula al paso existente que instala el software inicial e (idealmente) el HSM en el dispositivo. En lugar de crear un identificador de dispositivo en este paso, el dispositivo simplemente se programa con la información del servicio de aprovisionamiento, por lo que debe llamar a este para obtener la información de conexión y la asignación de solución IoT al activarse.

También en este paso, el fabricante proporciona al operador o implementador del dispositivo la información clave de identificación. Esto puede ser tan simple como confirmar que todos los dispositivos tienen un certificado X.509 generado a partir de un certificado de firma proporcionado por el operador o el implementador del dispositivo para extraer la parte pública de una clave de aprobación de TPM de cada dispositivo TPM. Muchos fabricantes de Silicon ofrecen estos servicios hoy en día.

### <a name="cloud-setup-step"></a>Paso de configuración de la nube
Este paso trata sobre la configuración de la nube para un aprovisionamiento automático adecuado. Por lo general hay dos tipos de usuarios implicados en el paso de configuración de la nube: alguien que sabe cómo se deben configurar los dispositivos inicialmente (un operador del dispositivo) y otra persona que sabe cómo se van a dividir los dispositivos entre las instancias de IoT Hub (un operador de la solución).

Hay una configuración inicial única del aprovisionamiento que es obligatoria, y esta tarea normalmente la realiza el operador de la solución. Una vez configurado el servicio de aprovisionamiento, este no tiene que modificarse a menos que cambie el caso de uso.

Una vez configurado el servicio del aprovisionamiento automático, este debe estar preparado para inscribir dispositivos. Este lo realiza el operador del dispositivo, que conoce la configuración deseada de los dispositivos y es el responsable de garantizar que el servicio de aprovisionamiento pueda correctamente dar fe de la identidad del dispositivo cuando se trate de buscar su instancia de IoT Hub. El operador del dispositivo toma la información clave de identificación del fabricante y lo agrega a la lista de inscripción. Puede haber actualizaciones posteriores de la lista de inscripción a medida que se agregan nuevas entradas o se actualizan las entradas existentes con la información más reciente sobre los dispositivos.

## <a name="registration-and-provisioning"></a>Registro y aprovisionamiento
*Aprovisionamiento* tiene varios significados según el sector en el que se use el término. En el contexto del aprovisionamiento de dispositivos de IoT para su solución en la nube, el aprovisionamiento es un proceso de dos partes:

1. La primera consiste en establecer la conexión inicial entre el dispositivo y la solución de IoT registrando el dispositivo.
2. La segunda es aplicar la configuración apropiada en el dispositivo en función de los requisitos específicos de la solución en la que se registró.

Solo cuando se han completado ambos pasos, podemos decir que el dispositivo ha sido totalmente aprovisionado. Algunos servicios en la nube solo proporcionan el primer paso del proceso de aprovisionamiento, registrando los dispositivos en el punto de conexión de la solución de IoT, pero sin proporcionar la configuración inicial. El servicio Device Provisioning automatiza ambos pasos para proporcionar una experiencia perfecta de aprovisionamiento para el dispositivo.

## <a name="features-of-the-device-provisioning-service"></a>Características del servicio Device Provisioning
El servicio Device Provisioning tiene muchas características que hacen que resulte idóneo para el aprovisionamiento de dispositivos.

* **Atestación segura**: compatible con las identidades X.509 y con las basadas en TPM.
* **Lista de inscripción** que contiene el registro completo de dispositivos o grupos de dispositivos que pueden registrarse en algún momento. La lista de inscripción contiene información sobre la configuración deseada del dispositivo una vez que se registra, y se puede actualizar en cualquier momento.
* **Varias directivas de asignación** para controlar la forma en que el servicio Device Provisioning asigna dispositivos a las instancias de IoT Hub para dar apoyo a sus escenarios.
* **Registros de supervisión y diagnóstico** para asegurarse de que todo funciona correctamente.
* **Compatibilidad con varios concentradores** que permite que el servicio Device Provisioning asigne dispositivos a más de una instancia de IoT Hub. El servicio Device Provisioning puede comunicarse con los concentradores de varias suscripciones de Azure.
* **Compatibilidad entre regiones** que permite que Device Provisioning Service asigne dispositivos a instancias de IoT Hub en otras regiones.

Puede aprender más sobre los conceptos y características implicados en el aprovisionamiento de dispositivos en [conceptos de dispositivo](concepts-device.md), [conceptos de servicio](concepts-service.md) y [conceptos de seguridad](concepts-security.md).

## <a name="cross-platform-support"></a>Compatibilidad multiplataforma
El servicio Device Provisioning, al igual que todos los servicios de Azure IoT, funciona entre plataformas con diversos sistemas operativos. Azure ofrece SDK de código abierto en una variedad de [lenguajes](https://github.com/Azure/azure-iot-sdks) para facilitar la conexión de dispositivos y administrar el servicio. Device Provisioning Service admite los siguientes protocolos de conexión de dispositivos:

* HTTPS
* AMQP
* AMQP sobre WebSockets
* MQTT
* MQTT sobre WebSockets

Device Provisioning Service solo admite conexiones HTTPS para las operaciones de servicio.

## <a name="regions"></a>Regiones
Device Provisioning Service está disponible en muchas regiones. Hay una lista actualizada de las regiones existentes y de las recién anunciadas para todos los servicios en [Regiones de Azure](https://azure.microsoft.com/regions/). Puede ver que Device Provisioning Service está disponible en la página [Estado de Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> Device Provisioning Service es global y no está enlazado a una ubicación. Sin embargo, debe especificar una región en la que van a residir los metadatos asociados con el perfil de Device Provisioning Service.

## <a name="availability"></a>Disponibilidad
Mantenemos un Acuerdo de Nivel de Servicio del 99,9 % para Device Provisioning Service y también puede [leer el SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). En el [SLA de Azure](https://azure.microsoft.com/support/legal/sla/) completo se explica la disponibilidad garantizada de Azure como un conjunto.

## <a name="quotas"></a>Cuotas
Cada suscripción de Azure tiene límites de cuota predeterminados que pueden afectar al ámbito de su solución de IoT. El límite actual por suscripción es de 10 servicios Device Provisioning por suscripción.

Más información sobre los límites de cuota:

* [Límites de servicio de suscripción de Azure](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Componentes de Azure relacionados
El servicio Device Provisioning permite automatizar el aprovisionamiento de dispositivos con Azure IoT Hub. Más información sobre [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>pasos siguientes
Ahora tiene una visión general sobre el aprovisionamiento de dispositivos de IoT en Azure. El paso siguiente es probar un escenario global de IoT.
> [!div class="nextstepaction"]
> [Configuración de un servicio Azure IoT Hub Device Provisioning con Azure Portal](quick-setup-auto-provision.md)
> [Creación y aprovisionamiento de un dispositivo simulado](quick-create-simulated-device.md)
> [Configuración de un dispositivo para el aprovisionamiento](tutorial-set-up-device.md)
