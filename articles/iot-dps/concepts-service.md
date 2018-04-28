---
title: Conceptos del servicio Azure IoT Hub Device Provisioning | Microsoft Docs
description: Describe conceptos del servicio de aprovisionamiento específicos de los dispositivos con DPS e IoT Hub
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d2bc58514ea716954ec3ac96151549168fedc2ed
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceptos del servicio IoT Hub Device Provisioning

IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que se utiliza para configurar el aprovisionamiento de dispositivos sin interacción de un centro IoT especificado. Con el servicio Device Provisioning puede [aprovisionar automáticamente](concepts-auto-provisioning.md) millones de dispositivos de forma segura y escalable.

El aprovisionamiento de dispositivos es un proceso de dos partes. La primera consiste en establecer la conexión inicial entre el dispositivo y la solución de IoT *registrando* el dispositivo. La segunda es aplicar la *configuración* apropiada en el dispositivo en función de los requisitos específicos de la solución. Una vez completados ambos pasos, el dispositivo se considera totalmente *aprovisionado*. El servicio Device Provisioning automatiza ambos pasos para proporcionar una experiencia perfecta de aprovisionamiento para el dispositivo.

Este artículo proporciona información general sobre los conceptos de aprovisionamiento más aplicables a la administración del *servicio*. Este artículo es más apropiado para las personas implicadas en [el paso de configuración en la nube](about-iot-dps.md#cloud-setup-step) para preparar un dispositivo para la implementación.

## <a name="service-operations-endpoint"></a>Punto de conexión de las operaciones del servicio

El punto de conexión de las operaciones del servicio es para administrar la configuración del servicio y el mantenimiento de la lista de inscripción. Este punto de conexión solo lo utiliza el administrador del servicio; no lo usan los dispositivos.

## <a name="device-provisioning-endpoint"></a>Punto de conexión de aprovisionamiento de dispositivos

El punto de conexión de aprovisionamiento de dispositivos es el único que usan todos los dispositivos para el aprovisionamiento automático. La dirección URL es la misma para todas las instancias de los servicios de aprovisionamiento a fin de evitar tener que volver a programar los dispositivos con nueva información de conexión en escenarios de la cadena de suministro. El [ámbito de identificador](#id-scope) garantiza el aislamiento de los inquilinos.

## <a name="linked-iot-hubs"></a>Centros de IoT vinculados

El servicio Device Provisioning solo puede aprovisionar los dispositivos para los centros de IoT que se hayan vinculado a él. Vincular un centro de IoT al servicio Device Provisioning proporciona los permisos de lectura/escritura del servicio al registro de dispositivos del centro de IoT; con el vínculo, el servicio puede registrar un identificador de dispositivo y establecer la configuración inicial en el dispositivo gemelo. Los centros de IoT vinculados pueden estar en cualquier región de Azure. Puede vincular centros de otras suscripciones a su servicio de aprovisionamiento.

## <a name="allocation-policy"></a>Directiva de asignación

La configuración del nivel de servicio que determina la forma en que el servicio Device Provisioning asigna dispositivos a un centro de IoT. Hay tres directivas de asignación admitidas:
* **Distribución uniformemente ponderada**: los centros de IoT vinculados tienen la misma probabilidad de tener dispositivos aprovisionados para ellos. Es la configuración predeterminada. Si va a aprovisionar dispositivos para un único centro de IoT Hub, puede mantener esta configuración.
* **Latencia más baja**: los dispositivos se aprovisionan en un centro de IoT con la latencia más baja en el dispositivo. Si varios centros de IoT vinculados proporcionarían la misma latencia más baja, el servicio de aprovisionamiento aplica hash a los dispositivos a través de esos centros
* **Configuración estática a través de la lista de inscripción**: la especificación del centro de IoT deseado en la lista de inscripción tiene prioridad sobre la directiva de asignación del nivel de servicio.

## <a name="enrollment"></a>Inscripción

Una inscripción es el registro de dispositivos o grupos de dispositivos que pueden registrarse con el aprovisionamiento automático. El registro de inscripción contiene información sobre el dispositivo o grupo de dispositivos, como:
- el [mecanismo de atestación](concepts-security.md#attestation-mechanism) que emplea el dispositivo
- la configuración inicial deseada (opcional)
- el centro de IoT preferido
- el identificador de dispositivo elegido

Hay dos tipos de inscripciones que admite el servicio Device Provisioning:

### <a name="enrollment-group"></a>Grupo de inscripción

Un grupo de inscripción es un grupo de dispositivos que comparten un mecanismo de atestación específico. Todos los dispositivos del grupo de inscripción presentan los certificados X.509 que han sido firmados por la misma entidad de certificación raíz o intermedia. Los grupos de inscripción solo pueden utilizar el mecanismo de atestación X.509. Tanto el nombre del grupo de inscripción como el nombre del certificado deben ser alfanuméricos, estar en minúsculas y pueden contener guiones.

> [!TIP]
> Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino.

### <a name="individual-enrollment"></a>Inscripción individual

Una inscripción individual es una entrada para el registro de un único dispositivo. Las inscripciones individuales pueden usar certificados X.509 de hoja o tokens de SAS (de un módulo de plataforma segura físico o virtual) como mecanismos de atestación. El identificador de registro de una inscripción individual está formado por caracteres alfanuméricos y en minúscula, y puede contener guiones. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT deseado.

> [!TIP]
> Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo permitan la autenticación mediante tokens de SAS a través de TPM como mecanismo de atestación.

## <a name="registration"></a>Registro

Se trata del registro de un dispositivo que se ha registrado o aprovisionado correctamente en un centro de IoT a través del servicio Device Provisioning. Los registros se crean automáticamente; se pueden eliminar, pero no se pueden actualizar.

## <a name="operations"></a>Operaciones

Las operaciones son la unidad de facturación del servicio Device Provisioning. Una operación es la finalización correcta de una instrucción para el servicio. Las operaciones incluyen registros y repeticiones de registros de dispositivos, además de cambios del servicio, como agregar entradas en la lista de inscripción y actualizarlas.
