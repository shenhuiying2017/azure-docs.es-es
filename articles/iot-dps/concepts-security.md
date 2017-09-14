---
title: Conceptos de seguridad del servicio Azure IoT Hub Device Provisioning | Microsoft Docs
description: "Describe conceptos del aprovisionamiento de la seguridad específicos de dispositivos con el servicio Azure IoT Hub Device Provisioning"
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
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.contentlocale: es-es
ms.lasthandoff: 09/06/2017

---

# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceptos de seguridad del servicio Azure IoT Hub Device Provisioning 

IoT Hub Device Provisioning es un servicio auxiliar de IoT Hub que se utiliza para configurar el aprovisionamiento de dispositivos sin interacción de un centro IoT especificado. Con el servicio Device Provisioning puede aprovisionar millones de dispositivos de forma segura y escalable. Este artículo proporciona información general sobre conceptos de *seguridad* implicados en el aprovisionamiento de dispositivos. Este artículo es apropiado para todas las personas implicadas en la preparación de un dispositivo para la implementación.

## <a name="attestation-mechanism"></a>Mecanismo de atestación

El mecanismo de atestación es el método utilizado para confirmar la identidad de un dispositivo. El mecanismo de atestación también es importante para la lista de inscripciones, que indica al servicio de aprovisionamiento qué método de atestación usar con un determinado dispositivo.

> [!NOTE]
> IoT Hub utiliza un "esquema de autenticación" para un concepto similar en ese servicio.

El servicio Device Provisioning admite dos formas de atestación:
* **Certificados X.509** basado en el flujo de autenticación de certificados X.509 estándar.
* **Tokens de SAS** basado en un desafío nonce que usa el estándar TPM para las claves. Esto no requiere un módulo de plataforma segura (TPM) físico en el dispositivo, pero el servicio espera atestar usando la clave de aprobación para cada [especificación de TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Módulo de seguridad de hardware

El módulo de seguridad de hardware, o HSM, se usa para el almacenamiento seguro y basado en hardware de secretos de dispositivo y es la forma más segura de almacenamiento secreto. Tanto los certificados X.509 como los tokens de SAS se pueden almacenar en el módulo de seguridad de hardware. Los módulos de seguridad de hardware se pueden usar con ambos mecanismos de atestación que admite el aprovisionamiento.

> [!TIP]
> Se recomienda usar un módulo con los dispositivos para almacenar los secretos de forma segura en estos.

Los secretos de dispositivo también pueden almacenarse con software (en memoria), pero es una forma menos segura de almacenamiento que un módulo.

## <a name="trusted-platform-module-tpm"></a>Módulo de plataforma segura (TPM)

Un módulo de plataforma segura puede hacer referencia a un estándar para almacenar de forma segura las claves usadas para autenticar la plataforma, o puede hacer referencia a la interfaz de E/S que se utiliza para interactuar con los módulos que implementan el estándar. Los módulos de plataforma segura pueden existir como hardware discreto, hardware integrado, basados en firmware o basados en software. Más información sobre [TPM y atestación con TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). El servicio Device Provisioning solo admite TPM 2.0.

## <a name="endorsement-key"></a>Clave de aprobación

La clave de aprobación es una clave asimétrica que se encuentra dentro del módulo de plataforma segura que se insertó en el momento de la fabricación y es único para cada módulo. La clave de aprobación no se puede cambiar ni quitar. La parte privada de la clave de aprobación nunca se difunde fuera del módulo de plataforma segura, mientras que la parte pública se utiliza para reconocer un módulo auténtico. Más información sobre la [clave de aprobación](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Clave raíz de almacenamiento

La clave raíz de almacenamiento se almacena en el modulo de plataforma segura y se usa para proteger las claves del módulo creadas por las aplicaciones, de modo que estas claves no se pueden utilizar sin el módulo. La clave raíz de almacenamiento se genera cuando toma posesión del módulo de plataforma segura; al desactivar este para que un nuevo usuario pueda tomar posesión, se genera una nueva clave raíz de almacenamiento. Más información sobre la [clave raíz de almacenamiento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Certificado raíz

Un certificado raíz es un tipo de certificado X.509 que representa una entidad de certificación y es autofirmado. Es el término de la cadena de certificados.

## <a name="intermediate-certificate"></a>Certificado intermedio

Un certificado intermedio es un certificado X.509 que ha sido firmado por el certificado raíz (o por otro certificado con el certificado raíz en su cadena) y que se usa para firmar el certificado de hoja.

## <a name="leaf-certificate"></a>Certificado de hoja

Un certificado de hoja, o el certificado de entidad final, se usa para identificar el titular del certificado y tiene el certificado raíz en su cadena de certificados. El certificado de hoja no se usa para firmar otros certificados.

