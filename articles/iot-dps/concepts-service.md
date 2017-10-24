---
title: Conceptos del servicio Azure IoT Hub Device Provisioning | Microsoft Docs
description: "Describe conceptos del servicio de aprovisionamiento específicos de los dispositivos con DPS e IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceptos del servicio IoT Hub Device Provisioning

IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que se utiliza para configurar el aprovisionamiento de dispositivos sin interacción de un centro IoT especificado. Con el servicio Device Provisioning pueden aprovisionar millones de dispositivos de forma segura y escalable.

El aprovisionamiento de dispositivos es un proceso de dos partes. La primera consiste en establecer la conexión inicial entre el dispositivo y la solución de IoT *registrando* el dispositivo. La segunda es aplicar la *configuración* apropiada en el dispositivo en función de los requisitos específicos de la solución. Una vez que ha completado ambos pasos, podemos decir que el dispositivo ha sido totalmente *aprovisionado*. El servicio Device Provisioning automatiza ambos pasos para proporcionar una experiencia perfecta de aprovisionamiento para el dispositivo.

Este artículo proporciona información general sobre los conceptos de aprovisionamiento más aplicables a la administración del *servicio*. Este artículo es más apropiado para las personas implicadas en [el paso de configuración en la nube](about-iot-dps.md#cloud-setup-step) para preparar un dispositivo para la implementación.

## <a name="service-operations-endpoint"></a>Punto de conexión de las operaciones del servicio

El punto de conexión de las operaciones del servicio es para administrar la configuración del servicio y el mantenimiento de la lista de inscripción. Este punto de conexión solo lo utiliza el administrador del servicio; no lo usan los dispositivos.

## <a name="device-provisioning-endpoint"></a>Punto de conexión de aprovisionamiento de dispositivos

El punto de conexión de aprovisionamiento de dispositivos es el central con el que todos los dispositivos hablan para el aprovisionamiento. La dirección URL es la misma para todos los servicios de aprovisionamiento a fin de evitar tener que volver a programar los dispositivos con nueva información de conexión en escenarios de la cadena de suministro. El [ámbito de identificador](#id-scope) garantiza el aislamiento de los inquilinos.

## <a name="linked-iot-hubs"></a>Centros de IoT vinculados

El servicio Device Provisioning solo puede aprovisionar los dispositivos para los centros de IoT que se hayan vinculado a él. Vincular un centro de IoT al servicio Device Provisioning proporciona los permisos de lectura/escritura del servicio al registro de dispositivos del centro de IoT; con el vínculo, el servicio puede registrar un identificador de dispositivo y establecer la configuración inicial en el dispositivo gemelo. Los centros de IoT vinculados pueden estar en cualquier región de Azure. Puede vincular centros de otras suscripciones a su servicio de aprovisionamiento.

## <a name="allocation-policy"></a>Directiva de asignación

La configuración del nivel de servicio que determina la forma en que el servicio Device Provisioning asigna dispositivos a un centro de IoT. Hay tres directivas de asignación admitidas:
* **Distribución uniformemente ponderada**: los centros de IoT vinculados tienen la misma probabilidad de tener dispositivos aprovisionados para ellos. Es la configuración predeterminada. Si va a aprovisionar dispositivos para un único centro de IoT Hub, puede mantener esta configuración.
* **Latencia más baja**: los dispositivos se aprovisionan en un centro de IoT con la latencia más baja en el dispositivo. Si varios centros de IoT vinculados proporcionarían la misma latencia más baja, el servicio de aprovisionamiento aplica hash a los dispositivos a través de esos centros
* **Configuración estática a través de la lista de inscripción**: la especificación del centro de IoT deseado en la lista de inscripción tiene prioridad sobre la directiva de asignación del nivel de servicio.

## <a name="enrollment"></a>Inscripción

Una inscripción es el registro de dispositivos o grupos de dispositivos que pueden registrarse en algún momento. El registro de inscripción contiene la información sobre el dispositivo o el grupo de dispositivos, incluido el método de atestación para los dispositivos y la configuración inicial deseada opcionalmente, el centro de IoT deseado y el identificador del dispositivo deseado. Hay dos tipos de inscripciones que admite el servicio Device Provisioning.

### <a name="enrollment-group"></a>Grupo de inscripción

Un grupo de inscripción es un grupo de dispositivos que comparten un mecanismo de atestación específico. Todos los dispositivos del grupo de inscripción presentan los certificados X.509 que han sido firmados por la misma CA raíz. Los grupos de inscripción solo pueden utilizar el mecanismo de atestación X.509. Tanto el nombre del grupo de inscripción como el nombre del certificado deben ser alfanuméricos, estar en minúsculas y pueden contener guiones.

> [!TIP]
> Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino.

### <a name="individual-enrollment"></a>Inscripción individual

Una inscripción individual es una entrada para el registro de un único dispositivo. Las inscripciones individuales pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. El identificador de registro de una inscripción individual está formado por caracteres alfanuméricos y en minúscula, y puede contener guiones. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT deseado.

> [!TIP]
> Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo se puedan utilizar tokens de SAS a través de módulos de plataforma segura reales o virtuales como mecanismo de atestación.

## <a name="registration"></a>Registro

Se trata del registro de un dispositivo que se ha registrado o aprovisionado correctamente en un centro de IoT a través del servicio Device Provisioning. Los registros se crean automáticamente; se pueden eliminar, pero no se pueden actualizar.

## <a name="operations"></a>Operaciones

Las operaciones son la unidad de facturación del servicio Device Provisioning. Una operación es la finalización correcta de una instrucción para el servicio. Las operaciones incluyen registros y repeticiones de registros de dispositivos, además de cambios del servicio, como agregar entradas en la lista de inscripción y actualizarlas.
