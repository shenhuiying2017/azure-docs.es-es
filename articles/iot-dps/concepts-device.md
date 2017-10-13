---
title: Conceptos de dispositivos en el servicio Azure Device Provisioning | Microsoft Docs
description: "Describe conceptos del aprovisionamiento de dispositivos específicos de los dispositivos con el servicio Device Provisioning e IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 5297bc57729d9e983d63244c71eb21995cf73f0e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Conceptos de dispositivos del servicio IoT Hub Device Provisioning

IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que se utiliza para configurar el aprovisionamiento de dispositivos sin interacción de un centro IoT especificado. Con el servicio Device Provisioning puede aprovisionar millones de dispositivos de forma segura y escalable.

Este artículo proporciona información general sobre conceptos de *dispositivos* implicados en el aprovisionamiento de dispositivos. Este artículo es más apropiado para las personas implicadas en [el paso de fabricación](about-iot-dps.md#manufacturing-step) para preparar un dispositivo para la implementación.

## <a name="attestation-mechanism"></a>Mecanismo de atestación

El mecanismo de atestación es el método utilizado para confirmar la identidad de un dispositivo. El mecanismo de atestación también es importante para la lista de inscripciones, que indica al servicio de aprovisionamiento qué método de atestación usar con un determinado dispositivo.

> [!NOTE]
> IoT Hub utiliza un "esquema de autenticación" para un concepto similar en ese servicio.

El servicio Device Provisioning admite dos formas de atestación:
* **Certificados X.509** basado en el flujo de autenticación de certificados X.509 estándar.
* **Tokens de SAS** basado en un desafío nonce que usa el estándar TPM para las claves. Esto no requiere un módulo de plataforma segura (TPM) físico en el dispositivo, pero el servicio espera atestar usando la clave de aprobación para cada [especificación de TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Módulo de seguridad de hardware

El módulo de seguridad de hardware, o HSM, se usa para el almacenamiento seguro y basado en hardware de secretos de dispositivo y es la forma más segura de almacenamiento secreto. Tanto los certificados X.509 como los tokens de SAS se pueden almacenar en el módulo de seguridad de hardware. Los módulos de seguridad de hardware se pueden usar con ambos mecanismos de atestación que admite el servicio de aprovisionamiento.

> [!TIP]
> Se recomienda usar un módulo con los dispositivos para almacenar los secretos de forma segura en estos.

Los secretos de dispositivo también pueden almacenarse con software (en memoria), pero es una forma menos segura de almacenamiento que un módulo.

## <a name="registration-id"></a>Identificador de registro

El identificador de registro se usa para identificar de forma única un dispositivo en el servicio Device Provisioning. El identificador de dispositivo debe ser único en el [ámbito del identificador](#id-scope) del servicio de aprovisionamiento. Cada dispositivo debe tener un identificador de registro. El identificador de registro está formado por caracteres alfanuméricos y en minúscula, y puede contener guiones.

* En el caso del módulo de plataforma segura, el propio módulo proporciona el identificador de registro.
* En el caso de la atestación basada en X.509, el identificador de registro se proporciona como el nombre del sujeto del certificado.

## <a name="device-id"></a>Id. de dispositivo

El identificador de dispositivo es el mismo que aparece en IoT Hub. El identificador de dispositivo deseado puede establecerse en la entrada de inscripción, pero no es necesario establecerlo. Si no se especifica ningún identificador de dispositivo deseado en la lista de inscripción, el identificador de registro se utiliza como identificador de dispositivo al registrar el dispositivo. Más información sobre [identificadores de dispositivo en IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Ámbito de identificador

El ámbito de identificador se asigna a un servicio Device Provisioning cuando el usuario lo crea y se utiliza para identificar de forma única el servicio de aprovisionamiento específico a través del que se registrará el dispositivo . El ámbito de identificador los genera el servicio y es inmutable, lo que garantiza su exclusividad.

> [!NOTE]
> Dicha exclusividad es importante para las operaciones de implementación de ejecución prolongada y en escenarios de fusiones y adquisiciones.
