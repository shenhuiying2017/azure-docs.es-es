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
ms.openlocfilehash: ab2bfff571af659552eef8117de041ca6367ce56
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
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

### <a name="endorsement-key"></a>Clave de aprobación

La clave de aprobación es una clave asimétrica que se encuentra dentro del módulo de plataforma segura (TPM) que se generó internamente o se insertó en el momento de la fabricación y es único para cada TPM. La clave de aprobación no se puede cambiar ni quitar. La parte privada de la clave de aprobación nunca se difunde fuera del módulo de plataforma segura, mientras que la parte pública se utiliza para reconocer un módulo auténtico. Más información sobre la [clave de aprobación](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Clave raíz de almacenamiento

La clave raíz de almacenamiento se almacena en el modulo de plataforma segura y se usa para proteger las claves del módulo creadas por las aplicaciones, de modo que estas claves no se pueden utilizar sin el módulo. La clave raíz de almacenamiento se genera cuando toma posesión del módulo de plataforma segura; al desactivar este para que un nuevo usuario pueda tomar posesión, se genera una nueva clave raíz de almacenamiento. Más información sobre la [clave raíz de almacenamiento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificados X.509

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Los certificados X.509 normalmente están organizados en una cadena de certificados de confianza en la que cada certificado de la cadena está firmado por la clave privada del certificado superior siguiente y así sucesivamente, terminando en un certificado raíz autofirmado. Esto establece una cadena de confianza delegada a partir del certificado raíz generado por una entidad de certificación (CA) raíz de confianza que va descendiendo a través de cada entidad de certificación intermedia hasta el certificado de entidad final instalado en un dispositivo. Para más información consulte [Autenticación de dispositivos mediante certificados de entidades de certificación X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

A menudo la cadena de certificados representa alguna jerarquía lógica o física asociada con los dispositivos. Por ejemplo, un fabricante puede emitir un certificado de entidad de certificación raíz autofirmado, utilizar ese certificado para generar un único certificado de CA intermedio para cada fábrica, usar los certificados de cada fábrica para generar un único certificado de CA intermedio para cada línea de producción en la planta y por último, utilizar el certificado de la línea de producción para generar un certificado de dispositivo (entidad final) único para cada dispositivo que se fabrica en la línea. Para más información consulte [Explicación de los conceptos de certificados de entidad de certificación X.509 en el sector de IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificado raíz

Un certificado raíz es un certificado X.509 autofirmado que representa una entidad de certificación (CA). Es el término o anclaje de veracidad de la cadena de certificados. Una organización puede emitir sus propios certificados raíz o puede adquirirlos a través de una entidad de certificación raíz. Para más información, consulte [Obtención de certificados de entidad de certificación X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). También se puede hacer referencia al certificado raíz como un certificado de CA raíz.

### <a name="intermediate-certificate"></a>Certificado intermedio

Un certificado intermedio es un certificado X.509 que ha sido firmado por el certificado raíz (o por otro certificado que actúa como certificado intermedio en la cadena hasta el certificado raíz). El último certificado intermedio en una cadena se usa para firmar el certificado de hoja. También se puede hacer referencia a un certificado intermedio como un certificado de CA intermedio.

### <a name="leaf-certificate"></a>Certificado de hoja

El certificado de hoja, o certificado de entidad final, identifica al titular del certificado. Tiene el certificado raíz en su cadena de certificados, así como cero o más certificados intermedios. El certificado de hoja no se usa para firmar otros certificados. Identifica de forma exclusiva al dispositivo ante el servicio de aprovisionamiento, y a veces se conoce como certificado del dispositivo. Durante la autenticación, el dispositivo usa la clave privada asociada con este certificado para responder a una prueba de desafío de posesión del servicio. Para más información consulte [Autenticación de dispositivos firmados con certificados de entidad de certificación X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Control del acceso de dispositivo al servicio de aprovisionamiento con certificados X.509

El servicio de aprovisionamiento expone dos tipos de entrada de inscripción que puede usar para controlar el acceso de los dispositivos que usan el mecanismo de atestación de X.509:  

- Las entradas de [inscripción individual](./concepts-service.md#individual-enrollment) se configuran con el certificado de dispositivo asociado a un dispositivo específico. Estas entradas controlan la inscripción para dispositivos específicos.
- Las entradas de [grupo de inscripción](./concepts-service.md#enrollment-group) están asociadas con un certificado de CA raíz o intermedio específico. Estas entradas controlan la inscripción para todos los dispositivos que tienen ese certificado intermedio o raíz en su cadena de certificados. 

Cuando un dispositivo se conecta al servicio de aprovisionamiento, el servicio da prioridad a las entradas de inscripción más específicas sobre las entradas de inscripción menos específicas. Es decir, si existe una inscripción individual para el dispositivo, el servicio de aprovisionamiento aplica a esa entrada. Si no hay ninguna inscripción individual para el dispositivo y existe un grupo de inscripción para el primer certificado intermedio en la cadena de certificados del dispositivo, el servicio aplica a esa entrada, y así sucesivamente hacia arriba en la cadena a la raíz. El servicio aplica la primera entrada aplicable que encuentra de forma que:

- Si la primera entrada de inscripción que se encuentra está habilitada, el servicio aprovisiona el dispositivo.
- Si la primera entrada de inscripción que se encuentra está deshabilitada, el servicio no aprovisiona el dispositivo.  
- Si no se encuentra ninguna entrada de inscripción para cualquiera de los certificados de la cadena de certificados del dispositivo, el servicio no aprovisiona el dispositivo. 

Este mecanismo y la estructura jerárquica de las cadenas de certificados proporciona una flexibilidad eficaz en la forma de controlar el acceso de dispositivos individuales, así como el de grupos de dispositivos. Por ejemplo, imagine cinco dispositivos con las siguientes cadenas de certificado: 

- *Dispositivo 1*: certificado raíz -> certificado A -> certificado de dispositivo 1
- *Dispositivo 2*: certificado raíz -> certificado A -> certificado de dispositivo 2
- *Dispositivo 3*: certificado raíz -> certificado A -> certificado de dispositivo 3
- *Dispositivo 4*: certificado raíz -> certificado B -> certificado de dispositivo 4
- *Dispositivo 5*: certificado raíz -> certificado B -> certificado de dispositivo 5

En principio, puede crear una única entrada de inscripción de grupo habilitada para el certificado raíz para habilitar el acceso para los cinco dispositivos. Si más adelante el certificado B se pone en peligro, puede crear una entrada de grupo de inscripción deshabilitada para el certificado B para evitar que el *Dispositivo 4* y el *Dispositivo 5* se inscriban. Si después el *Dispositivo 3* se pone en peligro, puede crear una entrada de inscripción individual deshabilitada para su certificado. Esto revoca el acceso del *Dispositivo 3*, pero sigue permitiendo que el *Dispositivo 1* y el *Dispositivo 2* se inscriban.